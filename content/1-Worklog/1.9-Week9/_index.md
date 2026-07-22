---
title: "Worklog Week 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:

* Complete and test the main components of the CloudPay system.
* Verify the connection between the frontend, Amazon Cognito, API Gateway, and AWS Lambda.
* Monitor request processing using Amazon CloudWatch.
* Complete the workshop implementation documentation.
* Record the issues and solutions encountered during development.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Review the Customer Web and Admin Dashboard through Amazon CloudFront.<br>- Verify that the frontend files can be accessed from S3 through CloudFront.<br>- Confirm that the S3 buckets remain private.<br>- Create a CloudFront invalidation after updating the frontend content. | 15/06/2026 | 15/06/2026 | AWS documentation<br>FCAJ program materials |
| Tuesday | - Test the Customer and Admin sign-in workflows using Amazon Cognito.<br>- Review the Callback URLs and Sign-out URLs.<br>- Confirm that users are assigned to the correct Cognito Groups.<br>- Record issues related to the Cognito domain, App Clients, and redirect URLs. | 16/06/2026 | 16/06/2026 | AWS documentation<br>FCAJ program materials |
| Wednesday | - Test the Payment API using `curl.exe`.<br>- Test a valid request containing `orderId` and `amount`.<br>- Test requests with missing or invalid data.<br>- Confirm that the API returns a transaction ID, `PENDING` status, and a simulated payment URL. | 17/06/2026 | 17/06/2026 | AWS documentation<br>FCAJ program materials |
| Thursday | - Test the connection between the Customer Web and the Payment API.<br>- Review the CORS configuration in API Gateway.<br>- Monitor Lambda execution logs using Amazon CloudWatch.<br>- Review errors that occur when Lambda or API Gateway cannot process a request. | 18/06/2026 | 18/06/2026 | AWS documentation<br>FCAJ program materials |
| Friday | - Summarize the CloudPay system testing results.<br>- Complete the workshop content using Markdown.<br>- Add screenshots and implementation steps.<br>- Prepare both Vietnamese and English content.<br>- Review active AWS resources to reduce the risk of unexpected charges. | 19/06/2026 | 19/06/2026 | AWS documentation<br>FCAJ program materials |

### Week 9 Results:

* Successfully tested both frontend interfaces:
  * Customer Web Application.
  * Admin Dashboard.
* Confirmed that frontend content was distributed through Amazon CloudFront.
* Confirmed that the S3 buckets could not be accessed publicly and directly.
* Successfully tested the basic sign-in and sign-out workflow using Amazon Cognito.
* Confirmed that Customer and Admin users were assigned to the correct Cognito Groups.
* Successfully tested the Payment API using `curl.exe`.
* Confirmed that the API could:
  * Receive `orderId`.
  * Receive `amount`.
  * Validate input data.
  * Generate a unique transaction ID.
  * Return the `PENDING` status.
  * Return a simulated payment URL.
* Successfully connected the Customer Web to the Payment API.
* Reviewed and adjusted the CORS configuration in Amazon API Gateway.
* Monitored Lambda execution logs using Amazon CloudWatch.
* Recorded several issues and solutions related to:
  * CloudFront caching.
  * Cognito Callback URLs.
  * API Gateway CORS.
  * Lambda input validation.
* Continued completing the CloudPay workshop documentation in Vietnamese and English.
* Reviewed active AWS resources to reduce the risk of unexpected charges.
* Understood that the current CloudPay version is a basic simulation and is not integrated with a real payment gateway.