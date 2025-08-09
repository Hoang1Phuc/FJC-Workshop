---
title : "Thêm Security Headers với Lambda@Edge"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 2.3.5 </b> "
---

### 🎯 Mục tiêu

Tăng cường bảo mật ứng dụng bằng cách thêm các **HTTP Security Headers** vào mọi phản hồi từ CloudFront thông qua **AWS Lambda@Edge**.

---

## **1️⃣ Viết code Lambda để thêm Security Headers**

Sử dụng Lambda@Edge với sự kiện **Viewer Response**:

```javascript
'use strict';
exports.handler = async (event) => {
    const response = event.Records[0].cf.response;
    const headers = response.headers;

    // Add Security Headers
    headers['strict-transport-security'] = [{
        key: 'Strict-Transport-Security',
        value: 'max-age=63072000; includeSubdomains; preload'
    }];
    headers['content-security-policy'] = [{
        key: 'Content-Security-Policy',
        value: "default-src 'self'"
    }];
    headers['x-content-type-options'] = [{
        key: 'X-Content-Type-Options',
        value: 'nosniff'
    }];
    headers['x-frame-options'] = [{
        key: 'X-Frame-Options',
        value: 'DENY'
    }];
    headers['referrer-policy'] = [{
        key: 'Referrer-Policy',
        value: 'same-origin'
    }];
    headers['permissions-policy'] = [{
        key: 'Permissions-Policy',
        value: 'geolocation=(), microphone=()'
    }];

    return response;
};
```

---

## **2️⃣ Triển khai Lambda@Edge**
- Mở Lambda → Nhấn Actions → Publish new version → Nhập mô tả (ví dụ: Add Security Headers).
- Deploy Lambda với **CloudFront event = Viewer Response**.
- Sau khi triển khai, mọi response từ CloudFront sẽ tự động có các security headers này.

---
## **3️⃣ Kiểm tra Security Headers**

Sau khi triển khai Lambda@Edge để thêm các Security Headers, bạn có thể kiểm tra bằng các cách sau:

### 🖥️ 1. Sử dụng cURL

Chạy lệnh trên máy tính hoặc AWS CloudShell:

```bash
curl -I https://<CloudFront-Domain>
```
Kết quả chuẩn: HTTP status code 200 OK (hoặc 302 nếu có redirect).

### ✅ Kết quả
- Tăng cường bảo mật website với các HTTP Security Headers.
- Giảm thiểu nguy cơ bị tấn công XSS, clickjacking, và dữ liệu bị lộ.
