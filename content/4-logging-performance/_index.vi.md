---
title : "Ghi Log Session và Đo Hiệu Năng CloudFront"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 4. </b> "
---

### 🎯 Mục tiêu

Bật ghi log và đo lường hiệu năng để giám sát và tối ưu ứng dụng **Lambda@Edge** với **CloudFront**.

---

## **1️⃣ Ghi log session**

- Mở **AWS CloudFront → Distributions → Settings → Edit**.  
- Bật **Standard Logging**, chọn **S3 bucket** để lưu log.  
- (Tùy chọn) Bật **Real-time logs** để xem log tức thời.  
- Kiểm tra trong S3 bucket → Thư mục `AWSLogs/<account-id>/CloudFront/`.

---

## **2️⃣ Đo hiệu năng**

- Sử dụng **CloudWatch Metrics** để theo dõi:
  - Thời gian phản hồi (Latency)
  - Tỷ lệ lỗi (Error rate)
  - Lưu lượng truy cập (Traffic)
- Tạo **CloudWatch Alarms** để cảnh báo khi:
  - Tỷ lệ lỗi > 5%.
  - Thời gian phản hồi vượt mức cho phép.

---

## **3️⃣ Phân tích dữ liệu**

- Dùng **AWS Athena** hoặc công cụ phân tích để đọc log từ S3.  
- Xác định **bottleneck** và tối ưu mã Lambda hoặc cấu hình CloudFront.
