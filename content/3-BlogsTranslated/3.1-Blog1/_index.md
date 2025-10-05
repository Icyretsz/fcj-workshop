---
title: "Blog 1"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# SportsCapital launches real-time event detection for sports trading powered by AWS

**By:** James Lockwood and Asim Jalis | **Date:** 03 JUN 2025

**Categories:** Amazon Bedrock, Amazon EC2, Amazon Machine Learning, Amazon RDS, Amazon SageMaker, Amazon Transcribe, Artificial Intelligence, AWS Lambda, Betting and Gaming, Compute, Database, Games, Industries, RDS for PostgreSQL

*This blog was coauthored by Aaron Riccio, Co-Founder and CEO of SportsCapital, and Pravin Santhanam, CTO of SportsCapital.*

---

## The Challenge in Sports Trading

Sportsbooks rely on trading teams to manage odds and prevent sharp bettors from exploiting inefficiencies in their pricing. News events, such as reports of player injuries or lineup changes, introduce volatility that can shift the market at any moment and require traders to make adjustments in real time. Any delay in this process can expose sportsbooks to millions in liabilities.

However, with live news and reporting inherently unstructured, efficiently detecting and programmatically using these events in a real-time environment isn't simple. The challenge is further compounded by the sheer volume of reporting that stems from thousands of local and national sources across all major leagues and college athletics.

## SportsCapital's Solution

SportsCapital's event detection system, built on top of Amazon Web Services (AWS) such as Amazon SageMaker and Amazon Bedrock, delivers an innovative solution. It uses cloud-based data processing pipelines and generative AI to turn real-time content into structured feeds for pricing models and trading applications. Top sportsbooks use the technology for real-time injury alerting and, more recently, it's been leveraged to build automated trading systems.

> "Our alerts allow traders to be among the first to know about an injury event before the rest of the market reacts. We're now starting to see more investment from sportsbooks into trading automation, but it's only viable if you can effectively use these news reports to dynamically adjust your pricing and projections."
>
> — **Aaron Riccio, CEO of SportsCapital**

## The Cloud Advantage

SportsCapital's cloud-powered infrastructure enables real-time data processing for short and longform content online. It transforms live news from a broad range of media formats into enriched metadata, such as news classification, linked entities, and relevancy.

> "Managed services from AWS are central to our architecture, providing the scalability and reliability needed to process thousands of news items each day while maintaining strict latency requirements. They provide an efficient solution to deliver our real-time services to clients."
>
> — **Pravin Santhanam, CTO of SportsCapital**

![Image 1: Real-time event detection in SportsCapital’s system built on AWS.](/images/3-BlogsTranslated/SC-Image-1-1.png)
**Image 1**: Real-time event detection in SportsCapital’s system built on AWS.

### System Architecture

Using Amazon Elastic Cloud Compute (Amazon EC2) instances, SportsCapital's event detection system pipeline continuously monitors sources to detect relevant events. New data is stored in its database infrastructure through the MongoDB Atlas cloud developer data platform on Amazon EC2 and managed with Amazon Relational Database Service (Amazon RDS) for PostgreSQL.

As new reports enter the system, database triggers initiate serverless processing through AWS Lambda. Lambda functions perform text clean up and entity linking, such as identifying the player, team, staff, or agent mentioned in the text.

Amazon SageMaker hosts machine learning (ML) models trained by SportsCapital to perform these enrichment tasks, allowing them to scale processing based on demand. Content triggers match algorithms that route relevant information to the appropriate destinations based on any custom filtering.

**The current latency**, or time-to-availability, from when a report is published to when it is fully processed averages about **seven seconds**.

### Processing Longform Content

Longer form content requires additional processing, for which SportsCapital depends on large language models (LLMs) in Amazon Bedrock. These LLMs parse the content into multiple snippets with independent pieces of information. The pipeline also converts audio to text using speech-to-text technology from Amazon Transcribe. This segmentation allows them to maintain the same downstream processing pipeline regardless of the source.

## The Underlying AI Infrastructure

Maintaining a competitive edge with AI requires the ability to rapidly innovate without infrastructure getting in the way. AWS helps reduce the need for custom infrastructure development at SportsCapital. At the same time, it provides the flexibility and low latency requirements needed to meet the company's real-time data processing needs.

> "With Amazon SageMaker, we can bring in our own container images to implement custom inference pipelines. Comprehensive logging capabilities also provide critical visibility into system performance, helping us quickly identify and resolve any issues. Plus, the provisioned concurrency feature in Amazon SageMaker reduces our processing latency, which is huge in a business where every second counts."
>
> — **Pravin Santhanam, CTO of SportsCapital**

### Using Amazon Bedrock

SportsCapital is also using Amazon Bedrock to experiment with foundation models, including LLMs like Anthropic's Claude Sonnet 3.5. The generative AI service provides the team with a unified API that makes foundation model integration more seamless. Built-in logging capabilities allow SportsCapital's team to create a consistent operational experience across its ML stack.

Claude Sonnet 3.5 also plays a key role in enabling the pipeline to summarize longform content. SportsCapital built a training dataset to quantify the performance of this task and will continue to test new ones to improve evaluation metrics.

> "Amazon Bedrock handles complex deployment infrastructure for LLMs in an invaluable way. We can incorporate advanced natural language processing [NLP] capabilities into our real-time workflows without worrying about the underlying infrastructure. This will remain crucial as our needs change, and we're excited about the potential to fine-tune custom LLMs through Amazon Bedrock as our pipeline evolves."
>
> — **Pravin Santhanam, CTO of SportsCapital**

## Next Steps

In the future, SportsCapital plans to scale the number of short- and long-form sources it ingests across a full calendar year of sports.

> "Our team is focused on scaling ingestion across thousands of sources throughout the year and processing breaking news with a level of precision and speed this industry hasn't seen before."
>
> — **Aaron Riccio, CEO of SportsCapital**

---

Stay up to date with SportsCapital's latest developments, and reach out to an AWS representative to find out how generative AI services like Amazon SageMaker and Amazon Bedrock can enhance your AI projects.
