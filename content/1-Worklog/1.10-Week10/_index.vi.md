---
title: "Worklog Tuần 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:

* Hoàn thiện hệ thống CloudPay và nội dung workshop hướng dẫn triển khai.
* Kiểm tra lại toàn bộ luồng hoạt động của frontend, xác thực người dùng và Payment API.
* Hoàn thiện nội dung workshop bằng tiếng Việt và tiếng Anh.
* Xây dựng website báo cáo thực tập bằng Hugo.
* Kiểm tra và dọn dẹp các tài nguyên AWS để hạn chế phát sinh chi phí.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Kiểm tra lại kiến trúc tổng quan của hệ thống CloudPay.<br>- Kiểm tra Customer Web và Admin Dashboard trên CloudFront.<br>- Xác nhận các S3 Bucket vẫn được giữ ở chế độ riêng tư.<br>- Kiểm tra Bucket Policy và Origin Access Control. | 22/06/2026 | 22/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 3 | - Kiểm tra lại Amazon Cognito User Pool.<br>- Kiểm tra App Client dành cho Customer và Admin.<br>- Kiểm tra Callback URL, Sign-out URL và Cognito Groups.<br>- Thử lại luồng đăng nhập và đăng xuất của người dùng. | 23/06/2026 | 23/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 4 | - Kiểm tra Lambda Function `cloudpay-create-payment`.<br>- Kiểm tra route `POST /payments` trên Amazon API Gateway.<br>- Kiểm tra dữ liệu đầu vào gồm `orderId` và `amount`.<br>- Kiểm tra kết quả trả về gồm mã giao dịch, trạng thái `PENDING` và đường dẫn thanh toán mô phỏng.<br>- Theo dõi log bằng Amazon CloudWatch. | 24/06/2026 | 24/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 5 | - Hoàn thiện nội dung workshop CloudPay.<br>- Viết các phần hướng dẫn về S3, CloudFront, Cognito, API Gateway, Lambda và CloudWatch.<br>- Bổ sung ảnh minh họa và các bước kiểm tra.<br>- Hoàn thiện nội dung bằng tiếng Việt và tiếng Anh. | 25/06/2026 | 25/06/2026 | Tài liệu AWS<br>Tài liệu Hugo<br>Tài liệu chương trình FCAJ |
| 6 | - Cập nhật nội dung workshop lên website báo cáo thực tập sử dụng Hugo.<br>- Kiểm tra menu, đường dẫn và nội dung Markdown.<br>- Kiểm tra hiển thị website trên máy tính cục bộ.<br>- Dọn dẹp các tài nguyên AWS không còn sử dụng để hạn chế phát sinh chi phí. | 26/06/2026 | 26/06/2026 | Tài liệu Hugo<br>Tài liệu AWS<br>Tài liệu chương trình FCAJ |

### Kết quả đạt được tuần 10:

* Hoàn thiện kiến trúc cơ bản của hệ thống **CloudPay – Hệ thống thanh toán trên AWS**.
* Kiểm tra thành công hai giao diện:
  * Customer Web Application.
  * Admin Dashboard.
* Xác nhận frontend được phân phối thông qua Amazon CloudFront.
* Xác nhận các S3 Bucket được giữ ở chế độ riêng tư và chỉ cho phép CloudFront truy cập.
* Kiểm tra thành công luồng đăng nhập và đăng xuất cơ bản bằng Amazon Cognito.
* Xác nhận người dùng được phân chia thành hai nhóm:
  * Customer.
  * Admin.
* Kiểm tra thành công Payment API với route `POST /payments`.
* Xác nhận Lambda có thể:
  * Kiểm tra `orderId`.
  * Kiểm tra `amount`.
  * Tạo mã giao dịch duy nhất.
  * Trả về trạng thái `PENDING`.
  * Trả về đường dẫn thanh toán mô phỏng.
* Theo dõi được quá trình thực thi Lambda thông qua Amazon CloudWatch Logs.
* Hoàn thiện nội dung workshop gồm các phần:
  * Tổng quan workshop.
  * Chuẩn bị môi trường.
  * Triển khai frontend với S3 và CloudFront.
  * Xác thực người dùng bằng Amazon Cognito.
  * Xây dựng Payment API bằng API Gateway và Lambda.
  * Dọn dẹp tài nguyên.
* Hoàn thiện nội dung workshop bằng tiếng Việt và tiếng Anh.
* Cập nhật nội dung lên website báo cáo thực tập sử dụng Hugo.
* Kiểm tra menu, đường dẫn và định dạng Markdown trên website.
* Dọn dẹp các tài nguyên AWS không còn sử dụng để hạn chế phát sinh chi phí.
* Hiểu rằng phiên bản CloudPay hiện tại là mô hình học tập cơ bản, chưa tích hợp cổng thanh toán thực tế và chưa triển khai đầy đủ các tính năng bảo mật nâng cao.