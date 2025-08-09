---
title : "Các bước chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---
### 🎯 Mục tiêu

Trong phần này, bạn sẽ thực hiện các bước chuẩn bị cần thiết để triển khai **AWS Lambda@Edge Workshop**, bao gồm:

---

## **🔑 Nội dung chuẩn bị**

1. **Tạo website tĩnh bằng S3**  
   - Tạo bucket S3 để lưu trữ nội dung website.  
   - Bật Static Website Hosting và cấu hình Bucket Policy cho phép truy cập public.  

2. **Tạo CloudFront Distribution**  
   - Phân phối website toàn cầu với độ trễ thấp bằng Amazon CloudFront.  
   - Cấu hình caching, SSL certificate, và behavior để tối ưu tốc độ.  

3. **Tạo Lambda@Edge Functions**  
   - Viết và deploy các Lambda Functions chạy tại các Edge Location của CloudFront.  
   - Dùng cho cá nhân hóa nội dung (Geo Redirect, A/B Testing) và bảo mật (User-Agent, Country Blocking).  

---

⚠️ **Lưu ý:**  
- Các bước cần thực hiện lần lượt để tránh lỗi phân quyền và truy cập.  
- Khi làm trên AWS Console, nên bật **AWS Region `us-east-1`** để Lambda@Edge hoạt động chính xác.  

---

👉 Chọn các mục con trong menu để bắt đầu.