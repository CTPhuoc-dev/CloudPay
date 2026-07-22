---
title: "Worklog Week 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

* Build a basic payment API for the CloudPay system.
* Learn how AWS Lambda processes requests without requiring server management.
* Create an HTTP API using Amazon API Gateway.
* Connect API Gateway to Lambda through the `POST /payments` route.
* Test the API, configure CORS, and review logs using Amazon CloudWatch.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Learn about the roles of AWS Lambda and Amazon API Gateway.<br>- Define the function of the Payment API.<br>- Design the input data containing `orderId` and `amount`.<br>- Define the response containing a transaction ID, payment status, and simulated payment URL. | 08/06/2026 | 08/06/2026 | AWS documentation<br>FCAJ program materials |
| Tuesday | - Create the `cloudpay-create-payment` Lambda function.<br>- Write the payment request processing code in Python.<br>- Validate the `orderId` and `amount` values.<br>- Generate a unique transaction ID with the initial status set to `PENDING`. | 09/06/2026 | 09/06/2026 | https://cloudjourney.awsstudygroup.com/<br>AWS documentation |
| Wednesday | - Create an HTTP API using Amazon API Gateway.<br>- Create the `POST /payments` route.<br>- Connect the route to the Lambda function.<br>- Create the `$default` stage and record the API Invoke URL. | 10/06/2026 | 10/06/2026 | AWS documentation<br>FCAJ program materials |
| Thursday | - Configure CORS for the HTTP API.<br>- Allow the CloudFront websites to send requests to the API.<br>- Test the API using `curl.exe` on Windows.<br>- Test both valid and invalid request data. | 11/06/2026 | 11/06/2026 | AWS documentation<br>FCAJ program materials |
| Friday | - Connect the Customer Web to the Payment API.<br>- Add a function that sends `orderId` and `amount` to the API.<br>- Display the transaction ID and payment status on the interface.<br>- Review Lambda execution logs using Amazon CloudWatch.<br>- Record the issues and solutions encountered during testing. | 12/06/2026 | 12/06/2026 | AWS documentation<br>FCAJ program materials |

### Week 8 Results:

* Understood the role of AWS Lambda in processing backend logic.
* Understood the role of Amazon API Gateway in receiving and routing API requests.
* Successfully created the `cloudpay-create-payment` Lambda function.
* Implemented functions to:
  * Validate `orderId`.
  * Validate `amount`.
  * Generate a unique transaction ID.
  * Return the initial `PENDING` status.
  * Generate a simulated payment URL.
* Successfully created an HTTP API for CloudPay.
* Created and tested the `POST /payments` route.
* Successfully connected Amazon API Gateway to AWS Lambda.
* Configured CORS so that the frontend could send requests to the API.
* Successfully tested the API using `curl.exe`.
* Connected the Customer Web to the Payment API.
* Displayed transaction information on the interface after submitting a request.
* Reviewed Lambda execution logs in Amazon CloudWatch.
* Understood that the current Payment API only simulates transaction creation and is not connected to a real payment gateway.
* Understood that the API route in the basic version is not yet fully protected by a JWT Authorizer.