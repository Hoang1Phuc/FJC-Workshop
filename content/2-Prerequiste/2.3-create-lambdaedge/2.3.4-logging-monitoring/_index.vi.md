---
title : "Ghi log và Giám sát hiệu năng"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3.4 </b> "
---

### 🎯 Mục tiêu

Ghi nhận log chi tiết và theo dõi hiệu năng của ứng dụng với **AWS Lambda@Edge** và **CloudFront**:
- Bật ghi log để dễ dàng debug và kiểm soát.
- Theo dõi hiệu năng qua CloudWatch Metrics và Alarms.

---

## **1️⃣ Bật Logging trong Lambda**

Thêm log để debug và giám sát:

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

    console.log("User-Agent:", userAgent);
    console.log("Country:", country);

    if (userAgent.includes('curl') || country === 'CN') {
        console.log("Access blocked for", userAgent, country);
        return {
            status: '403',
            statusDescription: 'Forbidden',
            body: 'Access denied.',
        };
    }
    console.log("Request allowed");
    return request;
};
```
![Logging](/images/2.prerequisite/044-logging.png)
- Khi Lambda được invoke, log sẽ hiển thị trong **CloudWatch → Log Groups → /aws/lambda/us-east-1.<function-name>**.
![Logging](/images/2.prerequisite/045-logging.png)
---

## **2️⃣ Bật Logging cho CloudFront**

1. Vào **AWS CloudFront → Distribution → Settings → Edit**.
2. Ở mục **Logging**, bật:
   - ✅ **Standard Logging**: chọn S3 bucket để lưu log.
   - ✅ **Real-time logs** (tùy chọn): ghi nhận request ngay lập tức.
3. Lưu thay đổi.
![Logging](/images/2.prerequisite/046-logging.png)
---

## **3️⃣ Giám sát hiệu năng với CloudWatch Metrics & Alarms**

1. Vào **CloudWatch → Metrics → Lambda → By Function Name**.
2. Theo dõi các metrics quan trọng:
   - **Invocations**: Số lần Lambda được gọi.
   - **Errors**: Số lượng lỗi.
   - **Duration**: Thời gian thực thi.
   - **Throttles**: Số lần bị giới hạn.
![CloudWatch Metrics](/images/2.prerequisite/047-CloudWatchMetrics.png)
3. Tạo **CloudWatch Alarms** để cảnh báo khi:
   - Tỉ lệ lỗi > 5%.
   - Thời gian thực thi vượt mức bình thường.
![create alarms](/images/2.prerequisite/048-createalarms.png)
![create alarms](/images/2.prerequisite/049-createalarms.png)
![create alarms](/images/2.prerequisite/050-createalarms.png)
![create alarms](/images/2.prerequisite/051-createalarms.png)

4. Bật **X-Ray Tracing** cho Lambda để phân tích chi tiết request.
   
  - Mở **AWS Lambda Console** → Chọn function (ví dụ: `block-ua-country`).  
  - Vào tab **Configuration**.  
  - Ở menu bên trái → Chọn **Monitoring and operations tools**.  
  - Trong mục **AWS X-Ray** → Tick chọn **Active tracing**.  
  - Nhấn **Save** để lưu thay đổi.
---

### ✅ Kết quả

- Ghi nhận đầy đủ log để dễ dàng debug.
- Có thể giám sát và cảnh báo hiệu năng của Lambda và CloudFront hiệu quả.
