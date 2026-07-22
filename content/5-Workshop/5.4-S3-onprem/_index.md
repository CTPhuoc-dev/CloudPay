---
title: "User Authentication with Amazon Cognito"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Overview

In this section, you will use Amazon Cognito to add user authentication to the CloudPay system.

CloudPay includes two types of users:

- **Customer**: Creates payments and views transaction history.
- **Admin**: Monitors and manages transactions.

Amazon Cognito manages user accounts, passwords, and the sign-in process.

The authentication flow is:

```text
Customer or Administrator
          ↓
Customer Web or Admin Dashboard
          ↓
     Amazon Cognito
          ↓
   Successful Sign-In
          ↓
Redirect to Amazon CloudFront
```

In this section, you will:

- Create a Cognito User Pool.
- Create App Clients for the Customer and Admin applications.
- Create the `Customer` and `Admin` groups.
- Create test user accounts.
- Connect the Cognito login page to the frontend.
- Test sign-in and sign-out.
- Prepare authentication for Amazon API Gateway.

![CloudPay Cognito Authentication](/images/5-Workshop/5.4-S3-onprem/diagram3.png)

#### Content

- [Prepare Authentication Resources](5.4.1-prepare/)
- [Connect Cognito to the Frontend](5.4.2-create-interface-endpoint/)
- [Test Cognito Sign-In](5.4.3-test-endpoint/)
- [Integrate Cognito with API Gateway](5.4.4-dns-simulation/)