---
title : "Triển khai A/B Testing với Lambda@Edge"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.3.1 </b> "
---
### 🎯 Mục tiêu

Sử dụng **AWS Lambda@Edge** để triển khai **A/B Testing** mà không cần backend. Người dùng được phân phối ngẫu nhiên giữa hai phiên bản website tĩnh được lưu trên S3.

---

## **Bước 1 – Tạo Lambda Function**

1. Mở **AWS Management Console** → Tìm và chọn **Lambda**.
2. Nhấn **Create function**.
   ![Create function](/images/2.prerequisite/015-create-function.png)

3. Chọn **Author from scratch**, sau đó nhập:
   - **Function name:** `ab-testing`
   - **Runtime:** `Node.js 18.x`
   - **Architecture:** `x86_64`
  ![Create function](/images/2.prerequisite/016-create-function.png)
4. Ở phần **Permissions**, chọn:
   - **Create a new role with basic Lambda permissions**.
5. Nhấn **Create function** để tạo mới.
   ![Create function](/images/2.prerequisite/017-create-function.png)
---

## **Bước 2 – Viết code A/B Testing**

1. Sau khi tạo, mở function **ab-testing**.
2. Chọn tab **Code**, xóa code mặc định và thêm đoạn code sau:

```javascript
'use strict';
exports.handler = async (event) => {
    const request = event.Records[0].cf.request;
    const random = Math.random();

    if (random < 0.5) {
        request.uri = '/index-v1.html';
    } else {
        request.uri = '/index-v2.html';
    }

    return request;
};
```
Nhấn **Deploy** để lưu code
 ![Deploy code](/images/2.prerequisite/018-deploycode.png)


### 🔑 Cấu hình IAM Role và Policy

Để tránh lỗi khi triển khai **Lambda@Edge**, cần cấu hình Role với đầy đủ quyền:

---

## **1️⃣ Gắn Policy cần thiết**

- **AWSLambdaBasicExecutionRole:** Cho phép Lambda ghi log vào **CloudWatch**.  
- **AWSLambdaRole:** Quyền cơ bản để Lambda hoạt động và tích hợp với dịch vụ AWS.

### **Cách thực hiện**

1. Vào **AWS Console → IAM → Roles**.  
2. Chọn **Role** đang gắn với Lambda Function (ví dụ: ab-testing-role).  
3. Chọn tab **Permissions → Attach policies**.  
4. Tìm và chọn:
   - AWSLambdaBasicExecutionRole
   - AWSLambdaRole
5. Nhấn **Attach policy** để lưu.  
6. Kiểm tra lại, trong tab **Permissions** phải có đủ **2 policy** này.
![Role](/images/2.prerequisite/019-role.png)
---

## **2️⃣ Sửa Trust Relationship**

1. Vào **IAM → Roles → ab-testing-role** (hoặc role bạn tạo).  
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
Sau khi them code
![Role](/images/2.prerequisite/020-trustpolicy.png)
## **Bước 3 – Publish Lambda Version**

Lambda@Edge yêu cầu một **published version** cụ thể và **không thể sử dụng** `$LATEST`.

1. Trong Lambda function → Nhấn **Actions → Publish new version**  
2. Nhập **Description** (ví dụ: `Initial AB Testing version`)  
3. Nhấn **Publish** để tạo version cố định (ví dụ: `Version 1`)  
![Publish](/images/2.prerequisite/021-publish.png)
## **Bước 4 – Deploy Lambda@Edge**

- Trong Lambda → Nhấn **Actions → Deploy to Lambda@Edge**  
![Deploy](/images/2.prerequisite/022-Deploy.png)
- Chọn:
  - **CloudFront Distribution:** Distribution đã tạo  
  - **CloudFront event:** Viewer Request  
  - **Region:** us-east-1  
- Nhấn **Deploy** và đợi vài phút để Lambda được sao chép toàn cầu.  
![Deploy](/images/2.prerequisite/023-Deploy.png)
---

## **Bước 5 – Upload website**

- Vào **S3 bucket** chứa website.  
- Upload:
  - `index-v1.html`
```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Công Nghệ Đám Mây - AWS Workshop</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #FF9900;
            --primary-dark: #E88A00;
            --secondary: #232F3E;
            --accent: #00A1E0;
            --text: #1A1A1A;
            --text-light: #4B5563;
            --background: #FFFFFF;
            --light-gray: #F3F4F6;
            --border: #E5E7EB;
            --rounded-sm: 4px;
            --rounded-md: 8px;
            --rounded-lg: 12px;
            --shadow-sm: 0 1px 3px rgba(0,0,0,0.1);
            --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
            --shadow-lg: 0 10px 25px rgba(0,0,0,0.1);
        }
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body>
    <header>
        <h1>Làm chủ công nghệ đám mây với AWS</h1>
    </header>
</body>
</html>
```
  - `index-v2.html`
```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Công Nghệ Đám Mây - AWS Workshop</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #00A1E0;
            --primary-dark: #E88A00;
            --secondary: #232F3E;
            --accent: #00A1E0;
            --text: #1A1A1A;
            --text-light: #4B5563;
            --background: #FFFFFF;
            --light-gray: #F3F4F6;
            --border: #E5E7EB;
            --rounded-sm: 4px;
            --rounded-md: 8px;
            --rounded-lg: 12px;
            --shadow-sm: 0 1px 3px rgba(0,0,0,0.1);
            --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
            --shadow-lg: 0 10px 25px rgba(0,0,0,0.1);
        }
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body>
    <header>
        <h1>Làm chủ công nghệ đám mây với AWS - Version B</h1>
    </header>
</body>
</html>
```
- Đảm bảo file có quyền public hoặc dùng OAC.  

---

## **Bước 6 – Kiểm tra**

- Mở **CloudFront Domain Name** trên trình duyệt.  
- Refresh nhiều lần:
  - Một số lượt hiển thị **Version A**  
![Domain](/images/2.prerequisite/024-domain.png)
  - Một số lượt hiển thị **Version B** 
![Domain](/images/2.prerequisite/025-domain.png) 
- Xác nhận Lambda hoạt động đúng, phân phối 50/50.