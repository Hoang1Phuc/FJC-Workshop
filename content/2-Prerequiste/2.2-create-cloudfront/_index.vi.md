---
title : "Tạo CloudFront Distribution"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b>2.2</b> "
---
### 🎯 Mục tiêu

Sử dụng **Amazon CloudFront** để phân phối website tĩnh từ S3 ra toàn cầu với độ trễ thấp, tăng hiệu suất và bảo mật.

---

### 🛠️ Hướng dẫn chi tiết từng bước

---

## **Bước 1 – Mở dịch vụ CloudFront**

- Truy cập AWS Management Console → Tìm và chọn **CloudFront**.  
- Nhấn **Create Distribution** để bắt đầu tạo mới.  
![Create Distribution](/images/2.prerequisite/008-create-cloudfront.png)

---

## **Bước 2 – Khởi tạo**

- **Distribution name:** Nhập tên để dễ quản lý (VD: `lambda-edge-distribution`).  
- **Description:** *(Tuỳ chọn)* Thêm mô tả ngắn.  
- **Distribution type:** Chọn **Single website or app**.  
![Get Started](/images/2.prerequisite/009-create-cloudfront.png)

---

## **Bước 3 – Cấu hình Origin**

- **Origin domain:** Chọn bucket S3 đã tạo (`lambda-edge-demo-site`).  
- **Origin path:** Để trống.  
- **Viewer Protocol Policy:** Chọn **Redirect HTTP to HTTPS** để bắt buộc HTTPS.  
- **Origin Access:**  
  - Nếu demo → để **Public**.  
  - Nếu production → bật **Origin Access Control (OAC)** để chặn truy cập trực tiếp vào S3.  
![Specify Origin](/images/2.prerequisite/010-specify-origin.png)
![Specify Origin](/images/2.prerequisite/011-specify-origin.png)
![Specify Origin](/images/2.prerequisite/012-specify-origin.png)
---

## **Bước 4 – Bảo mật và Cache Behavior**
### 🔧 Các bước kiểm tra
1. **Truy cập Distribution:**  
   - Mở **AWS Management Console → CloudFront → Distributions**.  
   - Chọn **ID** của Distribution vừa tạo.

2. **Kiểm tra Behavior:**  
   - Chọn tab **Behaviors**.  
   - Chọn **Default (*) Behavior → Edit** để xem chi tiết cấu hình.

3. **Xác nhận cấu hình:**  
   - **Caching Policy:** Đang là **CachingOptimized**, tối ưu tốc độ phân phối nội dung.  
   - **Compress objects automatically:** Bật (**Yes**) để nén các file HTML, CSS, JS.  
   - **Viewer Protocol Policy:** Chọn **Redirect HTTP to HTTPS** để đảm bảo kết nối bảo mật.  
   - **Allowed HTTP Methods:** **GET, HEAD** (thêm **POST** nếu website có form hoặc API).  
   - **TTL:** Mặc định là **86400 giây (24h)**, có thể giữ nguyên.

## **Bước 5 – Chứng chỉ SSL**

- **SSL/TLS certificate:**  
  - Nếu có tên miền riêng và chứng chỉ trong **AWS Certificate Manager (ACM)** → chọn **Custom SSL Certificate**.  
  - Nếu không có tên miền riêng → dùng **Default CloudFront Certificate** (AWS tự cấp chứng chỉ cho domain `*.cloudfront.net`).  

- **Price class:**  
  - Chọn **Use All Edge Locations** để phân phối toàn cầu.  
  - Hoặc chọn giới hạn khu vực để giảm chi phí.  

📌 **Lưu ý:**  
- Nếu chỉ sử dụng domain mặc định (`*.cloudfront.net`) thì **giữ nguyên cấu hình mặc định** và bỏ qua phần chứng chỉ tùy chỉnh.


## **Bước 6 – Lấy Domain Name**

- Khi trạng thái hiển thị **Deployed**, sao chép **Domain Name** (VD: `dxxxxxxx.cloudfront.net`).  
- Mở domain này trên trình duyệt để kiểm tra website đã phân phối qua CloudFront.  
  
![Get Domain Name](/images/2.prerequisite/013-get-domain-name.png)
![Get Domain Name](/images/2.prerequisite/014-get-domain-name.png)
---
### ✅ Kết quả
- Website đã được phân phối qua mạng lưới **Edge Locations** của CloudFront.  
- Người dùng toàn cầu sẽ được phục vụ nội dung từ máy chủ gần nhất → giảm độ trễ, tăng tốc độ tải trang.
