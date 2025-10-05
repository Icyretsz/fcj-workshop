---
title: "Week 4 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Learn more AWS services.
* Continue on FCJ project prototype.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                               | Start Date | Completion Date | Reference Material                                                                                                                                                                       |
| --- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Learn about AWS CloudWatch                                                                                                                                       | 09/29/2025 | 09/29/2025      | <https://000008.awsstudygroup.com/1-introduction/> <https://docs.aws.amazon.com/cloudwatch/> <https://www.youtube.com/watch?v=ZCHwJLqPLj8> <https://www.youtube.com/watch?v=Yxl7e88cTAQ> 
| 3   | - Learn about AWS Elasti Cache                                                                                                                                     | 09/30/2025 | 09/30/2025      | <https://docs.aws.amazon.com/elasticache/> <https://redis.io/> <https://www.youtube.com/watch?v=jgpVdJB2sKQ>                                                                             |
| 4   | - Continue on FCJ project development                                                                                                                              | 10/01/2025 | 10/01/2025      |                                                                                                                                                                                          |
| 5   | - Participate in AWS event: [AWS GenAI Builder Club] AI-Driven Development Life Cycle: Reimagining Software Engineering <br> - Continue on FCJ project development | 10/02/2025 | 10/02/2025      |                                                                                                                                                                                          |
| 6   | - Continue on FCJ project development                                                                                                                              | 10/03/2025 | 10/03/2025      |                                                                                                                                                                                          |


### Week 4 Achievements:
* Learned about AWS CloudWatch
  * What CloudWatch does: It collects metrics, logs, and events from AWS resources and applications.
  * Core concepts: Metrics, Logs, Events, the Dashboard, Alarms, CloudWatch Agent
  * Practice:
    * Launched and watched EC2 instance metrics (CPU, Memory, Network, Disk)
    * Created an alarm when EC2 CPU > 70% then link to an SNS topic to get email alerts
    * Viewed EC2 instance's logs in CloudWatch Logs
    * Viewed logs with queries in Logs Insight
    * Added widgets showing EC2 CPU Utilization, NetworkIn/Out then add to the dashboard
* Learned about AWS Elasti Cache
  * Learned about caching in general in web applications
  * Discovered about Redis
  * Learned Elasti Cache key concepts: nodes, clusters, shards, replication groups
  * Practice:
    * Created a Redis Cluster with cluster mode disabled in a VPC with an EC2 instance
    * Ran a small Node.js app on EC2 that checks for cached data and fetch from API if missed
    * Monitoring with CloudWatch: viewed metrics like CPUUtilization, CurrConnections, Evictions
    * Replication group and failover: created a replication group with multi-AZ and 1 replica per shard
    * Simulate failover to see new primary taking over
* FCJ project development
  * Refine main game logic:
    * Add practice mode (singleplayer mode)
    * Add player's caret with smooth animation when moving between characters
    * Add color indicator for each character
    * Add game duration
    * Add logic for handling overflow characters at the end of each word
  * Introduced multiplayer built on WebSocket
    * Add room concept:
      * Players can create rooms, other players can join rooms with roomId
    * Add carets for each player that moves in real-time reflecting other players typing progress
* AWS event participation:
  * Participated in AWS GenAI Builder Club: AI-Driven Development Life Cycle: Reimagining Software Engineering
  * Learned about AI in development concepts:
    * How to leverage AI in the development cycle to improve software quality
    * Current state of AI in software developing
    * Learned about AI-driven development
    * Learned about Kiro IDE through demos
