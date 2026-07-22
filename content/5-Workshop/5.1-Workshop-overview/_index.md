---
title: "Introduction"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Introduction to the AWS Payment System

An online payment system is one of the most important components of e-commerce platforms, ticket booking systems, ordering applications, online learning platforms, and other digital services.

A reliable payment system should meet the following requirements:

- Receive and process transactions quickly.
- Prevent transaction loss during traffic spikes.
- Prevent duplicate webhook processing.
- Protect the database from a large number of concurrent connections.
- Track transaction status and handle failed transactions.
- Notify customers of payment results.
- Provide administrators with transaction monitoring, reporting, and recovery features.

In this project, the payment system is designed using a combination of **Serverless Architecture** and **Event-Driven Architecture** on AWS.

Payment requests are received through Amazon API Gateway and processed by AWS Lambda. After a payment gateway sends an IPN or webhook callback to the system, validated transactions are sent to Amazon SQS for asynchronous processing.

Worker Lambda consumes transaction messages from the queue and writes the transaction data to Amazon RDS through AWS RDS Proxy. This design reduces pressure on the database and prevents database connection exhaustion when multiple Lambda instances run concurrently.

Transactions that fail repeatedly are moved to an Amazon SQS Dead Letter Queue. Administrators can monitor, review, and retry these failed transactions through the Admin Dashboard.

#### Workshop Overview

In this workshop, you will learn how to design and build an online payment system on AWS.

The system includes two main user groups:

- **Customer**: Signs in, creates payment requests, completes payments, and tracks transaction results.
- **Administrator**: Monitors transactions, views reports, reviews failed transactions, and retries messages stored in the DLQ.

The main transaction flow includes the following steps:

1. The customer accesses the application through AWS WAF and Amazon CloudFront.
2. The Web Application and Admin Dashboard are stored in Amazon S3.
3. Customers and administrators authenticate through Amazon Cognito.
4. The frontend sends payment requests to Amazon API Gateway.
5. Payment Lambda validates the order information and creates a payment URL.
6. The customer is redirected to a payment gateway such as MoMo, VNPay, or ZaloPay.
7. After the payment is completed, the payment gateway sends an IPN or webhook callback to the system.
8. Webhook Lambda validates the signature, payment amount, and transaction status.
9. Validated transactions are sent to the Amazon SQS Transaction Queue.
10. Worker Lambda consumes and processes the transaction messages.
11. Transaction data is written to Amazon RDS through AWS RDS Proxy.
12. Amazon SNS sends email, SMS, or events to related services.
13. Transactions that fail repeatedly are moved to the Amazon SQS DLQ.
14. Administrators monitor failed transactions and retry them when necessary.

In addition to the main transaction flow, the workshop also uses:

- **Amazon CloudWatch** to collect logs and metrics and create alarms.
- **AWS X-Ray** to trace requests across distributed services.
- **Amazon SNS** to send email or SMS alerts to administrators.
- **AWS IAM** to grant permissions based on the principle of least privilege.
- **Amazon VPC** to isolate Worker Lambda, RDS Proxy, and Amazon RDS in private subnets.

After completing this workshop, you will understand how to:

- Build payment APIs using Amazon API Gateway and AWS Lambda.
- Authenticate customers and administrators using Amazon Cognito.
- Process transactions asynchronously using Amazon SQS.
- Configure a Dead Letter Queue and message retry mechanism.
- Connect AWS Lambda to Amazon RDS through AWS RDS Proxy.
- Send customer notifications and system alerts through Amazon SNS.
- Monitor logs and metrics using Amazon CloudWatch.
- Trace errors and distributed requests using AWS X-Ray.
- Build administrative features for transaction monitoring and recovery.
- Clean up AWS resources to avoid unnecessary costs.

![AWS Payment System Overview](/images/5-Workshop/5.1-Workshop-overview/payment_architecture.png)