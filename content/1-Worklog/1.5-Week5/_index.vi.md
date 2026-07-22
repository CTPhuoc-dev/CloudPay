---
title: "Worklog Tuần 5"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Tìm hiểu kiến trúc Monolithic, Microservices và Serverless.
* Xác định đề tài và phạm vi của dự án CloudPay.
* Thiết kế kiến trúc tổng quan cho hệ thống thanh toán đơn giản trên AWS.
* Lựa chọn các dịch vụ AWS phù hợp với chức năng của hệ thống.
* Xây dựng luồng xử lý thanh toán mô phỏng và kế hoạch triển khai workshop.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu sự khác nhau giữa Monolithic Architecture, Microservices Architecture và Serverless Architecture.<br>- Phân tích ưu điểm của mô hình Serverless đối với một hệ thống thanh toán nhỏ.<br>- Xác định tên đề tài **CloudPay – Hệ thống thanh toán trên AWS**. | 18/05/2026 | 18/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| 3 | - Xác định các đối tượng sử dụng hệ thống gồm Customer và Admin.<br>- Xác định các chức năng chính:<br>&emsp; + Đăng nhập người dùng<br>&emsp; + Gửi yêu cầu thanh toán<br>&emsp; + Tạo thông tin giao dịch<br>&emsp; + Kiểm tra trạng thái và log xử lý | 19/05/2026 | 19/05/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 4 | - Thiết kế kiến trúc tổng quan cho dự án CloudPay.<br>- Lựa chọn các dịch vụ AWS:<br>&emsp; + Amazon S3 lưu trữ frontend<br>&emsp; + Amazon CloudFront phân phối nội dung<br>&emsp; + Amazon Cognito xác thực người dùng<br>&emsp; + Amazon API Gateway tiếp nhận yêu cầu thanh toán<br>&emsp; + AWS Lambda xử lý yêu cầu<br>&emsp; + Amazon CloudWatch theo dõi log<br>&emsp; + AWS IAM quản lý quyền truy cập | 20/05/2026 | 20/05/2026 | https://cloudjourney.awsstudygroup.com/<br>Tài liệu AWS |
| 5 | - Thiết kế luồng thanh toán mô phỏng.<br>- Xác định API `POST /payments`.<br>- Xác định dữ liệu đầu vào gồm `orderId` và `amount`.<br>- Thiết kế kết quả trả về gồm mã giao dịch, trạng thái `PENDING` và đường dẫn thanh toán mô phỏng.<br>- Tìm hiểu cách sử dụng Amazon Cognito Groups cho Customer và Admin. | 21/05/2026 | 21/05/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 6 | - Hoàn thiện sơ đồ kiến trúc và nội dung Proposal.<br>- Xây dựng kế hoạch triển khai workshop theo từng phần.<br>- Xác định các nội dung cần kiểm tra như bảo mật S3, quyền IAM, CORS và CloudWatch Logs.<br>- Tìm hiểu cách dọn dẹp tài nguyên để tránh phát sinh chi phí. | 22/05/2026 | 22/05/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |

### Kết quả đạt được tuần 5:

* Hiểu được sự khác nhau cơ bản giữa:
  * Monolithic Architecture
  * Microservices Architecture
  * Serverless Architecture
* Xác định được đề tài **CloudPay – Hệ thống thanh toán trên AWS**.
* Xác định được hai nhóm người dùng chính:
  * Customer
  * Admin
* Xác định được các chức năng cơ bản của hệ thống:
  * Xác thực và đăng nhập người dùng.
  * Gửi yêu cầu thanh toán.
  * Tạo thông tin giao dịch.
  * Trả về trạng thái thanh toán mô phỏng.
  * Theo dõi quá trình xử lý thông qua log.
* Hoàn thành kiến trúc tổng quan sử dụng:
  * Amazon S3
  * Amazon CloudFront
  * Amazon Cognito
  * Amazon API Gateway
  * AWS Lambda
  * Amazon CloudWatch
  * AWS IAM
* Thiết kế được API `POST /payments` cho chức năng tạo yêu cầu thanh toán.
* Xác định được cấu trúc dữ liệu đầu vào và kết quả trả về của API.
* Hoàn thành luồng xử lý thanh toán mô phỏng với trạng thái ban đầu là `PENDING`.
* Hoàn thành nội dung Proposal và kế hoạch xây dựng workshop.
* Hiểu rằng phiên bản hiện tại chỉ mô phỏng quá trình thanh toán và chưa tích hợp cổng thanh toán thực tế.