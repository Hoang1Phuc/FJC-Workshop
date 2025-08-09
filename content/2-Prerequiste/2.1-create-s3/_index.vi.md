---
    title: "Tạo S3 Bucket"
    date: "`r Sys.Date()`"
    weight: 1
    chapter: false
    pre : " <b> 2.1 </b> "
---
### 🎯 Mục tiêu

Tạo một S3 bucket để lưu trữ website tĩnh, đóng vai trò **origin** cho CloudFront.

---

### 🛠️ Các bước thực hiện

1. **Mở AWS Management Console → Dịch vụ S3**  
   Truy cập [AWS S3 Console](https://console.aws.amazon.com/s3/).

2. **Tạo Bucket mới**  
   - Nhấn **Create bucket**  
   ![S3 Bucket](/images/2.prerequisite/001-creates3.png)

   - Tên bucket: `lambda-edge-demo-site`  
   - Chọn Region: `us-east-1` *(bắt buộc cho Lambda@Edge)*  
   ![S3 Bucket](/images/2.prerequisite/002-creates3.png)

   - Bỏ chọn **Block all public access**  
   - Xác nhận cảnh báo quyền public  
   ![S3 Bucket](/images/2.prerequisite/003-creates3.png)

   - Nhấn **Create bucket** để tạo  
   ![S3 Bucket](/images/2.prerequisite/004-creates3.png)

3. **Upload Website**  
   - Tạo file `index.html` với nội dung:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Lambda@Edge Workshop</title>
</head>
<body>
  <h1>Welcome to AWS Lambda@Edge Workshop</h1>
</body>
</html>
```

   - Nhấn vào bucket vừa tạo để **Upload**  
   ![S3 Bucket](/images/2.prerequisite/005-upfile1.png)  
   ![S3 Bucket](/images/2.prerequisite/006-upfile1.png)  
   ![S3 Bucket](/images/2.prerequisite/007-upfile1.png)

4. **Bật Static Website Hosting**  
   - Vào **bucket** → Chọn tab **Properties**  
   - Kéo xuống phần **Static website hosting** → Nhấn **Edit**  
   - Chọn **Enable**  
   - Nhập:
     - Index document: `index.html`  
     - Error document: `index.html` *(hoặc file lỗi riêng nếu có)*  
   - Nhấn **Save changes**

5. **Thiết lập Bucket Policy (Public Access)**  
   - Chọn tab **Permissions → Bucket Policy** → Nhấn **Edit**  
   - Dán policy (đổi `lambda-edge-demo-site` thành tên bucket của bạn):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::lambda-edge-demo-site/*"
    }
  ]
}
```

   - Nhấn **Save**
