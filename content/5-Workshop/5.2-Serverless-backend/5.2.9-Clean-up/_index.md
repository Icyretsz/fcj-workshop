---
title : "Clean up"
date :  "2025-09-15" 
weight : 9 
chapter : false
pre : " <b> 5.2.9 </b> "
---

## Overview

This section covers how to properly clean up the resources you created during Part 2: Serverless Backend. Cleaning up is important to avoid ongoing AWS charges, especially for resources that have monthly costs like RDS.

**What you'll learn:**
- How to safely delete resources in the correct order
- Understanding resource dependencies
- Cost implications of keeping vs. deleting resources
- How to preserve configuration for future use
- Partial cleanup options

**Estimated time**: 15-20 minutes

## Should You Clean Up?

### Keep Resources If:
-   You're proceeding immediately to Part 2: Serverless Backend
-   You want to maintain the working frontend for reference
-   You're using this for a real project
-   Costs are acceptable for your use case

### Clean Up If:
-   You've completed the workshop and don't need the resources
-   You want to minimize AWS costs
-   You're practicing and will recreate later
-   You're approaching Free Tier limits

## Complete Cleanup (Step-by-Step)

Follow this order to avoid dependency errors:

## Step 1 Delete RDS instance and subnet group

### 1.1 Delete RDS instance
1. Go to **Aurora and RDS** dashboard
2. In the left sidebar, click **Databases**
3. Click on `workshop-posgresql-db`
4. On the right, click **Action** button, then click **Delete**
5. Uncheck **Create final snapshot** and **Retain automated backup** as these two will incur charges even after you deleted your RDS instance
6. Check **I acknowledge that upon instance deletion, automated backups, including system snapshots and point-in-time recovery, will no longer be available.**
7. Type `delete me` when prompted
8. click **Delete**

![Delete RDS](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/1.png)

### 1.2 Delete subnet group
1. Still in **Aurora and RDS** dashboard, in the left sidebar, click **Subnet group**
2. Select **workshop-db-subnet-group**
3. On the right, click **Delete**
4. Click **Delete** in the modal
5. Wait for the instance delete to complete, then proceed to delete subnet group in next step

![Delete Subnet Group](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/2.png)

## Step 2 Delete Lambda function
1. Go to **Lambda** dashboard
2. Select `workshop-lambda-sm-rds`
3. On the right, click **Action** button
4. Click **Delete** in the dropdown
5. Type `delete` when prompted and click **Delete**

![Delete Lambda](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/3.png)

## Step 3 Delete secret in AWS Secrets Manager
1. Go to **Secrets Manager** dashboard
2. In the secrets list, click on the secret name of RDS
3. On the right, click **Action** button, then click **Delete* in the dropdown
4. A modal appear, choose your **Waiting period**, after this period, the secret will be deleted
5. Click **Schedule deletion**

![Delete secret](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/4.png)

## Step 4 Delete API Gateway resources
1. Go to **API Gateway** dashboard
2. On the left sidebar, click **APIs**
3. Select `workshop-user-api`
4. On the right, click **Delete**
5. Type `confirm` when prompted
6. Click **Delete**

![Delete API gateway](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/5.png)

## Step 5 Delete Cognito user pool
1. Go to **Cognito** dashboard
2. On the left sidebar, click **User pools**
3. In the list, select `User pool - id`
4. Check two checkboxes, then type in the Cognito user pool name when prompted
5. Click **Delete**

![Delete User Pool](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/6.png)

## Step 6 Delete VPC and VPC endpoint

### 6.1 Delete VPC endpoint
1. Go to **VPC** dashboard
2. On the left sidebar, click **Endpoints**
3. Select `workshop-lambda-secretsmng-endpoint`
4. On the right, click **Action** button, then select **Delete** in the dropdown
5. Type in `delete` when prompted and click **Delete**
6. Wait for deletion to complete before proceed to VPC deletion

![Delete VPC endpoint](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/7.png)

### 6.2 Delete VPC
1. On the left sidebar, click **Your VPCs**
2. Select `workshop-backend-vpc`
3. On the right, click **Action** button, then click **Delete VPC**

![Delete VPC ](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/8.png)

## Step 7 Delete CloudWatch log groups
1. Go to **CloudWatch** dashboard
2. On the left sidebar, click **Log groups**
3. Delete all log groups of our resources

![Delete log groups ](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/9.png)

## Step 8 Delete roles
1. Go to **IAM** dashboard
2. On the left sidebar, click **Roles**
3. Delete these roles
- `api-gw-push-cloudwatch-logs`
- `workshop-lamda-secretsmng-role`

## Summary

### Cleanup Completion Checklist

**If performing complete cleanup:**
- [ ] RDS: No databases or subnet groups
- [ ] Lambda: No `workshop-lambda-sm-rds` function
- [ ] Secrets Manager: Secret scheduled for deletion
- [ ] API Gateway: No `workshop-user-api`
- [ ] Cognito user pool
- [ ] VPC Endpoints: No `workshop-secretsmng-endpoint`
- [ ] VPC: No `workshop-backend-vpc`
- [ ] CloudWatch: No workshop log groups
- [ ] IAM: No workshop roles/policies
