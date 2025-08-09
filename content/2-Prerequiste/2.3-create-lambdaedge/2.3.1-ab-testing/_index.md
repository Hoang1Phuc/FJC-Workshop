---
title : "Create Lambda@Edge Functions"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.3.1</b> "
---


### 🎯 Objective

Use **AWS Lambda@Edge** to deploy **A/B Testing** without the need for a backend. Users are randomly served between two static website versions stored in S3.

---

## **Step 1 – Create Lambda Function**

1. Open **AWS Management Console** → Search and select **Lambda**.
2. Click **Create function**.  
   ![Create function](/images/2.prerequisite/015-create-function.png)

3. Choose **Author from scratch**, then enter:
   - **Function name:** `ab-testing`
   - **Runtime:** `Node.js 18.x`
   - **Architecture:** `x86_64`  
   ![Create function](/images/2.prerequisite/016-create-function.png)
4. Under **Permissions**, select:
   - **Create a new role with basic Lambda permissions**.
5. Click **Create function** to finish.  
   ![Create function](/images/2.prerequisite/017-create-function.png)

---

## **Step 2 – Write A/B Testing Code**

1. After creating, open the **ab-testing** function.
2. Go to the **Code** tab, remove the default code, and add:

```javascript
'use strict';
exports.handler = async (event) => {
    const request = event.Records[0].cf.request;
    const random = Math.random();

    if (random < 0.5) {
        request.uri = '/index-v1.html';
    } else {
        request.uri = '/index-v2.html';
    }

    return request;
};
```
Click **Deploy** to save the code.  
![Deploy code](/images/2.prerequisite/018-deploycode.png)

---

### 🔑 IAM Role and Policy Configuration

To avoid errors when deploying **Lambda@Edge**, you need to configure the role with full permissions:

---

## **1️⃣ Attach Required Policies**

- **AWSLambdaBasicExecutionRole:** Allows Lambda to write logs to **CloudWatch**.  
- **AWSLambdaRole:** Basic permissions for Lambda to run and integrate with AWS services.

### **Steps**

1. Go to **AWS Console → IAM → Roles**.  
2. Select the **Role** attached to the Lambda function (e.g., ab-testing-role).  
3. Go to **Permissions → Attach policies**.  
4. Search and select:
   - AWSLambdaBasicExecutionRole
   - AWSLambdaRole
5. Click **Attach policy** to save.  
6. Verify that both policies are present under **Permissions**.  
![Role](/images/2.prerequisite/019-role.png)

---

## **2️⃣ Update Trust Relationship**

1. Go to **IAM → Roles → ab-testing-role** (or the role you created).  
2. Open the **Trust relationships** tab → Click **Edit trust policy**.  
3. Replace the content with:

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
After adding the policy:  
![Role](/images/2.prerequisite/020-trustpolicy.png)

---

## **Step 3 – Publish Lambda Version**

Lambda@Edge requires a **published version** and **cannot use** `$LATEST`.

1. In the Lambda function → Click **Actions → Publish new version**  
2. Enter **Description** (e.g., `Initial AB Testing version`)  
3. Click **Publish** to create a fixed version (e.g., `Version 1`)  
![Publish](/images/2.prerequisite/021-publish.png)

---

## **Step 4 – Deploy Lambda@Edge**

- In Lambda → Click **Actions → Deploy to Lambda@Edge**  
![Deploy](/images/2.prerequisite/022-Deploy.png)
- Choose:
  - **CloudFront Distribution:** the distribution you created  
  - **CloudFront event:** Viewer Request  
  - **Region:** us-east-1  
- Click **Deploy** and wait a few minutes for Lambda to replicate globally.  
![Deploy](/images/2.prerequisite/023-Deploy.png)

---

## **Step 5 – Upload Website**

- Go to the **S3 bucket** that hosts your website.  
- Upload:
  - `index-v1.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cloud Technology - AWS Workshop</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #FF9900;
            --primary-dark: #E88A00;
            --secondary: #232F3E;
            --accent: #00A1E0;
            --text: #1A1A1A;
            --text-light: #4B5563;
            --background: #FFFFFF;
            --light-gray: #F3F4F6;
            --border: #E5E7EB;
            --rounded-sm: 4px;
            --rounded-md: 8px;
            --rounded-lg: 12px;
            --shadow-sm: 0 1px 3px rgba(0,0,0,0.1);
            --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
            --shadow-lg: 0 10px 25px rgba(0,0,0,0.1);
        }
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body>
    <header>
        <h1>Master Cloud Technology with AWS</h1>
    </header>
</body>
</html>
```
  - `index-v2.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cloud Technology - AWS Workshop</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #00A1E0;
            --primary-dark: #E88A00;
            --secondary: #232F3E;
            --accent: #00A1E0;
            --text: #1A1A1A;
            --text-light: #4B5563;
            --background: #FFFFFF;
            --light-gray: #F3F4F6;
            --border: #E5E7EB;
            --rounded-sm: 4px;
            --rounded-md: 8px;
            --rounded-lg: 12px;
            --shadow-sm: 0 1px 3px rgba(0,0,0,0.1);
            --shadow-md: 0 4px 6px rgba(0,0,0,0.1);
            --shadow-lg: 0 10px 25px rgba(0,0,0,0.1);
        }
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body>
    <header>
        <h1>Master Cloud Technology with AWS - Version B</h1>
    </header>
</body>
</html>
```
- Make sure the files are publicly accessible or use OAC.  

---

## **Step 6 – Verify**

- Open your **CloudFront Domain Name** in the browser.  
- Refresh multiple times:
  - Some requests will show **Version A**  
![Domain](/images/2.prerequisite/024-domain.png)
  - Some requests will show **Version B**  
![Domain](/images/2.prerequisite/025-domain.png) 
- Confirm that Lambda is working correctly and distributing traffic 50/50.
  