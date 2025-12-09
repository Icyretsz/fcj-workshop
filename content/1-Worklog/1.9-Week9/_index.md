---
title: "Week 9 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:

* Design and plan comprehensive serverless architecture workshop
* Begin implementation of frontend deployment components
* Document step-by-step tutorial for serverless web application

### Tasks to be carried out this week:
| Day | Task                                                                             | Start Date | Completion Date | Reference Material                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Workshop planning and architecture design<br/>- Create workshop outline and structure | 27/10/2025 | 27/10/2025 | <https://docs.aws.amazon.com/whitepapers/latest/serverless-architectures-lambda/> |
| 3   | - Work on Prerequisites section<br/>- Setup S3 Static Website Hosting | 28/10/2025 | 28/10/2025 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html> |
| 4   | - Configure CloudFront Distribution<br/>- Setup custom domain and SSL certificates | 29/10/2025 | 29/10/2025 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/> |
| 5   | - Implement AWS WAF Configuration<br/>- Test security rules and rate limiting | 30/10/2025 | 30/10/2025 | <https://docs.aws.amazon.com/waf/latest/developerguide/> |
| 6   | - Review and test Part 1: Frontend Deployment<br/>- Document troubleshooting steps | 31/10/2025 | 31/10/2025 | |


### Week 9 Achievements:

**Workshop Architecture Planning and Design**
* Conceptualized comprehensive serverless architecture workshop:
    * Designed two-part structure: Frontend Deployment and Backend Deployment.
    * Planned integration of key AWS services: S3, CloudFront, WAF, API Gateway, Lambda, RDS, Cognito, and Secrets Manager.
    * Created detailed step-by-step tutorial outline for complete serverless web application deployment.
    * Organized workshop into logical sections with clear prerequisites and learning objectives.
* Defined workshop target audience and learning outcomes:
    * Aimed at developers transitioning to serverless architectures.
    * Focus on hands-on implementation with production-ready configurations.
    * Emphasis on security best practices and cost optimization.

**Part 1: Frontend Deployment - Prerequisites and Planning**
* Established workshop prerequisites:
    * AWS account with appropriate IAM permissions.
    * Basic understanding of web development (HTML, CSS, JavaScript).
    * Familiarity with AWS Console and basic AWS services.
    * Code editor and Git for version control.
* Prepared sample frontend application:
    * Created simple static website with HTML, CSS, and JavaScript.
    * Designed responsive UI for demo purposes.
    * Prepared assets and content for deployment testing.

**S3 Static Website Hosting Implementation**
* Configured S3 bucket for static website hosting:
    * Created S3 bucket with appropriate naming conventions.
    * Enabled static website hosting feature.
    * Uploaded website files (HTML, CSS, JavaScript, images).
    * Configured index document and error document.
* Set up bucket policies for public access:
    * Wrote bucket policy to allow public read access to website objects.
    * Configured CORS settings for cross-origin requests.
    * Tested website accessibility via S3 website endpoint.
* Implemented versioning and lifecycle policies:
    * Enabled S3 versioning for content rollback capability.
    * Created lifecycle rules for automated object management.
    * Documented best practices for S3 website hosting.

**CloudFront Distribution Setup**
* Configured CloudFront distribution for global content delivery:
    * Created CloudFront distribution with S3 bucket as origin.
    * Configured origin access identity (OAI) for secure S3 access.
    * Updated S3 bucket policy to restrict access to CloudFront only.
    * Set default root object and custom error responses.
* Optimized CloudFront settings:
    * Configured caching behaviors and TTL settings.
    * Enabled compression for improved performance.
    * Set up query string forwarding and header configuration.
    * Configured geographic restrictions if needed.
* SSL/TLS certificate configuration:
    * Requested SSL certificate via AWS Certificate Manager (ACM).
    * Validated domain ownership through DNS or email.
    * Associated certificate with CloudFront distribution.
    * Configured HTTPS-only access with security policies.
* Custom domain setup:
    * Configured alternate domain names (CNAMEs) in CloudFront.
    * Created Route 53 hosted zone for domain management.
    * Added A record (alias) pointing to CloudFront distribution.
    * Tested custom domain access and SSL certificate validity.

**AWS WAF Configuration for Security**
* Implemented AWS WAF for web application protection:
    * Created Web ACL (Access Control List) for CloudFront.
    * Configured managed rule groups for common threats:
        * Core Rule Set (CRS) for OWASP Top 10 protection.
        * Known Bad Inputs rule set for malicious request patterns.
        * SQL injection and XSS protection rules.
    * Set up rate-based rules to prevent DDoS attacks:
        * Configured rate limiting (e.g., 2000 requests per 5 minutes per IP).
        * Created IP reputation rules using threat intelligence.
        * Set up geographic blocking rules if required.
* Custom WAF rules implementation:
    * Created custom rules for application-specific security needs.
    * Configured URI path matching and header inspection rules.
    * Set up allow/block rules based on country of origin.
    * Implemented bot protection and challenge mechanisms.
* WAF monitoring and logging:
    * Enabled WAF logging to S3 or CloudWatch Logs.
    * Configured CloudWatch metrics for rule match counts.
    * Set up CloudWatch alarms for unusual traffic patterns.
    * Created dashboard for real-time WAF monitoring.

**Testing and Validation of Frontend Deployment**
* Comprehensive testing of deployed frontend:
    * Verified website accessibility via CloudFront URL and custom domain.
    * Tested HTTPS enforcement and SSL certificate validation.
    * Validated caching behavior and cache invalidation process.
    * Checked geographic access and edge location performance.
* Security testing:
    * Confirmed WAF rules are blocking malicious requests.
    * Tested rate limiting by simulating high request volumes.
    * Verified that direct S3 access is blocked (only CloudFront allowed).
    * Validated CORS configuration for API integration readiness.
* Performance optimization:
    * Analyzed CloudFront cache hit ratio and optimization opportunities.
    * Tested compression effectiveness for different file types.
    * Measured global latency from multiple geographic locations.
    * Optimized TTL settings based on content update frequency.

**Documentation and Workshop Content Creation**
* Created detailed workshop documentation:
    * Wrote step-by-step instructions for each configuration.
    * Included screenshots and diagrams for visual guidance.
    * Documented common issues and troubleshooting steps.
    * Prepared cleanup instructions to avoid unnecessary costs.
* Best practices documentation:
    * Security best practices for S3, CloudFront, and WAF.
    * Cost optimization strategies for frontend deployment.
    * Performance tuning recommendations.
    * Monitoring and maintenance guidelines.

**Lessons Learned and Challenges**
* Key insights from frontend deployment implementation:
    * Importance of proper cache invalidation strategy.
    * Security considerations when exposing S3 content.
    * Trade-offs between security rules and legitimate traffic.
    * Certificate validation timing and DNS propagation delays.
* Challenges addressed:
    * CloudFront distribution deployment time (15-20 minutes).
    * ACM certificate validation in us-east-1 region requirement.
    * WAF rule fine-tuning to avoid false positives.
    * Custom domain DNS propagation delays.

**Preparation for Part 2: Backend Deployment**
* Planned backend architecture components:
    * VPC and network design for secure backend resources.
    * RDS database setup for application data storage.
    * Lambda functions for serverless compute.
    * API Gateway for RESTful API endpoints.
    * Cognito for user authentication and authorization.
    * Secrets Manager for secure credential storage.
* Outlined backend integration requirements:
    * API authentication flow with Cognito JWT tokens.
    * Database connection from Lambda functions.
    * Environment variable management and secrets handling.
    * Frontend-backend communication via API Gateway.