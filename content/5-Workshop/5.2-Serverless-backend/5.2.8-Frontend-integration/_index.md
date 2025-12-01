---
title : "Frontend Integration"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.2.8 </b> "
---

## Overview

In this section, you'll finally combine what we have built so far in the backend to connect to our CloudFront frontend we setup in the previous part.

**What you'll accomplish:**
- Configure Lambda function to include CORS headers
- Configure API Gateway CORS settings
- Configure Cognito callback URL and login URL to your CloudFront endpoint
- Test the application CRUD operations

**Estimated time**: 30 minutes

## Step 1: Configure Lambda function
Since we are calling our APIs from a different origin (CloudFront) , we have to configure CORS headers properly

1. Go to Lambda console, select **workshop-lambda-sm-rds**
2. View the `response` function, this function will inlude proper CORS headers in the responses
3. Make sure the `Access-Control-Allow-Origin` is set with your CloudFront endpoint

```javascript
function respond(statusCode, payload) {
    return {
        statusCode,
        headers: {
            "Content-Type": "application/json",
            "Access-Control-Allow-Headers": "Content-Type",
            "Access-Control-Allow-Origin": "https://[cloudfront-id].cloudfront.net", //add your CloudFront endpoint here
            "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE"
        },
        body: JSON.stringify(payload),
    };
}
```
- This makes sure that the APIs are only allow to be called from trusted origin (which is your CloudFront front-end)

## Step 2: Configure API Gateway CORS settings of resources

- Make sure that you have set the correct CORS settings in our `/users` and `/{id}` resouces. We have already done this in [5.2.6 - API Gateway setup]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.6-API-Gateway" >}}#29-enable-cors-on-each-resources)

## Step 3: Configure Cognito callback URL and login URL to your CloudFront endpoint

- We already done this in [5.2.7 - Cognito]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.7-Cognito" >}}#72-configure-allowed-callback-urls)

## Step 4: Test the application

1. Go to your CloudFront endpoint

![Home page](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/2.png)

2. Click **Sign in**
3. You will be redirected to the hosted login UI

![login](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/3.png)

4. Upon succesful login, you will be redirected to the homepage.
5. The homepage will display your information and tokens, along with the user list fetched from RDS

![homepage](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/4.png)

6. You can test CRUD operations

## This concludes our workshop