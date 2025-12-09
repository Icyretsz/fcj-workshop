---
title : "Frontend Integration"
date :  "2025-09-15" 
weight : 8 
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
---
title : "Giới thiệu"
date :  "2025-09-15" 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Giới thiệu về VPC Endpoint

+ Điểm cuối VPC (endpoint) là thiết bị ảo. Chúng là các thành phần VPC có thể mở rộng theo chiều ngang, dự phòng và có tính sẵn sàng cao. Chúng cho phép giao tiếp giữa tài nguyên điện toán của bạn và dịch vụ AWS mà không gây ra rủi ro về tính sẵn sàng.
+ Tài nguyên điện toán đang chạy trong VPC có thể truy cập Amazon S3 bằng cách sử dụng điểm cuối Gateway. Interface Endpoint  PrivateLink có thể được sử dụng bởi tài nguyên chạy trong VPC hoặc tại TTDL.

#### Tổng quan về workshop
Trong workshop này, bạn sẽ sử dụng hai VPC.
+ **"VPC Cloud"** dành cho các tài nguyên cloud như Gateway endpoint và EC2 instance để kiểm tra.
+ **"VPC On-Prem"** mô phỏng môi trường truyền thống như nhà máy hoặc trung tâm dữ liệu của công ty. Một EC2 Instance chạy phần mềm StrongSwan VPN đã được triển khai trong "VPC On-prem" và được cấu hình tự động để thiết lập đường hầm VPN Site-to-Site với AWS Transit Gateway. VPN này mô phỏng kết nối từ một vị trí tại TTDL (on-prem) với AWS cloud. Để giảm thiểu chi phí, chỉ một phiên bản VPN được cung cấp để hỗ trợ workshop này. Khi lập kế hoạch kết nối VPN cho production workloads của bạn, AWS khuyên bạn nên sử dụng nhiều thiết bị VPN để có tính sẵn sàng cao.

![overview](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

![Home page](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/2.png)

2. Click **Sign in**
3. You will be redirected to the hosted login UI

![login](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/3.png)

4. Upon succesful login, you will be redirected to the homepage.
5. The homepage will display your information and tokens, along with the user list fetched from RDS

![homepage](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/4.png)

6. You can test CRUD operations

## This concludes our workshop
