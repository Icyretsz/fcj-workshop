---
title : "Part 4: Clean up"
date :  "2025-09-15" 
weight : 5
chapter : false
pre : " <b> 5.1.5 </b> "
---

## Overview

This section covers how to properly clean up the resources you created during frontend deployment. Cleaning up is important to avoid ongoing AWS charges, especially for resources that have monthly costs like WAF Web ACLs.

**What you'll learn:**
- How to safely delete resources in the correct order
- Understanding resource dependencies
- Cost implications of keeping vs. deleting resources
- How to preserve configuration for future use
- Partial cleanup options

**Estimated time**: 15-20 minutes

## Should You Clean Up?

### Keep Resources If:
- ✅ You're proceeding immediately to Part 2: Serverless Backend
- ✅ You want to maintain the working frontend for reference
- ✅ You're using this for a real project
- ✅ Costs are acceptable for your use case

### Clean Up If:
- ✅ You've completed the workshop and don't need the resources
- ✅ You want to minimize AWS costs
- ✅ You're practicing and will recreate later
- ✅ You're approaching Free Tier limits

## Complete Cleanup (Step-by-Step)

Follow this order to avoid dependency errors:

### Step 1: Disable and Delete CloudFront Distribution

CloudFront distributions must be disabled before deletion.

#### 1.1 Disable Distribution

1. Go to CloudFront console
2. Select your distribution (check the box)
3. Click **Disable**
4. Confirm by clicking **Disable** in the modal

**Status changes:**
- **Deploying**: Distribution is being disabled
- **Deployed**: Disabled successfully

**Wait time**: 5-15 minutes

#### 1.2 Wait for "Deployed" Status

1. Stay on the CloudFront distributions page
2. Refresh periodically (every 2-3 minutes)
3. Wait until **Status** column shows **Deployed**
4. **Last modified** field shows a date

![CloudFront Disabled](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/2.png)

#### 2.3 Delete Distribution

1. Select your disabled distribution (check the box)
2. Click **Delete**
3. Confirm by clicking **Delete** in the modal

**Expected result**: Distribution is removed from list

**Note**: If you upgraded your distribution to Pro, you must wait until the next billing cycle to delete it.

### Step 3: Delete SSL/TLS Certificate (Optional)

Only if you created a custom SSL certificate in ACM for custom domains.

#### 3.1 Check Certificate Usage

Before deleting, verify the certificate isn't used elsewhere:

1. Go to Certificate Manager console
2. Ensure you're in **us-east-1** region
3. Find your certificate
4. Check the **In use?** column

**If "Yes"**: Don't delete (still associated with resources)
**If "No"**: Safe to delete

#### 3.2 Delete Certificate

1. Select your certificate (check the box)
2. Click **Delete**
3. Confirm by clicking **Delete** in the modal

![ACM Delete](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/3.png)

**Expected result**: Certificate is removed from list

{{% notice note %}}
**Free Service:**
ACM certificates are free, so deleting them doesn't save costs. You might want to keep the certificate if:<br>
You'll recreate the distribution later<br>
You use the same domain for other AWS services<br>
Validation took a long time (you'd have to repeat it)<br>
  {{% /notice %}}

### Step 4: Delete S3 Bucket

S3 buckets must be empty before deletion.

#### 4.1 Empty the Bucket

1. Go to S3 console
2. Click on your bucket name: `workshop-frontend-[your-name]-[random]`
3. If there are files, click **Empty**
4. Type `permanently delete` to confirm
5. Click **Empty**

![S3 Empty Bucket](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/4.png)

#### 4.2 Delete the Bucket

1. Go back to the S3 buckets list
2. Select your bucket (check the box)
3. Click **Delete**
4. Type your bucket name to confirm
5. Click **Delete bucket**

### Step 5: Delete CloudWatch Alarms

Only if you created CloudWatch alarms in the optional section.

1. Go to CloudWatch console
2. Click **Alarms** in left navigation
3. Select alarm(s): `WAF-High-Blocked-Requests`
4. Click **Actions** → **Delete**
5. Confirm deletion

![CloudWatch Delete Alarm](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/5.png)

### Step 6: Delete CloudWatch log group

6. Click **Logs** -> **Log groups**
7. Select log groups you created in [Part 3]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF" >}}#91-enable-logging-destination-in-waf)
`aws-waf-logs-workshop1` and select delete

### Step 7: Delete SNS Topics

1. Go to SNS console
2. Click **Topics** in left navigation
3. Select topic: `Default_CloudWatch_Alarms_Topic`
4. Click **Delete**
5. Type `delete me` to confirm
6. Click **Delete**

## Summary

### Cleanup Completion Checklist

**If performing complete cleanup:**
- [ ] CloudFront distribution disabled and deleted
- [ ] SSL certificate deleted (if created)
- [ ] S3 bucket(s) emptied and deleted
- [ ] CloudWatch alarms deleted
- [ ] SNS topics deleted
- [ ] Verified no remaining charges in Billing Dashboard

### What's Next?

If continuing to Part 2: Serverless backend:
- Keep existing resources OR
- Proceed with backend deployment
- Backend will integrate with this frontend infrastructure

If finished with workshop:
- All resources cleaned up
- No ongoing charges
- Knowledge and skills gained! 🎉

**Congratulations!** You've successfully completed Part 1: Frontend Deployment, including proper resource cleanup. You now understand how to deploy, secure, and manage a serverless frontend on AWS! 🎉