---
title: "Create S3 Bucket"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre : " <b> 2.1 </b> "
---

### 🎯 Objective

Create an S3 bucket to host a static website, serving as the **origin** for CloudFront.

---

### 🛠️ Steps

1. **Open AWS Management Console → S3 Service**  
   Go to [AWS S3 Console](https://console.aws.amazon.com/s3/).

2. **Create a New Bucket**  
   - Click **Create bucket**  
   ![S3 Bucket](/images/2.prerequisite/001-creates3.png)

   - Bucket name: `lambda-edge-demo-site`  
   - Region: `us-east-1` *(required for Lambda@Edge)*  
   ![S3 Bucket](/images/2.prerequisite/002-creates3.png)

   - Uncheck **Block all public access**  
   - Acknowledge the public access warning  
   ![S3 Bucket](/images/2.prerequisite/003-creates3.png)

   - Click **Create bucket**  
   ![S3 Bucket](/images/2.prerequisite/004-creates3.png)

3. **Upload Website**  
   - Create a file `index.html` with the following content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Lambda@Edge Workshop</title>
</head>
<body>
  <h1>Welcome to AWS Lambda@Edge Workshop</h1>
</body>
</html>
```

   - Click on the newly created bucket to **Upload**  
   ![S3 Bucket](/images/2.prerequisite/005-upfile1.png)  
   ![S3 Bucket](/images/2.prerequisite/006-upfile1.png)  
   ![S3 Bucket](/images/2.prerequisite/007-upfile1.png)

4. **Enable Static Website Hosting**  
   - Open the **bucket** → Select the **Properties** tab  
   - Scroll down to **Static website hosting** → Click **Edit**  
   - Choose **Enable**  
   - Enter:
     - Index document: `index.html`  
     - Error document: `index.html` *(or a custom error page if available)*  
   - Click **Save changes**

5. **Set Bucket Policy (Public Access)**  
   - Go to **Permissions → Bucket Policy** → Click **Edit**  
   - Paste the following policy (replace `lambda-edge-demo-site` with your bucket name):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::lambda-edge-demo-site/*"
    }
  ]
}
```

   - Click **Save**
