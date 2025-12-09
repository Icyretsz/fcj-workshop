---
title: "Proposal"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Real-Time Typing Challenge Platform

<h2 style="text-align:center;">TypeRush</h2>

## An AWS-Powered Architecture for a Multiplayer Typing App

### 1. Executive Summary
TypeRush is an interactive web application that replicates the core experience of Monkeytype while introducing multiplayer functionality for live competitive typing sessions. Designed as a showcase of real-time web technologies and scalable cloud architecture, the platform combines a modern frontend experience with a serverless backend powered by AWS.

Users can create or join typing rooms, compete in real time, and view performance metrics such as Words Per Minute (WPM), accuracy, and rankings. The application leverages React for the frontend, AWS Lambda and API Gateway (WebSocket) for real-time synchronization, and Amazon RDS for player data and Amazon ElastiCache for session data. It also leverages Amazon Bedrock to generate daily challenges. Amazon Cognito secures user access and authentication. The solution demonstrates how cloud-native services can deliver fast, scalable, and cost-efficient real-time applications without the need for traditional server management.

### 2. Problem Statement
### What’s the Problem?
Existing typing platforms are often limited to single-player functionality, lacking real-time multiplayer interaction or open implementation examples. Most multiplayer solutions require dedicated servers or complex infrastructure, making them costly and difficult to maintain. For developers and enthusiasts, there is no accessible, serverless example demonstrating real-time synchronization, event-driven updates, and secure user management in one integrated system.

### The Solution
This project provides a serverless, real-time typing platform that allows multiple users to connect, type simultaneously, and see instant feedback on their progress. It is designed to be lightweight, cost-efficient, and scalable.

### Benefits and Return on Investment
The project serves as both a developer learning platform and a user-facing application. It demonstrates best practices in event-driven architecture, WebSocket implementation, and cloud-based scalability at minimal operational cost. For users, it provides an engaging, social typing experience. For developers, it offers an educational reference for building serverless real-time systems.

The platform requires no ongoing server maintenance, ensuring:
* Long-term sustainability
* Rapid scalability
* Low operational overhead

### 3. Solution Architecture
The platform employs a serverless AWS architecture to manage a real-time typing challenge application. Data is processed and stored using a variety of AWS services, and the frontend is a modern, responsive UI. The architecture is detailed below:
![TypeRush architecture](/images/2-Proposal/typerush_diagram.png)

### AWS Services Used
- **Amazon S3**: Hosts the static frontend.
- **CloudFront**: Provides a CDN for the frontend and API.
- **AWS WAF**: Acts as a firewall for CloudFront.
- **Route 53**: Manages domain and DNS routing.
- **Amazon Cognito**: Handles user sign-in and sign-up.
- **API Gateway**: Serves as the entry point for microservices and connects to private ECS via VPC Link.
- **AWS Lambda**: Powers the record and text microservices and the WebSocket API for the game microservice.
- **Amazon RDS**: Stores records and leaderboards.
- **DynamoDB**: Stores text data.
- **AWS Bedrock (Claude Sonnet 3)**: Functions as the LLM model for text generation.
- **Amazon SNS**: Manages notifications and alarms.
- **AWS CloudWatch**: Handles logging and monitoring.
- **AWS Secrets Manager**: Stores database and API secrets.
- **Amazon ECS (Fargate)**: Hosts the core game backend.
- **Elastic Load Balancer**: Balances game service traffic.
- **ElastiCache (Redis)**: Caches real-time game data.
- **CodePipeline**: Orchestrates builds and deployments.
- **CodeBuild**: Builds Docker images and Lambda services.
- **ECR (Elastic Container Registry)**: Stores Docker images.
- **GitLab**: Triggers pipeline builds.


### Component Design
- **Frontend Layer**: A modern, responsive UI built with React, hosted on Amazon S3, and served via Amazon CloudFront with AWS WAF and Amazon Route 53 for enhanced performance, global content delivery, and robust security.
- **API & Real-Time Communication**: AWS API Gateway serves as the unified gateway for both REST and WebSocket APIs, implementing rate limiting, authentication, and request validation. The WebSocket API powers real-time multiplayer interactions.
- **Compute & Business Logic**: AWS Lambda handles serverless processing tasks like data persistence and AI-powered text generation. Amazon ECS with AWS Fargate hosts containerized backend services, including the game server. VPC PrivateLink connects API Gateway to ECS Fargate containers, and an Elastic Load Balancer distributes traffic.
- **Data Layer**: Amazon RDS stores relational data like user profiles and leaderboards. Amazon DynamoDB manages non-relational data such as dynamically generated text. Amazon ElastiCache (Redis) provides in-memory caching for frequently accessed data.
- **Authentication & User Management**: Amazon Cognito handles secure user registration, authentication, and authorization, supporting social and federated login. Amazon Secret Manager secures storage for secrets like API keys.
- **CI/CD & DevOps**: AWS CodeBuild and AWS CodePipeline enable continuous integration and continuous deployment (CI/CD) across all application components, automating build, test, and deployment processes.

### 4. Technical Implementation
#### Implementation Phases
This project has two parts—setting up the cloud infrastructure and building the application—each following 4 phases:
- **Build Theory and Draw Architecture:** Research and design the AWS serverless architecture.
- **Calculate Price and Check Practicality:** Use the AWS Pricing Calculator to estimate costs and adjust if needed.
- **Fix Architecture for Cost or Solution Fit:** Tweak the design to stay cost-effective and usable.
- **Develop, Test, and Deploy:** Code the application and AWS services, then test and release to production.

#### Technical Requirements
- **Frontend:** Practical knowledge of React.
- **Backend:** Experience with AWS services including Lambda, API Gateway, ECS, Fargate, RDS, DynamoDB, and ElastiCache.
- **CI/CD:** Familiarity with CodePipeline, CodeBuild, and GitLab.
- **Infrastructure as Code:** Use AWS CDK/SDK to code interactions.
### 5. Timeline & Milestones
**Project Timeline**
- **Month 1:** Study all about AWS and AWS services.
- **Month 2:** Start planning for the project and the AWS services to implement.
- **Month 3:** Develop, implement, and launch.

### 6. Budget Estimation

---> [Budget Estimation File](/attachments/costs.pdf) <---

#### Infrastructure Costs

* AWS Services:
    * Amazon Route 53: $0.90
    * AWS Web Application Firewall (WAF): $9.03
    * Amazon CloudFront: $0.40 ($0 with free tier)
    * S3 Standard: $0.11 ($0 with free tier)
    * Data Transfer: $0.00 ($0 with free tier)
    * Amazon API Gateway: $15.92 ($0 with free tier)
    * Amazon Cognito: $0.00
    * Network Load Balancer: $18.49
    * AWS Fargate: $8.88
    * AWS Lambda: $0.02 ($0 with free tier)
    * Amazon ElastiCache: $16.06
    * Amazon RDS for PostgreSQL: $39.37 ($0 with free tier)
    * AWS Bedrock (Workload 1): $2.63
    * DynamoDB: $0.03 ($0 with free tier)
    * AWS Secrets Manager: $4.00
    * AWS CodePipeline: $1.00 ($0 with free tier)
    * AWS CodeBuild: $5.00 ($0 with free tier)

- With free tier account: about $60/month
- With paid tier account: about $122/month
> Both amounts assume 24/7 running services; in reality, it might be much lower.

### 7. Risk Assessment
#### Risk Matrix
- Technical Integration: Medium impact, medium probability.
- Performance and Scalability: High impact, medium probability.
- Cost Management: Medium impact, medium probability.
- Security: High impact, low probability.
- Data Reliability: Medium impact, low probability.
- Operational Risks: Low impact, medium probability.
- Learning Curve: Medium impact, high probability.

#### Mitigation Strategies
- Technical Integration: This risk is mitigated by phased implementation and adherence to AWS best practices.
- Performance and Scalability: Optimizing WebSocket communication, leveraging Redis caching, and monitoring with CloudWatch will help maintain responsiveness.
- Cost Management: These can be controlled through AWS Budgets, usage alerts, and auto-scaling policies.
- Security: Risks will be minimized using AWS WAF, Cognito, Secrets Manager, and strict IAM policies.
- Data Reliability: Clear data ownership boundaries and transactional writes will ensure consistency.
- Operational Risks: Risks in CI/CD pipelines will be reduced with staged deployments and rollback configurations.
- Learning Curve: The learning curve will be addressed through dedicated study and hands-on practice in the first project phase.

#### Contingency Plans
- If the service fails: A maintenance page will be displayed to users.
- If multiplayer is too slow: Multiplayer mode will be temporarily disabled, but single-player will remain active.
- If costs spike unexpectedly: The change that caused the spike will be immediately reversed.
- If a new update breaks the site: The system will automatically revert to the previous working version.

### 8. Expected Outcomes
#### Technical Improvements:
Upon completion, the project will deliver a scalable, secure, and fully functional real-time multiplayer typing platform built entirely on AWS.

#### Long-term Value:
It will demonstrate best practices in serverless architecture, event-driven design, and CI/CD automation. For users, it provides a smooth, engaging multiplayer typing experience. For us, it serves as our first hands-on, practical reference model for building real-time, serverless web applications on AWS with minimal cost and maintenance.
