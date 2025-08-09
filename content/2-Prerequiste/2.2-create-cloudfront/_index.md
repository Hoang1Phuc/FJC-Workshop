---
title : "Create CloudFront Distribution"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
### 🎯 Objective

Use **Amazon CloudFront** to distribute a static website from S3 globally with low latency, enhanced performance, and improved security.

---

### 🛠️ Detailed Step-by-Step Guide

---

## **Step 1 – Open CloudFront Service**

- Navigate to AWS Management Console → Search and select **CloudFront**.  
- Click **Create Distribution** to start creating a new distribution.  
![Create Distribution](/images/2.prerequisite/008-create-cloudfront.png)

---

## **Step 2 – Initialization**

- **Distribution name:** Enter a name for easy management (e.g., `lambda-edge-distribution`).  
- **Description:** *(Optional)* Add a short description.  
- **Distribution type:** Select **Single website or app**.  
![Get Started](/images/2.prerequisite/009-create-cloudfront.png)

---

## **Step 3 – Configure Origin**

- **Origin domain:** Select the S3 bucket you created (`lambda-edge-demo-site`).  
- **Origin path:** Leave blank.  
- **Viewer Protocol Policy:** Choose **Redirect HTTP to HTTPS** to enforce HTTPS.  
- **Origin Access:**  
  - For demo → keep as **Public**.  
  - For production → enable **Origin Access Control (OAC)** to block direct access to S3.  
![Specify Origin](/images/2.prerequisite/010-specify-origin.png)
![Specify Origin](/images/2.prerequisite/011-specify-origin.png)
![Specify Origin](/images/2.prerequisite/012-specify-origin.png)

---

## **Step 4 – Security and Cache Behavior**

### 🔧 Steps to Verify
1. **Access the Distribution:**  
   - Go to **AWS Management Console → CloudFront → Distributions**.  
   - Select the **ID** of the newly created Distribution.

2. **Check Behavior:**  
   - Open the **Behaviors** tab.  
   - Select **Default (*) Behavior → Edit** to view details.

3. **Verify configuration:**  
   - **Caching Policy:** Should be **CachingOptimized** for optimal content delivery.  
   - **Compress objects automatically:** Enabled (**Yes**) to compress HTML, CSS, JS files.  
   - **Viewer Protocol Policy:** **Redirect HTTP to HTTPS** to ensure secure connections.  
   - **Allowed HTTP Methods:** **GET, HEAD** (add **POST** if the website has forms or APIs).  
   - **TTL:** Defaults to **86400 seconds (24 hours)**, can be left as is.

---

## **Step 5 – SSL Certificate**

- **SSL/TLS certificate:**  
  - If you have a custom domain with a certificate in **AWS Certificate Manager (ACM)** → choose **Custom SSL Certificate**.  
  - If you do not have a custom domain → keep the **Default CloudFront Certificate** (AWS provides a free certificate for `*.cloudfront.net`).  

- **Price class:**  
  - Choose **Use All Edge Locations** for global distribution.  
  - Or select limited regions to reduce cost.  

📌 **Note:**  
- If using the default domain (`*.cloudfront.net`), keep the default settings and skip custom certificate configuration.

---

## **Step 6 – Get Domain Name**

- Once the status shows **Deployed**, copy the **Domain Name** (e.g., `dxxxxxxx.cloudfront.net`).  
- Open this domain in a browser to verify that the website is distributed through CloudFront.  

![Get Domain Name](/images/2.prerequisite/013-get-domain-name.png)
![Get Domain Name](/images/2.prerequisite/014-get-domain-name.png)

---

### ✅ Result
- The website is now distributed across CloudFront **Edge Locations** globally.  
- Users worldwide will be served content from the nearest edge server, resulting in lower latency and faster page loads.