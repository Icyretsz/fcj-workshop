---
title: "Week 10 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:

* Complete backend deployment section of serverless workshop
* Integrate frontend with backend services
* Test end-to-end serverless application functionality

### Tasks to be carried out this week:
| Day | Task                                                                             | Start Date | Completion Date | Reference Material                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - VPC and Network Setup<br/>- RDS Database Configuration | 03/11/2025 | 03/11/2025 | <https://docs.aws.amazon.com/vpc/><br/><https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 3   | - AWS Secrets Manager Configuration<br/>- Lambda Functions Development | 04/11/2025 | 04/11/2025 | <https://docs.aws.amazon.com/secretsmanager/><br/><https://docs.aws.amazon.com/lambda/> |
| 4   | - API Gateway Setup<br/>- Amazon Cognito Configuration | 05/11/2025 | 05/11/2025 | <https://docs.aws.amazon.com/apigateway/><br/><https://docs.aws.amazon.com/cognito/> |
| 5   | - Frontend Integration with Backend<br/>- End-to-end testing | 06/11/2025 | 06/11/2025 | |
| 6   | - Workshop documentation finalization<br/>- Clean up and cost optimization review | 07/11/2025 | 07/11/2025 | |


### Week 10 Achievements:

**Part 2: Backend Deployment - VPC and Network Setup**
* Designed and implemented secure VPC architecture:
    * Created VPC with appropriate CIDR block (e.g., 10.0.0.0/16).
    * Configured public and private subnets across multiple Availability Zones.
    * Set up Internet Gateway for public subnet internet access.
    * Created NAT Gateway in public subnet for private subnet outbound traffic.
* Network routing and security:
    * Configured route tables for public and private subnets.
    * Associated subnets with appropriate route tables.
    * Created security groups for Lambda, RDS, and other resources.
    * Documented security group rules and best practices.
* VPC endpoints for AWS services:
    * Created VPC endpoints for S3 (Gateway endpoint).
    * Set up VPC endpoints for Secrets Manager and other services (Interface endpoints).
    * Configured security groups and route tables for endpoint access.
    * Tested private connectivity to AWS services.

**RDS Database Setup**
* Provisioned RDS database instance:
    * Selected appropriate database engine (e.g., PostgreSQL, MySQL).
    * Chose instance class based on workload requirements.
    * Configured Multi-AZ deployment for high availability.
    * Placed RDS in private subnets for security.
* Database configuration:
    * Set up database subnet group spanning multiple AZs.
    * Configured security group allowing access only from Lambda.
    * Enabled automated backups with appropriate retention period.
    * Configured encryption at rest using AWS KMS.
* Database initialization:
    * Connected to RDS instance from bastion host or Cloud9.
    * Created database schema and tables.
    * Set up database users with appropriate permissions.
    * Inserted sample data for testing purposes.
* RDS monitoring and optimization:
    * Enabled Enhanced Monitoring and Performance Insights.
    * Configured CloudWatch alarms for CPU, memory, and connections.
    * Set up automated snapshots and backup verification.
    * Documented database connection parameters.

**AWS Secrets Manager Configuration**
* Implemented secure credential management:
    * Created secrets for RDS database credentials.
    * Stored API keys and other sensitive configuration.
    * Configured secret rotation for database passwords.
    * Set up IAM policies for Lambda to access secrets.
* Secret structure and organization:
    * Organized secrets with clear naming conventions.
    * Stored connection strings and environment-specific configs.
    * Documented secret ARNs for Lambda environment variables.
    * Implemented least-privilege access policies.
* Integration with Lambda and RDS:
    * Configured Lambda functions to retrieve secrets at runtime.
    * Implemented caching strategy for secret values.
    * Set up automatic secret rotation with Lambda rotation function.
    * Tested secret retrieval and connection establishment.

**Lambda Functions Development**
* Developed serverless backend functions:
    * Created Lambda functions for CRUD operations.
    * Implemented database connection pooling for efficiency.
    * Wrote handler functions for different API endpoints.
    * Configured appropriate runtime (Node.js, Python, etc.).
* Lambda configuration and optimization:
    * Set memory allocation and timeout values.
    * Configured environment variables for configuration.
    * Attached Lambda to VPC for RDS access.
    * Set up Lambda layers for shared dependencies.
* Error handling and logging:
    * Implemented comprehensive error handling.
    * Configured CloudWatch Logs for debugging.
    * Added structured logging with correlation IDs.
    * Set up CloudWatch alarms for function errors.
* Lambda security best practices:
    * Created IAM execution role with minimal permissions.
    * Granted access to Secrets Manager, RDS, and CloudWatch.
    * Enabled X-Ray tracing for performance monitoring.
    * Configured VPC security groups for Lambda functions.

**API Gateway Setup**
* Created REST API in API Gateway:
    * Designed RESTful API with appropriate resources and methods.
    * Configured Lambda proxy integration for backend functions.
    * Set up path parameters and query string handling.
    * Implemented proper HTTP status code responses.
* API Gateway security configuration:
    * Configured Cognito User Pool as authorizer.
    * Set up authorization on protected endpoints.
    * Implemented request validation schemas.
    * Configured throttling and rate limiting.
* CORS configuration:
    * Enabled CORS for frontend domain.
    * Configured allowed origins, headers, and methods.
    * Set up OPTIONS method for preflight requests.
    * Tested cross-origin requests from frontend.
* API deployment and stages:
    * Created deployment stages (dev, staging, prod).
    * Configured stage variables for environment-specific settings.
    * Enabled CloudWatch Logs for API Gateway.
    * Set up custom domain name for API (optional).

**Amazon Cognito Configuration**
* Set up Cognito User Pool:
    * Created user pool for authentication.
    * Configured sign-up and sign-in attributes.
    * Set password policies and MFA options.
    * Configured email verification and user confirmation.
* User Pool customization:
    * Customized email templates for verification and welcome messages.
    * Set up hosted UI for testing authentication flow.
    * Configured password recovery process.
    * Added custom attributes for user profiles.
* App client configuration:
    * Created app client for frontend application.
    * Configured OAuth 2.0 flows and grant types.
    * Set callback URLs and sign-out URLs.
    * Generated client ID and configured scopes.
* Cognito integration with API Gateway:
    * Configured API Gateway Cognito authorizer.
    * Set up token validation and claims mapping.
    * Tested authenticated API requests with JWT tokens.
    * Documented authentication flow for frontend developers.

**Frontend Integration with Backend**
* Integrated frontend with API Gateway:
    * Updated frontend code to call API endpoints.
    * Implemented API client with proper error handling.
    * Added loading states and user feedback.
    * Configured API base URL in frontend configuration.
* Authentication integration:
    * Integrated AWS Amplify or Cognito SDK in frontend.
    * Implemented sign-up, sign-in, and sign-out flows.
    * Added authentication state management.
    * Configured token storage and refresh mechanism.
* API request authentication:
    * Implemented JWT token extraction from Cognito.
    * Added Authorization header to API requests.
    * Handled token expiration and refresh.
    * Implemented logout and session management.
* User interface enhancements:
    * Created protected routes requiring authentication.
    * Added user profile display with Cognito data.
    * Implemented forms for data submission to backend.
    * Added data display from database via API.

**End-to-End Testing**
* Comprehensive application testing:
    * Tested complete user registration and authentication flow.
    * Verified API calls with authenticated and unauthenticated users.
    * Tested CRUD operations through frontend interface.
    * Validated data persistence in RDS database.
* Security testing:
    * Verified that unauthenticated requests are blocked.
    * Tested JWT token validation and expiration handling.
    * Confirmed CORS configuration works correctly.
    * Validated SQL injection protection and input sanitization.
* Performance testing:
    * Tested Lambda cold start times and warm execution.
    * Measured API response times from frontend.
    * Verified CloudFront caching of static assets.
    * Tested application under simulated load.
* Error scenario testing:
    * Tested error handling for invalid inputs.
    * Verified graceful degradation when services are unavailable.
    * Tested network error handling and retry logic.
    * Validated user-friendly error messages.

**Workshop Documentation Finalization**
* Completed comprehensive workshop guide:
    * Written detailed step-by-step instructions for all components.
    * Included architecture diagrams and service interaction flows.
    * Added troubleshooting section for common issues.
    * Created cleanup guide to remove all resources.
* Code samples and templates:
    * Provided Lambda function code templates.
    * Included CloudFormation templates for infrastructure (optional).
    * Created sample frontend application with comments.
    * Shared API request examples and authentication code.
* Cost optimization documentation:
    * Documented estimated costs for running the workshop.
    * Provided strategies for minimizing costs during learning.
    * Included cleanup checklist to avoid ongoing charges.
    * Suggested AWS Free Tier usage where applicable.

**Clean Up and Resource Management**
* Created comprehensive cleanup procedure:
    * Documented step-by-step resource deletion order.
    * Emphasized importance of deleting S3 buckets and RDS instances.
    * Included CloudFormation stack deletion if used.
    * Provided verification steps to ensure all resources are removed.
* Cost monitoring:
    * Set up billing alerts for unexpected charges.
    * Reviewed AWS Cost Explorer for workshop expenses.
    * Documented best practices for cost control.
    * Shared tips for preventing accidental resource provisioning.

**Key Takeaways and Lessons Learned**
* Technical insights:
    * Importance of proper VPC design for security and performance.
    * Lambda cold starts and optimization strategies.
    * Database connection management in serverless architectures.
    * JWT token handling and security best practices.
* Architectural decisions:
    * Trade-offs between serverless and traditional architectures.
    * When to use RDS vs DynamoDB for different use cases.
    * API Gateway vs Application Load Balancer considerations.
    * Multi-region deployment strategies for global applications.
* Workshop improvements identified:
    * Additional examples for common use cases.
    * Video tutorials to supplement written documentation.
    * Infrastructure as Code templates for faster setup.
    * Advanced topics for further learning.

**Future Workshop Enhancements**
* Planned additions for future iterations:
    * CI/CD pipeline setup with AWS CodePipeline.
    * Monitoring and observability with X-Ray and CloudWatch.
    * Advanced security with AWS Shield and security headers.
    * Multi-environment deployment strategies.
    * WebSocket API implementation with API Gateway.
    * GraphQL API alternative with AWS AppSync.
    * Containerized deployments with ECS/Fargate.
    * Infrastructure as Code with Terraform or CDK.