---
title: "Workshop"
date: "2025-09-15"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building and Deploying Fullstack Serverless Applications on AWS

## Workshop Overview

Welcome to this comprehensive hands-on workshop on building and deploying production-ready fullstack serverless applications on AWS. Over the course of three parts, you'll learn how to architect, secure, and automate the deployment of a modern web application using AWS's serverless services.

## What You'll Build

By the end of this series, you'll have built a complete serverless application featuring:

- **Secure Frontend**: A globally distributed, high-performance static website protected by AWS WAF
- **Scalable Backend**: RESTful APIs powered by Lambda functions with secure database access
- **User Authentication**: Complete user management with sign-up, login, and authorization
- **Automated Deployment**: A full CI/CD pipeline that automatically builds and deploys your application

## Workshop Structure

### Part 1: Frontend Deployment with CloudFront, WAF, and S3
Learn to deploy and secure a static frontend application using AWS's content delivery network. You'll configure CloudFront for global distribution, implement S3 for reliable storage, and protect your application with AWS WAF rules.

**Key Topics:**
- S3 bucket configuration for static website hosting
- CloudFront distribution setup and optimization
- AWS WAF rule configuration for security
- Custom domain and SSL certificate management

### Part 2: Backend Deployment with API Gateway, Lambda, and RDS
Build a secure, scalable backend infrastructure. You'll create RESTful APIs, implement serverless functions, set up a managed database, and integrate user authentication.

**Key Topics:**
- API Gateway REST API design and deployment
- Lambda function development and configuration
- RDS database setup and connection management
- AWS Secrets Manager for credential security
- Amazon Cognito for authentication and authorization
- Securing APIs with Cognito authorizers

## Prerequisites

**Required Knowledge:**
- Basic understanding of web application architecture
- Familiarity with JavaScript/Node.js or Python
- Basic command line/terminal usage
- Understanding of HTTP and REST APIs

**Required Tools:**
- AWS Account with administrative access
- AWS CLI installed and configured
- Text editor or IDE (VS Code recommended)
- Git installed locally

**Recommended:**
- Basic understanding of SQL
- Familiarity with JSON
- Experience with version control (Git)

## Architecture Overview

![Diagram]( /images/5-Workshop/diagram.png)

## Learning Outcomes

By completing this workshop series, you will be able to:
- Design and implement serverless architectures on AWS
- Secure web applications using industry best practices
- Implement user authentication and authorization flows
- Manage application secrets and database credentials securely
- Build and maintain automated deployment pipelines
- Optimize applications for performance and cost
- Troubleshoot common serverless deployment issues

## Cost Considerations

This workshop uses AWS services that may incur costs. We'll use AWS Free Tier eligible services where possible, but you should:
- Monitor your AWS billing dashboard regularly
- Delete resources after completing each workshop if not continuing immediately
- Set up billing alerts to avoid unexpected charges

Estimated cost for completing all workshops: $5-$15 (assuming no existing Free Tier usage)

## Getting Help

Throughout the workshops, you'll find:
- Step-by-step instructions with screenshots
- Code samples and configuration templates
- Common troubleshooting tips
- Links to AWS documentation for deeper dives

## Ready to Start?

Let's begin with Part 1: Frontend Deployment and build the foundation of your serverless application!

#### Content

1. [Part 1: Frontend Deployment: Frontend Deployment with CloudFront, WAF, and S3](5.1-Frontend-deployment)
1. [Part 2: Serverless Backend: Backend Deployment with API Gateway, Lambda, RDS, and Cognito](5.2-Serverless-backend)