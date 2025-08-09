---
title : "Chặn truy cập theo User-Agent hoặc Quốc gia với Lambda@Edge"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3.3 </b> "
---

### 🎯 Mục tiêu

Tăng cường bảo mật bằng cách **chặn truy cập từ một số User-Agent (ví dụ bot)** hoặc **quốc gia** không mong muốn thông qua AWS Lambda@Edge.

---

## **Bước 1 – Tạo Lambda Function**

1. Mở **AWS Management Console → Lambda**.
2. Nhấn **Create function**.
3. Chọn **Author from scratch**, sau đó nhập:
   - **Function name:** `block-ua-country`
   - **Runtime:** `Node.js 18.x`
   - **Architecture:** `x86_64`
4. Ở phần **Permissions**, chọn:
   - **Create a new role with basic Lambda permissions**.
5. Nhấn **Create function** để tạo mới.
![Create Function](/images/2.prerequisite/036-createfunction.png)
---

## **Bước 2 – Viết code chặn User-Agent hoặc Quốc gia**

1. Sau khi tạo, mở function `block-ua-country`.
2. Chọn tab **Code**, xóa code mặc định và thêm đoạn code sau:

```javascript
'use strict';
exports.handler = async (event) => {
    const request = event.Records[0].cf.request;
    const headers = request.headers;

    const userAgent = headers['user-agent']
        ? headers['user-agent'][0].value.toLowerCase()
        : '';
    const country = headers['cloudfront-viewer-country']
        ? headers['cloudfront-viewer-country'][0].value
        : 'US';

    // Chặn bot sử dụng curl hoặc quốc gia Trung Quốc (CN)
    if (userAgent.includes('curl') || country === 'CN') {
        return {
            status: '403',
            statusDescription: 'Forbidden',
            body: 'Access denied.',
        };
    }

    return request;
};
```

3. Nhấn **Deploy** để lưu code.
![Deploy code](/images/2.prerequisite/037-deploycode.png)
---

## **🔑 Cấu hình IAM Role và Policy**

### **1️⃣ Gắn Policy cần thiết**

- **AWSLambdaBasicExecutionRole**
- **AWSLambdaRole**

**Cách thực hiện:**

1. Vào **AWS Console → IAM → Roles**.
2. Chọn **Role** đang gắn với Lambda Function.
3. Chọn tab **Add Permissions → Attach policies**.
4. Gắn `AWSLambdaBasicExecutionRole` và `AWSLambdaRole`.
5. Nhấn **Attach policy** để lưu.
![Policy](/images/2.prerequisite/038-policy.png)
---

### **2️⃣ Sửa Trust Relationship**

1. Vào **IAM → Roles → block-ua-country-role**.
2. Chọn tab **Trust relationships → Edit trust policy**.
3. Thay nội dung bằng:

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
![Trust Policy](/images/2.prerequisite/039-trustpolicy.png)
---

## **Bước 3 – Publish Lambda Version**

Lambda@Edge yêu cầu một **published version**, không thể sử dụng `$LATEST`.

1. Trong Lambda function → Nhấn **Actions → Publish new version**.
2. Nhập **Description** (ví dụ: `Initial Block UA/Country version`).
3. Nhấn **Publish** để tạo version cố định.
![Publish](/images/2.prerequisite/040-publish.png)
---

## **Bước 4 – Deploy Lambda@Edge**

- Trong Lambda → Chọn **version vừa publish**.
- Nhấn **Actions → Deploy to Lambda@Edge**.
![Deploy](/images/2.prerequisite/041-deploy.png)
- Chọn:
  - **CloudFront Distribution**
  - **CloudFront event:** `Viewer Request`
  - **Region:** `us-east-1`
- Nhấn **Deploy** và đợi vài phút để Lambda được sao chép toàn cầu.
![Deploy](/images/2.prerequisite/042-deploy.png)
---

## **Bước 5 – Kiểm tra**

- Dùng `curl` để test User-Agent bị chặn:

```bash
curl -I -A "curl/7.79.1" https://<cloudfront-domain>/
```

- Kết quả trả về: `403 Forbidden`.
![Result](/images/2.prerequisite/043-result.png)
- Dùng VPN hoặc header giả lập quốc gia Trung Quốc (CN):

```bash
curl -I -H "CloudFront-Viewer-Country: CN" https://<cloudfront-domain>/
```

- Kết quả trả về: `403 Forbidden`.

- Với User-Agent và quốc gia bình thường → website vẫn truy cập được.

---

✅ **Kết quả:** Lambda@Edge sẽ chặn truy cập từ User-Agent hoặc quốc gia không mong muốn, trả về mã **403 Forbidden**.
