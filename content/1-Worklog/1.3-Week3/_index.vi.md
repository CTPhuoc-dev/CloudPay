---
title: "Worklog Tuần 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Tìm hiểu mô hình kết nối mạng Hybrid Cloud trên AWS.
* Hiểu vai trò và cách hoạt động của Amazon Transit Gateway.
* Thực hành kết nối nhiều VPC thông qua Transit Gateway.
* Tìm hiểu cách phân giải tên miền giữa AWS và môi trường nội bộ bằng Amazon Route 53 Resolver.
* Thực hành khởi tạo hạ tầng bằng AWS CloudFormation.
* Biết cách kiểm tra và dọn dẹp tài nguyên sau khi hoàn thành bài thực hành.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu khái niệm và chức năng của Amazon Transit Gateway.<br>- Tìm hiểu mô hình kết nối nhiều VPC.<br>- Tạo các VPC phục vụ bài thực hành. | 04/05/2026 | 04/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| 3 | - Tạo Amazon Transit Gateway.<br>- Cấu hình Amazon Side ASN và DNS Support.<br>- Tạo Transit Gateway Attachments để kết nối các VPC.<br>- Tìm hiểu và tạo Transit Gateway Route Table. | 05/05/2026 | 05/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| 4 | - Cấu hình Route Table cho từng VPC và subnet.<br>- Thêm route từ các VPC đến Transit Gateway.<br>- Kiểm tra khả năng kết nối giữa các EC2 Instance bằng lệnh ping.<br>- Kiểm tra và khắc phục các lỗi cấu hình mạng cơ bản. | 06/05/2026 | 06/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| 5 | - Tìm hiểu cách khởi tạo hạ tầng bằng AWS CloudFormation Template.<br>- Cấu hình Security Group cho DNS, RDP và SSH.<br>- Tạo Route 53 Resolver Outbound Endpoint.<br>- Tạo và cấu hình Resolver Rules. | 07/05/2026 | 07/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| 6 | - Tạo Route 53 Resolver Inbound Endpoint.<br>- Kiểm tra hoạt động của hệ thống routing và Hybrid DNS.<br>- Kiểm tra khả năng phân giải tên miền giữa các môi trường.<br>- Dọn dẹp tài nguyên sau khi hoàn thành bài thực hành:<br>&emsp; + Xóa Resolver Rules<br>&emsp; + Xóa Resolver Endpoints<br>&emsp; + Xóa Transit Gateway Attachments<br>&emsp; + Xóa Transit Gateway và các Route Table liên quan | 08/05/2026 | 08/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |

### Kết quả đạt được tuần 3:

* Hiểu được khái niệm và vai trò của Amazon Transit Gateway trong việc kết nối nhiều VPC.
* Tạo và cấu hình các VPC phục vụ bài thực hành.
* Triển khai Amazon Transit Gateway để kết nối các VPC trong cùng hệ thống mạng.
* Tạo và cấu hình Transit Gateway Attachments.
* Cấu hình Route Table để định tuyến lưu lượng giữa các VPC thông qua Transit Gateway.
* Kiểm tra kết nối giữa các EC2 Instance thuộc các VPC khác nhau.
* Biết cách sử dụng AWS CloudFormation Template để khởi tạo hạ tầng.
* Hiểu được mô hình Hybrid DNS trên AWS.
* Tạo và cấu hình thành công:
  * Route 53 Resolver Outbound Endpoint
  * Route 53 Resolver Inbound Endpoint
  * Resolver Rules
* Kiểm tra khả năng phân giải tên miền giữa AWS và môi trường mô phỏng nội bộ.
* Biết cách kiểm tra Security Group, Route Table và cấu hình DNS khi xảy ra lỗi kết nối.
* Dọn dẹp các tài nguyên sau khi hoàn thành bài thực hành để hạn chế phát sinh chi phí.