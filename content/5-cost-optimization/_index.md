---
title : "Cost Analysis and Optimal Edge Location Selection"
date :  "`r Sys.Date()`" 
weight : 5 
chapter : false
pre : " <b> 5. </b> "
---

### 🎯 Objective

Analyze operational costs of **Lambda@Edge** and select optimal **Edge Locations** to reduce costs and latency.

---

## **1️⃣ Analyze costs**

- Go to **AWS Cost Explorer**:
  - View Lambda invocation costs.
  - Analyze CloudFront and data transfer costs.
- Identify regions incurring high costs.

---

## **2️⃣ Select optimal Edge Locations**

- Use **CloudFront Reports** to understand user traffic distribution.  
- Select **Edge Locations** closest to users to minimize latency and transfer costs.

---

## **3️⃣ Optimize costs**

- Configure **Caching** and appropriate TTL to reduce Lambda calls.  
- Use **Regional Edge Cache** for less frequently accessed areas.  
- Enable asset compression to reduce data transfer size.
