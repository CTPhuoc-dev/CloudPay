---
title: "Worklog Tuần 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:

* Hoàn thiện và kiểm thử các thành phần chính của hệ thống CloudPay.
* Kiểm tra khả năng kết nối giữa frontend, Amazon Cognito, API Gateway và AWS Lambda.
* Theo dõi quá trình xử lý yêu cầu thông qua Amazon CloudWatch.
* Hoàn thiện nội dung hướng dẫn triển khai workshop.
* Ghi lại các lỗi phát sinh và cách khắc phục trong quá trình thực hiện.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Kiểm tra lại Customer Web và Admin Dashboard trên Amazon CloudFront.<br>- Kiểm tra khả năng truy cập các file frontend từ S3 thông qua CloudFront.<br>- Xác nhận S3 Bucket vẫn được giữ ở chế độ riêng tư.<br>- Thực hiện CloudFront Invalidation sau khi cập nhật giao diện. | 15/06/2026 | 15/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 3 | - Kiểm tra luồng đăng nhập của Customer và Admin bằng Amazon Cognito.<br>- Kiểm tra Callback URL và Sign-out URL.<br>- Xác nhận người dùng được thêm vào đúng Cognito Group.<br>- Ghi lại các lỗi liên quan đến domain, App Client và URL chuyển hướng. | 16/06/2026 | 16/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 4 | - Kiểm tra Payment API bằng `curl.exe`.<br>- Kiểm tra yêu cầu hợp lệ với `orderId` và `amount`.<br>- Kiểm tra các trường hợp thiếu dữ liệu hoặc dữ liệu không hợp lệ.<br>- Xác nhận API trả về mã giao dịch, trạng thái `PENDING` và đường dẫn thanh toán mô phỏng. | 17/06/2026 | 17/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 5 | - Kiểm tra kết nối giữa Customer Web và Payment API.<br>- Kiểm tra cấu hình CORS trên API Gateway.<br>- Theo dõi log thực thi của Lambda bằng Amazon CloudWatch.<br>- Kiểm tra lỗi trong trường hợp Lambda hoặc API Gateway không xử lý được yêu cầu. | 18/06/2026 | 18/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 6 | - Tổng hợp kết quả kiểm thử hệ thống CloudPay.<br>- Hoàn thiện nội dung workshop bằng Markdown.<br>- Bổ sung ảnh minh họa và các bước triển khai.<br>- Viết nội dung bằng tiếng Việt và tiếng Anh.<br>- Kiểm tra các tài nguyên AWS đang hoạt động để hạn chế phát sinh chi phí. | 19/06/2026 | 19/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |

### Kết quả đạt được tuần 9:

* Kiểm tra thành công hai giao diện:
  * Customer Web Application.
  * Admin Dashboard.
* Xác nhận nội dung frontend được phân phối thông qua Amazon CloudFront.
* Xác nhận các S3 Bucket không cho phép truy cập công khai trực tiếp.
* Kiểm tra thành công luồng đăng nhập và đăng xuất cơ bản bằng Amazon Cognito.
* Xác nhận người dùng Customer và Admin được thêm vào đúng Cognito Group.
* Kiểm tra thành công Payment API bằng `curl.exe`.
* Xác nhận API có thể:
  * Tiếp nhận `orderId`.
  * Tiếp nhận `amount`.
  * Kiểm tra dữ liệu đầu vào.
  * Tạo mã giao dịch duy nhất.
  * Trả về trạng thái `PENDING`.
  * Trả về đường dẫn thanh toán mô phỏng.
* Kết nối thành công Customer Web với Payment API.
* Kiểm tra và điều chỉnh cấu hình CORS trên Amazon API Gateway.
* Theo dõi được log thực thi Lambda bằng Amazon CloudWatch.
* Ghi lại một số lỗi và cách khắc phục liên quan đến:
  * CloudFront cache.
  * Cognito Callback URL.
  * API Gateway CORS.
  * Dữ liệu đầu vào của Lambda.
* Hoàn thiện thêm nội dung workshop CloudPay bằng tiếng Việt và tiếng Anh.
* Kiểm tra các tài nguyên AWS đang hoạt động để hạn chế phát sinh chi phí ngoài dự kiến.
* Hiểu rằng phiên bản CloudPay hiện tại là hệ thống mô phỏng cơ bản và chưa tích hợp cổng thanh toán thực tế.