---
title: "Week 2 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---


### Week 2 Objectives:

* Discuss with teammates on the first project.
* Explore and practice on AWS VPC, EC2 and S3.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                       | Start Date | Completion Date | Reference Material                                                                                                                                                                                                      |
| --- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Meet up with teamates to discuss about the first project                                                                                                                                 | 09/15/2025 | 09/15/2025      |
| 3   | - Learn basic AWS networking <br> + VPS <br> + Subnet <br> + Security groups <br> + Gateways <br> + Endpoints <br> + Route tables <br>                                                     | 09/16/2025 | 09/16/2025      | <https://docs.aws.amazon.com/vpc/?icmpid=docs_homepage_networking> <https://www.youtube.com/watch?v=2doSoMN2xvI&t=127s> <https://www.youtube.com/watch?v=7_NNlnH7sAg> <https://000003.awsstudygroup.com/>               |
| 4   | - Learn basic EC2 <br> + All about instances <br> + Managing security groups <br> + SSH methods to instance <br> + Session manager <br> + AMI <br> + EBS Volume                            | 09/17/2025 | 09/17/2025      | <https://docs.aws.amazon.com/ec2/?icmpid=docs_homepage_compute> <https://www.youtube.com/watch?v=-t148tYgnJU&pp=ygUDZWMy> <https://www.youtube.com/watch?v=eaicwmnSdCs&pp=ygUDZWMy> <https://000004.awsstudygroup.com/> |
| 5   | - **Practice:** <br> + Create fully-functional VPC <br> + Create working EC2 instance on top of created VPC <br> + Explore methods to connect to EC2 instance <br> + Reachability analyzer | 09/18/2025 | 09/18/2025      | <https://www.youtube.com/watch?v=-t148tYgnJU&pp=ygUDZWMy> <https://www.youtube.com/watch?v=eaicwmnSdCs&pp=ygUDZWMy> <https://000004.awsstudygroup.com/>                                                                 |
| 6   | - Learn basic S3 <br> + Data storage <br> + Static hosting <br> + Bucket security practices                                                                                                | 09/19/2025 | 09/20/2025      | <https://docs.aws.amazon.com/s3/?icmpid=docs_homepage_serverless> <https://000057.awsstudygroup.com/>                                                                                                                   |


### Week 2 Achievements:

* Met with teammates and discussed the first project plan.
  * Determined the theme of project: a type racing game called TypeRush
  * Created team Figma
  * Debated on architecture and tech stack: microservices. Stack: React, Tailwind, Typescript, NodeJS, with Websocket for multiplayer
  * Distributed initial task: I got designing game logic, setting up Websocket and build prototype
  * Initialized Github repo

* Learned the basics of **AWS Networking**:
    * Understood the role of **VPC** in isolating and securing resources.
    * Set up and configured **Subnets**.
    * Managed **Security groups** to control inbound and outbound traffic.
    * Learned about **Gateways**, **Endpoints**, and **Route tables** and how they direct traffic inside and outside the VPC.

* Gained in-depth knowledge of **Amazon EC2**:
    * Understood what **EC2 instances** are and explored different **instance families** (General Purpose, Compute Optimized, Memory Optimized, etc.) along with their use cases.
    * Learned about **instance types** and how they differ in vCPU, RAM, and network performance.
    * Understood cost models: **On-Demand**, **Reserved Instances**, and **Spot Instances**.
    * Practiced managing **security groups** for controlling access.
    * Explored different connection methods:
        - Using **SSH key pairs** for secure login.
        - Using **Session Manager** to access EC2 instances directly from the AWS Console without exposing SSH ports.
    * Assigned **Elastic IPs** to maintain a static IP for an EC2 instance.
    * Attached and managed **EBS volumes** to expand storage capacity.
    * Learned the basics of **AMIs (Amazon Machine Images)** to launch new instances from existing images.

* Hands-on practice with networking and compute:
    * Created a **fully-functional VPC**.
    * Launched and configured an **EC2 instance** within the VPC.
    * Tested multiple connection methods to the EC2 instance.
    * Used **Reachability Analyzer** to analyze and troubleshoot connectivity paths.

* Learned the fundamentals of **Amazon S3**:
    * Stored and managed data in **S3 buckets**.
    * Practiced **static website hosting** with S3.
    * Applied **bucket security best practices** to protect data.

* Strengthened knowledge of **networking, compute, and storage** on AWS through direct hands-on practice, building a solid foundation for real-world project work.  

