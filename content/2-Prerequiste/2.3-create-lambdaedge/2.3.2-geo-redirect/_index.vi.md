---
title : "Triển khai Geo Redirect với Lambda@Edge"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.3.2 </b> "
---

## 🎯 Mục tiêu
- Tự động **chuyển hướng người dùng** đến trang web phù hợp với quốc gia của họ.  
- Sử dụng **AWS Lambda@Edge** (gắn vào CloudFront) để redirect trước khi request đến S3.  
- Hỗ trợ **nhiều ngôn ngữ**, dễ mở rộng trong tương lai.

---

## 1️⃣ **Kiến trúc tổng quan**

1. **Người dùng** → **CloudFront Distribution**.  
2. **CloudFront** gửi thông tin `CloudFront-Viewer-Country`.  
3. **Lambda@Edge** đọc header và xác định quốc gia.  
4. Lambda trả về **HTTP 302 Redirect** đến trang ngôn ngữ tương ứng.  
5. Trình duyệt tải nội dung từ **S3 bucket** chứa các phiên bản ngôn ngữ.  

---

## 2️⃣ **Tạo Lambda Function**

1. Đăng nhập **AWS Management Console → Lambda**.  
2. Nhấn **Create function** → **Author from scratch**.  
3. Điền:
   - **Function name:** `geo-redirect`  
   - **Runtime:** `Node.js 18.x`  
   - **Architecture:** `x86_64`  
4. **Permissions:** chọn **Create a new role with basic Lambda permissions**.  
5. Nhấn **Create function**.
![Create Function](/images/2.prerequisite/026-createfunction.png)
---

## 3️⃣ **Viết Code Geo Redirect**

```javascript
'use strict';
exports.handler = async (event) => {
    const request = event.Records[0].cf.request;
    const headers = request.headers;

    const country = headers['cloudfront-viewer-country']
        ? headers['cloudfront-viewer-country'][0].value
        : 'US';

    const redirectMap = {
        'VN': '/vi/index.html',
        'FR': '/fr/index.html',
        'US': '/en/index.html'
    };

    const redirectUrl = redirectMap[country] || '/en/index.html';

    return {
        status: '302',
        statusDescription: 'Found',
        headers: {
            'location': [{ key: 'Location', value: redirectUrl }],
            'cache-control': [{ key: 'Cache-Control', value: 'no-cache' }]
        },
    };
};
```

- Dùng **redirectMap** để dễ mở rộng.  
- Thêm **Cache-Control** để tránh cache sai redirect.  

Nhấn **Deploy** để lưu

![Deploy code](/images/2.prerequisite/027-deploycode.png)
---

## 4️⃣ **Cấu hình IAM Role và Policy**

### Gắn Policy:

- `AWSLambdaBasicExecutionRole`  
- `AWSLambdaRole`  

**Cách làm:**  
1. Vào **AWS → IAM → Roles** → Chọn role của Lambda.  
2. **Permissions → Attach policies** → Chọn 2 policy trên → **Attach**.
![Role](/images/2.prerequisite/028-Role.png)
### Sửa Trust Relationship:

1. **IAM → Roles → geo-redirect-role**  
2. **Trust relationships → Edit trust policy**  
3. Dán:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "lambda.amazonaws.com",
          "edgelambda.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
![trust policy](/images/2.prerequisite/029-trustpolicy.png)
---

## 5️⃣ **Chuẩn bị nội dung đa ngôn ngữ**

### File HTML:

- `/en/index.html` – English  
```html
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="UTF-8">
 <title>Welcome</title>
</head>
<body>
 <h1>Welcome to our website!</h1>
 <p>This is the English version of the site.</p>
</body>
</html>

```
- `/vi/index.html` – Vietnamese  
```html
<!DOCTYPE html>
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Chào mừng</title>
</head>
<body>
  <h1>Chào mừng bạn đến với website của chúng tôi!</h1>
  <p>Đây là phiên bản tiếng Việt của trang.</p>
</body>
</html>
```
- `/fr/index.html` – French  
```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Bienvenue</title>
</head>
<body>
  <h1>Bienvenue sur notre site web!</h1>
  <p>Ceci est la version française du site.</p>
</body>
</html>

```
### Upload S3:

1. Mở **S3 → Bucket**.  
2. **Create folder**: `en`, `vi`, `fr`.  
3. Upload `index.html` tương ứng.  

### Quyền truy cập:

- Demo: **Actions → Make public**.  
- Production: Dùng **CloudFront OAC** (không public trực tiếp).
![uploads3](/images/2.prerequisite/030-uploads3.png)
---

## 6️⃣ **Publish Lambda Version**

1. Vào Lambda → **Actions → Publish new version**  
![Publish](/images/2.prerequisite/031-publish.png)
2. Nhập mô tả `Initial Geo Redirect version` → **Publish**
![Publish](/images/2.prerequisite/032-publish.png)

---

## 7️⃣ **Deploy Lambda@Edge**

1. Trong Lambda → Chọn **version vừa publish**
2. **Actions → Deploy to Lambda@Edge** 
![Deploy](/images/2.prerequisite/033-deploy.png)
3. Chọn:
   - **CloudFront Distribution**  
   - **Event:** `Viewer Request`  
   - **Region:** `us-east-1`  
4. Nhấn **Deploy** → Chờ vài phút để sao chép toàn cầu.
![Deploy](/images/2.prerequisite/034-deploy.png)
---
## 8️⃣ **Kiểm tra**

- Mở `https://<CloudFront-Domain>` → Tự động redirect theo quốc gia.  
- Test với VPN hoặc:

```bash
curl -I -H "CloudFront-Viewer-Country: VN" https://<CloudFront-Domain>
```

- Kết quả:
![Deploy](/images/2.prerequisite/035-result.png)
---

## 9️⃣ **Nâng cao**

- Cookie lưu ngôn ngữ để không redirect lại.  
- Dùng **CloudFront Functions** nếu chỉ cần logic đơn giản (chi phí thấp hơn).  
- Thêm **AWS WAF** để điều hướng hoặc chặn theo vùng.  
