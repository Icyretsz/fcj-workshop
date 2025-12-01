---
title : "S3 Static Website Hosting"
date :  "2025-09-15" 
weight : 2 
chapter : false
pre : " <b> 5.1.2 </b> "
---

## Overview

In this section, you'll set up Amazon S3 to host your static website. S3 (Simple Storage Service) provides a cost-effective and highly durable solution for hosting static content like HTML, CSS, JavaScript, and images.

**What you'll accomplish:**
- Clone the sample application repository
- Build the frontend application
- Create and configure an S3 bucket
- Upload your website files to S3
- Enable static website hosting
- Test your website

**Estimated time**: 30 minutes

## Costs Considerations
### Free-tier:
- S3 does not have free-tier benefits

### Paid-tier
1. Even paid tier cost is minimal for our workshop
4. Overall: <$0 (clean up immediately after finish workshop)

## Step 1: Prepare Your Application

### 1.1 Clone the Sample Repository

Open your terminal or command prompt and run:
```bash
git clone https://github.com/Icyretsz/fcj-workshop-serverless-frontend.git
```

### 1.2 Install Dependencies

The sample application uses Node.js and npm. Install the required dependencies:
```bash
npm install
```

**Troubleshooting:**
- If you don't have Node.js installed, download it from https://nodejs.org/ (LTS version recommended)
- Verify installation: `node --version` and `npm --version`
- Minimum required versions: Node.js 16.x or higher

### 1.3 Build the Application

Navigate to the root directory and build the production-ready version:
```bash
npm run build
```

**The command will:**
- The build process compiled your source code
- Optimized assets for production (minification, bundling)
- Created a `dist/` directory with all deployable files

### 1.4 Verify Build Output

Check the contents of your build directory:
```bash
ls -la dist/
```

**You should see:**
```
dist/
├── index.html
├── favicon.ico
├── static/
│   ├── css/
│   │   └── main.def456.css
│   └── js/
│       └── main.abc123.js
└── assets/
    └── images/
```

## Step 2: Create an S3 Bucket

### 2.1 Navigate to S3 Console

1. Log in to the AWS Management Console
2. In the search bar at the top, type "S3"
3. Click on **S3** under Services

![S3 Console Navigation]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/1.png)

### 2.2 Create New Bucket

1. Click the **Create bucket** button
2. Configure the following settings:

**General Configuration:**
- **Bucket name**: `workshop-frontend-[your-name]-[random-string]`
    - Example: `workshop-frontend-john-a1b2c3`
    - Must be globally unique across all AWS accounts
    - Use only lowercase letters, numbers, and hyphens
    - **Note**: Write down your bucket name - you'll need it throughout the workshop

![S3 Bucket Creation]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/2.png)

### 2.3 Configure Bucket Settings

**Object Ownership:**
- Keep default: **ACLs disabled (recommended)**

**Block Public Access settings:**
- **UNCHECK** "Block all public access"
- Check the acknowledgment box: "I acknowledge that the current settings might result in this bucket and the objects within becoming public"

{{% notice note %}}
**⚠️ Security Note**: We're making this bucket public for website hosting. In production, you'd use CloudFront to access the bucket privately, which we'll configure in Part 2.
{{% /notice %}}

![S3 Public Access Settings]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/3.png)

**Bucket Versioning:**
- Keep default: **Disable**

**Tags** (Optional but recommended):
- Key: `Project`, Value: `ServerlessWorkshop`
- Key: `Workshop`, Value: `Frontend`

**Default encryption:**
- Keep default: **Server-side encryption with Amazon S3 managed keys (SSE-S3)**

**Advanced settings:**
- Keep all defaults

### 2.4 Create the Bucket

1. Scroll to the bottom and click **Create bucket**
2. You should see a success message: "Successfully created bucket 'workshop-frontend-[your-name]-[random-string]'"

## Step 3: Configure Static Website Hosting

### 3.1 Enable Website Hosting

1. Click on your newly created bucket name
2. Navigate to the **Properties** tab
3. Scroll down to **Static website hosting** section
4. Click **Edit**

![S3 Properties Tab]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/4.png)

### 3.2 Configure Hosting Settings

**Static website hosting:**
- Select **Enable**

**Hosting type:**
- Select **Host a static website**

**Index document:**
- Enter: `index.html`

**Error document** (Optional):
- Enter: `index.html`
    - This allows client-side routing to work properly (for React, Vue, Angular apps)

![S3 Website Hosting Config]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/5.png)

- Click **Save changes**

### 3.3 View website endpoint
1. Scroll back down to **Static website hosting** section
2. **Copy the Bucket website endpoint URL**
    - Example: `http://workshop-frontend-john-a1b2c3.s3-website-us-east-1.amazonaws.com`
    - **Save this URL** - you'll use it to test the website

![S3 Website Endpoint]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/6.png)

## Step 4: Upload Website Files

### 4.1 Upload via AWS Console

1. Navigate to the **Objects** tab in your bucket
2. Click **Upload**

![S3 Upload Button]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/7.png)

### 4.2 Add Files

**Option A: Drag and Drop**
1. Open your file explorer/finder
2. Navigate to your `frontend/dist/` directory
3. Select ALL files and folders inside the build directory
4. Drag them into the upload area

**Option B: Browse Files**
1. Click **Add files** and **Add folder**
2. Navigate to `frontend/dist/`
3. Select all contents

{{% notice warning %}}
**⚠️ Important**: Upload the **contents** of the dist folder, not the dist folder itself. Your S3 bucket root should have `index.html` at the top level.
{{% /notice %}}

![S3 Upload Files]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/8.png)

### 4.3 Configure Upload Settings

**Permissions:**
- Keep defaults (inherited from bucket)

**Properties:**
- Keep defaults

**Storage class:**
- Keep default: **Standard**

### 4.4 Complete Upload

1. Scroll down and click **Upload**
2. Wait for the upload to complete
3. You should see "Upload succeeded" with a list of uploaded files
4. Click **Close**

![S3 Upload Success]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/9.png)

### 4.5 Verify Upload

Back in your bucket, you should now see:
- `index.html`
- `favicon.ico`
- `static/` folder
- `assets/` folder (if applicable)

## Step 5: Configure Bucket Policy for Public Access

### 5.1 Create Bucket Policy

1. Navigate to the **Permissions** tab
2. Scroll down to **Bucket policy** section
3. Click **Edit**

![S3 Permissions Tab]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/10.png)

### 5.2 Add Policy JSON

Copy and paste the following policy, **replacing `YOUR-BUCKET-NAME`** with your actual bucket name:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
        }
    ]
}
```

**Example with actual bucket name:**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::workshop-frontend-thien-bucket/*"
        }
    ]
}
```

**What this policy does:**
- Allows anyone (`"Principal": "*"`) to read (`s3:GetObject`) any object in your bucket
- Required for public website hosting

![S3 Bucket Policy]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/11.png)

### 5.3 Save Policy

- Click **Save changes**

## Step 6: Test Your Website

### 6.1 Access Your Website

1. Use the **Bucket website endpoint** URL you saved earlier
2. Open it in your web browser
3. Example: `http://workshop-frontend-john-a1b2c3.s3-website-us-east-1.amazonaws.com`

**Expected result:**
- The website should load successfully
- You should see the homepage of the application

![Website Success]( /images/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting/12.png)

### 6.2 Test Navigation

1. Click through different pages of your application
2. Verify images and styles load correctly
3. Check browser developer console for any errors (F12 or right-click → Inspect)

### 6.3 Test Direct Object Access

You can also access individual files directly:
- `http://your-bucket-endpoint/index.html`
- `http://your-bucket-endpoint /css/main.def456.css`

## Troubleshooting

### Issue: "403 Forbidden" Error

**Cause**: Bucket policy not configured correctly or bucket not public

**Solution:**
1. Verify bucket policy is correct and saved
2. Check that "Block all public access" is disabled
3. Ensure the Resource ARN in the policy matches your bucket name
4. Clear your browser cache and try again

### Issue: "404 Not Found" Error

**Cause**: File not uploaded or incorrect URL

**Solution:**
1. Verify files are in the bucket root (not in a subfolder)
2. Check that `index.html` exists at the bucket root
3. Verify the website endpoint URL is correct
4. Try accessing `http://your-endpoint/index.html` directly

### Issue: Styles or JavaScript Not Loading

**Cause**: Incorrect content types or file paths

**Solution:**
1. Check browser console for 404 errors
2. Verify file paths in your HTML match the uploaded structure
3. If using CLI, ensure content types are set correctly
4. Check that all files from the build directory were uploaded

### Issue: "Bucket name already exists"

**Cause**: S3 bucket names are globally unique

**Solution:**
1. Choose a different bucket name
2. Add more random characters or your AWS account ID
3. Example: `workshop-frontend-john-20241126-a1b2c3`

## Summary

Congratulations! You've successfully:
-   Cloned and built the sample application
-   Created an S3 bucket
-   Configured static website hosting
-   Uploaded your website files
-   Set up public access with bucket policy
-   Tested the live website

### What You've Deployed

The website is now:
- Hosted on AWS S3
- Publicly accessible via the S3 website endpoint
- Serving static files (HTML, CSS, JavaScript)
- Ready to be integrated with CloudFront in the next section

### Current Limitations

Right now, the website:
-   Only uses HTTP (not HTTPS)
-   Has no CDN/caching for global users
-   Has no DDoS protection or WAF
-   Uses a non-memorable S3 endpoint URL

**In the next sections**, we'll address these by adding CloudFront and WAF.

## Next Steps

Proceed to **Part 2: CloudFront Distribution Setup** to add global content delivery and improve performance.

### Quick Reference

**Your Resources:**
- Bucket Name: `_______________________________`
- Website Endpoint: `_______________________________`
- Region: `_______________________________`

**Useful Commands (AWS CLI):**
```bash
# Update website content
cd frontend
npm run build
aws s3 sync ./build/ s3://YOUR-BUCKET-NAME/ --delete

# List bucket contents
aws s3 ls s3://YOUR-BUCKET-NAME/ --recursive

# Delete all objects (for cleanup)
aws s3 rm s3://YOUR-BUCKET-NAME/ --recursive
```

---

**Ready to continue?** Let's move on to adding CloudFront for global content delivery!