---
title :  "Implement Geo Redirect with Lambda@Edge"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.3.2 </b> "
---

## 🎯 Objective
- Automatically **redirect users** to the website page that matches their country.  
- Use **AWS Lambda@Edge** (attached to CloudFront) to redirect before requests hit S3.  
- Support **multiple languages**, easily scalable for future needs.

---

## 1️⃣ **Architecture Overview**

1. **User** → **CloudFront Distribution**.  
2. **CloudFront** sends the `CloudFront-Viewer-Country` header.  
3. **Lambda@Edge** reads the header and detects the country.  
4. Lambda returns an **HTTP 302 Redirect** to the corresponding language page.  
5. The browser loads content from **S3 bucket** containing language-specific versions.  

---

## 2️⃣ **Create Lambda Function**

1. Log in to **AWS Management Console → Lambda**.  
2. Click **Create function** → **Author from scratch**.  
3. Fill in:
   - **Function name:** `geo-redirect`  
   - **Runtime:** `Node.js 18.x`  
   - **Architecture:** `x86_64`  
4. **Permissions:** select **Create a new role with basic Lambda permissions**.  
5. Click **Create function**.
![Create Function](/images/2.prerequisite/026-createfunction.png)
---

## 3️⃣ **Write Geo Redirect Code**

```javascript
'use strict';
exports.handler = async (event) => {
    const request = event.Records[0].cf.request;
    const headers = request.headers;

    const country = headers['cloudfront-viewer-country']
        ? headers['cloudfront-viewer-country'][0].value
        : 'US';

    const redirectMap = {
        'VN': '/vi/index.html',
        'FR': '/fr/index.html',
        'US': '/en/index.html'
    };

    const redirectUrl = redirectMap[country] || '/en/index.html';

    return {
        status: '302',
        statusDescription: 'Found',
        headers: {
            'location': [{ key: 'Location', value: redirectUrl }],
            'cache-control': [{ key: 'Cache-Control', value: 'no-cache' }]
        },
    };
};
```

- Use **redirectMap** for easier maintenance.  
- Add **Cache-Control** to avoid caching incorrect redirects.  

Click **Deploy** to save.

![Deploy code](/images/2.prerequisite/027-deploycode.png)
---

## 4️⃣ **Configure IAM Role and Policy**

### Attach Policies:

- `AWSLambdaBasicExecutionRole`  
- `AWSLambdaRole`  

**Steps:**  
1. Go to **AWS → IAM → Roles** → Select your Lambda role.  
2. **Permissions → Attach policies** → Choose both policies → **Attach**.
![Role](/images/2.prerequisite/028-Role.png)
### Update Trust Relationship:

1. **IAM → Roles → geo-redirect-role**  
2. **Trust relationships → Edit trust policy**  
3. Paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "lambda.amazonaws.com",
          "edgelambda.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
![trust policy](/images/2.prerequisite/029-trustpolicy.png)
---

## 5️⃣ **Prepare Multilingual Content**

### HTML Files:

- `/en/index.html` – English  
```html
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="UTF-8">
 <title>Welcome</title>
</head>
<body>
 <h1>Welcome to our website!</h1>
 <p>This is the English version of the site.</p>
</body>
</html>
```
- `/vi/index.html` – Vietnamese  
```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Chào mừng</title>
</head>
<body>
  <h1>Chào mừng bạn đến với website của chúng tôi!</h1>
  <p>Đây là phiên bản tiếng Việt của trang.</p>
</body>
</html>
```
- `/fr/index.html` – French  
```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Bienvenue</title>
</head>
<body>
  <h1>Bienvenue sur notre site web!</h1>
  <p>Ceci est la version française du site.</p>
</body>
</html>
```
### Upload to S3:

1. Go to **S3 → Bucket**.  
2. **Create folder**: `en`, `vi`, `fr`.  
3. Upload the corresponding `index.html` file.  

### Access Permissions:

- Demo: **Actions → Make public**.  
- Production: Use **CloudFront OAC** (do not make public directly).
![uploads3](/images/2.prerequisite/030-uploads3.png)
---

## 6️⃣ **Publish Lambda Version**

1. Go to Lambda → **Actions → Publish new version**  
![Publish](/images/2.prerequisite/031-publish.png)
2. Enter a description `Initial Geo Redirect version` → **Publish**
![Publish](/images/2.prerequisite/032-publish.png)

---

## 7️⃣ **Deploy Lambda@Edge**

1. In Lambda → Select the **published version**
2. **Actions → Deploy to Lambda@Edge** 
![Deploy](/images/2.prerequisite/033-deploy.png)
3. Choose:
   - **CloudFront Distribution**  
   - **Event:** `Viewer Request`  
   - **Region:** `us-east-1`  
4. Click **Deploy** → Wait a few minutes for global replication.
![Deploy](/images/2.prerequisite/034-deploy.png)
---
## 8️⃣ **Testing**

- Open `https://<CloudFront-Domain>` → It should automatically redirect based on the user’s country.  
- Test with VPN or:

```bash
curl -I -H "CloudFront-Viewer-Country: VN" https://<CloudFront-Domain>
```

- Expected result:
![Deploy](/images/2.prerequisite/035-result.png)
---

## 9️⃣ **Advanced Enhancements**

- Use cookies to remember user’s selected language.  
- Consider **CloudFront Functions** for simple logic (lower cost).  
- Add **AWS WAF** to control or block by region.  
