---
title :  "Kết nối, Test và Xác minh chức năng"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3. </b> "
---

### 🎯 Mục tiêu

Đảm bảo ứng dụng **Lambda@Edge** hoạt động chính xác bằng cách thực hiện kết nối, kiểm thử và xác minh kết quả.

---

## **1️⃣ Kết nối**

- Kiểm tra tên miền **CloudFront** đã được liên kết với Lambda.  
- Sử dụng trình duyệt hoặc công cụ `curl` để gửi yêu cầu HTTP:

```bash
curl -I https://<CloudFront-Domain>
```

---

## **2️⃣ Kiểm thử chức năng**

- Làm mới nhiều lần để xác nhận rằng các phiên bản khác nhau (A/B testing) hoặc **Geo Redirect** đang hoạt động.  
- Sử dụng **VPN** để mô phỏng vị trí địa lý khác và kiểm tra geo-redirect.  
- Mở trình duyệt → **F12 → Network** để kiểm tra **HTTP headers** và nội dung trả về.

---

## **3️⃣ Xác minh kết quả**

- Kiểm tra mã trạng thái HTTP trả về:
  - `200 OK` → Trang tải bình thường.
  - `302 Found` → Geo Redirect hoạt động đúng.
- Xác nhận rằng file HTML được phân phối chính xác đến người dùng.
