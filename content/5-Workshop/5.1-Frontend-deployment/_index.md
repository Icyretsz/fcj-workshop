---
title : "Part 1: Frontend Deployment with CloudFront, WAF, and S3"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

## Introduction

Welcome to the first workshop in our serverless application series! In this hands-on session, you'll learn how to deploy a secure, high-performance static website using AWS's content delivery and storage services.

Modern web applications require fast, reliable, and secure content delivery to users worldwide. In this workshop, you'll build the frontend infrastructure that forms the foundation of a production-ready serverless application. You'll configure Amazon S3 to host your static website files, set up Amazon CloudFront to distribute your content globally with low latency, and implement AWS WAF (Web Application Firewall) to protect your application from common web exploits.

![Diagram]( /images/5-Workshop/5.1-Frontend-deployment/5.1/diagram.png)

### What You'll Build

By the end of this workshop, you'll have deployed a complete frontend infrastructure featuring:

- **Static Website Hosting**: An S3 bucket configured to serve your HTML, CSS, and JavaScript files
- **Global Content Delivery**: A CloudFront distribution that caches and delivers your content from edge locations worldwide
- **Security Layer**: AWS WAF rules protecting your application from common threats like SQL injection, cross-site scripting (XSS), and DDoS attacks
- **HTTPS Security**: SSL/TLS certificate configuration for secure communication
- **Custom Domain** (Optional): Your website accessible via a custom domain name

### Why This Architecture?

This architecture offers several key benefits:

- **Performance**: CloudFront edge locations ensure fast load times for users regardless of their geographic location
- **Scalability**: Automatically handles traffic spikes without manual intervention
- **Cost-Effective**: Pay only for what you use, with no servers to manage
- **Security**: Multiple layers of protection including WAF, DDoS protection, and encryption
- **Reliability**: Built on AWS's highly available infrastructure with 99.9% uptime SLA

## Workshop Structure

This workshop is divided into the following sections:

1. **Part 1: S3 Static Website Hosting**
    - Create and configure S3 bucket
    - Upload website files
    - Configure bucket for static website hosting
    - Test basic website access

2. **Part 2: CloudFront Distribution Setup**
    - Create CloudFront distribution
    - Configure origin settings
    - Set up cache behaviors
    - Test global content delivery

3. **Part 3: AWS WAF Configuration**
    - Create Web ACL
    - Configure security rules
    - Associate WAF with CloudFront
    - Test security rules

4. **Part 4: Cleanup** (Optional)
    - Remove resources to avoid charges
    - Save configuration for future use

### Workshop Duration

**Estimated Time**: 2-3 hours

- Setup and preparation: 15 minutes
- S3 configuration: 30 minutes
- CloudFront setup: 45 minutes
- WAF implementation: 45 minutes
- Testing and validation: 30 minutes