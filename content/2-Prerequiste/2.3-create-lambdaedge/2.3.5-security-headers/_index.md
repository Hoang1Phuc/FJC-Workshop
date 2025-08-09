
### 🎯 Objective

Enhance application security by adding **HTTP Security Headers** to every response from CloudFront using **AWS Lambda@Edge**.

---

## **1️⃣ Write Lambda Code to Add Security Headers**

Use Lambda@Edge with the **Viewer Response** event:

```javascript
'use strict';
exports.handler = async (event) => {
    const response = event.Records[0].cf.response;
    const headers = response.headers;

    // Add Security Headers
    headers['strict-transport-security'] = [{
        key: 'Strict-Transport-Security',
        value: 'max-age=63072000; includeSubdomains; preload'
    }];
    headers['content-security-policy'] = [{
        key: 'Content-Security-Policy',
        value: "default-src 'self'"
    }];
    headers['x-content-type-options'] = [{
        key: 'X-Content-Type-Options',
        value: 'nosniff'
    }];
    headers['x-frame-options'] = [{
        key: 'X-Frame-Options',
        value: 'DENY'
    }];
    headers['referrer-policy'] = [{
        key: 'Referrer-Policy',
        value: 'same-origin'
    }];
    headers['permissions-policy'] = [{
        key: 'Permissions-Policy',
        value: 'geolocation=(), microphone=()'
    }];

    return response;
};
```

---

## **2️⃣ Deploy Lambda@Edge**
- Open Lambda → Click **Actions → Publish new version** → Enter description (e.g., Add Security Headers).
- Deploy Lambda with **CloudFront event = Viewer Response**.
- After deployment, every response from CloudFront will automatically include these security headers.

---

## **3️⃣ Verify Security Headers**

After deploying Lambda@Edge to add Security Headers, you can verify them as follows:

### 🖥️ 1. Using cURL

Run the command on your computer or AWS CloudShell:

```bash
curl -I https://<CloudFront-Domain>
```
The correct result: HTTP status code 200 OK (or 302 if there is a redirect)

### ✅ Expected Result
- Your website is enhanced with HTTP Security Headers.
- Mitigates risks of XSS attacks, clickjacking, and data exposure.
