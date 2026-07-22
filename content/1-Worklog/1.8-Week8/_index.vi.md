---
title: "Worklog Tuần 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

* Xây dựng API thanh toán cơ bản cho hệ thống CloudPay.
* Tìm hiểu cách AWS Lambda xử lý yêu cầu mà không cần quản lý máy chủ.
* Tạo HTTP API bằng Amazon API Gateway.
* Kết nối API Gateway với Lambda thông qua route `POST /payments`.
* Kiểm tra API, cấu hình CORS và theo dõi log bằng Amazon CloudWatch.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu vai trò của AWS Lambda và Amazon API Gateway.<br>- Xác định chức năng của Payment API.<br>- Thiết kế dữ liệu đầu vào gồm `orderId` và `amount`.<br>- Xác định dữ liệu trả về gồm mã giao dịch, trạng thái và đường dẫn thanh toán mô phỏng. | 08/06/2026 | 08/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 3 | - Tạo Lambda Function `cloudpay-create-payment`.<br>- Viết mã xử lý yêu cầu thanh toán bằng Python.<br>- Kiểm tra dữ liệu `orderId` và `amount`.<br>- Tạo mã giao dịch duy nhất và trạng thái ban đầu `PENDING`. | 09/06/2026 | 09/06/2026 | https://cloudjourney.awsstudygroup.com/<br>Tài liệu AWS |
| 4 | - Tạo HTTP API bằng Amazon API Gateway.<br>- Tạo route `POST /payments`.<br>- Kết nối route với Lambda Function.<br>- Tạo stage `$default` và lấy Invoke URL của API. | 10/06/2026 | 10/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 5 | - Cấu hình CORS cho HTTP API.<br>- Cho phép các website CloudFront gửi yêu cầu đến API.<br>- Kiểm tra API bằng lệnh `curl.exe` trên Windows.<br>- Kiểm tra trường hợp dữ liệu hợp lệ và dữ liệu không hợp lệ. | 11/06/2026 | 11/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 6 | - Kết nối Customer Web với Payment API.<br>- Thêm chức năng gửi `orderId` và `amount` đến API.<br>- Hiển thị mã giao dịch và trạng thái thanh toán trên giao diện.<br>- Kiểm tra log thực thi Lambda bằng Amazon CloudWatch.<br>- Ghi lại lỗi và cách khắc phục trong quá trình kiểm thử. | 12/06/2026 | 12/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |

### Kết quả đạt được tuần 8:

* Hiểu được vai trò của AWS Lambda trong việc xử lý logic phía backend.
* Hiểu được vai trò của Amazon API Gateway trong việc tiếp nhận và định tuyến yêu cầu API.
* Tạo thành công Lambda Function `cloudpay-create-payment`.
* Xây dựng chức năng:
  * Kiểm tra `orderId`.
  * Kiểm tra `amount`.
  * Tạo mã giao dịch duy nhất.
  * Trả về trạng thái ban đầu `PENDING`.
  * Tạo đường dẫn thanh toán mô phỏng.
* Tạo thành công HTTP API cho hệ thống CloudPay.
* Tạo và kiểm tra route `POST /payments`.
* Kết nối thành công Amazon API Gateway với AWS Lambda.
* Cấu hình CORS để frontend có thể gửi yêu cầu đến API.
* Kiểm tra API thành công bằng `curl.exe`.
* Kết nối Customer Web với Payment API.
* Hiển thị được thông tin giao dịch trên giao diện sau khi gửi yêu cầu.
* Kiểm tra được log thực thi của Lambda trong Amazon CloudWatch.
* Hiểu rằng Payment API hiện chỉ mô phỏng quá trình tạo giao dịch và chưa kết nối với cổng thanh toán thực tế.
* Hiểu rằng route API trong phiên bản cơ bản hiện chưa được bảo vệ hoàn chỉnh bằng JWT Authorizer.