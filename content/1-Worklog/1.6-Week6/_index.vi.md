---
title: "Worklog Tuần 6"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

* Bắt đầu triển khai dự án **CloudPay – Hệ thống thanh toán trên AWS**.
* Xây dựng giao diện cơ bản cho Customer Web và Admin Dashboard.
* Tìm hiểu cách lưu trữ website tĩnh bằng Amazon S3.
* Tìm hiểu cách phân phối nội dung bằng Amazon CloudFront.
* Đảm bảo các S3 Bucket không cho phép truy cập công khai trực tiếp.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Xác định giao diện cần xây dựng cho hệ thống CloudPay.<br>- Tạo giao diện HTML cơ bản cho Customer Web.<br>- Tạo giao diện HTML cơ bản cho Admin Dashboard.<br>- Kiểm tra giao diện trên trình duyệt cục bộ. | 25/05/2026 | 25/05/2026 | Tài liệu HTML, CSS<br>Tài liệu chương trình FCAJ |
| 3 | - Tìm hiểu Amazon S3 và chức năng lưu trữ website tĩnh.<br>- Tạo hai S3 Bucket cho Customer Web và Admin Dashboard.<br>- Bật Block Public Access.<br>- Cấu hình Bucket Owner Enforced và mã hóa SSE-S3. | 26/05/2026 | 26/05/2026 | https://cloudjourney.awsstudygroup.com/<br>Tài liệu AWS |
| 4 | - Tải các file `index.html` lên hai S3 Bucket.<br>- Kiểm tra danh sách object trong Bucket.<br>- Thử truy cập trực tiếp file trên S3 và xác nhận hệ thống trả về lỗi `AccessDenied` do Bucket đang ở chế độ riêng tư. | 27/05/2026 | 27/05/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 5 | - Tìm hiểu Amazon CloudFront và Origin Access Control.<br>- Tạo CloudFront Distribution cho Customer Web.<br>- Tạo CloudFront Distribution cho Admin Dashboard.<br>- Cấu hình S3 Bucket làm Origin và đặt `index.html` làm Default Root Object. | 28/05/2026 | 28/05/2026 | https://cloudjourney.awsstudygroup.com/<br>Tài liệu AWS |
| 6 | - Cập nhật Bucket Policy để chỉ cho phép CloudFront truy cập dữ liệu trong S3.<br>- Kiểm tra hai website thông qua CloudFront Domain Name.<br>- Thực hiện CloudFront Invalidation sau khi cập nhật nội dung.<br>- Ghi lại lỗi và cách khắc phục trong quá trình triển khai. | 29/05/2026 | 29/05/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |

### Kết quả đạt được tuần 6:

* Hoàn thành giao diện cơ bản cho:
  * Customer Web Application.
  * Admin Dashboard.
* Tạo thành công hai S3 Bucket để lưu trữ nội dung frontend.
* Cấu hình bảo mật cho S3 Bucket:
  * Block Public Access.
  * Bucket Owner Enforced.
  * Mã hóa SSE-S3.
* Tải thành công các file frontend lên Amazon S3.
* Hiểu rằng S3 Bucket riêng tư không thể được truy cập trực tiếp từ Internet.
* Tạo thành công hai Amazon CloudFront Distribution.
* Cấu hình Origin Access Control để CloudFront truy cập nội dung trong S3.
* Thiết lập `index.html` làm Default Root Object.
* Truy cập thành công Customer Web và Admin Dashboard thông qua CloudFront.
* Biết cách thực hiện CloudFront Invalidation sau khi cập nhật nội dung website.
* Hiểu được vai trò của Amazon S3 và CloudFront trong việc triển khai frontend an toàn trên AWS.