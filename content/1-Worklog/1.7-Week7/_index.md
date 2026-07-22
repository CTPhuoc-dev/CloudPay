---
title: "Worklog Week 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

* Learn about Amazon Cognito and the user authentication process.
* Build sign-in functionality for the Customer Web and Admin Dashboard.
* Create Customer and Admin user groups.
* Connect the Cognito managed login page to both websites deployed through CloudFront.
* Test the user sign-in, sign-out, and redirection workflow.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Learn about the role of Amazon Cognito in user management and authentication.<br>- Study the main components, including User Pools, App Clients, Users, and Groups.<br>- Identify the two CloudPay user groups: Customer and Admin. | 01/06/2026 | 01/06/2026 | AWS documentation<br>FCAJ program materials |
| Tuesday | - Create an Amazon Cognito User Pool for CloudPay.<br>- Configure email-based sign-in.<br>- Set a basic password policy.<br>- Create a Cognito domain for the managed login page. | 02/06/2026 | 02/06/2026 | https://cloudjourney.awsstudygroup.com/<br>AWS documentation |
| Wednesday | - Create an App Client for the Customer Web.<br>- Create an App Client for the Admin Dashboard.<br>- Disable the Client Secret because both applications are frontend websites.<br>- Configure Callback URLs and Sign-out URLs using the CloudFront domain names.<br>- Configure the `openid`, `email`, and `profile` scopes. | 03/06/2026 | 03/06/2026 | AWS documentation<br>FCAJ program materials |
| Thursday | - Create two Cognito Groups: Customer and Admin.<br>- Create test accounts for each group.<br>- Add the users to their corresponding groups.<br>- Review the User Pool ID, App Client IDs, and Cognito domain information. | 04/06/2026 | 04/06/2026 | AWS documentation<br>FCAJ program materials |
| Friday | - Add sign-in and sign-out buttons to the Customer Web and Admin Dashboard.<br>- Configure the sign-in buttons to redirect users to the Cognito managed login page.<br>- Test the sign-in process and redirection back to CloudFront.<br>- Check the `code` parameter in the URL after sign-in.<br>- Test the sign-out process and record the issues encountered. | 05/06/2026 | 05/06/2026 | AWS documentation<br>FCAJ program materials |

### Week 7 Results:

* Understood the role of Amazon Cognito in identity management and user authentication.
* Successfully created a Cognito User Pool for CloudPay.
* Created two App Clients for:
  * Customer Web Application.
  * Admin Dashboard.
* Configured the Callback URLs and Sign-out URLs using the CloudFront domain names.
* Successfully created two user groups:
  * Customer.
  * Admin.
* Created test accounts and assigned users to their corresponding groups.
* Added sign-in and sign-out buttons to both frontend interfaces.
* Successfully redirected users from CloudFront to the Cognito managed login page.
* Successfully signed in and returned to the CloudFront website.
* Verified the `code` parameter in the URL after sign-in.
* Understood that the current version only demonstrates a basic sign-in and redirection workflow and does not exchange the Authorization Code for a JWT token.
* Completed the Cognito preparation required for integration with API Gateway in the following weeks.