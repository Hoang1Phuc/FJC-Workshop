---
title : "Block access by User-Agent or Country with Lambda@Edge"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3.3 </b> "
---

### 🎯 Objective

Enhance security by **blocking access from certain User-Agents (e.g., bots)** or **unwanted countries** using AWS Lambda@Edge.

---

## **Step 1 – Create Lambda Function**

1. Open **AWS Management Console → Lambda**.
2. Click **Create function**.
3. Select **Author from scratch**, then enter:
   - **Function name:** `block-ua-country`
   - **Runtime:** `Node.js 18.x`
   - **Architecture:** `x86_64`
4. In **Permissions**, choose:
   - **Create a new role with basic Lambda permissions**.
5. Click **Create function**.
![Create Function](/images/2.prerequisite/036-createfunction.png)
---

## **Step 2 – Write Code to Block User-Agent or Country**

1. After creation, open the `block-ua-country` function.
2. In the **Code** tab, delete the default code and add:

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

    // Block bots using curl or traffic from China (CN)
    if (userAgent.includes('curl') || country === 'CN') {
        return {
            status: '403',
            statusDescription: 'Forbidden',
            body: 'Access denied.',
        };
    }

    return request;
};
```

3. Click **Deploy** to save the code.
![Deploy code](/images/2.prerequisite/037-deploycode.png)
---

## **🔑 Configure IAM Role and Policy**

### **1️⃣ Attach Necessary Policies**

- **AWSLambdaBasicExecutionRole**
- **AWSLambdaRole**

**Steps:**

1. Go to **AWS Console → IAM → Roles**.
2. Select the **Role** attached to the Lambda Function.
3. Go to **Add Permissions → Attach policies**.
4. Attach `AWSLambdaBasicExecutionRole` and `AWSLambdaRole`.
5. Click **Attach policy** to save.
![Policy](/images/2.prerequisite/038-policy.png)
---

### **2️⃣ Edit Trust Relationship**

1. Go to **IAM → Roles → block-ua-country-role**.
2. Open the **Trust relationships → Edit trust policy** tab.
3. Replace content with:

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
![Trust Policy](/images/2.prerequisite/039-trustpolicy.png)
---

## **Step 3 – Publish Lambda Version**

Lambda@Edge requires a **published version** and cannot use `$LATEST`.

1. In the Lambda function → Click **Actions → Publish new version**.
2. Enter a **Description** (e.g., `Initial Block UA/Country version`).
3. Click **Publish** to create a fixed version.
![Publish](/images/2.prerequisite/040-publish.png)
---

## **Step 4 – Deploy Lambda@Edge**

- In Lambda → Select the **published version**.
- Click **Actions → Deploy to Lambda@Edge**.
![Deploy](/images/2.prerequisite/041-deploy.png)
- Choose:
  - **CloudFront Distribution**
  - **CloudFront event:** `Viewer Request`
  - **Region:** `us-east-1`
- Click **Deploy** and wait a few minutes for the Lambda to propagate globally.
![Deploy](/images/2.prerequisite/042-deploy.png)
---

## **Step 5 – Testing**

- Use `curl` to test a blocked User-Agent:

```bash
curl -I -A "curl/7.79.1" https://<cloudfront-domain>/
```

- Expected result: `403 Forbidden`.
![Result](/images/2.prerequisite/043-result.png)
- Use VPN or a header to simulate traffic from China (CN):

```bash
curl -I -H "CloudFront-Viewer-Country: CN" https://<cloudfront-domain>/
```

- Expected result: `403 Forbidden`.

- With normal User-Agent and country → website remains accessible.

---

✅ **Result:** Lambda@Edge blocks access from unwanted User-Agents or countries, returning **403 Forbidden**.
