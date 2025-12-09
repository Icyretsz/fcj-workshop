---
title: "Data Science On AWS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---



# Summary Report

### Event Objectives

- Provide a comprehensive overview of building a modern Data Science system on AWS.
- Introduce the end-to-end Data Science pipeline, from data processing to model deployment.
- Offer hands-on experience with key AWS services like AWS Glue and Amazon SageMaker.
- Discuss practical considerations such as cost, performance, and the benefits of cloud vs. on-premise solutions.

### Speakers

- **Văn Hoàng Kha** – Cloud Solutions Architect, AWS Community Builder
- **Bạch Doãn Vương** – Cloud Develops Engineer, AWS Community Builder

### Key Highlights

#### End-to-End Data Science Pipeline on AWS

The workshop outlined the complete data science journey on the cloud, using core services:

- **Amazon S3**: For scalable data storage.
- **AWS Glue**: For serverless data integration, ETL (Extract, Transform, Load), and data cleaning.
- **Amazon SageMaker**: For building, training, and deploying machine learning models at scale.

#### Practical Demonstrations

- **Demo 1: Data Processing with AWS Glue**: Showcased how to process and clean a real-world IMDb dataset, emphasizing the importance of data quality for model accuracy.
- **Demo 2: Model Training with SageMaker**: Demonstrated the process of training and deploying a Sentiment Analysis model, making the abstract concepts of ML deployment concrete.
- **Integrating Custom Models**: Showcased how to leverage frameworks like TensorFlow and PyTorch within SageMaker, using a sample project from a provided GitHub repository.

#### Broadening AI/ML Capabilities with Managed Services

An overview of AWS's pre-built AI services that accelerate development:

- **Amazon Transcribe**: Speech-to-text conversion.
- **Amazon Comprehend**: Natural language processing for sentiment analysis and topic extraction.
- **Amazon Rekognition**: Image and video analysis.
- **Amazon Personalize**: Building personalized recommendation systems.

### Key Takeaways

#### Data-First Mindset

- **Business-first approach**: Always start with the business context of the data, as emphasized by the need for feature engineering.
- **Data quality is paramount**: The workshop stressed that the accuracy of any ML model is directly dependent on the quality of the input data.
- **Data as an asset**: Data collection, governance, and security are the foundational pillars of a data-driven organization.

#### Technical Architecture

- **Modular Pipeline**: The standard architecture involves a pipeline from S3 (storage) to AWS Glue (ETL) to Amazon SageMaker (ML), allowing for a clean separation of concerns.
- **Flexibility**: AWS supports both low-code solutions like SageMaker Canvas and code-intensive custom model training using frameworks like TensorFlow/PyTorch.
- **Serverless benefits**: Using services like AWS Glue removes the need for managing infrastructure, allowing teams to focus on data and models.

#### Strategy

- **Phased approach**: Start with data collection and cleaning before moving to complex model training.
- **Cloud vs. On-premise**: The discussion highlighted that the cloud offers significant advantages in scalability, pay-for-what-you-use cost models, and access to powerful computing resources without upfront investment.
- **ROI Measurement**: Leverage cloud benefits to reduce development time and infrastructure overhead, leading to faster time-to-market for AI-powered features.

### Applying to Work

- **Automate ETL**: Use AWS Glue to create automated data cleaning and preparation jobs for analytics and ML.
- **Adopt SageMaker**: Pilot Amazon SageMaker for training and deploying ML models to streamline the MLOps lifecycle.
- **Implement Sentiment Analysis**: Apply the concepts from the demo to analyze customer feedback from reviews or support tickets.
- **Explore Pre-built AI**: Integrate services like Amazon Rekognition for content moderation or Amazon Transcribe for call center analytics.
- **Consolidate Knowledge**: Build a small project based on the workshop's guidance to reinforce the concepts learned.

### Event Experience

Attending the "Data Science on AWS" workshop provided a valuable, hands-on journey into cloud-based machine learning. Key experiences included:

#### Learning from highly skilled speakers
- The speakers, both AWS Community Builders, shared practical insights and best practices from their real-world experience.

#### Hands-on technical exposure
- The live demos of processing data with AWS Glue and training a model with SageMaker were extremely effective at translating theory into practice.

#### Leveraging modern tools
- Explored the comprehensive AWS ecosystem for data science, understanding how different services fit together to form a cohesive pipeline.
- Learned about both fully managed AI services and the powerful customization options available within SageMaker.

#### Lessons learned
- A solid data preparation strategy is non-negotiable for success in machine learning.
- AWS significantly lowers the barrier to entry for building and deploying sophisticated data science systems.
- Modern cloud platforms provide the flexibility to choose between low-code tools for speed and custom code for specific, complex requirements.

#### Some event photos
<div style="display: flex; gap: 10px;">
  <img src="/images/event2pic1.jpg" alt="Event Photo 1" style="width: 50%;">
  <img src="/images/event2pic2.jpg" alt="Event Photo 2" style="width: 50%;">
</div>
<div style="text-align: center;">
  <img src="/images/event2pic3.jpeg" alt="Event Photo 3" style="width: 50%;">
</div>

> Overall, the workshop provided not only technical knowledge but also practical experience with building end-to-end data science pipelines on AWS, emphasizing the importance of data quality and the power of cloud-native ML services.
