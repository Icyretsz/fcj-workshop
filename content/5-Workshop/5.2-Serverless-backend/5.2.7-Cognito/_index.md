---
title : "Amazon Cognito Configuration"
date :  "2025-09-15" 
weight : 7 
chapter : false
pre : " <b> 5.2.7 </b> "
---

## Overview

In this section, you'll configure Amazon Cognito to handle user authentication and authorization for your serverless application. Cognito will manage user sign-up, sign-in, password recovery, and generate JWT tokens that secure your API endpoints.

**What you'll accomplish:**
- Understand Amazon Cognito concepts and architecture
- Create a Cognito User Pool for user management
- Configure user attributes and password policies
- Set up app client for your application
- Test user registration and authentication flows
- Integrate Cognito with API Gateway as an authorizer
- Secure API endpoints with JWT token validation
- Test authenticated API calls

**Estimated time**: 50-60 minutes

## Cognito Architecture

### What We'll Build
```
User (Browser/Mobile)
    ↓
Sign Up / Sign In
    ↓
Amazon Cognito User Pool
    ├── User Management (sign-up, sign-in, verification)
    ├── Token Generation (ID Token, Access Token, Refresh Token)
    └── User Attributes (email, phone, custom attributes)
    ↓
JWT Token (ID Token)
    ↓
API Gateway (with Cognito Authorizer)
    ├── Validates JWT Token
    ├── Extracts user claims (sub, email, etc.)
    └── Passes to Lambda if valid
    ↓
Lambda Function (workshop-lambda-sm-rds)
    ├── Receives user info from authorizer
    └── Performs authorized operations
    ↓
RDS PostgreSQL
```

## Costs Considerations
- Free for up to 10,000 monthly active users (MAUs)
- Overall: $0 (assume immediate clean up after workshop)

### Cognito Concepts

**User Pool:**
- Directory of users for your application
- Handles authentication (sign-up, sign-in)
- Manages user attributes and profiles
- Issues JWT tokens after successful authentication

**App Client:**
- Configuration for how your app connects to user pool
- Defines authentication flows
- Controls token expiration times

**JWT Tokens:**
- **ID Token**: Contains user identity and attributes (used for API authorization)
- **Access Token**: Used to access Cognito user pool APIs
- **Refresh Token**: Used to get new ID/Access tokens without re-authentication

**Hosted UI:**
- Pre-built sign-up/sign-in pages
- Customizable branding
- Handles OAuth 2.0 flows
- Alternative to building custom auth UI

**Cognito Authorizer:**
- API Gateway feature
- Validates JWT tokens automatically
- Denies requests with invalid/expired tokens
- Passes user claims to Lambda

## Step 1: Create Cognito User Pool

### 1.1 Navigate to Cognito Console

1. Go to AWS Console
2. Search for "Cognito"
3. Click **Amazon Cognito** under Services

![Cognito Console](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/1.png)

![Cognito Page](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/2.png)

### 1.2 Create User directory

1. Click **Get started for free in less than five minutes**

You'll go through a step-by-step configuration wizard.

## Cognito user directory setup

### 1.3 Define your application

1. **Application type**
- Since we use Cognito to authenticate users in the frontend we deployed in Part 1, choose **Single-page application (SPA)**

2. **Name you application**
- Enter name: `workshop-cognito-SPA`

![Define application](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/3.png)

3. Click **Next**

### 1.4 Configure options

1. **Options for sign-in identifiers**
- The required attribute for user to log-in. Select **Email**

2. **Self-registration**
- **Enabled** as default
- This option allows users to initiate sign-up by themselves. If chose **Disable**, only admin that has access to Cognito can create new account

3. **Required attributes for sign-up**
- Select **name** and **phone_number**
- These are the additional required attributes when sign-up

![Configure options](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/4.png)

### 1.5 Add a return URL (optional)
- When specified a return URL, our application will redirect user back to said URL after successful log-in.
- Left blank for now. We will configure this later when we integrate our front-end app

5. Click **Create user directory**

### 1.6 View resources
After you click **Create user directory** in the previous step, you will be redirected to another page

On this page, you can view:
- Built-in log-in and sign-up page by clicking **View login page**
- Quick setup guide
  - This provides developers guidance on how to integrate authentication in Front-end apps
  
We will leave this page for now and comeback later when we integrate our front-end

![Configure options](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/5.png)

Click **Go to overview** at the bottom of the page

### 1.7 View User Pool Details

After creation, you'll see your user pool overview.

**Save these important details:**

**User pool ID:**
- Format: `[region]_abcd1234`

**User pool ARN:**
- Format: `arn:aws:cognito-idp:us-east-1:123456789012:userpool/[region]_abcd1234`

![Cognito Pool Details](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/6.png)

**Get App client ID:**
1. On the left sidebar, click **App clients** under **Applications**
2. Copy the **Client ID**
    - Format: `1234567890abcdefghijklmnop`

![Cognito App Client ID](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/7.png)

3. In the middle, you can see
- **Authentication flow session duration**: the maximum duration when user initiate authentication
- **Refresh token expiration**: duration before refresh token expires
- **Access token expiration**: duration before access token is expired and user is required (or not, based on settings) to log-in again
- To edit these durations and more, click **Edit** in the same card in the right

4. Edit app client information
- **Authentication flow**: authentication flows that your app will support
  - **Choice-based sign-in: ALLOW_USER_AUTH**: allows multiple sign-in options for users (biometric, OPT...)
  - **Sign in with secure remote password (SRP): ALLOW_USER_SRP_AUTH**: sign-in with username and password
  - **Get new user tokens from existing authenticated sessions: ALLOW_REFRESH_TOKEN_AUTH**: when access token expires, refresh token are used to extend and refetch new access token without the user have to initiate login again
- **Duration and expiration times**: you can also modify duration and expiration times here

5. You can also view some notable configurations on the app client page
  - **Quick setup guilde**: provides developers guidance on how to integrate Cognito to Front-end apps
  - **Attribute permissions**: view and edit attribute permissions that this app client can read and write
    - You can see that **email** is not writeable since we chose **email** as the required sign-up and sign-in attribute
  - **Login pages**: customize  login and logout behaviors
  - **Threat protection**: Configure threat protection for adaptive authentication and compromised credentials.
  - **Analytics**

## Step 2: Test User Registration

### 2.1 Access Hosted UI

1. In Cognito console, go to your user pool
2. On the left sidebar, click **App clients** under **Applications**
3. Click **View login page**

This opens the Cognito-hosted authentication page in a new tab.

![Cognito View Hosted UI](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/8.png)

### 2.2 Sign Up a New User

1. On the Hosted UI page, click **Create an account**

2. Fill in the registration form:

**Email:** Your actual email address
- You'll receive a verification code here

**Name:** Your full name (alphanumeric, case-sensitive)

**Phone number:** `+1234567890`
- Format: `+[country code][number]`
- Example: `+84 0123456789` for Vietnam

**Password:**
- Must meet password policy:
    - At least 8 characters
    - Uppercase letter
    - Lowercase letter
    - Number
    - Special character

**Confirm password** 

![Cognito Sign Up Form](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/9.png)

3. Click **Sign up**

### 3.3 Verify Email Address

1. Check your email inbox
2. You should receive an email from `no-reply@verificationemail.com`
3. **Subject:** "Your verification code"
4. **Body contains:** 6-digit verification code
    - Example: `123456`

5. Go back to the login page
6. Enter the 6-digit code in the **Confirmation code** field
7. Click **Confirm Account**

![Cognito Verification Email](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/11.png)


**Didn't receive the email?**

1. Check spam/junk folder
2. Wait 2-3 minutes (can be delayed)
3. Click "Resend code" on the verification page
4. Verify email address is correct
5. Check Cognito email sending quota (50/day limit)

If still not working:
- Go to Cognito console → Users
- Find your user (status: UNCONFIRMED)
- Click Actions → Confirm account (admin override)

After successful verification, you'll be redirected to the return URL page.

![Cognito Sign In Page](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/12.png)

We will setup return URL later to redirect user to our front-end homepage

### 3.4 Login

1. Close the browser page
2. Go back to our app client page, then click **View login page** again
3. You will see the folowing
   - This is because you have previously signed-in after sign-up
   - After a successful sign-in, Cognito will store httpOnly cookies containing access token and refresh token
   - If those are not expired yet, you won't have to initiate login again (input email and password)
4. Click **Sign in as [your email]**, you will be redirected to the return URL
5. If you want to input email and password, simply click **Sign in as a different user?**

![Cognito Sign In Page](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/13.png)

## Step 4: View Users in Cognito Console

### 4.1 View User List

1. In Cognito console, select user pool
2. Click **Users** in left navigation
3. You should see your newly created user

![Cognito Users List](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/14.png)

### 4.2 View User Details

1. Click on the username: `testuser`
2. View detailed information:

**User attributes:**
- `sub`: UUID (unique identifier)
- `email`: Your email
- `email_verified`: true
- `name`: Your name
- `phone_number`: +1234567890
- `phone_number_verified`: false (we didn't verify phone)

![Cognito User Details](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/15.png)

**Group memberships:**
- None (we haven't created groups yet)

## Step 5: View Authentication Tokens

We will view how an access token looks like via CloudShell

### 5.1 Get access token via CloudShell

1. In the bottom right corner, look for CloudShell

![Cognito User Details](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/18.png)

2. When click on **CloudShell**, a drawer contain a CLI appear from bottom

![CloudShell](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/19.png)

3. Create a json file on your local machine with the following content:

```JSON
{
    "UserPoolId": "{your-user-pool-id}",
    "ClientId": "{your-client-id}",
    "AuthFlow": "ADMIN_NO_SRP_AUTH",
    "AuthParameters": {
        "USERNAME": "admin@example.com",
        "PASSWORD": "password123"
    }
}
```

For example

![json](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/20.png)

- **UserPoolId** can be found in your user pool page
- **ClientId** can be found in your app client page
- **username** and **password** of your new account you just created

4. Save the json file as `auth.json`
5. Back to your **CloudShell** CLI, click **Action** on the right, click **Upload file**

![upload](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/21.png)

6. Select your `auth.json` file to upload
7. Then in the CLI, run `ls`. You will see your `auth.json` file

![json in CLI](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/22.png)

8. Run the following command
`aws cognito-idp admin-initiate-auth --region {your-aws-region} --cli-input-json file://auth.json`
9. If you encounter this error `An error occurred (InvalidParameterException) when calling the AdminInitiateAuth operation: Auth flow not enabled for this client`
   - Go to your app client page
   - In **App client information** card, click **Edit**
   - ![app client card](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/23.png)
   - Under **Authentication flows**, check **Sign in with server-side administrative credentials: ALLOW_ADMIN_USER_PASSWORD_AUTH**
   - Save changes

10. Run the command again, you will have the following response

```JSON
{
    "ChallengeParameters": {},
    "AuthenticationResult": {
        "AccessToken": "eyJraWQiOiJTNVwvTmlNSnorR0VQUzlVNis2dlBjRCttQ2tLZDNOdFFCcEp1NEhRbXhoUT0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IiLCJpc3MiOiJodHRwczpcL1wvY29nbml0by1pZHAuYXAtc291dGhlYXN0LTEuYW1hem9uYXdzLmNvbVwvYXAtc291dGhlYXN0LTFfVHV1dGRSVExkIiwiY2xpZW50X2lkIjoiNTZtc2RjdHMwcjJ1YWhrdDZjMzBsdWxiZWgiLCJvcmlnaW5fanRpIjoiNWNmOWFiMWUtNDdjZC00MzZlLTk2OWYtZmU5ZDE3YTgxOGNhIiwiZXZlbnRfaWQiOiJkMTM5Y2Y3Zi02YmE3LTQzYzQtYjNiZS0xYWJmMmEyNjNkMzkiLCJ0b2tlbl91c2UiOiJhY2Nlc3MiLCJzY29wZSI6ImF3cy5jb2duaXRvLnNpZ25pbi51c2VyLmFkbWluIiwiYXV0aF90aW1lIjoxNzY0NDg3MDI1LCJleHAiOjE3NjQ0OTA2MjUsImlhdCI6MTc2NDQ4NzAyNSwianRpIjoiNGZhN2RkZjctZmJiYS00MjM3LWE2MzctYTkyMWE1NDZlODlmIiwidXNlcm5hbWUiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IifQ.bUHsq_dNyxJ2GBARcP5IRY8Qi-AZbpTItD_BkH4uy7b-BYLZxNx03peI68DFmhd2mDpI_exw6DigB2I6hFGeQ2VbUlH0kKxvA0gDXqQNj4bnDnU2KCIzPbj_pIydab_tjxuU6FQi4l5tTxM6ygKciIUowqX3GLgSvyXPWprZzZtQoYgyMETT-rae4EWBhUoT1Em76YAvlXwqxHp0RJi8vwABWaB6Q34buCO1wM4SWzs5ZNfLSy28FK0xLEdGuAXwtXRnu2Myn_wmcePK_K-kiFi7aMX4wEB4Vl7zoPqUhgzI2unSLBCDNHX-svTSRy6sk9IaXZkzp_TbH4bfflFLPA",
        "ExpiresIn": 3600,
        "TokenType": "Bearer",
        "RefreshToken": "eyJjdHkiOiJKV1QiLCJlbmMiOiJBMjU2R0NNIiwiYWxnIjoiUlNBLU9BRVAifQ.pHOj9o4WG_bH5rw2E2tF5Oxpx4WbVEOMZb2oVduNH6Wxsl8NE_UfAhS6IVi6rcaGZkdgC1e1DX8Z-zqSwrequpT7eIKsUeb5WDmcu4my1RVfx2vrmmKKCuyHs3fCG8eyopjAe2-9vKnVJI6T-nr_Sg9YJ54EzWie8sHO2_pdLMOkcNgxn4Hhv8f8J0PsrIyovaI62O6uomTul1lMqUQ3Q4AHTRwROZEVqCQlzEYmMnsFBUbVTzHuA0hbEy4v8hRx8jqLjNwcvOf9qqYgyusZt0wiErnMGR11p00AsOgIcJ6A_484DoH4TPu2eqAy7UIZF5g-ak66iTncSedMQermXw.uA8Bb7gC4JfnFcCx.oFSb-D6NM1q2KbP8MGDqcnDOq8OD921MSb-oyirVPIu1taU-iPizZ08IvdGSc_kV-bRSep2-vmD-jKQePXNI4aZ0S6YxTV09XwLJlRkU8DMeiH5Z1LY8_ZMf2KBNCHpfAONb3l3_xQj3hHGgIpc2W2lHX-e9YaKadDKSlU4BTs5P2BOrTq4fkVGRfjp5sC_FlcsSOULCzgM8afHQTCnONzNmMrGHn6k9famKFFDUVX7W9tTI5YwndJ9eW_vU0ZYIETIP_dF4X0DateJb108NtgLfRs5U8eerBVCUh9RnExR9tuPSzqqP0nDCD9JN1QQE_Z-zmQFngVsEvL040d19serbQ7Fjyikzmm2udX5SHgYtQlGWHaFjp3hOQKotJhlFpYR-CmC9StB2BzLl2fA2Wol5FIi0VhrnWOOmnxlgN1m7k1kLAWC7S_hieJgbCw4kSpeG7QLk_9BYllT3dNKjgzyBS7ppYYAaRk1wXtm7f52Yf8nwD0qO-9aksa6rQFRJ5xzYUfdNyZwLbJvHDcTfyxedgcUGEGtRf-wAU7bxmzmKiFtnFr4nrTNsgkfWoDoepc23PzuQqsHZ_J7AYZHzBJmrlxz5NhYG9s6c7WkeEXTogXly7-hJdDen8NFXgEZOW9Aed1W6aQNjdzd726WIVNT_r5UcgvlAQs5y43tiLnsMjKmGpwW7PuWC8VGG4w1UhWCUH_5jZjgAuI4PDeZF2a7w3-O3jDsZhs4u0yBeDxFXtx_NESDA8Uvh_j5IAip8Gm_aO_pACelyGwhKb9aWVaQtixI7GBRLIwY8RptjnRVaLKxkGMsqfvYSOZHugQIeMCADoVMkl1COHv7bHy4289JGGpIPYVOfer-TIS6NzHf3xCqxNWSn0cBuYznJTrzjpwIv6L5t302WleAH5EvF-gyEfNU7oeiZp4q4-e11kppLoP6yFg4LOA8BxPiJBsDMA0qIn3MyfFG7UQmChLxOZyMkQCEWgTQey-WMtwKqAb3En_aoCfW_-IN2b9v0pdwq86B7NTVtrxGPLbhdFmVpF42kPxYTOasFrWymN6QeDgy4DBMcqKpBf3Tz3wGeSYZfsgD8AjyhaE6N56uARAIM23i-eVaNvd-b8-SSG5IOSC0z1kg9nARQmaC89Q-F9tsFeLQfQOGgIPbc9x24VeNAaUBE9TqtKHKofLX2Ea1xsotRK8bliZn1vceLJgrH1ixueFiqVo7UPIuKOw7_v390p1Rg0Jh8CL0iA2uC9jSTfT09-M_wkVmROZPh8Ac4hNlWe-p-N5061f956fxnoGuqcQ_m0fmW0s4K45N94Gevs1nB3JrVTB-llS3fGE5vPR_hB8qTwbJx0-ev.4IY2Y2G57kBAuuYXqaNucA",
        "IdToken": "eyJraWQiOiJWdjVBZXc1bHpZQUNvdWxaNVwvN3ZPSEEwUXBycTB3Sm1peXZQaFI1bVBYbz0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IiLCJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwiaXNzIjoiaHR0cHM6XC9cL2NvZ25pdG8taWRwLmFwLXNvdXRoZWFzdC0xLmFtYXpvbmF3cy5jb21cL2FwLXNvdXRoZWFzdC0xX1R1dXRkUlRMZCIsInBob25lX251bWJlcl92ZXJpZmllZCI6ZmFsc2UsImNvZ25pdG86dXNlcm5hbWUiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IiLCJvcmlnaW5fanRpIjoiNWNmOWFiMWUtNDdjZC00MzZlLTk2OWYtZmU5ZDE3YTgxOGNhIiwiYXVkIjoiNTZtc2RjdHMwcjJ1YWhrdDZjMzBsdWxiZWgiLCJldmVudF9pZCI6ImQxMzljZjdmLTZiYTctNDNjNC1iM2JlLTFhYmYyYTI2M2QzOSIsInRva2VuX3VzZSI6ImlkIiwiYXV0aF90aW1lIjoxNzY0NDg3MDI1LCJuYW1lIjoiVHJhbiBNaW5oIFRoaWVuIiwicGhvbmVfbnVtYmVyIjoiKzg0MDc4MzQ3NjM0MSIsImV4cCI6MTc2NDQ5MDYyNSwiaWF0IjoxNzY0NDg3MDI1LCJqdGkiOiIyODY4NjgwNy03YTg0LTQ5MDctOTE3OC00YTNhMDZiYjM3NzAiLCJlbWFpbCI6InRoaWVuLnRtMjcyN0BnbWFpbC5jb20ifQ.rplRmMWbQFNyVXswjDecit4hbg3niMgxti0Xr6uFgjy0NBScI1t_vcd2M2N262KWnQzzki-ovYE8YKA14RUAdbz5lo9Xllqn0OHxTqWSTQnjqf4tYT7_SjWbH3_bSgarAzxT89-sK0dAMfZZYPrGfbRZeptCWWwyJxeEtffk09Pc7p8FAxszTFKro9pTnmlHYMUjADRW6ULfzdI7wNoBsRo1MNtIjXfxq9rtVPeVNQ4MvFraRzP9Fv9oZ9H_hgH1mC3XRXYyy5YyraY4bPFxBqLMrhTHRmhMrkUB8HRuOOiaYRmsf40aHp3qpsSpR0zy7WkiUE8W6FahBCgXoTSY5g"
    }
}
```

11. Note your **AccessToken** and **IdToken**, we will use this to authorize

### 5.2 Decode JWT Token (Understanding What's Inside)

To understand what information is in the token:

1. Go to https://jwt.io/
2. Paste your **AccessToken** in the **Encoded value** box (left side)
3. View the decoded **Payload** (right side):
```json
{
  "sub": "893a75fc-60f1-7096-5bbb-f43e009f2b3b",
  "iss": "https://cognito-idp.ap-southeast-1.amazonaws.com/ap-southeast-1_TuutdRTLd",
  "client_id": "56msdcts0r2uahkt6c30lulbeh",
  "origin_jti": "5cf9ab1e-47cd-436e-969f-fe9d17a818ca",
  "event_id": "d139cf7f-6ba7-43c4-b3be-1abf2a263d39",
  "token_use": "access",
  "scope": "aws.cognito.signin.user.admin",
  "auth_time": 1764487025,
  "exp": 1764490625,
  "iat": 1764487025,
  "jti": "4fa7ddf7-fbba-4237-a637-a921a546e89f",
  "username": "893a75fc-60f1-7096-5bbb-f43e009f2b3b"
}
```
4. Paste your **IdToken**
```JSON
{
  "sub": "893a75fc-60f1-7096-5bbb-f43e009f2b3b",
  "email_verified": true,
  "iss": "https://cognito-idp.ap-southeast-1.amazonaws.com/ap-southeast-1_TuutdRTLd",
  "phone_number_verified": false,
  "cognito:username": "893a75fc-60f1-7096-5bbb-f43e009f2b3b",
  "origin_jti": "5cf9ab1e-47cd-436e-969f-fe9d17a818ca",
  "aud": "56msdcts0r2uahkt6c30lulbeh",
  "event_id": "d139cf7f-6ba7-43c4-b3be-1abf2a263d39",
  "token_use": "id",
  "auth_time": 1764487025,
  "name": "Tran Minh Thien",
  "phone_number": "+840783476341",
  "exp": 1764490625,
  "iat": 1764487025,
  "jti": "28686807-7a84-4907-9178-4a3a06bb3770",
  "email": "Your email here"
}
```

![JWT decode](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/26.png)

5. You can see information of your user

**Key claims explained:**
- `sub`: User's unique identifier (UUID) - **use this as `cognito_sub` in your database**
- `email`: User's email address
- `email_verified`: Email verification status (true/false)
- `name`: User's full name
- `cognito:username`: Username chosen during sign-up
- `phone_number`: User's phone number
- `exp`: Expiration time (Unix timestamp) - token expires after this time
- `iat`: Issued at time (Unix timestamp) - when token was created
- `aud`: Audience (your app client ID)
- `iss`: Issuer (Cognito user pool URL)

This information will be available to your Lambda function when requests are authenticated.

## Step 6: Integrate Cognito with API Gateway

Now let's secure your API by requiring valid Cognito JWT tokens.

**How it works:**
- After signed-in, access token will be stored as httpOnly cookies
- When initiate API calls, we will send this access token in the request header
- API Gateway will verify this access token to authorize users

### 6.1 Create Cognito Authorizer in API Gateway

1. Go to API Gateway console
2. Select your API: `workshop-user-api`
3. Click **Authorizers** in left navigation
4. Click **Create an authorizer**

![API Gateway Authorizers](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/16.png)

**Create Authorizer:**

**Name:** `CognitoUserPoolAuthorizer`

**Type:** Select **Cognito**

**Cognito User Pool:**
- Click in the field
- Select your region
- Select your user pool: `workshop-user-pool`

**Token Source:** `Authorization`
- This is the HTTP header where the token will be sent
- Format: `Authorization: Bearer <id_token>`

**Token Validation:**
- Leave blank (optional regex to validate token format)

![API Gateway Authorizer Settings](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/17.png)

5. Click **Create authorizer**

### 6.2 Test the Authorizer

Before applying to methods, let's test it works:

1. In the authorizer page
2. In **Token value** field, paste your ID token from Step 5
    - The full JWT string starting with `eyJ...`
3. Click **Test authorizer**

**Expected result:**

You will see the same information as the jwt decoder from above

![API Gateway Test Authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/27.png)

**Success!** The authorizer validated your token and extracted the claims.

These claims will be passed to your Lambda function in `event.requestContext.authorizer.claims`.


**Test Failed?**

**Error: "Unauthorized"**

**Causes:**
- Token expired (tokens last 1 hour)
- Wrong user pool selected
- Token from different user pool
- Malformed token (check you copied the entire string)

**Solution:**
1. Run token fetching command again to get fresh token
2. Verify user pool ID matches
3. Ensure you copied the complete token (no spaces, no line breaks)
4. Check token hasn't expired (decode at jwt.io and check `exp` claim)

### 5.3 Apply Authorizer to API Methods

Now secure your API endpoints with the authorizer.

**Secure GET /users:**

1. Click **Resources** in left navigation
2. Expand `/users` resource
3. Click **GET** method
4. Click **Method Request** tab

5. Click the **Edit** button in the **Method request settings** card

![API Gateway edit authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/28.png)

6. Select **CognitoUserPoolAuthorizer** from the dropdown
7. You'll see "Authorization: CognitoUserPoolAuthorizer" displayed.

![API Gateway Apply Authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/29.png)

| Section | Purpose |
|--------|---------|
| Authorization | Require Cognito JWT |
| Scopes | Restrict access by OAuth scope |
| Validator | Validate input (body/params/headers) |
| API Key | Require x-api-key if enabled |
| Operation Name | Label for logs/metrics |
| Query Params | Validate URL parameters |
| Headers | Validate header fields |
| Request Body | Validate JSON body |

**Repeat for all other methods:**

Apply the same authorizer to:
-  **POST** `/users` (Create a user)
-  **GET** `/users/{id}` (Get single user)
-  **PUT** `/users/{id}` (Update user)
-  **DELETE** `/users/{id}` (Delete user)

**Which Methods to Secure?**

**For this workshop:** Secure ALL methods to demonstrate full authentication.

**For production:** Consider your requirements:
- **Always secure:** POST, PUT, DELETE (write operations)
- **Sometimes public:** GET (read operations might be public data)
- **Authorization logic:** Even if authenticated, check if user has permission (covered later)

Example:
- Public: GET /users (list all users - public directory)
- Authenticated: POST /users (create user - must be logged in)
- Authorized: PUT /users/{id} (update user - must be owner or admin)
- Authorized: DELETE /users/{id} (delete user - must be owner or admin)


### 5.4 Deploy API with Authentication

1. Click **Deploy API**
2. . **Deployment stage:** `dev`
3. **Deployment description:** `Added Cognito authentication`
4. Click **Deploy**

**Your API is now secured!** 

All requests must include a valid JWT token in the `Authorization` header.

## Step 6: Test Authenticated API Calls

### 6.1 Test Without Token (Should Fail)

Try calling the API in Postman without authentication:

![Test API authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/30.png)

**Expected response:**
```json
{
  "message": "Unauthorized"
}
```

**Status code:** `401 Unauthorized`

**Perfect!** The API rejected the unauthenticated request.

### 6.2 Test With Valid Token (Should Succeed)

Call the API with your ID token:
1. Go to **Authorization** tab
2. **Auth type**: select **Bearer Token**
3. **Token**: paste your IdToken
4. Click **Send** again

![Test API authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/31.png)

5. You will now see all users fetched with status 200

![Test API authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/32.png)

## Step 7: Configure App Client Settings

We will now edit App Client settings 

### 7.1 Update App Client

1. Go back to your app client page in Amazon Cognito
2. Go to **Login pages* tab
3. Click **Edit** in **Managed login pages configuration** card

![Cognito Edit App Client](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/33.png)

### 7.2 Configure Allowed Callback URLs

After successful authentication, Cognito redirects users to these URLs.

If you have a deployed frontend (from [5.1.3-Cloudfront-setup]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup" >}}#step-3-test-your-cloudfront-distribution)):
```
https://d1234abcd.cloudfront.net/callback
```

![Cognito callback url](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/34.png)

**Callback URLs Explained:**

After successful authentication, Cognito redirects users to a callback URL with authentication tokens in the URL fragment.

**Format:**
```
https://yourdomain.com/callback#id_token=eyJraWQi...&access_token=eyJraWQi...
```

**Examples:**
- Development: `http://localhost:3000/callback`
- Production: `https://app.yourdomain.com/callback`
- Cloudfront: `https://d1234abcd.cloudfront.net/callback`

You can add multiple callback URLs for different environments.

### 7.3 Configure Allowed Sign-out URLs

URLs where users are redirected after sign-out.

If you have a deployed frontend:
```
https://d1234abcd.cloudfront.net
```

![Cognito singout url](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/35.png)

### 7.4 Configure OAuth 2.0 Settings

**Identity providers:**
-   Keep **Cognito user pool** checked

**OAuth 2.0 grant types:**
-   **Authorization code grant**
-   **Implicit grant** (only enable this if you want the token exposed in the callback URL, not recommended)

**OpenID Connect scopes:**
-   **OpenID**
-   **Email**
-   **Phone**

4. Click **Save changes**

### 7.5 Test the callback URL

1. Still in your app client page, click **View login page**
2. In the new tab, sign in with your created user
3. If sign-in is successful, you will be redirected to your frontend that you setup in 5.1

## Step 8: Access User Info in Lambda

### 8.1 Understanding Authorizer Context

When API Gateway validates a token with the Cognito authorizer, it automatically passes user claims to your Lambda function.

**Where to find claims:**
```typescript
event.requestContext.authorizer.claims
```

**Available claims:**
```typescript
{
  sub: "12345678-1234-1234-1234-123456789012",
  email: "test@example.com",
  email_verified: "true",
  name: "Test User",
  "cognito:username": "testuser",
  phone_number: "+1234567890",
  phone_number_verified: "false",
  ...
}
```

### 8.2 Example: Log Authenticated User

Add logging to see who's making requests:
```typescript
export const handler = async (event: APIGatewayEvent): Promise<LambdaResponse> => {
  console.log('Received event:', JSON.stringify(event, null, 2));

  // Log authenticated user
  const claims = event.requestContext.authorizer?.claims;
  if (claims) {
    console.log('Authenticated user:', {
      cognitoSub: claims.sub,
      username: claims['cognito:username'],
      email: claims.email
    });
  } else {
    console.log('Unauthenticated request (authorizer not configured or bypassed)');
  }

  // ... rest of handler logic
};
```

### 8.3 Auto-populate cognitoSub from Token

Instead of requiring `cognitoSub` in request body, extract it from the authenticated user:
Modify your lambda code
```javascript
async function createUser(body) {
    const client = await connectToRds();
    try {
        const data = JSON.parse(event.body);

        // Get authenticated user's cognito sub from token
        const claims = event.requestContext.authorizer?.claims;

        if (!claims || !claims.sub) {
            return createResponse(401, {
                success: false,
                error: 'Authentication required'
            });
        }

        const cognitoSub = claims.sub; // From JWT token
        const email = claims.email; // From JWT token
        const username = claims['cognito:username']; // From JWT token
        const role = data.role || 'user'; // Default to 'user' role
        const phoneNumber = claims.phone_number; // From JWT token

        const result = await client.query(`INSERT INTO users (cognito_sub, username, email, role, phone_number)
       VALUES ($1, $2, $3, $4, $5)
       RETURNING *`, [cognitoSub, username, email, role, phoneNumber]);
        return respond(201, { success: true, data: result.rows[0] });
    }
    catch (err) {
        return respond(400, { success: false, error: err.message });
    }
    finally {
        await client.end();
    }
}
```

**Update handler to pass event:**
```javascript
const handler = async (event) => {
    log("[users-handler] process start.");
    const method = event.httpMethod;
    const id = event.pathParameters?.id;
    try {
        switch (method) {
            case "POST":
                return createUser(event); //update event here
            case "GET":
                return id ? getUser(id) : getAllUsers();
            case "PUT":
                if (!id)
                    return respond(400, { success: false, error: "Missing user ID" });
                return updateUser(id, event.body);
            case "DELETE":
                if (!id)
                    return respond(400, { success: false, error: "Missing user ID" });
                return deleteUser(id);
            default:
                return respond(400, { success: false, error: `Unsupported HTTP method: ${method}` });
        }
    }
    catch (err) {
        return respond(500, { success: false, error: err.message });
    }
    finally {
        log("[users-handler] process end.");
    }
};
```

You can download fully working source code that includes the above changes here:
- Github repository: https://github.com/Icyretsz/fcj-workshop-serverless-backend-ver2
- Only the zip file for lambda: https://fcj-workshop-files.s3.ap-southeast-1.amazonaws.com/userHandler-final.zip
- Note that this repo is different from the repo in 5.2.5
- After upload the zip to Lambda, you should find the response function and modify Access-Control-Allow-Origin from `*` to your CloudFront endpoint to only allow requests from your CloudFront

![Cognito singout url](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/36.png)

## Summary

Congratulations! You've successfully:
-   Created Amazon Cognito User Pool
-   Configured sign-in, sign-up, and verification settings
-   Set up Cognito hosted UI
-   Registered and verified test users
-   Obtained JWT tokens from CLI
-   Created Cognito authorizer in API Gateway
-   Secured API endpoints with authentication
-   Tested authenticated API calls
-   Extracted user information from JWT tokens in Lambda
-   Understood Cognito pricing model

### What You've Built

Your application now has:
- **User Authentication**: Sign-up, sign-in, email verification
- **JWT Token Generation**: Secure tokens for API access
- **API Authorization**: API Gateway validates tokens automatically
- **User Identity**: Lambda functions know who's making requests
- **Scalable Auth**: Handles millions of users with Cognito
- **Secure by Default**: No passwords stored in your database
