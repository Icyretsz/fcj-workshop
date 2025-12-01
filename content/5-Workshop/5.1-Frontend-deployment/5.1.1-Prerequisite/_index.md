---
title : "Prerequisites"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.1.1 </b> "
---

### Required AWS Knowledge

- **AWS Console Navigation**: Ability to navigate the AWS Management Console and find services
- **Basic AWS Concepts**: Understanding of AWS regions, availability zones, and basic service interactions
- **No prior experience with S3, CloudFront, or WAF required** - we'll cover everything step by step

### Required Technical Skills

- **Basic Web Development**: Understanding of HTML, CSS, and JavaScript
- **File System Operations**: Ability to create, edit, and organize files and folders
- **Command Line Basics**: Comfortable running basic terminal/command prompt commands
- **Text Editing**: Familiarity with any code editor or IDE

### Required AWS Account Setup

Before starting this workshop, ensure you have:

1. **AWS Account**
    - Active AWS account with administrative access
    - Credit card on file (required even for Free Tier)
    - MFA (Multi-Factor Authentication) enabled on root account (strongly recommended)

2. **IAM User** (Recommended)
    - IAM user with appropriate permissions instead of using root account
    - Required permissions:
        - `AmazonS3FullAccess`
        - `CloudFrontFullAccess`
        - `WAFv2FullAccess`
        - `AWSCertificateManagerFullAccess` (if using custom domain)
    - Access key and secret key generated (for CLI access)

3. **Billing Alerts**
    - Set up AWS Budgets or billing alerts to monitor costs
    - Recommended: Set alert at $10 threshold

### Required Tools and Software

Install the following tools on your local machine:

1. **Text Editor or IDE**
    - VS Code (recommended): https://code.visualstudio.com/
    - Or any editor of your choice (Sublime Text, Atom, etc.)

2. **Web Browser**
    - Modern browser (Chrome, Firefox, Safari, or Edge)
    - Multiple tabs recommended for console navigation

3. **Git** (Optional but recommended)
    - Download: https://git-scm.com/
    - Used for version control and sample code retrieval

### Sample Application

We'll provide a simple static website for this workshop.

### Optional: Custom Domain Setup

If you want to use a custom domain (e.g., `www.yoursite.com`):

- **Domain Name**: Registered domain (can use Route 53 or external registrar)
- **DNS Access**: Ability to modify DNS records for your domain
- **Note**: This is optional; you can complete the workshop using CloudFront's default domain

### Cost Expectations for Part 1: Frontend Deployment

**Free Tier Eligible Services:**
- **S3**: 5GB storage, 20,000 GET requests, 2,000 PUT requests (first 12 months)
- **CloudFront**: 1TB data transfer out, 10,000,000 HTTP/HTTPS requests (first 12 months)
- **AWS WAF**: No Free Tier, but minimal cost for basic rules

**Estimated Costs** (if exceeding Free Tier):
- S3 storage: $0.023 per GB per month
- CloudFront data transfer: $0.085 per GB (varies by region)
- WAF: $5.00 per month per web ACL + $1.00 per rule per month
- **Total estimated cost for this workshop**: $0-$2 (within Free Tier) or $5-$10 (with WAF)

**Cost Saving Tips:**
- Delete resources immediately after workshop if not continuing
- Use small sample files to minimize storage and transfer costs
- Start with basic WAF rules and expand later

## Ready to Begin?

Once you've completed all prerequisites and verified your setup, you're ready to start building your secure, globally distributed frontend infrastructure!

Let's move on to **Part 1: S3 Static Website Hosting**.