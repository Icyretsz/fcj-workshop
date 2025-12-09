---
title: "DevOps on AWS"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Summary Report

### Event Objectives

- Instill the DevOps mindset, covering its culture, principles, and key performance metrics.
- Provide a deep dive into building CI/CD pipelines using native AWS DevOps services.
- Teach Infrastructure as Code (IaC) principles using AWS CloudFormation and the AWS CDK.
- Explore the AWS container ecosystem, including Docker, Amazon ECR, ECS, EKS, and App Runner.
- Demonstrate how to implement comprehensive monitoring and observability using CloudWatch and AWS X-Ray.

### Speakers

- **Truong Quang Tinh** - Platform Engineer (TymeX), AWS Community Builder
- **Bao Huynh** - AWS Community Builder
- **Thinh Nguyen** - AWS Community Builder
- **Vi Tran** - AWS Community Builder
- **Van Hoang Kha** – Cloud Engineer, AWS Community Builder
- **Long Huynh** - AWS Community Builder
- **Quy Pham** - AWS Community Builder
- **Nghiem Le** - AWS Community Builder

### Key Highlights

#### From Manual Operations to Infrastructure as Code (IaC)

- The workshop highlighted the pitfalls of "ClickOps" (manual console-based management), such as being slow, error-prone, and difficult to replicate.
- **AWS CloudFormation**: Introduced as the native IaC solution, using YAML/JSON templates to define and manage AWS resources in "Stacks" and its ability to detect configuration drift.
- **AWS Cloud Development Kit (CDK)**: Presented as a developer-centric IaC framework that allows defining infrastructure in familiar programming languages (e.g., Python, TypeScript), using reusable "Constructs" to accelerate development.

#### Building a Full CI/CD Pipeline

- A complete, automated pipeline was demonstrated using the suite of AWS developer tools:
    - **AWS CodeCommit**: For secure source control.
    - **AWS CodeBuild**: For automated builds and testing.
    - **AWS CodeDeploy**: For managing complex deployments like Blue/Green and Canary releases.
    - **AWS CodePipeline**: To orchestrate the entire release process from source to deployment.

#### Containerization and Orchestration

- The session covered the fundamentals of containerization with Docker and the importance of a container registry like **Amazon ECR** for storing and scanning images.
- A detailed comparison of orchestration services was provided:
    - **Amazon ECS**: An AWS-native, simpler solution deeply integrated with the AWS ecosystem, ideal for teams wanting lower operational overhead.
    - **Amazon EKS**: A managed Kubernetes service that aligns with the open-source standard, offering greater flexibility and multi-cloud portability at the cost of higher complexity.
    - **AWS Fargate & App Runner**: Serverless compute options that remove the need to manage underlying servers for containers, simplifying deployment and operations.

#### Monitoring and Observability

- The importance of full-stack observability was emphasized for maintaining and debugging distributed systems.
- **Amazon CloudWatch**: Used for collecting metrics, logs, and setting up alarms and dashboards.
- **AWS X-Ray**: Demonstrated for distributed tracing to analyze and debug performance bottlenecks in microservices architectures.

### Key Takeaways

#### Design Mindset

- **Automate Everything**: Transition from manual "ClickOps" to a fully automated IaC approach to ensure consistency, speed, and reliability.
- **Infrastructure as Code is Non-Negotiable**: IaC is the foundation for modern DevOps, enabling collaboration, versioning, and reproducibility of environments.
- **Choose the Right Tool for the Team**: The choice between CloudFormation, CDK, ECS, and EKS should be based on team skills, ecosystem needs, and the desired balance between simplicity and control.

#### Technical Architecture

- **CI/CD Pipelines**: Every project should have an automated pipeline that handles code integration, testing, and deployment to ensure rapid and safe releases.
- **Container-First for Microservices**: Use containers to package applications and their dependencies, and an orchestrator (ECS or EKS) to manage them at scale.
- **Full-Stack Observability**: Implement a robust monitoring strategy with metrics, logs (CloudWatch), and distributed tracing (X-Ray) to gain deep insights into application performance and health.

#### Modernization Strategy

- **Phased Adoption**: Introduce DevOps practices incrementally. Start by converting one manual process to IaC or building a CI/CD pipeline for a single service.
- **Leverage Serverless**: Use serverless options like AWS Fargate and App Runner to reduce operational complexity and allow teams to focus on application logic rather than infrastructure management.
- **Measure What Matters**: Focus on key DevOps metrics like Deployment Frequency, Lead Time for Changes, and Mean Time to Recovery (MTTR) to drive continuous improvement.

### Applying to Work

- **Automate a Deployment**: Convert a manually deployed application to use an AWS CodePipeline workflow.
- **Codify Infrastructure**: Define an existing S3 bucket or EC2 instance using an AWS CloudFormation template or a CDK application.
- **Containerize an Application**: Create a Dockerfile for a web application and push the image to Amazon ECR.
- **Pilot a Container Service**: Deploy a simple containerized application using AWS App Runner or ECS with the Fargate launch type.
- **Improve Observability**: Create a CloudWatch Dashboard for a critical application and configure alarms for key metrics like CPU utilization and error rates.

### Event Experience

Attending the "DevOps on AWS" workshop was extremely valuable, offering a comprehensive and practical guide to implementing modern DevOps practices on the cloud.

#### Learning from highly skilled speakers

- The AWS Community Builders provided deep, practical knowledge, breaking down complex topics into understandable concepts.

#### Hands-on technical exposure

- The multiple live demos, including a full CI/CD pipeline walkthrough and a microservices deployment on ECS, provided a clear, real-world context for the tools and services discussed.

#### Leveraging modern tools

- The workshop provided a thorough exploration of the modern AWS DevOps toolkit, from advanced IaC with the CDK to serverless containers with App Runner.

#### Networking and discussions

- The Q&A sessions offered opportunities to discuss career pathways and the AWS certification roadmap, providing valuable guidance for professional development.

#### Lessons learned

- Adopting IaC is the single most impactful step towards achieving a mature DevOps practice.
- AWS provides a complete, integrated toolset to build a sophisticated DevOps platform, with options suitable for teams of all sizes and skill levels.
- Effective monitoring and observability are not optional; they are critical for operating reliable and performant applications in the cloud.

#### Some event photos
{{< image-grid >}}
<img src="/images/event5.2.jpeg" alt="Event Photo 1">
<img src="/images/event5.4.jpeg" alt="Event Photo 2">
<img src="/images/event5.3.jpeg" alt="Event Photo 3">
<img src="/images/event5.1.jpeg" alt="Event Photo 4">
{{< /image-grid >}}
