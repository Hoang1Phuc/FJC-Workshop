---
title : "Phân tích Chi Phí và Chọn Vị Trí Edge Location Tối Ưu"
date :  "`r Sys.Date()`" 
weight : 5 
chapter : false
pre : " <b> 5. </b> "
---

### 🎯 Mục tiêu

Phân tích chi phí vận hành **Lambda@Edge** và chọn vị trí **Edge Location** tối ưu để giảm chi phí và độ trễ.

---

## **1️⃣ Phân tích chi phí**

- Vào **AWS Cost Explorer**:
  - Xem chi phí Lambda invocations.
  - Phân tích chi phí CloudFront và dữ liệu truyền tải.
- Xác định các khu vực gây chi phí cao.

---

## **2️⃣ Chọn vị trí Edge Location tối ưu**

- Sử dụng **CloudFront Reports** để biết phân bố lưu lượng người dùng.  
- Chọn **Edge Location** gần người dùng nhất để giảm độ trễ và chi phí truyền tải.

---

## **3️⃣ Tối ưu hóa chi phí**

- Cấu hình **Caching** và TTL hợp lý để giảm số lần gọi Lambda.  
- Sử dụng **Regional Edge Cache** cho khu vực ít lưu lượng.  
- Bật nén (compression) cho các asset để giảm dung lượng truyền tải.
