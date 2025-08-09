---
title : "Connect, Test, and Verify Functionality"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

### 🎯 Objective

Ensure that the **Lambda@Edge** application works correctly by connecting, testing, and verifying results.

---

## **1️⃣ Connect**

- Verify that the **CloudFront** domain is linked with Lambda.  
- Use a browser or the `curl` tool to send HTTP requests:

```bash
curl -I https://<CloudFront-Domain>
```

---

## **2️⃣ Test functionality**

- Refresh multiple times to confirm different versions (A/B testing) or **Geo Redirect** are functioning.  
- Use **VPN** to simulate different geographic locations and test geo-redirect.  
- Open browser → **F12 → Network** to check **HTTP headers** and response content.

---

## **3️⃣ Verify results**

- Check the returned HTTP status code:
  - `200 OK` → Page loads normally.
  - `302 Found` → Geo Redirect is working correctly.
- Confirm that the correct HTML file is delivered to the user.
