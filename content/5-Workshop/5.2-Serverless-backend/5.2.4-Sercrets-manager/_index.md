---
title : "AWS Secrets Manager Configuration"
date :  "2025-09-15" 
weight : 4 
chapter : false
pre : " <b> 5.2.4 </b> "
---

## Overview

In this section, you'll configure AWS Secrets Manager to securely store your database credentials. Instead of hardcoding passwords in Lambda functions, you'll retrieve them dynamically from Secrets Manager, following security best practices.

**What you'll accomplish:**
- Understand AWS Secrets Manager and its benefits
- Store RDS database credentials as a secret
- Configure secret structure for database connections
- Set up IAM permissions for Lambda to access secrets
- Test secret retrieval
- Understand secret rotation (optional configuration)

**Estimated time**: 15-20 minutes

## Why AWS Secrets Manager?

### Security Benefits

**Never Hardcode Credentials:**
- Credentials stored encrypted at rest
- Access controlled via IAM policies
- Audit trail of who accessed secrets
- No credentials in application code or environment variables

**Automatic Rotation:**
- Periodic credential rotation without downtime
- Reduces risk of credential compromise
- Lambda functions automatically use new credentials

**Centralized Management:**
- Single source of truth for credentials
- Easy to update across multiple applications
- Version history of secret values

### Cost Considerations

**Pricing:**
- $0.40 per secret per month
- $0.05 per 10,000 API calls
- VPC Endpoint (PrivateLink): $0.013/hour

**For this workshop:**
- 1 secret for database credentials
- Minimal API calls from Lambda
- **Estimated cost**: <$1 (assume clean up immediately after workshop)

## Step 1: View Database Secret

In previous section - RDS Database Setup, during [database creation]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.3-RDS" >}}#25-settings), we have selected the option
**Managed in AWS Secrets Manager - most secure** and used the default (**aws/secretmanager(default)**). This option create
a RDS-managed secret for us. Let's view it

### 1.1 Navigate to Secrets Manager Console

1. In AWS Console search bar, type "Secrets Manager"
2. Click on **Secrets Manager** under Services

![Secrets Manager Console](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/1.png)

### 1.2 View Secret

1. In the Secrets Manager dashboard, you will see the RDS-managed secret in the list

![RDS Secret](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/2.png)

### 1.3 View Secret Details

1. Click on your secret name
2. You'll see detailed information

![RDS Secret](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/3.png)

**Secret ARN:**
- **Copy this ARN** - you'll need it for IAM policies

**Secret value (encrypted):**
- Click **Retrieve secret value** to view

![RDS Secret](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/4.png)

### 1.4 View rotation behavior

1. Go to the **Rotation** tab
2. You can see the rotation behaviors
    - **Rotation status**: true - enabled
    - **Rotation schedule**: 7 days - the secret will be rotated every 7 days
    - **Last rotated date** and **Next rotation data**: dates indicate the last rotation and the next rotation
3. Click **Edit rotation** to modify behavior

![Secret rotation](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/5.png)

4. On the **Edit rotation configuration** modal, you can see several notable settings:
    - **Automatic rotation**: Enables or disables automatic password/secret rotation. When turned on, Secrets Manager will rotate the secret based on the schedule you define.
    - **Rotation schedule**: Controls when and how often the secret is rotated.
      - You can choose **time unit** and amount of time units for rotation
      - You can select **Schedule expression** for complex rotation schedules
    - **Windows duration - optional**: 
      - The amount of time (in hours) during which Secrets Manager is allowed to perform the rotation.
      - This is often used to avoid maintenance windows or prevent rotations during peak hours.
    - **Rotate immediately when the secret is stored (checkbox)**
      - If checked, Secrets Manager will rotate the secret immediately after it is created or stored. After that, it follows the regular rotation schedule.

![Secret rotation edit](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/6.png)

## Step 2: Create IAM Role for Lambda Access

Lambda functions need permission to read this secret. We'll create a custom IAM role for our Lambda function to assume.

### 2.1 Navigate to IAM Console

1. In AWS Console search bar, type "IAM"
2. Click on **IAM** under Services
3. In left navigation, click **Roles**

![IAM](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/7.png)

### 2.2 Create Role

1. Click **Create role**

### 2.3 Select trusted entity

1. **Trusted entity type**: select **AWS service**
2. **Service or use case**: select **Lambda**
3. Click **Next**

![role - trusted entity](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/8.png)

### 2.4 Add permissions
1. **Permission policies:**
   - In the search field, search and select `AWSLambdaVPCAccessExecutionRole` and `SecretsManagerReadWrite`
2. Click **Next**

### 2.5 Name, review and create
1. **Role name:** `workshop-lamda-secretsmng-role`
2. **Description:** `Allows Lambda functions to call Secrets Manager to fetch secrets.`

![role - review](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/9.png)

### Understanding the IAM Role

#### AWSLambdaVPCAccessExecutionRole
```json
{
  "Effect": "Allow",
  "Action": [
    "logs:CreateLogGroup",
    "logs:CreateLogStream",
    "logs:PutLogEvents",
    "ec2:CreateNetworkInterface",
    "ec2:DescribeNetworkInterfaces",
    "ec2:DeleteNetworkInterface",
    "ec2:AssignPrivateIpAddresses",
    "ec2:UnassignPrivateIpAddresses"
  ],
  "Resource": "arn:aws:logs:*:*:*"
}
```
- Create and write to CloudWatch Log Groups
- Essential for debugging and monitoring
- Create ENIs (Elastic Network Interfaces) in VPC
- Required for Lambda to connect to RDS in private subnet
- Manages network connectivity

#### SecretsManagerReadWrite

This policy includes all permissions related to Secrets Manager and allow Lambda to reach Secrets Manager to connect to RDS instance

#### How Lambda Uses These Permissions
```
Lambda Function Execution
    ↓
1. Create ENI in VPC (VPCAccessExecutionRole)
    ↓
2. Retrieve DB credentials (WorkshopLambdaSecretsPolicy)
    ↓
3. Connect to RDS via ENI
    ↓
4. Execute database queries
    ↓
5. Write logs to CloudWatch (BasicExecutionRole)
    ↓
6. Return response
```

### Security Best Practices

 **Principle of Least Privilege:**
- Policy only grants read access (not write/delete)
- Scoped to specific secret (not all secrets)
- No wildcard permissions

 **Resource-Based Restrictions:**
- Uses specific ARN pattern
- Can't accidentally access other secrets
- Easier to audit and troubleshoot

## Step 3: Create VPC endpoint for Lambda function
For a Lambda function reside inside a private subnet to access Secrets Manager secrets, it must go through a VPC endpoint.
We'll create a VPC endpoint now in VPC console

### 3.1 Navigate to VPC Console
1. In AWS Console search bar, type "VPC"
2. Click on **VPC** under Services
3. In left navigation, under **PrivateLink and Lattice**, click **Endpoints**

![navigate endpoint](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/10.png)

4. Click **Create Endpoint**

### 3.2 Endpoint creation
1.  **Name tag - optional:** `workshop-lambda-secretsmng-endpoint`
2. **Type:**: AWS services

![endpoint type](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/11.png)

3. **Services**: in the search field, type `secrets`.
   - You will see a service named `com.amazonaws.[region].secretsmanager`. Select it

![endpoint services](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/12.png)

4. **Network settings:**
   - VPC: select `workshop-backend-vpc`
   - **Subnet:**
      - Select the subnet that is inside the same availability zone as our RDS's subnet
      - Recall that in [VPC and Network Setup]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.2-VPC" >}}#understanding-the-network-architecture))
      we decided to put Lambda in private subnet 1, RDS in private subnet 3, both reside in ap-southeast-1a availability zone
      - In this case, we will select `apse1-az2 (ap-southeast-1a)`
      - **Subnet ID:** select `workshop-backend-subnet-private1-ap-southeast-1a` 
   - **Security groups:** select **workshop-endpoint-sm-sg** security group that we created in [VPC and Network Setup]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.2-VPC" >}}#21-create-security-group-for-vpc-endpoint)
   - **Policy**: full access. This allows Lambda to use all Secrets Manager operations
(allowed in the role we just created) through the endpoint

5. Click **Create endpoint**

### 3.3 Verify Endpoint

Once status is **Available**:
1. Click on the endpoint ID
2. Note the **DNS names** - Lambda will use these automatically
3. Verify **Subnets** shows workshop-private-subnet-1
4. Verify **Security groups** shows workshop-lambda-sg

**Cost Savings with VPC Endpoints:**

Interface VPC Endpoint costs: ~$7.20/month + $0.01/GB data processed

**Without VPC Endpoint:**
- Lambda → NAT Gateway → Internet → Secrets Manager
- NAT Gateway cost: ~$32/month + $0.045/GB

**With VPC Endpoint:**
- Lambda → VPC Endpoint → Secrets Manager (private)
- Lower latency, more secure, lower cost for high-traffic applications

For this workshop with minimal traffic, the difference is small, but it demonstrates production best practices.

## Summary

Congratulations! You've successfully:
- View RDS secret in detail in AWS Secrets Manager
- Created IAM role for Lambda to access the secret
- Created VPC endpoint for Lambda to read secrets in AWS Secrets Manager

## Next Steps

Proceed to **Part 4: Lambda Functions Development** to create Lambda functions and connect the pieces together (VPC endpoint, Lambda role) that will use these secrets to connect to your RDS database.

---

**Ready to continue?** Your credentials are now securely stored and your Lambda functions will have proper permissions to access them! 🔐