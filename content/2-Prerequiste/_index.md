---
title : "Preparation "
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2. </b> "
---
### 🎯 Objective

This section covers the necessary preparations for deploying the **AWS Lambda@Edge Workshop**, including:

---

## **🔑 Preparation Steps**

1. **Create a Static Website using S3**  
   - Set up an S3 bucket to store website content.  
   - Enable Static Website Hosting and configure Bucket Policy for public access.  

2. **Create a CloudFront Distribution**  
   - Distribute the website globally with low latency using Amazon CloudFront.  
   - Configure caching, SSL certificate, and behaviors for optimal performance.  

3. **Create Lambda@Edge Functions**  
   - Write and deploy Lambda functions to run at CloudFront Edge Locations.  
   - Use for personalization (Geo Redirect, A/B Testing) and security (User-Agent, Country Blocking).  

---

⚠️ **Note:**  
- Follow the steps sequentially to avoid permission and access errors.  
- Use **AWS Region `us-east-1`** to ensure Lambda@Edge works properly.  

---

👉 Select a subtopic in the menu to start.