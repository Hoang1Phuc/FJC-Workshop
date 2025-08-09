---
title : "Session Logging and CloudFront Performance Measurement"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

### 🎯 Objective

Enable logging and measure performance to monitor and optimize **Lambda@Edge** applications with **CloudFront**.

---

## **1️⃣ Enable session logging**

- Go to **AWS CloudFront → Distributions → Settings → Edit**.  
- Enable **Standard Logging** and choose an **S3 bucket** to store logs.  
- (Optional) Enable **Real-time logs** for immediate log visibility.  
- Check logs in S3 bucket → `AWSLogs/<account-id>/CloudFront/` folder.

---

## **2️⃣ Measure performance**

- Use **CloudWatch Metrics** to monitor:
  - Response time (Latency)
  - Error rate
  - Traffic volume
- Create **CloudWatch Alarms** to alert when:
  - Error rate > 5%.
  - Response time exceeds the threshold.

---

## **3️⃣ Analyze data**

- Use **AWS Athena** or other analysis tools to read logs from S3.  
- Identify **bottlenecks** and optimize Lambda code or CloudFront configuration.
