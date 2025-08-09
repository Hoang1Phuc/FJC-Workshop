+++
title = "Clean up resources"
date = 2022
weight = 6
chapter = false
pre = "<b>6. </b>"
+++
--

### 🎯 Objective

Analyze the cost of running Lambda@Edge and select optimal Edge Locations to minimize expenses.

---

## **1️⃣ Cost Analysis**

- Use AWS Cost Explorer to review:
  - Lambda costs
  - CloudFront and data transfer costs
- Identify high-cost regions.

---

## **2️⃣ Optimal Location Selection**

- Based on user distribution and CloudFront reports.
- Choose Edge Locations closest to users to reduce latency and transfer costs.

---

## **3️⃣ Optimization**

- Utilize caching and appropriate TTLs to reduce Lambda invocations.
- Consider using Regional Edge Cache for low-traffic regions.