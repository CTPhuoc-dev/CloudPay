---
title: "Worklog Week 5"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Learn about Monolithic, Microservices, and Serverless architectures.
* Define the topic and scope of the CloudPay project.
* Design the overall architecture of a simple payment system on AWS.
* Select suitable AWS services for each system function.
* Design a simulated payment workflow and prepare the workshop implementation plan.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Learn the differences between Monolithic Architecture, Microservices Architecture, and Serverless Architecture.<br>- Analyze the advantages of serverless architecture for a small payment system.<br>- Define the project topic as **CloudPay – Payment System on AWS**. | 18/05/2026 | 18/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| Tuesday | - Identify the main system users: Customer and Admin.<br>- Define the main system functions:<br>&emsp; + User authentication and sign-in<br>&emsp; + Payment request submission<br>&emsp; + Transaction information generation<br>&emsp; + Payment status and execution log review | 19/05/2026 | 19/05/2026 | AWS documentation<br>FCAJ program materials |
| Wednesday | - Design the overall architecture of the CloudPay project.<br>- Select the main AWS services:<br>&emsp; + Amazon S3 for frontend storage<br>&emsp; + Amazon CloudFront for content delivery<br>&emsp; + Amazon Cognito for user authentication<br>&emsp; + Amazon API Gateway for receiving payment requests<br>&emsp; + AWS Lambda for request processing<br>&emsp; + Amazon CloudWatch for log monitoring<br>&emsp; + AWS IAM for access control | 20/05/2026 | 20/05/2026 | https://cloudjourney.awsstudygroup.com/<br>AWS documentation |
| Thursday | - Design the simulated payment workflow.<br>- Define the `POST /payments` API.<br>- Define the input data, including `orderId` and `amount`.<br>- Design the response containing a transaction ID, `PENDING` status, and a simulated payment URL.<br>- Learn how Amazon Cognito Groups can be used for Customer and Admin users. | 21/05/2026 | 21/05/2026 | AWS documentation<br>FCAJ program materials |
| Friday | - Complete the architecture diagram and project proposal.<br>- Prepare the workshop implementation plan.<br>- Identify important topics such as S3 security, IAM permissions, CORS, and CloudWatch Logs.<br>- Learn how to clean up AWS resources to avoid unexpected charges. | 22/05/2026 | 22/05/2026 | AWS documentation<br>FCAJ program materials |

### Week 5 Results:

* Understood the basic differences between:
  * Monolithic Architecture
  * Microservices Architecture
  * Serverless Architecture
* Defined the project topic as **CloudPay – Payment System on AWS**.
* Identified the two main user groups:
  * Customer
  * Admin
* Defined the basic system functions:
  * User authentication and sign-in.
  * Payment request submission.
  * Transaction information generation.
  * Simulated payment status response.
  * Processing monitoring through execution logs.
* Completed the overall architecture using:
  * Amazon S3
  * Amazon CloudFront
  * Amazon Cognito
  * Amazon API Gateway
  * AWS Lambda
  * Amazon CloudWatch
  * AWS IAM
* Designed the `POST /payments` API for creating payment requests.
* Defined the API input and response data structures.
* Completed the simulated payment workflow with an initial status of `PENDING`.
* Completed the project proposal and workshop implementation plan.
* Understood that the current version only simulates the payment process and does not integrate with a real payment gateway.