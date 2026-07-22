---
title: "Worklog Week 12"
date: 2026-07-13
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Week 12 Objectives:

* Review and complete the main components of the **CloudPay – Payment System on AWS** project.
* Complete the workshop and internship report content in Vietnamese and English.
* Test the complete internship report website built with Hugo.
* Organize the project results, documentation, and screenshots for the final presentation.
* Clean up AWS resources and prepare the project for handover.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Review the overall architecture of the CloudPay system.<br>- Test the Customer Web and Admin Dashboard delivered through Amazon CloudFront.<br>- Review Amazon Cognito, API Gateway, AWS Lambda, and Amazon CloudWatch.<br>- Record the completed functions and the limitations of the current version. | 13/07/2026 | 13/07/2026 | AWS documentation<br>FCAJ program materials |
| Tuesday | - Test the sign-in and sign-out workflows using Amazon Cognito.<br>- Test the payment request function from the Customer Web.<br>- Test the `POST /payments` route and its response data.<br>- Review Lambda execution logs and resolve the remaining issues. | 14/07/2026 | 14/07/2026 | AWS documentation<br>FCAJ program materials |
| Wednesday | - Complete the CloudPay workshop documentation.<br>- Add implementation steps, screenshots, and testing results.<br>- Review the Vietnamese and English content.<br>- Ensure consistent AWS terminology throughout the documentation. | 15/07/2026 | 15/07/2026 | AWS documentation<br>Hugo documentation<br>FCAJ program materials |
| Thursday | - Review the complete internship report website built with Hugo.<br>- Check the Worklog, Proposal, Workshop, Events, Self-Evaluation, and Feedback sections.<br>- Review the menu, internal links, images, and Markdown tables.<br>- Run `hugo server -D` and test the website locally. | 16/07/2026 | 16/07/2026 | Hugo documentation<br>Markdown documentation<br>FCAJ program materials |
| Friday | - Organize the project source code, documentation, and screenshots.<br>- Prepare the CloudPay presentation and demonstration content.<br>- Review Billing and Cost Management.<br>- Delete or stop unused AWS resources.<br>- Back up the completed version and prepare the project for handover. | 17/07/2026 | 17/07/2026 | AWS documentation<br>FCAJ program materials |

### Week 12 Results:

* Completed the basic version of the **CloudPay – Payment System on AWS** project.
* Successfully reviewed the main AWS components:
  * Amazon S3.
  * Amazon CloudFront.
  * Amazon Cognito.
  * Amazon API Gateway.
  * AWS Lambda.
  * Amazon CloudWatch.
  * AWS IAM.
* Successfully tested the Customer Web and Admin Dashboard.
* Retested the sign-in and sign-out workflows using Amazon Cognito.
* Successfully tested the Payment API through the `POST /payments` route.
* Confirmed that the system could:
  * Receive `orderId` and `amount`.
  * Validate input data.
  * Generate a transaction ID.
  * Return the `PENDING` status.
  * Return a simulated payment URL.
* Monitored request processing through Amazon CloudWatch Logs.
* Completed the CloudPay workshop documentation in Vietnamese and English.
* Added the implementation steps, screenshots, testing results, and resource cleanup instructions.
* Completed the main sections of the internship report website:
  * Worklog.
  * Proposal.
  * Blogs Posted.
  * Events Participated.
  * Workshop.
  * Self-Evaluation.
  * Feedback.
* Successfully reviewed the menu, links, images, and Markdown formatting on the Hugo website.
* Organized the source code, documentation, and screenshots for the project presentation and handover.
* Reviewed costs and cleaned up unused AWS resources.
* Understood the limitations of the current version:
  * It only simulates the payment process.
  * It is not connected to a real payment gateway.
  * It does not include complete transaction storage or advanced security features.
* Gained additional practical experience in serverless application development, frontend deployment, user authentication, system testing, and technical documentation on AWS.