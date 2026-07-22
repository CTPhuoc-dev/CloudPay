---
title: "Worklog Week 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:

* Complete the CloudPay system and its implementation workshop.
* Review the complete workflow connecting the frontend, user authentication, and Payment API.
* Complete the workshop content in Vietnamese and English.
* Develop the internship report website using Hugo.
* Review and clean up AWS resources to reduce the risk of unexpected charges.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Review the overall CloudPay architecture.<br>- Test the Customer Web and Admin Dashboard through CloudFront.<br>- Confirm that the S3 buckets remain private.<br>- Review the bucket policies and Origin Access Control configuration. | 22/06/2026 | 22/06/2026 | AWS documentation<br>FCAJ program materials |
| Tuesday | - Review the Amazon Cognito User Pool.<br>- Review the App Clients for Customer and Admin.<br>- Check the Callback URLs, Sign-out URLs, and Cognito Groups.<br>- Test the user sign-in and sign-out workflows again. | 23/06/2026 | 23/06/2026 | AWS documentation<br>FCAJ program materials |
| Wednesday | - Review the `cloudpay-create-payment` Lambda function.<br>- Test the `POST /payments` route in Amazon API Gateway.<br>- Validate the `orderId` and `amount` input values.<br>- Confirm that the response contains a transaction ID, `PENDING` status, and simulated payment URL.<br>- Monitor the execution logs using Amazon CloudWatch. | 24/06/2026 | 24/06/2026 | AWS documentation<br>FCAJ program materials |
| Thursday | - Complete the CloudPay workshop documentation.<br>- Write the implementation steps for S3, CloudFront, Cognito, API Gateway, Lambda, and CloudWatch.<br>- Add screenshots and testing instructions.<br>- Complete the content in Vietnamese and English. | 25/06/2026 | 25/06/2026 | AWS documentation<br>Hugo documentation<br>FCAJ program materials |
| Friday | - Add the workshop content to the Hugo internship report website.<br>- Review the menu, internal links, and Markdown content.<br>- Test the website locally.<br>- Clean up unused AWS resources to reduce the risk of unexpected charges. | 26/06/2026 | 26/06/2026 | Hugo documentation<br>AWS documentation<br>FCAJ program materials |

### Week 10 Results:

* Completed the basic architecture of the **CloudPay – Payment System on AWS** project.
* Successfully tested both frontend interfaces:
  * Customer Web Application.
  * Admin Dashboard.
* Confirmed that the frontend content was distributed through Amazon CloudFront.
* Confirmed that the S3 buckets remained private and could only be accessed through CloudFront.
* Successfully tested the basic sign-in and sign-out workflow using Amazon Cognito.
* Confirmed that users were divided into two groups:
  * Customer.
  * Admin.
* Successfully tested the Payment API through the `POST /payments` route.
* Confirmed that the Lambda function could:
  * Validate `orderId`.
  * Validate `amount`.
  * Generate a unique transaction ID.
  * Return the `PENDING` status.
  * Return a simulated payment URL.
* Monitored Lambda execution through Amazon CloudWatch Logs.
* Completed the main workshop sections:
  * Workshop overview.
  * Environment preparation.
  * Frontend deployment using S3 and CloudFront.
  * User authentication using Amazon Cognito.
  * Payment API development using API Gateway and Lambda.
  * AWS resource cleanup.
* Completed the workshop content in Vietnamese and English.
* Added the workshop content to the Hugo internship report website.
* Reviewed the website menu, links, and Markdown formatting.
* Cleaned up unused AWS resources to reduce the risk of unexpected charges.
* Understood that the current CloudPay version is a basic learning project and does not include a real payment gateway or complete advanced security features.