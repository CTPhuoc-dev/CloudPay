---
title: "Prerequisites"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Prerequisite Overview

Before deploying the CloudPay system, you need to prepare an AWS account, development tools, IAM permissions, and payment gateway sandbox information.

In this workshop, you will deploy the following components:

- Amazon S3 and Amazon CloudFront for the Web Application and Admin Dashboard.
- Amazon Cognito for customer and administrator authentication.
- Amazon API Gateway for providing application APIs.
- AWS Lambda for payment, webhook, worker, and administrative processing.
- Amazon SQS for asynchronous transaction processing.
- Amazon SQS Dead Letter Queue for storing failed transaction messages.
- Amazon RDS and AWS RDS Proxy for transaction data storage.
- Amazon SNS for sending notifications and alerts.
- Amazon CloudWatch and AWS X-Ray for system monitoring.
- Amazon VPC, private subnets, and Security Groups for database protection.

#### AWS Account

You need an active AWS account.

It is recommended to use a separate AWS account for learning or workshop purposes to avoid affecting other environments.

Before starting, verify that:

- The AWS account has permission to create and delete resources.
- Multi-Factor Authentication is enabled.
- The root account is not used to perform the workshop.
- An AWS Budget has been created to control costs.
- You have reviewed the AWS services that may generate charges.

#### Deployment Region

This workshop uses the Singapore AWS Region:

```text
ap-southeast-1
```

The following resources should be deployed in the same Region:

- AWS Lambda.
- Amazon API Gateway.
- Amazon Cognito.
- Amazon SQS.
- Amazon SNS.
- Amazon RDS.
- AWS RDS Proxy.
- Amazon CloudWatch.
- AWS Secrets Manager.
- Amazon VPC.

Amazon CloudFront is a global service, but the Amazon S3 origin is deployed in the Singapore Region.

#### Required Tools

Install the following tools on your computer:

- Git.
- Visual Studio Code.
- Node.js or Python.
- AWS CLI version 2.
- AWS SAM CLI.
- Postman or another API testing tool.
- Google Chrome or Microsoft Edge.

Verify the installed tools by running:

```bash
aws --version
sam --version
node --version
npm --version
git --version
```

When using Python, run:

```bash
python --version
pip --version
```

#### Configure AWS CLI

Open Terminal or Command Prompt and run:

```bash
aws configure
```

Enter the following information:

```text
AWS Access Key ID: <ACCESS_KEY_ID>
AWS Secret Access Key: <SECRET_ACCESS_KEY>
Default region name: ap-southeast-1
Default output format: json
```

Do not store the Access Key or Secret Access Key in the source code, GitHub repository, or workshop documentation.

Verify the AWS identity currently being used:

```bash
aws sts get-caller-identity
```

Expected output:

```json
{
    "UserId": "EXAMPLEUSERID",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/cloudpay-workshop-user"
}
```

![Verify AWS CLI](/images/5-Workshop/5.2-Prerequisite/aws-cli-check.png)

#### IAM Permissions

The IAM User or IAM Role used to deploy this workshop requires permissions to work with:

- AWS CloudFormation.
- Amazon S3.
- Amazon CloudFront.
- Amazon Cognito.
- Amazon API Gateway.
- AWS Lambda.
- Amazon SQS.
- Amazon SNS.
- Amazon RDS.
- AWS RDS Proxy.
- AWS Secrets Manager.
- Amazon CloudWatch.
- AWS X-Ray.
- Amazon VPC.
- AWS WAF.
- AWS IAM.

The following is an example IAM policy for the workshop deployment account:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudPayInfrastructure",
            "Effect": "Allow",
            "Action": [
                "cloudformation:*",
                "apigateway:*",
                "lambda:*",
                "sqs:*",
                "sns:*",
                "rds:*",
                "secretsmanager:*",
                "cognito-idp:*",
                "cloudfront:*",
                "wafv2:*",
                "cloudwatch:*",
                "logs:*",
                "xray:*",
                "s3:*",
                "ec2:CreateVpc",
                "ec2:DeleteVpc",
                "ec2:CreateSubnet",
                "ec2:DeleteSubnet",
                "ec2:CreateRouteTable",
                "ec2:DeleteRouteTable",
                "ec2:AssociateRouteTable",
                "ec2:DisassociateRouteTable",
                "ec2:CreateRoute",
                "ec2:DeleteRoute",
                "ec2:CreateInternetGateway",
                "ec2:DeleteInternetGateway",
                "ec2:AttachInternetGateway",
                "ec2:DetachInternetGateway",
                "ec2:CreateSecurityGroup",
                "ec2:DeleteSecurityGroup",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:CreateTags",
                "ec2:DeleteTags",
                "ec2:Describe*"
            ],
            "Resource": "*"
        },
        {
            "Sid": "CloudPayIAMRoles",
            "Effect": "Allow",
            "Action": [
                "iam:CreateRole",
                "iam:DeleteRole",
                "iam:GetRole",
                "iam:PassRole",
                "iam:AttachRolePolicy",
                "iam:DetachRolePolicy",
                "iam:PutRolePolicy",
                "iam:DeleteRolePolicy",
                "iam:GetRolePolicy",
                "iam:TagRole",
                "iam:UntagRole"
            ],
            "Resource": "arn:aws:iam::*:role/CloudPay-*"
        }
    ]
}
```

{{% notice warning %}}
This policy should only be used in a workshop or learning account. In a production environment, permissions must be restricted to specific resources according to the principle of least privilege.
{{% /notice %}}

#### IAM Roles for Lambda Functions

Do not use one shared IAM Role for all Lambda functions.

Each Lambda function should have a separate IAM Role:

| Lambda function | Required permissions |
|---|---|
| Payment Lambda | Read payment gateway secrets and create initial transactions |
| Webhook Lambda | Send messages to the Transaction Queue |
| Worker Lambda | Read SQS messages, access the database, and publish to SNS |
| Admin API Lambda | Read transaction data and perform retry operations |
| Notification Lambda | Publish notifications to Amazon SNS |

Example permissions for Webhook Lambda:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SendTransactionMessage",
            "Effect": "Allow",
            "Action": [
                "sqs:SendMessage"
            ],
            "Resource": "arn:aws:sqs:ap-southeast-1:ACCOUNT_ID:cloudpay-transaction-queue"
        }
    ]
}
```

Example permissions for Worker Lambda:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ProcessTransactionMessages",
            "Effect": "Allow",
            "Action": [
                "sqs:ReceiveMessage",
                "sqs:DeleteMessage",
                "sqs:GetQueueAttributes"
            ],
            "Resource": "arn:aws:sqs:ap-southeast-1:ACCOUNT_ID:cloudpay-transaction-queue"
        },
        {
            "Sid": "PublishNotifications",
            "Effect": "Allow",
            "Action": [
                "sns:Publish"
            ],
            "Resource": "arn:aws:sns:ap-southeast-1:ACCOUNT_ID:cloudpay-notification-topic"
        },
        {
            "Sid": "ReadDatabaseSecret",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:ap-southeast-1:ACCOUNT_ID:secret:cloudpay/database-*"
        }
    ]
}
```

Replace `ACCOUNT_ID` with your AWS Account ID.

#### Payment Gateway Sandbox Information

To test the payment system, register for the Sandbox environment of at least one payment gateway:

- MoMo Sandbox.
- VNPay Sandbox.
- ZaloPay Sandbox.

The payment gateway normally provides the following information:

```text
PARTNER_CODE
MERCHANT_ID
ACCESS_KEY
SECRET_KEY
PAYMENT_ENDPOINT
RETURN_URL
IPN_URL
```

Do not store sensitive information directly in the source code.

Secrets should be stored in AWS Secrets Manager, for example:

```text
cloudpay/payment-gateway
cloudpay/database
```

Example payment gateway secret:

```json
{
    "partnerCode": "YOUR_PARTNER_CODE",
    "accessKey": "YOUR_ACCESS_KEY",
    "secretKey": "YOUR_SECRET_KEY",
    "paymentEndpoint": "SANDBOX_PAYMENT_ENDPOINT"
}
```

#### Prepare the Return URL and IPN URL

The payment gateway uses two main URLs:

- **Return URL**: Redirects the customer back to the application after payment.
- **IPN URL/Webhook URL**: Receives the transaction result directly from the payment gateway.

Example URLs:

```text
https://api-id.execute-api.ap-southeast-1.amazonaws.com/dev/payment/return
https://api-id.execute-api.ap-southeast-1.amazonaws.com/dev/payment/webhook
```

The IPN URL must be accessible from the Internet because the external payment gateway needs to send requests to this endpoint.

Webhook Lambda must validate the payment gateway signature before accepting the transaction.

#### Prepare the Database

Amazon RDS can use PostgreSQL or MySQL in this workshop.

Recommended configuration:

```text
Database Engine: PostgreSQL
Database Name: cloudpay
Master Username: cloudpay_admin
Port: 5432
Deployment: Single-AZ
Public Access: No
```

Do not hard-code the database password in the source code.

Store the connection information in AWS Secrets Manager:

```json
{
    "engine": "postgres",
    "host": "RDS_PROXY_ENDPOINT",
    "port": 5432,
    "dbname": "cloudpay",
    "username": "cloudpay_admin",
    "password": "YOUR_SECURE_PASSWORD"
}
```

{{% notice warning %}}
Amazon RDS and AWS RDS Proxy may generate charges even when the system has little or no transaction activity. Complete the Clean-Up section after finishing the workshop.
{{% /notice %}}

#### Prepare Alert Information

Amazon SNS is used to send notifications and system alerts.

Prepare an email address to receive:

- Lambda error alerts.
- API Gateway error alerts.
- Transaction Queue backlog alerts.
- DLQ message alerts.
- High database CPU alerts.
- Test transaction notifications.

Example:

```text
admin-email@example.com
```

After creating the SNS Subscription, open the confirmation email and select **Confirm subscription**.

#### Prepare Environment Variables

The Lambda functions use the following environment variables:

```text
ENVIRONMENT=dev
TRANSACTION_QUEUE_URL=<SQS_QUEUE_URL>
DLQ_URL=<DLQ_URL>
NOTIFICATION_TOPIC_ARN=<SNS_TOPIC_ARN>
DATABASE_SECRET_ARN=<DATABASE_SECRET_ARN>
PAYMENT_SECRET_ARN=<PAYMENT_GATEWAY_SECRET_ARN>
PAYMENT_RETURN_URL=<RETURN_URL>
PAYMENT_WEBHOOK_URL=<WEBHOOK_URL>
LOG_LEVEL=INFO
```

Do not store AWS Access Keys, Secret Access Keys, or database passwords as plain-text environment variables.

#### Recommended Source Code Structure

Create the following project directory structure:

```text
cloudpay-workshop/
├── frontend/
│   ├── customer-app/
│   └── admin-dashboard/
├── backend/
│   ├── payment-function/
│   ├── webhook-function/
│   ├── worker-function/
│   └── admin-api-function/
├── infrastructure/
│   └── template.yaml
├── tests/
├── docs/
└── README.md
```

Move to the project directory:

```bash
cd cloudpay-workshop
```

Validate the AWS SAM template:

```bash
sam validate --template-file infrastructure/template.yaml
```

Expected result:

```text
infrastructure/template.yaml is a valid SAM Template
```

Build the source code:

```bash
sam build --template-file infrastructure/template.yaml
```

![SAM Build](/images/5-Workshop/5.2-Prerequisite/sam-build.png)

#### Deploy Resources Using AWS SAM

Run the following command:

```bash
sam deploy --guided
```

Enter the following deployment information:

```text
Stack Name: cloudpay-workshop
AWS Region: ap-southeast-1
Parameter Environment: dev
Confirm changes before deploy: Y
Allow SAM CLI IAM role creation: Y
Disable rollback: N
Save arguments to configuration file: Y
```

After the first guided deployment, you can use:

```bash
sam deploy \
  --stack-name cloudpay-workshop \
  --region ap-southeast-1 \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
  --resolve-s3
```

The deployment process may take between 10 and 25 minutes because Amazon RDS, RDS Proxy, and Amazon CloudFront require additional initialization time.

![Deploy CloudPay Stack](/images/5-Workshop/5.2-Prerequisite/sam-deploy.png)

#### Verify the CloudFormation Stack

Open the AWS Management Console and navigate to:

```text
CloudFormation → Stacks → cloudpay-workshop
```

The stack should have the following status:

```text
CREATE_COMPLETE
```

![CloudFormation Complete](/images/5-Workshop/5.2-Prerequisite/cloudformation-complete.png)

Open the **Resources** tab to verify the created resources.

#### Expected Resources

After a successful deployment, the system may include:

- 01 Amazon VPC.
- 02 private subnets.
- A Security Group for Worker Lambda.
- A Security Group for Amazon RDS.
- 01 Amazon RDS database.
- 01 AWS RDS Proxy.
- 01 Amazon SQS Transaction Queue.
- 01 Amazon SQS Dead Letter Queue.
- 01 Amazon SNS Topic.
- 01 Amazon Cognito User Pool.
- 01 Amazon API Gateway.
- Payment Lambda.
- Webhook Lambda.
- Worker Lambda.
- Admin API Lambda.
- CloudWatch Log Groups.
- CloudWatch Alarms.
- An Amazon S3 Bucket for the frontend.
- An Amazon CloudFront Distribution.

The actual number of resources may vary depending on the implementation template.

#### Verify the Transaction Queue

Navigate to:

```text
Amazon SQS → Queues
```

Verify the following queues:

```text
cloudpay-transaction-queue
cloudpay-transaction-dlq
```

The Transaction Queue should have a Redrive Policy that moves messages to the DLQ after repeated processing failures.

![SQS Queues](/images/5-Workshop/5.2-Prerequisite/sqs-queues.png)

#### Verify the Lambda Functions

Navigate to:

```text
AWS Lambda → Functions
```

Verify the following functions:

```text
cloudpay-payment-function
cloudpay-webhook-function
cloudpay-worker-function
cloudpay-admin-api-function
```

![Lambda Functions](/images/5-Workshop/5.2-Prerequisite/lambda-functions.png)

#### Verify Amazon RDS and RDS Proxy

Navigate to:

```text
Amazon RDS → Databases
```

The database should have the following status:

```text
Available
```

Then navigate to:

```text
Amazon RDS → Proxies
```

The RDS Proxy should have the following status:

```text
Available
```

![RDS Proxy](/images/5-Workshop/5.2-Prerequisite/rds-proxy.png)

#### Expected Results

After completing the prerequisite section:

- AWS CLI is connected to the correct AWS account.
- The default Region is configured as `ap-southeast-1`.
- The IAM User or IAM Role has sufficient deployment permissions.
- A Payment Gateway Sandbox account has been registered.
- Payment gateway secrets have been stored securely.
- Amazon RDS is not publicly accessible.
- The Transaction Queue and DLQ have been created.
- The required Lambda functions have been deployed.
- The CloudFormation Stack has the `CREATE_COMPLETE` status.
- The SNS email subscription has been confirmed.
- The environment is ready for the next workshop steps.