---
title: "Frontend Deployment with S3 and CloudFront"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Hosting and Delivering the CloudPay Frontend

In this section, you will deploy the CloudPay user interfaces using Amazon S3 and Amazon CloudFront.

The system includes two separate interfaces:

- **Customer Web Application**: Allows customers to sign in, create payment requests, and view transaction results.
- **Admin Dashboard**: Allows administrators to monitor transactions, view reports, review failed transactions, and perform retry operations.

Frontend files such as HTML, CSS, JavaScript, and images are stored in Amazon S3. Both S3 buckets remain private to prevent users from accessing the application files directly.

Amazon CloudFront is placed in front of Amazon S3 to deliver content with low latency. CloudFront uses Origin Access Control to retrieve content securely from the private S3 buckets without enabling public access.

The frontend access flow is:

```text
Customer or Administrator
          ↓
        HTTPS
          ↓
  Amazon CloudFront
          ↓
Origin Access Control
          ↓
Private Amazon S3 Bucket
```

In this section, you will complete the following tasks:

- Create an S3 bucket for the Customer Web Application.
- Create an S3 bucket for the Admin Dashboard.
- Upload a test `index.html` file.
- Enable S3 Block Public Access.
- Configure server-side encryption for S3 objects.
- Create CloudFront distributions.
- Configure Origin Access Control.
- Update the S3 bucket policies.
- Redirect HTTP requests to HTTPS.
- Test frontend access through CloudFront.
- Confirm that direct access to Amazon S3 is denied.
- Create a CloudFront invalidation after updating frontend content.

![CloudPay Frontend Deployment Overview](/images/5-Workshop/5.3-S3-vpc/diagram2.png)

#### Content

- [Create S3 Buckets for Web Applications](5.3.1-create-gwe/)
- [Configure CloudFront and Test Access](5.3.2-test-gwe/)