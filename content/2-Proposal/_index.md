---
title: "Proposal"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# CloudPay - Payment System on AWS
## A Secure, Scalable, and High-Availability Online Payment Solution

### 1. Executive Summary

CloudPay is an online payment system designed on AWS, enabling businesses to receive, validate, and process transactions from multiple payment gateways such as MoMo, VNPay, and ZaloPay.

The system is built using a combination of **Serverless Architecture** and **Event-Driven Architecture**. AWS Lambda, Amazon API Gateway, and Amazon SQS are used to process payment requests. Transaction data is stored in Amazon RDS through AWS RDS Proxy to prevent database connection exhaustion when many Lambda instances run concurrently.

In addition to the customer payment flow, the system also provides an administrative area for administrators. Administrators can sign in, monitor transactions, view reports, review failed transactions, and retry failed messages through the Dead Letter Queue.

CloudPay focuses on four main requirements:

- Preventing payment transaction loss.
- Protecting the database during traffic spikes.
- Providing monitoring, alerting, and failure recovery.
- Providing centralized transaction management and monitoring for administrators.

### 2. Problem Statement

#### Current Problems

Online payment systems commonly face the following challenges:

- Transaction traffic may increase suddenly during promotions or peak hours.
- A large number of Lambda functions connecting directly to the database may exhaust available connections.
- Payment gateways may send the same webhook multiple times.
- Transactions may be missed if an error occurs during database processing.
- It is difficult to trace errors across API Gateway, Lambda, Queue, and Database.
- Administrators may lack tools to monitor and retry failed transactions.
- Customers may not receive payment notifications on time.

#### Proposed Solution

CloudPay uses AWS Serverless Architecture combined with asynchronous processing.

Customers access the system through AWS WAF and Amazon CloudFront. The customer web application and Admin Dashboard are hosted on Amazon S3. Customers and administrators authenticate through Amazon Cognito before calling APIs through Amazon API Gateway.

Payment Lambda is responsible for creating the payment URL and redirecting the customer to the selected payment gateway. After the transaction is completed, the payment gateway sends an IPN or Webhook callback to the system.

Webhook Lambda performs the following tasks:

- Validates the webhook signature.
- Verifies the transaction identifier.
- Verifies the payment amount and status.
- Prevents duplicate processing.
- Sends valid transactions to Amazon SQS.

Worker Lambda processes messages from the queue and writes transaction data to Amazon RDS through AWS RDS Proxy. If a message fails repeatedly, it is moved to the Amazon SQS Dead Letter Queue.

Administrators can:

- View the transaction list.
- Search payment history.
- View reports.
- Monitor failed transactions.
- Retry transactions from the DLQ.

#### Benefits and Return on Investment

The proposed solution provides the following benefits:

- Reduces the risk of transaction loss through Amazon SQS.
- Reduces database load and improves database stability through AWS RDS Proxy.
- Automatically scales according to transaction volume.
- Reduces server management effort.
- Supports faster error detection and recovery.
- Improves customer experience through email and SMS notifications.
- Allows administrators to monitor the system from a centralized interface.

For a learning environment or a low-traffic MVP, most Serverless services can operate within AWS Free Tier limits. The main costs are expected to come from Amazon RDS, RDS Proxy, logs, and network traffic.

### 3. Solution Architecture

CloudPay uses a combination of Serverless Architecture and Event-Driven Architecture.

![AWS Payment System Architecture](/images/2-Proposal/payment_architecture.png)

#### Main Processing Flow

1. The customer sends an HTTPS request to the system.
2. AWS WAF inspects and filters malicious requests.
3. Amazon CloudFront delivers the user interface from Amazon S3.
4. The customer signs in through Amazon Cognito.
5. Cognito returns a JWT token.
6. The frontend calls APIs through Amazon API Gateway.
7. Payment Lambda creates a payment URL.
8. The customer is redirected to MoMo, VNPay, or ZaloPay.
9. The payment gateway sends an IPN or Webhook callback to the system.
10. Webhook Lambda validates the signature and transaction information.
11. Valid transactions are sent to Amazon SQS.
12. Worker Lambda reads and processes messages from the queue.
13. Worker Lambda writes transaction data to Amazon RDS through RDS Proxy.
14. Amazon SNS sends email, SMS, or integration events to other services.
15. Failed transactions are moved to Amazon SQS DLQ.
16. Administrators monitor and retry failed transactions.

#### AWS Services Used

- **AWS WAF**: Protects the application and filters malicious requests.
- **Amazon CloudFront**: Delivers the web interface with low latency.
- **Amazon S3**: Stores the Web Application and Admin Dashboard.
- **Amazon Cognito**: Authenticates customers and administrators.
- **Amazon API Gateway**: Receives and routes API requests.
- **AWS Lambda**: Handles payment, webhook, worker, and administrative operations.
- **Amazon SQS**: Stores transaction messages and controls processing load.
- **Amazon SQS DLQ**: Stores transactions that fail during processing.
- **AWS RDS Proxy**: Manages database connections.
- **Amazon RDS**: Stores transaction data and payment history.
- **Amazon SNS**: Sends notifications and system alerts.
- **Amazon CloudWatch**: Collects logs and metrics.
- **AWS X-Ray**: Traces requests across AWS services.
- **AWS IAM**: Manages access permissions.
- **Amazon VPC**: Isolates Worker Lambda, RDS Proxy, and Amazon RDS in private subnets.

#### Component Design

##### Edge and Frontend

AWS WAF is placed in front of Amazon CloudFront to inspect incoming requests. CloudFront distributes static content stored in Amazon S3, including the customer interface and Admin Dashboard.

##### Authentication

Amazon Cognito manages two user groups:

- Customer.
- Administrator.

After successful authentication, Cognito returns a JWT token. API Gateway validates the token before allowing access to protected APIs.

##### Payment Lambda

Payment Lambda performs the following tasks:

- Validates order information.
- Calculates the payment amount.
- Generates a transaction identifier.
- Stores the initial transaction status.
- Creates the payment URL.
- Returns the payment URL to the frontend.

##### Webhook Lambda

Webhook Lambda performs the following tasks:

- Validates the webhook signature.
- Verifies the transaction identifier.
- Verifies the payment amount.
- Verifies the payment status.
- Prevents duplicate processing.
- Sends the transaction to Amazon SQS.

##### Transaction Queue

Amazon SQS acts as a buffer between Webhook Lambda and Worker Lambda.

Amazon SQS helps:

- Control the number of transactions being processed.
- Prevent excessive concurrent writes to the database.
- Automatically retry messages when Worker Lambda fails.
- Reduce the risk of transaction loss.

##### Worker Lambda

Worker Lambda performs the following tasks:

- Updates the payment status.
- Stores transaction history.
- Updates the order status.
- Creates audit logs.
- Publishes notification events.
- Ensures idempotent processing.

##### RDS Proxy and Amazon RDS

Worker Lambda connects to Amazon RDS through AWS RDS Proxy.

RDS Proxy helps:

- Pool and reuse database connections.
- Reduce the number of concurrent database connections.
- Improve system scalability.
- Increase database stability.

Amazon RDS is deployed using a Primary and Standby configuration to improve availability.

##### Admin API Lambda

Admin API Lambda provides the following functions:

- View the transaction list.
- Search transactions.
- View payment history.
- View reports and statistics.
- Review failed transactions.
- Retry transactions from the DLQ.

##### Observability

Amazon CloudWatch collects:

- API Gateway errors.
- Lambda errors.
- Lambda execution duration.
- SQS message count.
- DLQ message count.
- RDS connection count.
- Database CPU usage.
- Database latency.

AWS X-Ray supports request tracing across API Gateway, Lambda, SQS, Worker Lambda, RDS Proxy, and Amazon RDS.

Amazon SNS sends email or SMS alerts to administrators when:

- A Lambda function fails.
- A new message appears in the DLQ.
- API Gateway has a high error rate.
- Database CPU usage increases beyond the configured threshold.
- The transaction queue contains too many pending messages.

### 4. Technical Implementation

#### Implementation Phases

1. **Research and Requirement Analysis**
   - Analyze the payment workflow.
   - Research IPN and Webhook mechanisms.
   - Identify customer and administrator requirements.

2. **Architecture Design**
   - Design the AWS architecture.
   - Define the data flow.
   - Design the database.
   - Design the failure-handling mechanism.

3. **Frontend Development**
   - Build the payment interface.
   - Build the payment result page.
   - Build the Admin Dashboard.

4. **Backend Development**
   - Create Amazon API Gateway.
   - Develop Payment Lambda.
   - Develop Webhook Lambda.
   - Develop Worker Lambda.
   - Develop Admin API Lambda.

5. **Queue and Database Integration**
   - Create the Transaction Queue.
   - Configure the DLQ.
   - Configure AWS RDS Proxy.
   - Connect Worker Lambda to Amazon RDS.

6. **Monitoring and Alerting**
   - Configure CloudWatch Logs.
   - Create CloudWatch Alarms.
   - Enable AWS X-Ray.
   - Create an SNS Topic.

7. **Testing**
   - Test successful payments.
   - Test failed payments.
   - Test duplicate webhooks.
   - Test retry behavior.
   - Test the DLQ.
   - Test the Admin Dashboard.

8. **Clean-Up**
   - Delete Lambda functions.
   - Delete API Gateway APIs.
   - Delete the Queue and DLQ.
   - Delete RDS Proxy and Amazon RDS.
   - Delete the S3 Bucket and CloudFront distribution.
   - Delete CloudWatch Alarms and SNS Topics.

#### Technical Requirements

- **Frontend**: React or Next.js.
- **Backend**: Node.js or Python.
- **Infrastructure as Code**: AWS SAM, AWS CDK, or Terraform.
- **Database**: Amazon RDS for PostgreSQL or MySQL.
- **Authentication**: Amazon Cognito.
- **API**: Amazon API Gateway REST API or HTTP API.
- **Queue**: Amazon SQS Standard Queue.
- **Monitoring**: Amazon CloudWatch and AWS X-Ray.
- **Payment Gateway**: MoMo, VNPay, or ZaloPay Sandbox.

#### IAM Requirements

Each Lambda function uses a separate IAM Role.

- Payment Lambda is allowed only to create and update initial transaction records.
- Webhook Lambda is allowed only to send messages to Amazon SQS.
- Worker Lambda is allowed only to read SQS messages and access RDS Proxy.
- Admin API Lambda is allowed only to read the required data and perform retry operations.
- Worker Lambda is allowed only to publish events to the required SNS Topic.

The following practices must not be used:

- Assigning `AdministratorAccess` to Lambda functions.
- Hard-coding AWS Access Keys or secrets in the source code.
- Deploying Amazon RDS in a public subnet.
- Making Amazon S3 public when it is not required.

### 5. Timeline and Milestones

#### Weeks 1–2: Analysis and Design

- Study AWS Serverless services.
- Analyze the payment processing problem.
- Design the architecture diagram.
- Identify the required AWS services.

#### Weeks 3–4: Frontend and Authentication

- Create an Amazon S3 Bucket.
- Configure Amazon CloudFront.
- Create a Cognito User Pool.
- Build the login interface.
- Build the basic Admin Dashboard.

#### Weeks 5–6: Payment API

- Create Amazon API Gateway.
- Develop Payment Lambda.
- Integrate the payment gateway Sandbox.
- Generate the payment URL.
- Test the redirect flow.

#### Weeks 7–8: Webhook and Queue

- Develop Webhook Lambda.
- Implement signature validation.
- Create the Transaction Queue.
- Create the DLQ.
- Test retry and redrive behavior.

#### Weeks 9–10: Database and Worker

- Create Amazon RDS.
- Create AWS RDS Proxy.
- Develop Worker Lambda.
- Store transaction data in the database.
- Publish events to Amazon SNS.

#### Week 11: Administration and Monitoring

- Complete Admin API Lambda.
- Build the transaction report page.
- Configure Amazon CloudWatch.
- Enable AWS X-Ray.
- Create SNS Alarms.

#### Week 12: Testing and Completion

- Perform end-to-end testing.
- Test failure scenarios.
- Review logs and metrics.
- Complete the workshop documentation.
- Clean up unnecessary resources.

### 6. Budget Estimation

The following costs are estimates for a learning environment or MVP:

- AWS Lambda: 0–10 USD per month.
- Amazon API Gateway: 0–10 USD per month.
- Amazon SQS: 0–5 USD per month.
- Amazon SNS: 0–5 USD per month.
- Amazon S3: 1–5 USD per month.
- Amazon CloudFront: 1–10 USD per month.
- Amazon Cognito: 0–5 USD per month.
- Amazon RDS: 15–40 USD per month.
- AWS RDS Proxy: depends on the database configuration.
- CloudWatch Logs and AWS X-Ray: 1–10 USD per month.

**Estimated total cost: approximately 25–80 USD per month.**

Cost reduction methods:

- Use AWS Free Tier where available.
- Run Amazon RDS only during development and testing.
- Reduce CloudWatch Logs retention.
- Use a small database configuration.
- Delete resources after completing the workshop.
- Create an AWS Budget Alarm.

### 7. Risk Assessment

| Risk | Impact | Probability |
|---|---|---|
| Duplicate Webhook | High | Medium |
| Transaction not recorded | High | Low |
| Database connection exhaustion | High | Medium |
| Queue backlog | Medium | Medium |
| Invalid payment signature | High | Low |
| Sensitive data exposure | High | Low |
| AWS cost overrun | Medium | Medium |
| Administrator retry failure | Medium | Low |
| Lambda cold start | Low | Medium |

#### Mitigation Strategies

- Use the Transaction ID as an idempotency key.
- Send transactions to Amazon SQS before performing database operations.
- Use AWS RDS Proxy to manage the database connection pool.
- Monitor `ApproximateNumberOfMessagesVisible`.
- Monitor `ApproximateAgeOfOldestMessage`.
- Create CloudWatch Alarms when the queue backlog exceeds the configured threshold.
- Use HTTPS and JWT tokens.
- Validate webhook signatures.
- Use AWS Secrets Manager.
- Apply IAM Least Privilege.
- Deploy Amazon RDS in private subnets.
- Create AWS Budgets and billing alerts.
- Clean up resources after completing the workshop.

#### Contingency Plans

- Failed messages are moved to the DLQ.
- Administrators can retry failed transactions.
- Transactions are logged for reconciliation.
- Amazon RDS uses a Standby instance.
- Infrastructure as Code is used to redeploy the system when necessary.

### 8. Expected Outcomes

After completion, the system is expected to:

- Generate payment URLs.
- Redirect customers to payment gateways.
- Receive and validate Webhooks.
- Process transactions asynchronously.
- Store transaction data in Amazon RDS.
- Send email or SMS notifications.
- Monitor logs and metrics.
- Trace errors through AWS X-Ray.
- Move failed transactions to the DLQ.
- Allow administrators to view reports and retry failed transactions.

#### Success Criteria

- Valid transactions are recorded completely.
- Duplicate Webhooks do not create duplicate records.
- The Transaction Queue can handle temporary traffic spikes.
- Worker Lambda does not exhaust database connections.
- Failed transactions are correctly moved to the DLQ.
- Administrators can view transaction history.
- Administrators can retry failed transactions.
- CloudWatch Alarms successfully send notifications.
- Complete deployment and clean-up documentation is provided.

#### Long-Term Value

CloudPay can be developed for:

- E-commerce websites.
- Ticket booking systems.
- Ordering applications.
- Online learning platforms.
- Service fee collection systems.
- Bill payment applications.

The architecture can be extended with:

- Additional payment gateways.
- Refund processing.
- Transaction reconciliation.
- Fraud Detection.
- Audit Logs.
- Revenue Dashboards.
- CI/CD Pipelines.
- AWS KMS Encryption.
- AWS Secrets Manager.
- Multi-Region Disaster Recovery.

The project demonstrates the ability to design a practical AWS system using Serverless and Managed Services while addressing performance, security, monitoring, scalability, and operational requirements.