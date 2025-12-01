---
title : "CloudFront Distribution Setup"
date :  "2025-09-15" 
weight : 3 
chapter : false
pre : " <b> 5.1.3 </b> "
---

## Overview

In this section, you'll configure Amazon CloudFront as a Content Delivery Network (CDN) in front of your S3 bucket. CloudFront will cache your content at edge locations around the world, providing faster load times for users regardless of their geographic location.

**What you'll accomplish:**
- Create a CloudFront distribution
- Configure your S3 bucket as the origin
- Set up cache behaviors and optimization
- Configure SSL/TLS with HTTPS
- Update S3 bucket to restrict direct access
- Test your CloudFront distribution

**Estimated time**: 45 minutes

## Why CloudFront?

**Benefits over direct S3 access:**
- **Performance**: Content served from edge locations near your users (200+ locations worldwide)
- **Security**: HTTPS support, DDoS protection, and integration with AWS WAF
- **Cost**: Reduced S3 data transfer costs through caching
- **Scalability**: Handles traffic spikes automatically
- **Custom Domains**: Use your own domain name with SSL certificate

## Costs Considerations
### Free-tier:
- $0/month

### Paid-tier
1. $15/month
4. Overall: <$0 or <$1 if Pro (clean up immediately after finish workshop)

## Step 1: Create CloudFront Distribution

### 1.1 Navigate to CloudFront Console

1. In the AWS Console search bar, type "CloudFront"
2. Click on **CloudFront** under Services
3. Click **Create distribution**

![CloudFront Console](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/1.png)

### 1.2 Step 1: Choose a plan
- For this workshop, we will continue with the free plan.
- Click **Next**

![Choose a plan]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/2.png)

### 1.3 Step 2: Get started
- On this step, we will configure the name of the distribution

**Distribution name**:
- Set as: `workshop-frontend-cf`

**Route 53 managed domain (optional)**:
- If you already have a Route 53 managed domain, you can specify it here to use the domain instead of CloudFront's generated domain

- Leave the rest as default
- Click **Next**

![Distribution name]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/3.png)

### 1.4 Step 3: Specify origin

**Origin type:**
- Select **Amazon S3** as origin

**Origin:**
1. Click on the **Browse S3** button
2. On the appeared modal, select the S3 bucket you created earlier in S3 Static Website Hosting tutorial. Then click **Choose**

![S3 origin]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/4.png)

3. You will notice that there will be a warning appear. This is because we have configured our S3 Bucket for static website hosting. You should ignore it as we will disable **S3 static website hosting** in later steps

![S3 origin warning]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/5.png)

{{% notice note %}}
**Understanding the Warning**:<br>
In [S3 Static Website Hosting]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting" >}}#23-configure-bucket-settings),
we configured the bucket for public access to enable **S3 static website hosting**. CloudFront has detected this
configuration. That is why when you click the **Use website endpoint**, the URL in the textbox will appear in this
format: `[your-bucket-name].s3-website-[region].amazonaws.com`, this is called the **S3 website endpoint**.<br>
While **S3 static website hosting** works well for direct access, it requires public bucket permissions that expose
your content to anyone on the internet. A more secure approach is to keep your S3 bucket private and serve content
exclusively through CloudFront using **Origin Access Control (OAC)**. Using OAC requires the origin URL as the **bucket endpoint**,
which has this format: `[your-bucket-name].s3.[region].amazonaws.com`
{{% /notice %}}

**Why OAC is better:**

- S3 bucket remains private (no public access)
- Content only accessible via CloudFront
- Better security posture
- Same functionality with improved protection

We'll implement this secure configuration in the following steps by switching from the S3 website endpoint to OAC,
then disabling public access to the bucket.

4. Confirm that the **S3 origin** URL has this format:
    - Format: `[your-bucket-name].s3.[region].amazonaws.com `
    - Example: `workshop-frontend-thien-bucket.s3.ap-southeast-1.amazonaws.com`

5. **Origin path:** Leave empty

**Settings**:
- Be sure to check **Allow private S3 bucket access to CloudFront - Recommended** as this will allow OAC
- Leave the rest as default

Click **Next**

![Origin settings]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/6.png)

### 1.5 Step 4: Enable security

By default, **Web Application Firewall (WAF)** is enabled. You should leave the settings in this step as they are.
We will configure WAF in more details in later part of this workshop.

Click **Next**

![WAF]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/7.png)

### 1.6 Step 5: Review and create

1. Review your CloudFront settings
2. Click **Create distribution** when you are ready
3. You will be redirected to the results page

**⏱️ Wait Time**: CloudFront distribution deployment takes 5-15 minutes. While waiting, let's proceed with configuring 
our S3 Bucket to support OAC

![Creation successful]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/8.png)

## Step 2: Secure Your S3 Bucket (Restrict Direct Access)

Now that CloudFront is serving your content, let's prevent users from bypassing CloudFront and accessing S3 directly.

### 2.1 Navigate to Your S3 Bucket

1. Go to S3 console
2. Click on your bucket name
3. Go to **Permissions** tab

### 2.2 Block Public Access

1. Scroll to **Block public access (bucket settings)**
2. Click **Edit**
3. **Check** all four options:
    - Block public access to buckets and objects granted through new access control lists (ACLs)
    - Block public access to buckets and objects granted through any access control lists (ACLs)
    - Block public access to buckets and objects granted through new public bucket or access point policies
    - Block public access to buckets and objects granted through any public bucket or access point policies
4. Click **Save changes**
5. Type `confirm` when prompted
6. Click **Confirm**

![S3 Block Public Access]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/9.png)

### 2.3 Update Bucket Policy

1. Scroll to **Bucket policy**
2. You will notice that permissions for CloudFront-only access policy was automatically created. Those permissions allow
CloudFront to access our S3 Bucket

![S3 Block Public Access]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/10.png)

3. We should now remove
   the **PublicReadGetObject** permission we created in
[S3 Static Website Hosting]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting" >}}#52-add-policy-json)

2. Click **Edit**
3. Delete this statement:

```json
 {
  "Sid": "PublicReadGetObject",
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::[your-bucket-name]/*"
}
```

5. Click **Save changes**

![S3 CloudFront Policy]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/11.png)

### 2.4 Disable S3 Static Website Hosting

1. From your Bucket, go to **Properties** tab
2. Scroll down to **Static website hosting** section, click **Edit**
3. Under **Static website hosting**, check **Disable**
4. Click **Save changes**

![Disable S3 website hosting]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/12.png)

With this change, we will no longer be able to access our website via **S3 website endpoint** in
[S3 Static Website Hosting]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting" >}}#33-view-website-endpoint)

### Step 3: View CloudFront Origin Settings (Optional)

If you want, you can view CloudFront's origin settings to better understand how CloudFront connect to our S3 Bucket

1. Go to **CloudFront console** -> **Distributions**
2. Select your newly-created distribution in the list
3. Go to **Origins** tab
4. Under **Origins**, select your origin S3 bucket. Then click **Edit**

![Navigate to Cloudfront origin]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/13.png)

5. In the next screen, you will see the Origin's settings. There are several notable settings:
    - **Origin domain**: your **S3 Bucket Endpoint**
    - **Origin access**: access method to the origin. Here you should see the option **Origin access control settings (recommended)** selected
    - **Origin access control**: you should see an OAC created by CloudFront pre-selected. It should have name follow this format: `oac-[your-bucket-name].s3.[region]-[random-string]`
   ![OAC]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/14.png)

{{% notice note %}}
You may also see the message *"You must allow access to CloudFront using this policy statement. 
Learn more about giving CloudFront permission to access the S3 bucket"*. This message means that you must add
required policy statements in your S3 Bucket for CloudFront to access the S3 bucket you created in S3 Static Website Hosting tutorial to host the
sample website. But as of 26/11/2025, in the CloudFront creation process, particularly [1.4 Step 3: Specify origin](#14-step-3-specify-origin),
we selected **Allow private S3 bucket access to CloudFront - Recommended**. This option will automatically insert required
policy statements for CloudFront to access the S3 bucket, as we 
already saw in [2.3 Update Bucket Policy](#23-update-bucket-policy). So you can ignore this message.
{{% /notice %}}

## Step 3: Test Your CloudFront Distribution

### 3.1 Access via CloudFront Domain

1. Go back to your Distribution's page, on the top panel you will see your website's endpoint under 
**Distribution domain name**. You can access this endpoint to go to you website

![Domain name]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/16.png)

2. Open a new browser tab
3. Navigate to: `https://[your-cloudfront-domain].cloudfront.net`
    - Example: `https://d1234abcd.cloudfront.net`
4. **Note**: Use HTTPS, not HTTP

**Expected result:**
- Your website loads successfully
- Browser shows connection is secure (HTTPS)
- Content loads from CloudFront, not S3 directly

![Website via CloudFront]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/17.png)

### 3.2 Verify Direct S3 Access is Blocked

Try accessing your old S3 website endpoint:
- Example: `http://workshop-frontend-john-a1b2c3.s3-website-us-east-1.amazonaws.com`

**Expected result:**
- **404 Not Found** error
- This confirms S3 is now protected, the **S3 Website Endpoint** is no longer exists and our website is now only accessible via CloudFront

![S3 Access Blocked]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/18.png)

### 3.3 Test HTTPS Redirect

1. Try accessing with HTTP: `http://[your-cloudfront-domain].cloudfront.net`

**Expected result:**
- Automatically redirects to HTTPS
- Browser URL changes to `https://...`

### 3.4 Check Response Headers

1. Open browser Developer Tools (F12)
2. Go to **Network** tab
3. Refresh the page
4. Click on the first request (usually the document)
5. Look at **Response Headers**

**You should see:**
- `x-amz-cf-id`: CloudFront request ID
- `x-cache`: Shows cache status (Miss from cloudfront, Hit from cloudfront, etc.)
- `age`: Time in seconds the object has been in the cache

![CloudFront Headers]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/19.png)

### 3.5 Test From Different Locations (Optional)

Use an online tool to test your site from multiple global locations:

**Tools:**
- https://www.webpagetest.org/
- https://tools.pingdom.com/
- https://www.dotcom-tools.com/website-speed-test

**What to look for:**
- Fast load times from various geographic locations
- CloudFront serving from nearby edge locations

## Step 4: Configure Custom Domain with Route 53 (Optional)

**Skip if you're not using a custom domain.**

### 4.1 Update DNS Records

**If the domain name is purchased from an external DNS provider:**

1. Go to Route 53 console
2. Click on your hosted zone
3. Click **Create hosted zone**
4. Configure:
    - **Domain name**: your domain name
    - **Type**: Public hosted zone
    - Click **Create hosted zone**

![Route 53 Alias]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/21.png)

5. You will be redirected to the hosted zone page. Under Records, note the NS type nameservers (total 4 of them)

![Route 53 Nameservers]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/22.png)

6. Go to your external DNS provider. Find the **Nameservers** settings, then add 4 nameservers you got from Route 53 earlier
7. Wait up to 24 hours for changes to propagate. In the meantime, let's set up CloudFront to use the custom domain
8. Go to your newly-created distribution page, under **Alternate domain names**, click **Add domain**
9. Under **Domains to serve**, input the domain name that you purchased. Click **Next**

![Route 53 Nameservers]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/23.png)

10. On the next screen, click on **Create certificate**. A new SSL certificate will be created for you

![Create SSL cert]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/24.png)
![SSL created]( /images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/25.png)

11. On the next screen, review changes then click **Add domains**
12. Now you can access the sample website using the custom domain

### 4.2 Test Custom Domain

1. Wait 5-10 minutes for DNS propagation
2. Navigate to: `https://www.yourdomain.com`

**Expected result:**
- Your website loads via your custom domain
- HTTPS works with your SSL certificate
- No certificate warnings

### 4.3 Verify DNS Propagation

Use a DNS checker tool:
- https://dnschecker.org/
- Enter your domain name
- Check that it resolves to your CloudFront distribution

## Step 5: Cache Invalidation

When you update your website, CloudFront caches the old version. Learn how to clear the cache.

### 5.1 Create an Invalidation

1. Go to CloudFront console
2. Click on your distribution ID
3. Go to **Invalidations** tab
4. Click **Create invalidation**

### 5.2 Specify Paths to Invalidate

**Object paths:**

For all files:
```
/*
```

For specific files:
```
/index.html
 /css/*
 /js/*
```

For a single file:
```
/index.html
```

5. Click **Create invalidation**

**Note**: Invalidations usually complete in 1-2 minutes

### 5.3 Invalidation Costs

- First 1,000 invalidation paths per month: **FREE**
- After that: $0.005 per path

**Best practices:**
- Use versioned filenames (e.g., `main.abc123.js`) to avoid frequent invalidations
- Invalidate only specific files when possible
- For complete rebuilds, `/*` is acceptable

## Step 6: Performance Verification

### 6.1 Test Loading Speed

1. Open your website in an incognito/private window
2. Open Developer Tools (F12)
3. Go to **Network** tab
4. Refresh the page
5. Check the **DOMContentLoaded** and **Load** times at the bottom

**Good benchmarks:**
- DOMContentLoaded: < 1 second
- Full page load: < 2 seconds
- First Contentful Paint: < 1 second

![Performance Metrics](images/performance-metrics.png)

### 6.2 Check CloudFront Cache Hit Ratio

1. Go to CloudFront console
2. Click on your distribution
3. Go to **Monitoring** tab
4. Check the **Cache hit rate** graph

**Target**: 80%+ cache hit rate after initial traffic

![Cache Hit Rate](images/cache-hit-rate.png)

### 6.3 Verify Compression

1. In Developer Tools Network tab
2. Click on a CSS or JS file request
3. Look at Response Headers
4. Verify `content-encoding: gzip` or `content-encoding: br` (brotli)

**File size comparison:**
- Without compression: ~100 KB
- With compression: ~25 KB (75% reduction)

## Troubleshooting

### Issue: CloudFront serves old/cached content after update

**Solution:**
1. Create a cache invalidation for affected paths
2. Or, implement cache-busting with versioned filenames
3. Verify your build process generates unique filenames

### Issue: "The request could not be satisfied" error

**Causes:**
- CloudFront can't reach S3 origin
- Origin configuration incorrect
- S3 bucket policy blocking CloudFront

**Solution:**
1. Check CloudFront origin settings point to correct S3 endpoint
2. Verify S3 bucket policy allows CloudFront access
3. Ensure Origin Access Control is configured correctly
4. Check S3 bucket exists and has content

### Issue: SSL certificate not showing in CloudFront

**Solution:**
1. Verify certificate is in **us-east-1** region
2. Check certificate status is **Issued** (not Pending)
3. Wait a few minutes and refresh the CloudFront page
4. Ensure certificate covers the domains in CNAME settings

### Issue: Custom domain not working

**Solution:**
1. Verify DNS records are correct (CNAME pointing to CloudFront)
2. Check DNS propagation with dnschecker.org
3. Ensure SSL certificate includes your custom domain
4. Wait up to 48 hours for full DNS propagation (usually much faster)

### Issue: "Access Denied" when accessing via CloudFront

**Solution:**
1. Check S3 bucket policy includes correct distribution ARN
2. Verify Origin Access Control is created and associated
3. Ensure bucket policy allows CloudFront service principal
4. Try creating a new cache invalidation

### Issue: Slow initial load, then fast subsequent loads

**This is expected behavior:**
- First request: Cache miss, CloudFront fetches from S3 (slower)
- Subsequent requests: Cache hit, served from edge (fast)
- This is normal and improves with more traffic

## Summary

Congratulations! You've successfully:
-  Created a CloudFront distribution
-  Configured S3 as the origin
-  Enabled HTTPS with SSL/TLS
-  Secured S3 to only allow CloudFront access
-  (Optional) Set up a custom domain
-  Tested cache performance
-  Learned cache invalidation

### What You've Achieved

Your website now has:
- **Global Distribution**: Served from 200+ edge locations worldwide
- **HTTPS Security**: Encrypted traffic with SSL/TLS
- **Better Performance**: Reduced latency through caching
- **DDoS Protection**: Built-in AWS Shield Standard
- **Cost Optimization**: Reduced S3 data transfer costs
- **Scalability**: Automatic handling of traffic spikes

### Architecture So Far
```
Internet Users
      ↓
CloudFront (HTTPS)
      ↓
S3 Bucket (Private)
```

### What's Next

In Part 3, we'll configure AWS WAF to protect your application from:
- SQL injection attacks
- Cross-site scripting (XSS)
- Bot traffic and scraping
- Geographic restrictions
- Rate limiting

**Useful AWS CLI Commands:**
```bash
# Invalidate entire cache
aws cloudfront create-invalidation \
  --distribution-id YOUR-DISTRIBUTION-ID \
  --paths "/*"

# Invalidate specific paths
aws cloudfront create-invalidation \
  --distribution-id YOUR-DISTRIBUTION-ID \
  --paths "/index.html" " /css/*"

# Get distribution status
aws cloudfront get-distribution \
  --id YOUR-DISTRIBUTION-ID \
  --query "Distribution.Status"

# Update website and invalidate
cd frontend
npm run build
aws s3 sync ./build/ s3://YOUR-BUCKET-NAME/ --delete
aws cloudfront create-invalidation \
  --distribution-id YOUR-DISTRIBUTION-ID \
  --paths "/*"
```

---

**Ready to continue?** Let's proceed to **Part 3: AWS WAF Configuration** to add security protection!