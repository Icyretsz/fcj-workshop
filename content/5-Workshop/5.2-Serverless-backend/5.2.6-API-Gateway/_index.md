---
title : "API Gateway Setup"
date :  "2025-09-15" 
weight : 6 
chapter : false
pre : " <b> 5.2.6 </b> "
---

## Overview

In this section, you'll create an Amazon API Gateway REST API that serves as the entry point for your serverless backend. API Gateway will receive HTTP requests from clients, route them to your Lambda function, and return responses.

**What you'll accomplish:**
- Understand API Gateway concepts and integration types
- Create a REST API in API Gateway
- Define resources and methods for user operations
- Configure Lambda proxy integration
- Enable CORS for frontend integration
- Deploy API to a stage
- Test API endpoints with various tools
- Monitor API usage and performance
- Understand API Gateway pricing

**Estimated time**: 40-50 minutes

## API Gateway Architecture

### What We'll Build
```
Client (Browser/Mobile/Postman)
    ↓
HTTPS Request
    ↓
API Gateway REST API
    ├── POST   /users          → Lambda (Create User)
    ├── GET    /users          → Lambda (Get All Users)
    ├── GET    /users/{id}     → Lambda (Get Single User)
    ├── PUT    /users/{id}     → Lambda (Update User)
    └── DELETE /users/{id}     → Lambda (Delete User)
    ↓
Lambda Function (workshop-userHandler)
    ↓
RDS PostgreSQL
```

### Costs Considerations
## Free-tier:

1M REST API CALLS RECEIVED | 1M HTTP API CALLS RECEIVED | 1M MESSAGES | 750,000 CONNECTION MINUTES per month

## Paid-tier
1. REST API
| **API Calls (per month)** | **Price (per million)** |
|---------------------------|--------------------------|
| First 333 million         | $4.25                    |
| Next 667 million          | $3.53                    |
| Next 19 billion           | $3.00                    |
| Over 20 billion           | $1.91                    |

2. Caching: 0.5GB -> $0.028/hour
3. Additional costs: CloudWatch logs (free-tier eligible)
4. Overall: <$5 (clean up immediately after finish workshop)

### API Gateway Concepts

**REST API:**
- Resource-based API (e.g., `/users`, `/users/{id}`)
- Supports all HTTP methods (GET, POST, PUT, DELETE, etc.)
- Request/response transformation
- Built-in throttling and caching

**Resources:**
- URL paths (e.g., `/users`)
- Can be nested (e.g., `/users/{id}/tasks`)

**Methods:**
- HTTP operations on resources (GET, POST, PUT, DELETE)
- Each method can have different integration

**Integration Types:**
- **Lambda Proxy**: Passes entire request to Lambda (recommended)
- **Lambda**: Custom request/response mapping
- **HTTP**: Proxy to HTTP endpoint
- **Mock**: Returns static response
- **AWS Service**: Direct AWS service integration

**Stages:**
- Deployment environments (e.g., dev, staging, prod)
- Each stage has unique URL
- Can have different settings per stage

## Step 1: Create REST API

### 1.1 Navigate to API Gateway Console

1. Go to AWS Console
2. Search for "API Gateway"
3. Click **API Gateway** under Services

![API Gateway Console](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/1.png)

### 1.2 Create API

1. Click **Create an API**

You'll see several API types:

- **HTTP API**: Simpler, cheaper, faster (70% cost reduction)
- **REST API**: Full features, request/response transformation
- **WebSocket API**: Real-time bidirectional communication
- **REST API (Private)**: VPC-only access

2. Under **REST API**, click **Build**

![API Gateway REST API](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/2.png)

### 1.3 Configure API Settings

1. **Choose the protocol:**
- Keep **REST** selected

2. **Create new API:**
- Select **New API**

**API details:**

3. **API name:** `workshop-user-api`

4. **Description:** `REST API for user management in serverless workshop`

5. **Endpoint Type:**
- Select **Regional**
    - Deployed in current region
    - Lower latency for users in same region
    - Can add CloudFront later for global distribution

{{% notice info %}}
**Endpoint Types:**<br>
**Regional**: Deployed in a single region, recommended for most use cases<br>
**Edge Optimized**: Automatically distributed via CloudFront (adds latency for regional traffic)<br>
**Private**: Only accessible within VPC<br>
For this workshop, **Regional** is best. You already have CloudFront from Part 1: Frontend Deployment if you want global distribution.
{{% /notice %}}

6. **Security policy**
- Select **SecurityPolicy_TLS13_1_2_2021_06**
- This option protects data in transit between a client and server with TLS 1.3

3. Click **Create API**

![API Gateway Settings](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/3.png)

You'll be taken to the API Gateway console showing your new API.

![API Gateway Created](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/4.png)

## Step 2: Create Resources and Methods

### 2.1 Create /users Resource

A resource represents a REST API endpoint path.

1. In the API Gateway console, select **Resources** in left navigation (if not already selected)
2. Click **Create Resource**

**Proxy resource**:
- Essentially a catch-all resource
- A proxy resource (often created as {proxy+}) is a special type of resource in API Gateway that forwards all requests to a single backend (such as a Lambda function) — regardless of the URL path or HTTP method.
- We won't be using this for our workshop since our API is simple

**Resource Path:** `/`

**Resource Name:** `users`
- This becomes the URL path

**CORS:**
- Check this box
- Automatically adds OPTIONS method with CORS headers
- Enable CORS on all child methods
- Required for browser-based frontends

![API Gateway Resource Settings](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/5.png)

4. Click **Create Resource**

You'll see `/users` appear in the resource tree.

![API Gateway Users Resource](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/6.png)

### 2.2 Create /users/{id} Resource

Create a child resource with path parameter for single user operations.

1. Select `/users` resource (click on it)
2. Click **Create method**

**New Child Resource:**

**Resource Path:** `user`
- Singular, represents a single user

**Resource Name:** `{id}`
- Curly braces indicate a path parameter

**CORS:**
- Check this box

![API Gateway ID Resource](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/7.png)

3. Click **Create Resource**

Your resource tree now shows:

![API Gateway Resource Tree](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/8.png)

### 2.3 Create POST Method on /users

Methods define HTTP operations on resources.

1. Click on `/users` resource
2. Click **Create Method**
3. Select **POST**

**Setup - POST:**

**Integration type:**
- Select **Lambda Function**

**Use Lambda Proxy integration:**
- Check this box
- Passes entire request to Lambda as-is
- Lambda returns API Gateway-formatted response

**Lambda Region:**
- Select your region (e.g., `ap-southeast-1`)

**Lambda Function:**
- Type: `workshop-lambda-sm-rds`
- Should auto-complete

**Permission prompt:**
You'll see a popup: "Add Permission to Lambda Function"

This grants API Gateway permission to invoke your Lambda function.

7. Click **Create method**

![API Gateway POST Setup](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/9.png)

### 2.4 Create GET Method on /users

Get all users.

1. Click on `/users` resource
2. Click **Create Method**
3. **Method**: **GET**

**Setup - GET:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Checked
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Click **Create method**

### 2.5 Create GET Method on /users/{id}

Get single user.

1. Click on `/users/{id}` resource
2. Click **Create Method**
3. **Method**: **GET**

**Setup - GET:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Checked
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Click **Create method**

### 2.6 Create PUT Method on /users/{id}

Update user.

1. Click on `/users/{id}` resource
2. Click **Create Method**
3. **Method**: **PUT**

**Setup - PUT:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Checked
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Click **Create method**

### 2.7 Create DELETE Method on /users/{id}

Delete user.

1. Click on `/users/{id}` resource
2. Click **Create Method**
3. **Method**: **DELETE**

**Setup - DELETE:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Checked
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Click **Create method**

### 2.8 Verify Resource Structure

Your API structure should now look like:

![API Gateway Complete Structure](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/10.png)

### 2.9 Enable CORS on each resources
1. Click **/users** resource
2. Click **Enable CORS**
3. Select the methods: GET, POST
4. **Access-Control-Allow-Origin**: input your CloudFront endpoint to restrict origin, or `*` for debug
5. Click **Save**

![API Gateway Complete Structure](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/26.png)

6. Do the same for **/{id}** resource (methods: GET, PUT, DELETE)
7. After enabling CORS for those two resources, you will see OPTION method in each resource
8. Click on OPTION method, go to **Integration response** to see the details

![API Gateway Complete Structure](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/27.png)

## Step 3: Deploy API

APIs must be deployed to a stage before they're accessible.

### 3.1 Create Deployment

1. Click **Deploy API**

**Deployment stage:**
- Select **[New Stage]**

**Stage name:** `dev`
- Short for development
- Other common names: `prod`, `staging`, `test`

**Stage description:** `Development stage for workshop`

**Deployment description:** `Initial deployment`

![API Gateway Deploy Settings](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/11.png)

3. Click **Deploy**

### 3.2 Get API Endpoint

After deployment, you'll see the **Stage Editor**.

**Invoke URL** is your API's base URL:
```
https://abc123xyz.execute-api.[region].amazonaws.com/dev
```

![API Gateway Invoke URL](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/12.png)

## Step 5: Test API Endpoints

### 5.1 Test with API Gateway Console

API Gateway provides a built-in testing tool.

**Test POST /users (Create User):**

1. In the left navigation, click **Resources**
2. Click on `/users` → **POST** method
3. Go to **Test* tab

**Request Body:**
```json
{
  "cognitoSub": "test-sub-123",
  "username": "Test User",
  "email": "test@example.com",
  "role": "user",
  "phoneNumber": "1234567890"
}
```

![API Gateway Test POST](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/13.png)

4. Click **Test**

**Expected Response:**

**Status:** 201

**Response Body:**
```json
{
  "success": true,
  "data": {
    "id": 3,
    "cognito_sub": "test-sub-123",
    "username": "Test User",
    "email": "test@example.com",
    "role": "user",
    "phone_number": "1234567890"
  }
}
```

**Response Headers:**
```
Content-Type: application/json
```

**Logs:**
Shows Lambda execution logs inline

![API Gateway Test Response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/14.png)

**Test GET /users (Get All Users):**

1. Click on `/users` → **GET** method
2. Click **Test**
3. No request body needed
4. Click **Test**

**Expected Response:**

**Status:** 200

**Response Body:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "cognito_sub": "demo-sub-1",
      "username": "Alice",
      "email": "alice@example.com",
      "role": "admin",
      "phone_number": "1234567890"
    },
    {
      "id": 2,
      "cognito_sub": "demo-sub-2",
      "username": "Bob",
      "email": "bob@example.com",
      "role": "user",
      "phone_number": "0987654321"
    },
    {
      "id": 3,
      "cognito_sub": "test-sub-123",
      "username": "Test User",
      "email": "test@example.com",
      "role": "user",
      "phone_number": "1234567890"
    }
  ]
}
```

![API Gateway Test Response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/15.png)

**Test GET /users/{id} (Get Single User):**

1. Click on `/users/{id}` → **GET** method
2. Click **Test**

**Path Parameters:**
- **id:** `1`

![API Gateway Test Response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/16.png)

3. Click **Test**

**Expected Response:**

**Status:** 200

**Response Body:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "cognito_sub": "demo-sub-1",
    "username": "Alice",
    "email": "alice@example.com",
    "role": "admin",
    "phone_number": "1234567890"
  }
}
```

### 5.2 Test with curl (Command Line)

Test your deployed API from terminal:

**Set your API URL:**
```bash
API_URL="https://YOUR-API-ID.execute-api.[region].amazonaws.com/dev"
```

**Create User:**
```bash
curl -X POST "${API_URL}/users" \
  -H "Content-Type: application/json" \
  -d '{
    "cognitoSub": "curl-test-456",
    "username": "Curl User",
    "email": "curl@example.com",
    "role": "user",
    "phoneNumber": "5551234567"
  }'
```

**Get All Users:**
```bash
curl -X GET "${API_URL}/users"
```

**Get Single User:**
```bash
curl -X GET "${API_URL}/users/1"
```

**Update User:**
```bash
curl -X PUT "${API_URL}/users/1" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "Updated Name",
    "email": "updated@example.com",
    "role": "admin",
    "phoneNumber": "9998887777"
  }'
```

**Delete User:**
```bash
curl -X DELETE "${API_URL}/users/3"
```

### 5.3 Test with Postman

Postman provides a user-friendly interface for API testing.

**Import API to Postman:**

1. In API Gateway console, go to **Stages** → **dev**
2. Click **Stage action** → **Export**
3. In the **Export API** modal, leave all settings as default. Click **Export API**
4. Download the json file 
5. Open Postman, click **Import**
6. On the opened modal, click **files**

![Postman Import](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/17.png)

7. On the next modal, click **Import**

![Postman Import](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/18.png)

8. Expand **workshop-user-api**, select **GET /users** to test get all users route
9. Click **Send**
10. View response

![Postman response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/19.png)

## Step 6: Configure Stage Settings

### 6.1 Enable CloudWatch Logging

1. In API Gateway console, go to **Stages**
2. Click **dev** stage
3. Under **Logs and racing** card, click **Edit**

**CloudWatch Settings:**

**CloudWatch Logs:**
- Select **Errors and info logs**
- This includes
  - Request received
  - Request body (if enabled)
  - Integration request
  - Integration response
  - Execution summary
  - Errors

**Data tracing:**
- Check this box (for development)
- Logs request/response bodies
- Disable in production (may contain sensitive data)

**Enable Detailed Metrics:**
- Check this box
- Provides method-level metrics
- Small additional cost ($0.50/month per metric)

![API Gateway Stage Logging](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/20.png)

4. Click **Save Changes**

**Grant API Gateway Permission:**

If this is your first API with logging, you'll need to set up an IAM role:

1. In **IAM** dashboard, create role with:
    - **Trusted entity type:** AWS Service
    - **Use case:** API Gateway
    - **Policy:** **AmazonAPIGatewayPushToCloudWatchLogs**
    - **Role name:** `api-gw-push-cloudwatch-logs`
2. Copy the Role ARN
3. Go back to **API Gateway** dashboard
4. On the left sidebar, click **Settings**
5. In **Logging**, click **Edit**
6. Paste the Role ARN to the textbox
6. Click **Save changes**

### 6.2 Configure Throttling

Protect your API from abuse with rate limiting.

1. Still in **dev** stage settings
2. in **Stage details** card, click **Edit**

**Throttling settings:**

**Enabled**

**Rate:** `1000` requests per second
- Burst capacity for temporary spikes

**Burst:** `2000` requests
- Total requests allowed in burst

![API Gateway Throttling](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/22.png)


**Throttling Limits:**

When limits are exceeded:
- Client receives **429 Too Many Requests**
- Requests are rejected before reaching Lambda
- Protects backend from overload
- No Lambda costs for throttled requests

**Default AWS Account Limits:**
- 10,000 requests per second per region
- Can request increase via support ticket

For this workshop, 1000 req/s is more than sufficient.


### 6.3 Enable Caching (Optional)

API caching reduces Lambda invocations and improves performance.

1. In **dev** stage, click **Settings** tab
2. Scroll to **Cache Settings**

**Provision API cache:**
- Check this box

**Cache capacity:** `0.5 GB`
- Smallest size
- Sufficient for workshop

**Cache time-to-live (TTL):** `300` seconds (5 minutes)
- How long responses are cached

**Per-key cache invalidation:**
- Invalidate certain data in cache based on a key instead of the whole cache

![API Gateway Throttling](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/21.png)

**Caching Costs:**

API Gateway caching is relatively expensive:
- 0.5 GB cache: $0.028/hour (~$20.16/month)

**For this workshop:** Skip caching to avoid costs. It's included here for completeness.

**When to use caching:**
- High read traffic (>1000 req/min)
- Data doesn't change frequently
- Latency is critical
- Cost of Lambda invocations > cost of cache

For user data that changes frequently, caching may not be appropriate.


## Step 7: Monitor API Performance

### 7.1 View API Gateway Metrics

1. In API Gateway console, go to **Dashboard** (left navigation)
2. Select your API: `workshop-user-api`
3. Select stage: `dev`

**Metrics displayed:**

**API calls:**
- Total number of requests over time period
- Broken down by hour/day

**Integration latency:**
- Time spent in Lambda function
- Excludes API Gateway overhead

**Latency:**
- Total request time (API Gateway + Lambda)
- Includes network, integration, and processing time

**4XX errors:**
- Client errors (bad requests, not found, etc.)
- Should be low in well-designed APIs

**5XX errors:**
- Server errors (Lambda errors, timeouts, etc.)
- Should be monitored closely

![API Gateway Dashboard](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/23.png)

### 7.2 View CloudWatch Logs

1. Go to CloudWatch console
2. Click **Log groups**
3. Find: `API-Gateway-Execution-Logs_{api-id}/dev`
4. Click on log group
5. Click on latest log stream

**Sample log entry:**
```
(abc-123-def) Method request body before transformations: {
  "cognitoSub": "test-123",
  "username": "Test User",
  "email": "test@example.com",
  "role": "user",
  "phoneNumber": "1234567890"
}

(abc-123-def) Endpoint request URI: https://lambda.us-east-1.amazonaws.com/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:workshop-userHandler/invocations

(abc-123-def) Endpoint response body before transformations: {
  "statusCode": 201,
  "headers": {
    "Content-Type": "application/json"
  },
  "body": "{\"success\":true,\"data\":{\"id\":1,...}}"
}

(abc-123-def) Method response body after transformations: {
  "success": true,
  "data": {
    "id": 1,
    "cognito_sub": "test-123",
    "username": "Test User",
    "email": "test@example.com",
    "role": "user",
    "phone_number": "1234567890"
  }
}

(abc-123-def) Method completed with status: 201
```

![API Gateway CloudWatch Logs](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/24.png)

## Summary

Congratulations! You've successfully:
-   Created REST API in API Gateway
-   Defined resources and methods for CRUD operations
-   Configured Lambda proxy integration
-   Enabled CORS for frontend compatibility
-   Deployed API to dev stage
-   Tested endpoints with multiple tools
-   Configured logging and monitoring
-   Set up throttling for protection

### What You've Built

Your API Gateway now provides:
- **RESTful endpoints** for all user operations
- **HTTPS security** by default
- **CORS support** for frontend integration
- **Request throttling** for protection
- **CloudWatch logging** for debugging
- **Metrics** for monitoring

## Next Steps

Proceed to **Part 6: Amazon Cognito Configuration** to secure API endpoints with authorization provided by **AWS Cognito**

---

**Ready to continue?** Your API endpoints are now fully functional and ready! 🚀
