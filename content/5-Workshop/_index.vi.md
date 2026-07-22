---
title: "Workshop"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng hệ thống thanh toán CloudPay trên AWS

#### Tổng quan

Trong workshop này, bạn sẽ xây dựng một hệ thống thanh toán đơn giản có tên **CloudPay** trên nền tảng AWS.

Hệ thống cho phép:

- Customer đăng nhập vào hệ thống.
- Customer gửi yêu cầu thanh toán.
- Admin đăng nhập vào trang quản trị.
- Amazon API Gateway tiếp nhận yêu cầu.
- AWS Lambda xử lý giao dịch.
- Amazon CloudWatch lưu log thực thi.

Kiến trúc tổng quát:

```text
Customer hoặc Admin
          ↓
Amazon CloudFront
          ↓
Amazon S3
          ↓
Amazon Cognito
          ↓
Amazon API Gateway
          ↓
AWS Lambda
          ↓
Payment URL mô phỏng
```

Các dịch vụ AWS được sử dụng:

- **Amazon S3**: Lưu trữ Customer Web Application và Admin Dashboard.
- **Amazon CloudFront**: Phân phối nội dung frontend.
- **Amazon Cognito**: Quản lý tài khoản và đăng nhập.
- **Amazon API Gateway**: Tiếp nhận yêu cầu thanh toán.
- **AWS Lambda**: Xử lý yêu cầu và tạo giao dịch.
- **Amazon CloudWatch**: Theo dõi log thực thi.
- **AWS IAM**: Quản lý quyền truy cập giữa các dịch vụ.

{{% notice note %}}
Workshop sử dụng Payment URL mô phỏng và không thực hiện giao dịch tiền thật.
{{% /notice %}}

#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequisite/)
3. [Triển khai Frontend với S3 và CloudFront](5.3-S3-vpc/)
4. [Xác thực người dùng với Amazon Cognito](5.4-S3-onprem/)
5. [Tạo Payment API](5.5-Policy/)

#### Kết quả mong đợi

Sau khi hoàn thành workshop:

- Customer Web Application được triển khai trên AWS.
- Admin Dashboard được triển khai trên AWS.
- Người dùng có thể đăng nhập bằng Amazon Cognito.
- API Gateway có thể nhận yêu cầu thanh toán.
- Lambda có thể tạo Transaction ID và Payment URL mô phỏng.
- Log thực thi có thể được kiểm tra trong CloudWatch.
- Các tài nguyên có thể được dọn dẹp sau khi hoàn thành.