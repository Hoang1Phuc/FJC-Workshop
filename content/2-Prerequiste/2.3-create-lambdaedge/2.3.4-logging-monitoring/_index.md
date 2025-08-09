---
title : "Logging and Performance Monitoring"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3.4 </b> "
---

### 🎯 Objective

Record detailed logs and monitor application performance using **AWS Lambda@Edge** and **CloudFront**:
- Enable logging for easier debugging and control.
- Monitor performance through CloudWatch Metrics and Alarms.

---

## **1️⃣ Enable Logging in Lambda**

Add logs for debugging and monitoring:

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

    console.log("User-Agent:", userAgent);
    console.log("Country:", country);

    if (userAgent.includes('curl') || country === 'CN') {
        console.log("Access blocked for", userAgent, country);
        return {
            status: '403',
            statusDescription: 'Forbidden',
            body: 'Access denied.',
        };
    }
    console.log("Request allowed");
    return request;
};
```
![Logging](/images/2.prerequisite/044-logging.png)
- When Lambda is invoked, logs will appear in **CloudWatch → Log Groups → /aws/lambda/us-east-1.<function-name>**.
![Logging](/images/2.prerequisite/045-logging.png)
---

## **2️⃣ Enable Logging for CloudFront**

1. Go to **AWS CloudFront → Distribution → Settings → Edit**.
2. In the **Logging** section, enable:
   - ✅ **Standard Logging**: select an S3 bucket to store logs.
   - ✅ **Real-time logs** (optional): capture requests instantly.
3. Save the changes.
![Logging](/images/2.prerequisite/046-logging.png)
---

## **3️⃣ Monitor Performance with CloudWatch Metrics & Alarms**

1. Go to **CloudWatch → Metrics → Lambda → By Function Name**.
2. Monitor key metrics:
   - **Invocations**: Number of times Lambda is invoked.
   - **Errors**: Number of errors.
   - **Duration**: Lambda execution time.
   - **Throttles**: Number of times execution was throttled.
![CloudWatch Metrics](/images/2.prerequisite/047-CloudWatchMetrics.png)
3. Create **CloudWatch Alarms** to alert when:
   - Error rate > 5%.
   - Execution time exceeds normal limits.
![create alarms](/images/2.prerequisite/048-createalarms.png)
![create alarms](/images/2.prerequisite/049-createalarms.png)
![create alarms](/images/2.prerequisite/050-createalarms.png)
![create alarms](/images/2.prerequisite/051-createalarms.png)

4. Enable **X-Ray Tracing** for Lambda to analyze request details.
   
  - Open **AWS Lambda Console** → Select your function (e.g., `block-ua-country`).  
  - Go to the **Configuration** tab.  
  - From the left menu → Select **Monitoring and operations tools**.  
  - Under **AWS X-Ray** → Check **Active tracing**.  
  - Click **Save** to apply changes.
---

### ✅ Result

- Complete logging for easier debugging.
- Effective monitoring and alerting for Lambda and CloudFront performance.
