---
title: "Week 6 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

* Connect and get acquainted with members of First Cloud Journey.
* Understand basic AWS services, how to use the console & CLI.

### Tasks to be carried out this week:
| Day | Task                                                                                                                           | Start Date | Completion Date | Reference Material                                                                                                                                                    |
| --- |--------------------------------------------------------------------------------------------------------------------------------|------------|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Study on security on AWS Cloud: <br> + How to privately access to S3 with VPC Endpoints <br> - Continue on build FCJ project | 10/06/2025 | 10/06/2025     | <https://000111.awsstudygroup.com/>                                                                                                                                   
| 3   | - Continue on security: <br> + AWS WAF <br> - Study about AWS cloud through AWS Certified Cloud Practitioner questions         | 10/07/2025 | 10/07/2025     | <https://000026.awsstudygroup.com/1-introduction/> <https://www.udemy.com/course/6-tests-aws-certified-cloud-practitioner-clf-c02-2025/> <https://aws.amazon.com/waf/> |
| 4   | - Continue on security: <br> + AWS WAF <br> - Continue building FCJ project                                                    | 10/08/2025 | 10/08/2025     | <https://aws.amazon.com/waf/>                                                                                                                             |
| 5   | - Weekly FCJ team meeting                                                                                                      | 10/09/2025 | 10/09/2025     |                                                                                                                             |
| 6   | - Continue building FCJ project                                                                                                | 10/10/2025 | 10/10/2025     |                                                                                                                             |


### Week 6 Achievements:

* Learned about **Accessing Private S3 Buckets via VPC Endpoints**
    * **Concepts learned:**
        * Gateway and Interface Endpoints allow access to AWS services privately without traversing the public internet.
        * **Gateway Endpoints**: Used for S3 and DynamoDB to route traffic from inside a VPC to the service securely.
        * **Interface Endpoints (AWS PrivateLink)**: Enable private connections to AWS services, partner services, or custom services using private IPs inside a VPC.
        * **AWS Direct Connect**: Provides a dedicated private connection between on-premises data centers and AWS, bypassing the public internet.
        * **AWS Site-to-Site VPN**: Establishes a secure encrypted connection between on-premises and AWS VPCs over the public internet (cheaper and easier than Direct Connect).
    * **Key takeaways:**
        * Gateway endpoints only handle **outbound traffic from within the VPC**.
        * On-prem environments must use **Interface Endpoints** combined with **VPN or Direct Connect** for private S3 access.
        * AWS-managed services such as **CloudFront, Bedrock, SNS, SQS, and Lambda (non-VPC)** operate **outside the VPC plane**, so they can communicate with services like S3 without endpoints.
        * VPC-based resources (e.g., EC2, ECS, VPC-attached Lambda) require **VPC endpoints or a NAT gateway** to reach S3 securely.
    * **Practice:**
        * Created a private S3 bucket and configured a Gateway VPC Endpoint for S3.
        * Verified EC2 instances in a private subnet could access S3 without using an Internet Gateway.
        * Tested S3 uploads from ECS to confirm correct routing through the VPC endpoint.
        * Observed failed connections when S3 bucket and instance were in different regions, fixed by aligning regions.
        * Reviewed CloudFront and pre-signed URL approaches for securely serving S3 content in web applications.
        * Simulate the on-prem environment by setting up a separate VPC and connect it to S3 by using AWS Direct Connect.
* Learned about web applications security concepts
    * **Practice**: 
        * Explore and exploit common security risks on web applications on OWASP Juice Shop
        * Explore about AWS WAF
        * Study 66 questions of AWS Certified Cloud Practitioner exam

