+++
title = "Dọn dẹp tài nguyên  "
date = 2021
weight = 6
chapter = false
pre = "<b>6. </b>"
+++


### 🎯 Mục tiêu

Xoá toàn bộ tài nguyên (CloudFront Distribution và S3 Bucket) để tránh phát sinh chi phí sau khi hoàn thành Workshop.

---

## **Bước 1 – Xóa CloudFront Distribution**

1. Vào **AWS Management Console → CloudFront → Distributions**.  
2. Chọn Distribution bạn đã tạo.  
3. Nhấn **Disable** để vô hiệu hoá Distribution.  
   - Chờ trạng thái chuyển từ **Enabled → Disabled** (mất khoảng vài phút).  
4. Sau khi Disabled, chọn **Delete** để xoá hoàn toàn Distribution.  
   - Xác nhận thao tác xoá khi được yêu cầu.  


---

## **Bước 2 – Xóa S3 Bucket**

1. Vào **AWS Management Console → S3 → Buckets**.  
2. Chọn bucket `lambda-edge-demo-site`.  
3. Xoá toàn bộ file trong bucket:  
   - Chọn tất cả object → Nhấn **Delete** → Xác nhận xoá.  
4. Quay lại danh sách bucket → Chọn bucket → Nhấn **Delete bucket**.  
   - Nhập tên bucket để xác nhận xoá.  


---

## **Bước 3 – Kiểm tra**

- Đảm bảo Distribution và Bucket đã được xoá hoàn toàn trong CloudFront và S3.  
- Bạn có thể tạo lại từ đầu nếu cần làm lại Workshop.

---

✅ **Kết quả:**  
- Toàn bộ tài nguyên đã được dọn dẹp, không còn phát sinh chi phí duy trì dịch vụ AWS.