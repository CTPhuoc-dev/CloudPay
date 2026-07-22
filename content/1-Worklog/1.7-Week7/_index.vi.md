---
title: "Worklog Tuần 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

* Tìm hiểu dịch vụ Amazon Cognito và quy trình xác thực người dùng.
* Xây dựng chức năng đăng nhập cho Customer Web và Admin Dashboard.
* Tạo các nhóm người dùng Customer và Admin.
* Kết nối trang đăng nhập của Cognito với hai website được triển khai bằng CloudFront.
* Kiểm tra luồng đăng nhập, đăng xuất và chuyển hướng người dùng.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu vai trò của Amazon Cognito trong việc quản lý và xác thực người dùng.<br>- Tìm hiểu các thành phần chính gồm User Pool, App Client, User và Group.<br>- Xác định hai nhóm người dùng của hệ thống CloudPay gồm Customer và Admin. | 01/06/2026 | 01/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 3 | - Tạo Amazon Cognito User Pool cho hệ thống CloudPay.<br>- Cấu hình đăng nhập bằng email.<br>- Thiết lập chính sách mật khẩu cơ bản.<br>- Tạo domain cho trang đăng nhập được quản lý bởi Cognito. | 02/06/2026 | 02/06/2026 | https://cloudjourney.awsstudygroup.com/<br>Tài liệu AWS |
| 4 | - Tạo App Client cho Customer Web.<br>- Tạo App Client cho Admin Dashboard.<br>- Không tạo Client Secret do hai ứng dụng là website frontend.<br>- Cấu hình Callback URL và Sign-out URL bằng CloudFront Domain Name.<br>- Cấu hình các scope gồm `openid`, `email` và `profile`. | 03/06/2026 | 03/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 5 | - Tạo hai Cognito Group gồm Customer và Admin.<br>- Tạo tài khoản thử nghiệm cho từng nhóm.<br>- Thêm người dùng vào nhóm tương ứng.<br>- Kiểm tra thông tin User Pool ID, App Client ID và Cognito Domain. | 04/06/2026 | 04/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 6 | - Thêm nút đăng nhập và đăng xuất vào Customer Web và Admin Dashboard.<br>- Cấu hình nút đăng nhập chuyển hướng đến trang đăng nhập của Cognito.<br>- Kiểm tra quá trình đăng nhập và chuyển hướng trở lại CloudFront.<br>- Kiểm tra tham số `code` trên URL sau khi đăng nhập.<br>- Kiểm tra chức năng đăng xuất và ghi lại các lỗi phát sinh. | 05/06/2026 | 05/06/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |

### Kết quả đạt được tuần 7:

* Hiểu được vai trò của Amazon Cognito trong việc quản lý danh tính và xác thực người dùng.
* Tạo thành công Cognito User Pool cho hệ thống CloudPay.
* Tạo hai App Client dành cho:
  * Customer Web Application.
  * Admin Dashboard.
* Cấu hình Callback URL và Sign-out URL bằng CloudFront Domain Name.
* Tạo thành công hai nhóm người dùng:
  * Customer.
  * Admin.
* Tạo tài khoản thử nghiệm và thêm người dùng vào nhóm tương ứng.
* Thêm nút đăng nhập và đăng xuất vào hai giao diện frontend.
* Chuyển hướng thành công từ CloudFront đến trang đăng nhập của Cognito.
* Đăng nhập thành công và được chuyển hướng trở lại website CloudFront.
* Kiểm tra được tham số `code` trên URL sau khi đăng nhập.
* Hiểu rằng phiên bản hiện tại mới thực hiện luồng đăng nhập và chuyển hướng cơ bản, chưa xử lý đổi Authorization Code thành JWT Token.
* Hoàn thành bước chuẩn bị thông tin Cognito để tích hợp với API Gateway trong các tuần tiếp theo.