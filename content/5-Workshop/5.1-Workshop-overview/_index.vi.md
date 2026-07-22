---
title: "Giới thiệu"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Giới thiệu về hệ thống thanh toán trên AWS

Hệ thống thanh toán trực tuyến là một trong những thành phần quan trọng của các nền tảng thương mại điện tử, đặt vé, đặt hàng và cung cấp dịch vụ trực tuyến.

Một hệ thống thanh toán cần đảm bảo các yêu cầu chính:

- Tiếp nhận và xử lý giao dịch nhanh chóng.
- Không làm mất giao dịch khi lưu lượng truy cập tăng cao.
- Ngăn chặn việc xử lý trùng lặp Webhook.
- Bảo vệ cơ sở dữ liệu trước số lượng lớn kết nối đồng thời.
- Theo dõi trạng thái giao dịch và xử lý các giao dịch thất bại.
- Gửi thông báo kết quả thanh toán cho người dùng.
- Cung cấp chức năng quản trị, báo cáo và xử lý lỗi cho Admin.

Trong dự án này, hệ thống được xây dựng theo kiến trúc **Serverless** kết hợp **Event-Driven Architecture** trên AWS.

Các yêu cầu thanh toán được tiếp nhận thông qua Amazon API Gateway và xử lý bởi AWS Lambda. Sau khi cổng thanh toán gửi IPN hoặc Webhook về hệ thống, giao dịch hợp lệ sẽ được đưa vào Amazon SQS để xử lý bất đồng bộ.

Worker Lambda đọc giao dịch từ hàng đợi và ghi dữ liệu vào Amazon RDS thông qua AWS RDS Proxy. Cách triển khai này giúp giảm áp lực lên cơ sở dữ liệu và hạn chế tình trạng cạn kết nối khi nhiều Lambda chạy đồng thời.

Các giao dịch xử lý thất bại nhiều lần sẽ được chuyển vào Amazon SQS Dead Letter Queue. Admin có thể theo dõi, kiểm tra và thực hiện xử lý lại các giao dịch lỗi.

#### Tổng quan về workshop

Trong workshop này, bạn sẽ xây dựng và tìm hiểu kiến trúc của hệ thống thanh toán trực tuyến trên AWS.

Hệ thống gồm hai nhóm người dùng chính:

- **Customer**: Người dùng thực hiện đăng nhập, tạo yêu cầu thanh toán và theo dõi kết quả giao dịch.
- **Admin**: Người quản trị theo dõi giao dịch, xem báo cáo, kiểm tra lỗi và thực hiện retry các giao dịch trong DLQ.

Luồng hoạt động chính của hệ thống gồm:

1. Người dùng truy cập ứng dụng thông qua AWS WAF và Amazon CloudFront.
2. Giao diện Web Application và Admin Dashboard được lưu trữ trên Amazon S3.
3. Người dùng và Admin đăng nhập thông qua Amazon Cognito.
4. Frontend gửi yêu cầu thanh toán đến Amazon API Gateway.
5. Payment Lambda kiểm tra thông tin đơn hàng và tạo Payment URL.
6. Người dùng được chuyển đến cổng thanh toán như MoMo, VNPay hoặc ZaloPay.
7. Sau khi thanh toán, cổng thanh toán gửi IPN/Webhook về hệ thống.
8. Webhook Lambda xác thực chữ ký, số tiền và trạng thái giao dịch.
9. Giao dịch hợp lệ được gửi vào Amazon SQS Transaction Queue.
10. Worker Lambda đọc message và xử lý giao dịch.
11. Dữ liệu được ghi vào Amazon RDS thông qua AWS RDS Proxy.
12. Amazon SNS gửi Email, SMS hoặc sự kiện đến các dịch vụ liên quan.
13. Giao dịch xử lý thất bại được chuyển vào Amazon SQS DLQ.
14. Admin theo dõi giao dịch lỗi và thực hiện retry khi cần thiết.

Ngoài luồng xử lý giao dịch, workshop còn sử dụng:

- **Amazon CloudWatch** để thu thập log, metric và tạo cảnh báo.
- **AWS X-Ray** để truy vết request giữa các dịch vụ.
- **Amazon SNS** để gửi cảnh báo Email hoặc SMS cho Admin.
- **AWS IAM** để cấp quyền theo nguyên tắc Least Privilege.
- **Amazon VPC** để cô lập Worker Lambda, RDS Proxy và Amazon RDS trong private subnet.

Sau khi hoàn thành workshop, bạn sẽ hiểu cách:

- Xây dựng API thanh toán bằng API Gateway và Lambda.
- Xác thực người dùng bằng Amazon Cognito.
- Xử lý giao dịch bất đồng bộ với Amazon SQS.
- Cấu hình Dead Letter Queue và cơ chế retry.
- Kết nối Lambda với Amazon RDS thông qua RDS Proxy.
- Gửi thông báo bằng Amazon SNS.
- Theo dõi log và metric bằng Amazon CloudWatch.
- Truy vết lỗi bằng AWS X-Ray.
- Xây dựng chức năng quản trị dành cho Admin.
- Clean-up tài nguyên để tránh phát sinh chi phí.

![Tổng quan hệ thống thanh toán trên AWS](/images/5-Workshop/5.1-Workshop-overview/payment_architecture.png)