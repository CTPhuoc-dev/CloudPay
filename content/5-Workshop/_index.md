---
title: "Workshop"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Build the CloudPay Payment System on AWS

#### Overview

In this workshop, you will build a simple payment system named **CloudPay** on AWS.

The system allows:

- Customers to sign in to the application.
- Customers to submit payment requests.
- Administrators to sign in to the admin dashboard.
- Amazon API Gateway to receive payment requests.
- AWS Lambda to process transactions.
- Amazon CloudWatch to store execution logs.

The general architecture is:

```text
Customer or Administrator
          ↓
Amazon CloudFront
          ↓
Amazon S3
          ↓
Amazon Cognito
          ↓
Amazon API Gateway
          ↓
AWS Lambda
          ↓
Simulated Payment URL
```

The workshop uses the following AWS services:

- **Amazon S3**: Hosts the Customer Web Application and Admin Dashboard.
- **Amazon CloudFront**: Distributes the frontend applications.
- **Amazon Cognito**: Manages user accounts and sign-in.
- **Amazon API Gateway**: Receives payment requests.
- **AWS Lambda**: Processes requests and creates transactions.
- **Amazon CloudWatch**: Stores and displays execution logs.
- **AWS Identity and Access Management (IAM)**: Controls access between AWS services.

{{% notice note %}}
This workshop uses a simulated Payment URL and does not process real financial transactions.
{{% /notice %}}

#### Content

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequisite/)
3. [Deploy the Frontend with S3 and CloudFront](5.3-S3-vpc/)
4. [User Authentication with Amazon Cognito](5.4-S3-onprem/)
5. [Create the Payment API](5.5-Policy/)

#### Expected Results

After completing this workshop:

- The Customer Web Application is deployed on AWS.
- The Admin Dashboard is deployed on AWS.
- Users can sign in using Amazon Cognito.
- API Gateway can receive payment requests.
- Lambda can generate a Transaction ID and a simulated Payment URL.
- Execution logs can be reviewed in CloudWatch.
- Workshop resources can be deleted after completion.