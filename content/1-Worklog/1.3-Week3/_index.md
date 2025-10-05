---
title: "Week 3 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---


### Week 3 Objectives:

* Learn and practice intermediate AWS services.
* Gain hands-on experience with managed database, compute optimization, autoscaling, and monitoring.
* Start building the first project main game prototype

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                                                                                              | Start Date | Completion Date | Reference Material                                                                                                                                                                                                                                    |
|-----|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Learn about **Amazon RDS** and supported engines (MySQL, PostgreSQL, etc.) <br> - **Practice:** <br>&emsp; + Create RDS instance <br>&emsp; + Configure security groups <br>&emsp; + Connect via client <br>&emsp; + Create DB, insert data                                     | 09/22/2025 | 09/22/2025      | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html> <https://www.youtube.com/watch?v=GvUaA9cygUk&pp=ygUHYXdzIHJkcw%3D%3D> <https://www.youtube.com/watch?v=GvUaA9cygUk&pp=ygUHYXdzIHJkcw%3D%3D> <https://000045.awsstudygroup.com/> |
| 3   | - Learn about **Amazon Lightsail** and how it differs from EC2 <br> - **Practice:** <br>&emsp; + Launch Lightsail instance (Linux or WordPress) <br>&emsp; + Connect via SSH <br>&emsp; + Deploy static website <br>&emsp; + Explore pricing                                      | 09/23/2025 | 09/23/2025      | <https://lightsail.aws.amazon.com/> <https://www.youtube.com/watch?v=COK3ko4np-0&t=305s&pp=ygUNYXdzIGxpZ2h0c2FpbA%3D%3D> <https://000045.awsstudygroup.com/>                                                                                          |
| 4   | - Learn about **Amazon EC2 Autoscaling** concepts (launch templates, scaling groups, policies) <br> - **Practice:** <br>&emsp; + Create Launch Template <br>&emsp; + Create Auto Scaling Group <br>&emsp; + Configure scaling policy <br>&emsp; + Stress test and observe scaling | 09/24/2025 | 09/24/2025      | <https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html> <https://000045.awsstudygroup.com/>                                                                                                                      |
| 5   | - Weekly team meetup <br> - Start working on the main game prototype                                                                                                                                                                                                              | 09/25/2025 | 09/25/2025      |                                                                                                                                                                                                                                                       |
| 6   | - Continue on building the first prototype                                                                                                                                                                                                                                        | 09/26/2025 | 09/26/2025      |                                                                                                                                                                                                                                                       |

---

### Week 3 Achievements:
* Weekly meetup:
  * Designed together the app's UI
  * Come up with more business rules
  * Design more game mode
* Project prototype building:
  * Installed libraries: antd, socket.io, idv4...
  * Dived-deep into game mechanics and logic
  * Built simple UI
  * Built basic game logic for the main game
    * Each word will have its own <input> field
    * Press space to proceed to the next word
* Understood the differences between **managed database (RDS)** and self-managed EC2-hosted databases.
* Deployed a working application on **Amazon Lightsail**, compared cost and use cases with EC2.
* Successfully created an **EC2 Auto Scaling Group** with scaling policies that respond to load.
* Gained hands-on skills with services beyond basic EC2, moving closer to production-grade AWS infrastructure.  
