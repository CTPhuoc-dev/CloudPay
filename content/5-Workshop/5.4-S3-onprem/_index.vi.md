---
title: "Xác thực người dùng với Amazon Cognito"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Tổng quan

Trong phần này, bạn sẽ sử dụng Amazon Cognito để tạo chức năng đăng nhập cho hệ thống CloudPay.

CloudPay có hai loại người dùng:

- **Customer**: Thực hiện thanh toán và xem lịch sử giao dịch.
- **Admin**: Theo dõi và quản lý các giao dịch.

Amazon Cognito sẽ quản lý tài khoản người dùng, mật khẩu và quá trình đăng nhập.

Luồng xác thực:

```text
Customer hoặc Admin
        ↓
Customer Web hoặc Admin Dashboard
        ↓
Amazon Cognito
        ↓
Đăng nhập thành công
        ↓
Chuyển về CloudFront
```

Trong phần này, bạn sẽ:

- Tạo Cognito User Pool.
- Tạo App Client cho Customer và Admin.
- Tạo nhóm `Customer` và `Admin`.
- Tạo tài khoản kiểm thử.
- Kết nối trang đăng nhập Cognito với frontend.
- Kiểm tra đăng nhập và đăng xuất.
- Chuẩn bị xác thực cho API Gateway.

![CloudPay Cognito Authentication](/images/5-Workshop/5.4-S3-onprem/diagram3.png)

#### Nội dung

- [Chuẩn bị tài nguyên xác thực](5.4.1-prepare/)
- [Kết nối Cognito với Frontend](5.4.2-create-interface-endpoint/)
- [Kiểm tra đăng nhập Cognito](5.4.3-test-endpoint/)
- [Tích hợp Cognito với API Gateway](5.4.4-dns-simulation/)