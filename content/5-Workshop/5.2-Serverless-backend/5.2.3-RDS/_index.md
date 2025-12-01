---
title : "RDS Database Setup"
date :  "2025-09-15" 
weight : 3 
chapter : false
pre : " <b> 5.2.3 </b> "
---

## Overview

In this section, you'll create an Amazon RDS PostgreSQL database instance within your VPC. The database will be deployed in a private subnet, making it inaccessible from the internet while still allowing your Lambda functions to connect securely.

**What you'll accomplish:**
- Understand RDS architecture and configuration options
- Create a DB subnet group for RDS deployment
- Configure and launch an RDS PostgreSQL instance
- Set up database parameters and options
- Create initial database schema and tables
- Verify database connectivity
- Understand RDS security and backup settings

**Estimated time**: 35-40 minutes (includes 10-15 min database creation wait time)

## Why Amazon RDS?

### Benefits Over Self-Managed Databases

**Managed Service:**
- Automated backups and point-in-time recovery
- Automated software patching and updates
- Monitoring and metrics built-in
- High availability options (Multi-AZ)
- Read replicas for scaling reads

**Operational Simplicity:**
- No server provisioning or OS management
- Easy scaling of compute and storage
- Automated failover for Multi-AZ deployments
- CloudWatch integration for monitoring

**Cost Efficiency:**
- Pay only for what you use
- Reserved instances for production workloads
- Storage auto-scaling available

## Understanding RDS Configuration

### Costs considerations

For this workshop, we'll use **db.t3.micro**:
- 2 vCPUs, 1 GB RAM
- Burstable performance (suitable for dev/test)
- Free Tier eligible (750 hours/month for first 12 months)
- $0.017/hour (~$12.41/month) outside Free Tier
- Overall: $0 (free-tier) or <$2 (if clean up immediately after workshop)

### Single-AZ vs Multi-AZ

**Single-AZ (Workshop Setup):**
- Database in one Availability Zone
- Lower cost
- Good for development/testing
- ~5 minutes downtime for maintenance

**Multi-AZ (Production):**
- Synchronous replication to standby in different AZ
- Automatic failover (~1-2 minutes)
- Higher cost (~2x)
- Better for production workloads

### Storage Configuration

**Storage Type:** General Purpose SSD (gp3)
- Balance of price and performance
- Burstable IOPS
- Suitable for most workloads

**Allocated Storage:** 20 GB
- Minimum for PostgreSQL
- Free Tier includes 20 GB
- Can be increased later without downtime

## Step 1: Create DB Subnet Group

RDS requires a DB subnet group that defines which subnets the database can be deployed in.

### 1.1 Navigate to RDS Console

1. In AWS Console search bar, type "RDS"
2. Click on **RDS** under Services
3. In the left navigation, click **Subnet groups**

![RDS SG Config](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/1.png)

### 1.2 Create DB Subnet Group

1. Click **Create DB subnet group**

**Subnet group details:**

**Name:** `workshop-db-subnet-group`

**Description:** `Subnet group for workshop RDS database`

**VPC:** Select `workshop-backend-vpc`

### 1.3 Add Subnets

**Availability Zones:**
1. Select 2 availability zones that you create the subnets in the VPC setup in the previous [section]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.2-VPC" >}}#12-create-vpc)
    - Select: `ap-southeast-1a` and `ap-southeast-1b`

**Subnets:**
- Select 2 private subnets, each from different Availability Zone:
    - `workshop-backend-subnet-private3-ap-southeast-1a`
    - `workshop-backend-subnet-private4-ap-southeast-1b`
    - ![RDS SG Config](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/3.png)

![DB Subnet Group Subnets](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/2.png)

**Why Two Subnets?**

Even though we're deploying a single-AZ database, RDS requires the subnet group to have at least two subnets in different Availability Zones. This is an AWS requirement that:
- Allows easy migration to Multi-AZ later
- Provides flexibility for read replicas
- Ensures consistent configuration practices

The database will only use one subnet (we'll specify which one during instance creation).


### 1.4 Create Subnet Group

1. Click **Create**
2. You should see success message
3. Subnet group appears in the list with status **Complete**

![DB Subnet Group Created](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/4.png)

## Step 2: Create RDS PostgreSQL Instance

### 2.1 Start Database Creation

1. In RDS console left navigation, click **Databases**
2. Click **Create database**

### 2.2 Choose Database Creation Method

**Database creation method:**
- Select **Full configuration**
    - Provides full configuration options
    - More control than Easy create

![RDS Creation Method](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/5.png)

### 2.3 Engine Options

**Engine type:**
- Select **PostgreSQL**

**Engine Version:**
- Select **PostgreSQL 17.** (or latest available)
- Use default unless you need a specific version

![RDS Engine Options](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/6.png)


**PostgreSQL Version:**

We recommend PostgreSQL 17 or later for this workshop as it includes:
- Better JSON support
- Improved performance
- Enhanced security features

For production, choose an LTS (Long-Term Support) version and test your application thoroughly before upgrading.


### 2.4 Templates

**Templates:**
- Select **Sandbox** (or **Free tier** if applicable)

**Availability and durability**
- Select **Single-AZ DB instance deployment (1 instances)**

![RDS Templates](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/7.png)

{{% notice tip %}}
**Multi-AZ for Production:**<br>
For production workloads, enable Multi-AZ deployment:<br>
Automatic failover to standby instance<br>
Synchronous replication<br>
~1-2 minute failover time<br>
~2x cost of Single-AZ<br>
Change to Multi-AZ later with minimal downtime via database modification.
{{% /notice %}}

### 2.5 Settings

**DB instance identifier:** `workshop-postgres-db`
- This is the name of your RDS instance
- Must be unique in your AWS account per region

**Credentials Settings:**

**Master username:** `postgres`
- Default PostgreSQL admin user
- Cannot be changed after creation

**Credentials management:**
- Select **Managed in AWS Secrets Manager - most secure**
- Under **Select the encryption key**, select **aws/secretmanager(default)**

![RDS Settings](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/8.png)

{{% notice note %}}
AWS Secrets Manager will generate and store our database credentials so that we don't have to hardcode our secrets in
the Lambda function that query our database<br>
{{% /notice %}}

### 2.6 Instance Configuration

**DB instance class:**
- Select **Burstable classes (includes t classes)**
- Select **db.t3.micro**
    - 2 vCPUs, 1 GB RAM
    - Free Tier eligible
    - Sufficient for workshop and small applications

![RDS Instance Class](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/9.png)

### 2.7 Storage

**Storage type:**
- Select **General Purpose SSD (gp3)** (if available)
- Or **General Purpose SSD (gp2)** (Free Tier default)

**Allocated storage:** `20` GB
- Minimum for PostgreSQL
- Free Tier includes 20 GB

**Storage autoscaling:**
- Keep **Enable storage autoscaling** checked
- **Maximum storage threshold:** `100` GB
- Database automatically grows if needed (charged for additional storage)

![RDS Storage](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/10.png)

### 2.8 Connectivity

**Compute resource:**
- Select **Don't connect to an EC2 compute resource**
- We'll manually configure VPC settings

**Network type:**
- Keep **IPv4** selected

**Virtual private cloud (VPC):**
- Select `workshop-backend-vpc`

**DB subnet group:**
- Select `workshop-db-subnet-group`

**Public access:**
- Select **No**
- Database will not be accessible from internet
- Only accessible from within VPC

**VPC security group:**
- Select **Choose existing**
- Remove the default security group
- Select `workshop-rds-sg` (created in Part 1)

**Availability Zone:**
- Select **ap-southeast-1a**
- This matches where `workshop-private-subnet-3` is located

{{% notice info %}}
**Why Specify Availability Zone?**<br>
Although our DB subnet group includes both AZs, we're explicitly choosing **us-east-1b** to ensure the database is created in `workshop-private-subnet-3`. This provides:<br>
Predictable placement for troubleshooting<br>
Same availability zone with our Lambda's subnet
Better organization of resources<br>
{{% /notice %}}

![RDS Connectivity](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/11.png)

### 2.10 Database Authentication

**Database authentication:**
- Keep **Password authentication** selected
- We'll use username/password for simplicity

**Other options (not using in workshop):**
- **Password and IAM database authentication**: More secure, uses IAM roles
- **Password and Kerberos authentication**: For enterprise Active Directory integration

### 2.11 Monitoring

**Database Insights**: select **standard**
- Performance history will be retained for 7 days

**Turn on Performance Insights:**
- Keep **Enabled** for this workshop
- This monitoring option shows the source of database load like SQL queries,
so you can tune SQL statements or increase system resources.

**Enable Enhanced monitoring:**
- Keep **disabled** for this workshop
- Provides OS-level metrics

![RDS Monitoring](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/12.png)

**For production**: Enable both for better observability.

### 2.12 Additional Configuration

Click **Additional configuration** to expand settings.

**Database options:**

**Initial database name:** `workshopdb`
- Creates a database upon instance creation
- If left empty, no database is created (you'd have to create it manually)

**DB parameter group:**
- Keep default: `default.postgres17`

**Option group:**
- Keep default: `default:postgres-17`

![RDS Database Options](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/13.png)

**Backup:**

**Enable automated backups:**
- Keep **enabled**
- Free within retention period

**Backup retention period:** `7` days
- Free Tier includes backups up to DB instance storage size
- Sufficient for development/testing

**Backup window:**
- Select **No preference** (AWS chooses optimal time)
- Or select specific time if you have preferences

**Enable Backup replication:**
- Keep **disabled**
- Replicates backups to another region (additional cost)

![RDS Backup](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/14.png)

**Encryption:**

**Enable encryption:**
- Keep **enabled** (selected by default)
- Uses AWS KMS for encryption at rest
- No additional cost (uses AWS managed key)

**AWS KMS key:**
- Select `(default) aws/rds`
- AWS-managed key, no key management required

{{% notice info %}}
**Encryption Best Practice:**<br>
Always enable encryption for databases containing sensitive data:<br>
Encrypts data at rest<br>
Encrypts automated backups<br>
Encrypts read replicas<br>
Cannot be disabled after creation<br>
Minimal performance impact<br>
  {{% /notice %}}

**Maintenance:**

**Enable auto minor version upgrade:**
- Keep **enabled**
- PostgreSQL minor version updates applied automatically
- Applied during maintenance window
- Recommended for security patches

**Maintenance window:**
- Select **No preference**
- Or choose specific time (e.g., weekends for production)

**Deletion protection:**
- Keep **disabled** for this workshop
- Prevents accidental deletion
- **Enable in production**

### 2.13 Estimate Costs

Before creating, review the estimated monthly cost:
- Located at the bottom right of the page
- **Free Tier estimate**: $0 (within 750 hours/month)
- **Outside Free Tier**: ~$20-25/month for db.t3.micro

### 2.14 Create Database

1. Review all settings
2. Click **Create database**
3. You'll see a success banner

![RDS Creating](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/15.png)

**Database creation time:** 10-15 minutes

**Status progression:**
- Creating → Backing-up → Available

## Step 3: Monitor Database Creation

### 3.1 Check Status

1. Stay in RDS console → Databases
2. Find your database: `workshop-postgres-db`
3. Monitor the **Status** column

![RDS Status](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/16.png)

**Status indicators:**
- **Creating**: Initial provisioning
- **Backing-up**: Initial automated backup
- **Available**: Ready to use  

### 3.2 View Database Details

Once status is **Available**:

1. Click on the database identifier: `workshop-postgres-db`
2. You'll see detailed information

**Important information to note:**

**Endpoint & port:**
- **Endpoint**: `workshop-postgres-db.xxxxxxxxxx.[region].rds.amazonaws.com`
- **Port**: `5432`
- Copy the endpoint - you'll need it for Lambda connections

![RDS Endpoint](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/17.png)

**Connectivity & security:**
- VPC: workshop-backend-vpc
- Subnets: Both private subnets
- Security groups: workshop-rds-sg
- Public accessibility: No

**Configuration:**
- DB instance class: db.t3.micro
- Storage: 20 GB gp3 (or gp2)
- Multi-AZ: No

## RDS Best Practices

### Security

 **Implemented in this workshop:**
- Database in private subnet (no public access)
- Security group restricting access to Lambda only
- Encryption at rest enabled
- Secrets Manager for storing database credentials

 **For production, also implement:**
- IAM database authentication
- Secrets Manager with automatic rotation
- Enhanced monitoring and logging
- Regular security patches (auto minor version upgrade enabled)
- CloudWatch alarms for anomalies

### Performance

 **Implemented:**
- Appropriate instance class for workload
- gp3 storage for balanced performance
- Indexes on commonly queried columns

 **For production, also consider:**
- Read replicas for read-heavy workloads
- Connection pooling (RDS Proxy)
- Query performance insights
- Proper database configuration tuning

### Availability

 **Implemented:**
- Automated backups (7-day retention)
- DB subnet group in multiple AZs

 **For production, also implement:**
- Multi-AZ deployment for automatic failover
- Cross-region read replicas for DR
- Longer backup retention (30 days)
- Backup replication to another region

### Cost Optimization

**Implemented:**
- Free tier (if applicable)
- Right-sized instance (db.t3.micro)
- Single-AZ deployment
- Standard backup retention

 **For production, also consider:**
- Reserved instances for predictable workloads (up to 69% savings)
- Stop database instances during non-business hours (dev/test)
- Storage autoscaling instead of over-provisioning
- Regular review of CloudWatch metrics

## Summary

Congratulations! You've successfully:
-   Created a DB subnet group for RDS deployment
-   Launched an RDS PostgreSQL database instance
-   Configured the database in a private subnet
-   Set up security groups for Lambda access
-   Enabled encryption and automated backups
-   Prepared database schema for application
-   Obtained database endpoint for Lambda connections

## Next Steps

Proceed to **Part 3: AWS Secrets Manager Configuration** to securely store and manage your database credentials.

---

**Ready to continue?** Your database is now ready to receive connections from Lambda functions! 🎉