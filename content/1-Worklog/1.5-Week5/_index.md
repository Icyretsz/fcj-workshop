---
title: "Week 5 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:
* Learn about AWS Lambda
* Learn about Amazon Cognito and modern authorization and authentication in web apps
* Learn about API Gateway

### Tasks to be carried out this week:
| Day | Task                                                                                      | Start Date | Completion Date | Reference Material                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| --- |-------------------------------------------------------------------------------------------|------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Learn about AWS Lambda                                                                  | 10/06/2025 | 10/06/2025     | <https://000078.awsstudygroup.com/> <https://aws.amazon.com/lambda/> <https://www.youtube.com/watch?v=e1tkFsFOBHA> <https://www.youtube.com/watch?v=jgpRAiar2LQ>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
| 3   | - Learn about modern authen/authorize in modern web apps<br/>- Learn about Amazon Cognito | 10/07/2025 | 10/07/2025     | <https://dev.to/ifechukwuobiezue/mastering-modern-authentication-in-web-applications-oauth-20-demystified-2b8b> <https://aws.amazon.com/pm/cognito/?trk=246e27b6-9b79-45af-ba59-fdf3f8be64f8&sc_channel=ps&ef_id=CjwKCAiA86_JBhAIEiwA4i9Ju4vtraj9L5zlL1SabX8bG7JmTWk4rdIX4ehPV0jJd8BKZlIMymtVdhoCunwQAvD_BwE:G:s&s_kwcid=AL!4422!3!785574083501!e!!g!!amazon%20cognito!23300619811!193013719910&gad_campaignid=23300619811&gbraid=0AAAAADjHtp9IwNbHJlX1E1BUO5b4ZKeV_&gclid=CjwKCAiA86_JBhAIEiwA4i9Ju4vtraj9L5zlL1SabX8bG7JmTWk4rdIX4ehPV0jJd8BKZlIMymtVdhoCunwQAvD_BwE> <https://www.youtube.com/watch?v=tCNcG1lcCHY> <https://www.youtube.com/watch?v=UBUNrFtufWo> <https://www.youtube.com/watch?v=fyTxwIa-1U0> |
| 4   | - Learn about API Gateway                                                                 | 10/08/2025 | 10/08/2025     | <https://www.youtube.com/watch?v=6ULyxuHKxg8> <https://www.youtube.com/watch?v=jgpRAiar2LQ>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 5   | - Continue with FCJ Final Project                                                         | 10/09/2025 | 10/09/2025     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 6   | - Continue with FCJ Final Project                                                         | 10/10/2025 | 10/10/2025     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |


### Week 5 Achievements:

* Learned the fundamentals of AWS Lambda:
    * Understood the serverless compute model and its benefits (no server management, pay-per-use, automatic scaling).
    * Explored Lambda function anatomy: handler, event, context, and runtime environments.
    * Learned about Lambda execution environments and how they work (cold starts vs warm starts).
    * Understood Lambda pricing model: free tier (1M requests + 400K GB-seconds), then pay per request and compute time.
    * Explored different Lambda runtimes: Node.js, Python, Java, Go, and custom runtimes.
* Hands-on practice with Lambda functions:
    * Created first Lambda function using Node.js runtime.
    * Configured function settings: memory allocation, timeout, and environment variables.
    * Tested Lambda functions directly in the console with test events.
    * Explored Lambda logs in CloudWatch to debug and monitor executions.
    * Learned about Lambda layers for sharing code and dependencies across functions.

* Gained comprehensive understanding of modern authentication:
    * Learned OAuth 2.0 and OpenID Connect protocols and their role in modern web applications.
    * Understood the difference between authentication (who you are) and authorization (what you can do).
    * Explored JWT (JSON Web Tokens) structure: header, payload, signature.
    * Learned about token types: ID tokens (identity), Access tokens (API access), Refresh tokens (get new tokens).
    * Understood token-based authentication flow and security best practices.
* Mastered Amazon Cognito for user management:
    * Created and configured Cognito User Pools for user directory and authentication.
    * Set up user attributes: email, username, phone number, custom attributes.
    * Configured password policies and security settings (MFA options, account recovery).
    * Implemented email verification flow for user sign-ups.
    * Created app clients and configured OAuth 2.0 settings (grant types, scopes, callback URLs).
    * Set up Cognito Hosted UI for quick authentication testing.
    * Integrated Cognito with API Gateway as an authorizer to secure API endpoints.
* Hands-on authentication implementation:
    * Registered test users through Cognito Hosted UI.
    * Tested complete authentication flow: sign-up, email verification, sign-in.
    * Obtained and decoded JWT tokens to understand user claims.
    * Configured API Gateway Cognito authorizer to validate JWT tokens.
    * Tested authenticated API calls with Bearer tokens in Authorization headers.
    * Extracted user information from JWT claims in Lambda functions (`event.requestContext.authorizer.claims`).
    * Implemented auto-population of user data (cognitoSub, email) from authenticated tokens.
* Understood production-ready authentication patterns:
    * Learned about token refresh mechanism to maintain user sessions without re-authentication.
    * Explored token expiration handling and security implications.
    * Understood the importance of HTTPS and secure token storage.
    * Learned Cognito pricing model: first 50,000 MAU (Monthly Active Users) free.

* Learned Amazon API Gateway fundamentals:
    * Understood API Gateway role as the "front door" for backend services.
    * Explored different API types: REST API, HTTP API, WebSocket API.
    * Learned REST API concepts: resources, methods, stages, deployments.
    * Understood Lambda proxy integration vs custom integration patterns.
    * Explored API Gateway pricing: $3.50 per million requests (REST API).
* Built complete REST API infrastructure:
    * Created REST API with multiple resources and methods (GET, POST, PUT, DELETE).
    * Designed RESTful endpoints following best practices (`/users`, `/users/{id}`).
    * Configured Lambda proxy integration for seamless Lambda function invocation.
    * Set up path parameters for dynamic routing (`{id}`).
    * Implemented proper HTTP status codes for different scenarios (200, 201, 400, 404, 500).
* Configured CORS (Cross-Origin Resource Sharing):
    * Understood CORS and why it's needed for browser-based applications calling APIs from different domains.
    * Enabled CORS on API Gateway resources to allow frontend integration.
    * Configured allowed origins, headers, and methods for secure cross-origin requests.
    * Set up OPTIONS method for CORS preflight requests.
* Implemented API security and monitoring:
    * Deployed API to stages (dev, prod) for environment management.
    * Configured request throttling to protect backend from abuse (rate: 1000 req/s, burst: 2000).
    * Enabled CloudWatch Logs for request/response logging and debugging.
    * Set up detailed CloudWatch metrics for monitoring API performance.
    * Created CloudWatch alarms for 4XX errors, 5XX errors, and high latency.
* Hands-on API testing:
    * Tested API endpoints using API Gateway console test feature.
    * Made authenticated API calls using curl with Bearer tokens.
    * Used Postman to create complete API collection for all CRUD operations.
    * Verified proper error handling and status codes for various scenarios.
    * Exported API documentation in OpenAPI/Swagger format.

* Applied serverless knowledge to final project:
    * Designed and implemented serverless backend architecture for project requirements.
    * Integrated authentication, API Gateway, Lambda, and database components.
    * Tested end-to-end workflows and refined implementation.
    * Documented architecture decisions and implementation details.
    * Prepared project presentation and demonstration materials.
    
* **FCJ Project Development**
    * Completed **main game logic:**
        * Added timing modes: 15, 30, 60, unlimited
        * Built timer logic, countdown display and result calculation when the game ends
    * Further discussion with the team about the project
        * Multiplayer mode: 
          * Type Race: typing race
          * Wave Rush: typing race with multiple rounds