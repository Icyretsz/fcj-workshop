---
title : "Prerequisites"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.2.1 </b> "
---

### Required AWS Knowledge

- **Part 1: Frontend Deployment Completion**: Understanding of AWS console navigation and basic services; Having a frontend application ready to connect
- **HTTP/REST APIs**: Knowledge of HTTP methods (GET, POST, PUT, DELETE) and REST principles
- **Database Basics**: Understanding of relational databases, tables, and SQL queries
- **JSON**: Familiarity with JSON format for API requests/responses
- **Basic Networking**: Understanding of concepts like VPC, subnets, and security groups
- **IAM**: Familiarity with IAM roles and policies. Apply best practice of least-privileges to resources

### Required Technical Skills

- **Programming**: Intermediate knowledge of either:
    - **Node.js** (JavaScript/ TypeScript) - Recommended for this workshop
- **SQL Queries**: Basic SELECT, INSERT, UPDATE, DELETE statements
- **API Testing**: Using tools like Postman or curl
- **Command Line**: Comfortable with terminal commands
- **Environment Variables**: Understanding of configuration management

### Required AWS Account Setup

Before starting this workshop, ensure you have:

1. **AWS Account**
    - Active AWS account with administrative access or permissions for:
        - Lambda
        - API Gateway
        - RDS
        - Cognito
        - Secrets Manager
        - VPC
        - IAM
        - CloudWatch Logs

2. **IAM User/Role Permissions**
    - Required managed policies:
        - `AWSLambda_FullAccess`
        - `AmazonAPIGatewayAdministrator`
        - `AmazonRDSFullAccess`
        - `AmazonCognitoPowerUser`
        - `SecretsManagerReadWrite`
        - `AmazonVPCFullAccess`
        - `IAMFullAccess` (for creating Lambda execution roles)
        - `CloudWatchLogsFullAccess`

3. **Billing Alerts Configured**
    - Set up AWS Budgets or billing alerts
    - Recommended threshold: $20-30 for this workshop
    - RDS can incur higher costs than previous workshop

### Required Tools and Software

Install the following tools on your local machine:

1. **AWS CLI (Version 2) (optional)**
    - Download: https://aws.amazon.com/cli/
    - Verify installation: `aws --version`
    - Ensure credentials are configured: `aws configure`

2. **Node.js and npm** (if using Node.js for Lambda)
    - Download: https://nodejs.org/ (LTS version recommended)
    - Minimum version: Node.js 18.x or higher
    - Verify: `node --version` and `npm --version`

3. **Python** (if using Python for Lambda)
    - Download: https://www.python.org/
    - Minimum version: Python 3.9 or higher
    - Verify: `python --version` or `python3 --version`
    - pip installed: `pip --version`

4. **Git**
    - Download: https://git-scm.com/
    - Verify: `git --version`

5. **Text Editor or IDE**
    - VS Code (recommended): https://code.visualstudio.com/
    - Extensions recommended:
        - AWS Toolkit
        - ESLint (for JavaScript)
        - Python (if using Python)

6. **API Testing Tool**
    - **Postman** (recommended): https://www.postman.com/downloads/
    - Or **Insomnia**: https://insomnia.rest/download
    - Or **curl** (command line)

7. **Database Client** (Optional but helpful)
    - **DBeaver** (free, supports PostgreSQL): https://dbeaver.io/
    - Or **pgAdmin**: https://www.pgadmin.org/
    - Or **psql** command line tool

### Sample Application Code

We'll provide sample Lambda function code and SQL scripts:

**Option 1**: Clone the workshop repository
```bash
git clone https://github.com/your-workshop/serverless-app-backend.git
cd serverless-app-backend
```

**Option 2**: Write code from scratch following the workshop
- We'll provide all code snippets in the tutorial
- Good for learning and understanding each component

### Optional: Completed Part 1: Frontend Deployment

While not strictly required, having completed Part 1: Frontend Deployment provides context:
- You'll understand how the frontend will consume these APIs
- You'll have a complete end-to-end application

**If you skipped Part 1: Frontend Deployment:**
- You can still complete this workshop independently
- We'll test APIs using Postman instead of the frontend
- You can integrate with any frontend later

### Cost Expectations for Workshop 2

**Estimated Costs for this Workshop:**

**Within Free Tier (first 12 months):**
- RDS (single-AZ, limited hours): $0-5
- Lambda: $0
- API Gateway: $0
- Cognito: $0
- Secrets Manager: $0 (30-day trial)
- **Total**: $0-5

### Pre-Workshop Checklist

Before beginning, verify you have:

- [ ] AWS account with appropriate permissions
- [ ] AWS CLI installed and configured
- [ ] Node.js/Python installed (based on your preference)
- [ ] Git installed
- [ ] Code editor/IDE installed
- [ ] API testing tool installed (Postman recommended)
- [ ] Billing alerts configured at $20
- [ ] Sample code repository cloned (or ready to write from scratch)
- [ ] At least 3-4 hours available for focused work
- [ ] Understanding of REST APIs and databases

### What You'll Learn

By completing this workshop, you will:

- Design and implement serverless API architectures
- Create and configure Lambda functions with proper permissions
- Set up and secure RDS databases in VPC
-   Implement API Gateway REST APIs with multiple endpoints
-   Configure Cognito user pools and authentication flows
-   Integrate Cognito authorizers with API Gateway
-   Manage secrets securely with AWS Secrets Manager
- Restrict API Gateway access with Lambda functions and AWS Secrets Manager
-   Connect Lambda functions to RDS databases
-   Implement proper error handling and logging
-   Test APIs with authentication tokens

## Ready to Begin?

Once you've completed all prerequisites and verified your setup, you're ready to start building your secure, scalable serverless backend infrastructure!

### Expected Outcomes

By the end of this workshop, you'll have:
- A fully functional REST API with multiple endpoints
- Secure user authentication and authorization
- Database-backed data persistence
- Professional logging and monitoring
- Production-ready security practices
- A backend that integrates with your Part 1: Frontend Deployment frontend (if completed)

### Next Step

Let's move on to **Part 1: VPC and Network Setup** to create the secure network foundation for your database and Lambda functions.

---

**Let's build your serverless backend!** 🚀