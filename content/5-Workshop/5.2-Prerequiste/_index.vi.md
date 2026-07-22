---
title: "Các bước chuẩn bị"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---
#### Tổng quan phần chuẩn bị

Trước khi triển khai hệ thống CloudPay, bạn cần chuẩn bị tài khoản AWS, công cụ phát triển, quyền IAM và thông tin thử nghiệm của cổng thanh toán.

Trong workshop này, chúng ta sẽ triển khai các thành phần chính:

- Amazon S3 và Amazon CloudFront cho Web Application và Admin Dashboard.
- Amazon Cognito để xác thực Customer và Admin.
- Amazon API Gateway để cung cấp API.
- AWS Lambda để xử lý thanh toán, Webhook, Worker và Admin API.
- Amazon SQS để xử lý giao dịch bất đồng bộ.
- Amazon SQS Dead Letter Queue để lưu giao dịch xử lý thất bại.
- Amazon RDS và AWS RDS Proxy để lưu trữ dữ liệu giao dịch.
- Amazon SNS để gửi thông báo và cảnh báo.
- Amazon CloudWatch và AWS X-Ray để giám sát hệ thống.
- Amazon VPC, private subnet và Security Group để bảo vệ database.
#### Tài khoản AWS

Bạn cần có một tài khoản AWS đang hoạt động. Khuyến nghị sử dụng tài khoản AWS dành riêng cho môi trường học tập hoặc workshop để tránh ảnh hưởng đến các hệ thống khác.

Trước khi bắt đầu, cần kiểm tra:

- Tài khoản AWS có quyền tạo và xóa tài nguyên.
- Đã bật xác thực đa yếu tố MFA.
- Không sử dụng tài khoản root để thực hiện workshop.
- Đã tạo AWS Budget để kiểm soát chi phí.
- Đã kiểm tra các dịch vụ có thể phát sinh phí.

#### Region triển khai

Trong workshop này, chúng ta sử dụng AWS Region Singapore:

```text
ap-southeast-1

## Phần 4 — Công cụ và AWS CLI

````markdown
#### Công cụ cần thiết

Cài đặt các công cụ sau trên máy tính:

- Git.
- Visual Studio Code.
- Node.js hoặc Python.
- AWS CLI phiên bản 2.
- AWS SAM CLI.
- Postman hoặc công cụ kiểm thử API.
- Google Chrome hoặc Microsoft Edge.

Kiểm tra các công cụ bằng lệnh:

```bash
aws --version
sam --version
node --version
npm --version
git --version

## Phần 5 — IAM permissions

````markdown
#### IAM permissions

IAM User hoặc IAM Role dùng để triển khai workshop cần có quyền làm việc với:

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

Ví dụ IAM Policy dành cho tài khoản triển khai workshop:

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
Policy trên chỉ nên sử dụng trong tài khoản workshop hoặc môi trường học tập. Trong môi trường production, cần giới hạn quyền theo từng tài nguyên và áp dụng nguyên tắc Least Privilege.
{{% /notice %}}

#### IAM Role cho các Lambda function

Không sử dụng chung một IAM Role cho tất cả Lambda function.

| Lambda function | Quyền cần thiết |
|---|---|
| Payment Lambda | Đọc secret cổng thanh toán và tạo giao dịch ban đầu |
| Webhook Lambda | Gửi message vào Transaction Queue |
| Worker Lambda | Đọc SQS, truy cập database và publish SNS |
| Admin API Lambda | Đọc giao dịch và thực hiện retry |
| Notification Lambda | Publish thông báo đến SNS |

Ví dụ quyền của Webhook Lambda:

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

Ví dụ quyền của Worker Lambda:

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

Thay `ACCOUNT_ID` bằng AWS Account ID của bạn.
#### Thông tin cổng thanh toán Sandbox

Để kiểm thử hệ thống, bạn cần đăng ký môi trường Sandbox của ít nhất một cổng thanh toán:

- MoMo Sandbox.
- VNPay Sandbox.
- ZaloPay Sandbox.

Các thông tin thường được cung cấp gồm:

```text
PARTNER_CODE
MERCHANT_ID
ACCESS_KEY
SECRET_KEY
PAYMENT_ENDPOINT
RETURN_URL
IPN_URL
```

Không lưu trực tiếp các thông tin bí mật trong source code.

Các secret nên được lưu trong AWS Secrets Manager, ví dụ:

```text
cloudpay/payment-gateway
cloudpay/database
```

Ví dụ nội dung secret của cổng thanh toán:

```json
{
    "partnerCode": "YOUR_PARTNER_CODE",
    "accessKey": "YOUR_ACCESS_KEY",
    "secretKey": "YOUR_SECRET_KEY",
    "paymentEndpoint": "SANDBOX_PAYMENT_ENDPOINT"
}
```

#### Chuẩn bị Return URL và IPN URL

Cổng thanh toán sử dụng hai địa chỉ chính:

- **Return URL**: Đưa người dùng trở lại ứng dụng sau khi thanh toán.
- **IPN URL/Webhook URL**: Nhận kết quả giao dịch trực tiếp từ cổng thanh toán.

Ví dụ:

```text
https://api-id.execute-api.ap-southeast-1.amazonaws.com/dev/payment/return
https://api-id.execute-api.ap-southeast-1.amazonaws.com/dev/payment/webhook
```

IPN URL phải có thể truy cập từ Internet vì cổng thanh toán bên ngoài AWS cần gửi request đến địa chỉ này.

Webhook Lambda phải kiểm tra chữ ký trước khi chấp nhận giao dịch.
#### Chuẩn bị thông tin database

Trong workshop, Amazon RDS có thể sử dụng PostgreSQL hoặc MySQL.

Cấu hình đề xuất:

```text
Database Engine: PostgreSQL
Database Name: cloudpay
Master Username: cloudpay_admin
Port: 5432
Deployment: Single-AZ
Public Access: No
```

Không hard-code mật khẩu database trong source code.

Thông tin kết nối được lưu trong AWS Secrets Manager:

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
Amazon RDS và AWS RDS Proxy có thể phát sinh chi phí ngay cả khi hệ thống có ít giao dịch. Hãy thực hiện phần Clean-up sau khi kết thúc workshop.
{{% /notice %}}

#### Chuẩn bị thông tin nhận cảnh báo

Amazon SNS được sử dụng để gửi thông báo và cảnh báo.

Chuẩn bị một địa chỉ email để nhận:

- Cảnh báo Lambda error.
- Cảnh báo API Gateway error.
- Cảnh báo Transaction Queue tồn đọng.
- Cảnh báo DLQ có message mới.
- Cảnh báo database CPU cao.
- Thông báo giao dịch thử nghiệm.

Ví dụ:

```text
admin-email@example.com
```

Sau khi tạo SNS Subscription, mở email và chọn **Confirm subscription**.

#### Chuẩn bị biến môi trường

Các Lambda function sử dụng các biến môi trường sau:

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

Không lưu AWS Access Key, Secret Access Key hoặc mật khẩu database trong biến môi trường dưới dạng plain text.
#### Cấu trúc source code đề xuất

Tạo cấu trúc thư mục dự án:

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

Di chuyển vào thư mục dự án:

```bash
cd cloudpay-workshop
```

Kiểm tra file AWS SAM template:

```bash
sam validate --template-file infrastructure/template.yaml
```

Kết quả mong đợi:

```text
infrastructure/template.yaml is a valid SAM Template
```

Build source code:

```bash
sam build --template-file infrastructure/template.yaml
```

![SAM Build](/images/5-Workshop/5.2-Prerequisite/sam-build.png)
#### Triển khai tài nguyên bằng AWS SAM

Chạy lệnh:

```bash
sam deploy --guided
```

Nhập các thông tin:

```text
Stack Name: cloudpay-workshop
AWS Region: ap-southeast-1
Parameter Environment: dev
Confirm changes before deploy: Y
Allow SAM CLI IAM role creation: Y
Disable rollback: N
Save arguments to configuration file: Y
```

Sau lần triển khai đầu tiên, có thể sử dụng:

```bash
sam deploy \
  --stack-name cloudpay-workshop \
  --region ap-southeast-1 \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
  --resolve-s3
```

Quá trình triển khai có thể mất từ 10 đến 25 phút vì Amazon RDS, RDS Proxy và CloudFront cần thời gian khởi tạo.

![Triển khai CloudPay Stack](/images/5-Workshop/5.2-Prerequisite/sam-deploy.png)

#### Kiểm tra CloudFormation Stack

Truy cập:

```text
CloudFormation → Stacks → cloudpay-workshop
```

Stack cần có trạng thái:

```text
CREATE_COMPLETE
```

![CloudFormation Complete](/images/5-Workshop/5.2-Prerequisite/cloudformation-complete.png)

Mở tab **Resources** để kiểm tra các tài nguyên đã được tạo.
#### Các tài nguyên dự kiến được tạo

Sau khi triển khai thành công, hệ thống có thể gồm:

- 01 Amazon VPC.
- 02 private subnet.
- Security Group cho Worker Lambda.
- Security Group cho Amazon RDS.
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
- Amazon S3 Bucket cho frontend.
- Amazon CloudFront Distribution.

Số lượng tài nguyên có thể thay đổi tùy theo template triển khai thực tế.

#### Kiểm tra Transaction Queue

Truy cập:

```text
Amazon SQS → Queues
```

Kiểm tra hai queue:

```text
cloudpay-transaction-queue
cloudpay-transaction-dlq
```

Transaction Queue cần có Redrive Policy để chuyển message sang DLQ sau khi xử lý thất bại nhiều lần.

![SQS Queues](/images/5-Workshop/5.2-Prerequisite/sqs-queues.png)

#### Kiểm tra Lambda function

Truy cập:

```text
AWS Lambda → Functions
```

Kiểm tra các function:

```text
cloudpay-payment-function
cloudpay-webhook-function
cloudpay-worker-function
cloudpay-admin-api-function
```

![Lambda Functions](/images/5-Workshop/5.2-Prerequisite/lambda-functions.png)

#### Kiểm tra Amazon RDS và RDS Proxy

Truy cập:

```text
Amazon RDS → Databases
```

Database cần có trạng thái:

```text
Available
```

Sau đó truy cập:

```text
Amazon RDS → Proxies
```

RDS Proxy cần có trạng thái:

```text
Available
```

![RDS Proxy](/images/5-Workshop/5.2-Prerequisite/rds-proxy.png)

#### Kết quả mong đợi

Sau khi hoàn thành phần chuẩn bị:

- AWS CLI đã kết nối đúng tài khoản.
- Region được cấu hình là `ap-southeast-1`.
- IAM User hoặc IAM Role có đủ quyền triển khai.
- Payment Gateway Sandbox đã được đăng ký.
- Secret của cổng thanh toán đã được lưu an toàn.
- Amazon RDS không được public.
- Transaction Queue và DLQ đã được tạo.
- Các Lambda function đã được triển khai.
- CloudFormation Stack có trạng thái `CREATE_COMPLETE`.
- Email SNS Subscription đã được xác nhận.
- Hệ thống sẵn sàng cho các bước thực hành tiếp theo.