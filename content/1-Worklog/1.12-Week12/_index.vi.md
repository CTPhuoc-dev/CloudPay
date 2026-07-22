---
title: "Worklog Tuần 12"
date: 2026-07-13
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu tuần 12:

* Rà soát và hoàn thiện các thành phần chính của dự án **CloudPay – Hệ thống thanh toán trên AWS**.
* Hoàn thiện nội dung workshop và báo cáo thực tập bằng tiếng Việt và tiếng Anh.
* Kiểm tra toàn bộ website báo cáo được xây dựng bằng Hugo.
* Tổng hợp kết quả, tài liệu và hình ảnh phục vụ phần trình bày dự án.
* Dọn dẹp tài nguyên AWS và chuẩn bị bàn giao sản phẩm.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Rà soát kiến trúc tổng quan của hệ thống CloudPay.<br>- Kiểm tra Customer Web và Admin Dashboard được phân phối qua Amazon CloudFront.<br>- Kiểm tra Amazon Cognito, API Gateway, AWS Lambda và Amazon CloudWatch.<br>- Ghi nhận các chức năng đã hoàn thành và các giới hạn của phiên bản hiện tại. | 13/07/2026 | 13/07/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 3 | - Kiểm tra lại luồng đăng nhập và đăng xuất bằng Amazon Cognito.<br>- Kiểm tra chức năng gửi yêu cầu thanh toán từ Customer Web.<br>- Kiểm tra route `POST /payments` và dữ liệu trả về.<br>- Theo dõi log thực thi Lambda và khắc phục các lỗi còn lại. | 14/07/2026 | 14/07/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |
| 4 | - Hoàn thiện nội dung Workshop CloudPay.<br>- Bổ sung các bước triển khai, ảnh minh họa và kết quả kiểm thử.<br>- Rà soát nội dung tiếng Việt và tiếng Anh.<br>- Kiểm tra tính thống nhất của thuật ngữ AWS trong toàn bộ tài liệu. | 15/07/2026 | 15/07/2026 | Tài liệu AWS<br>Tài liệu Hugo<br>Tài liệu chương trình FCAJ |
| 5 | - Rà soát toàn bộ website báo cáo thực tập bằng Hugo.<br>- Kiểm tra Worklog, Proposal, Workshop, Events, Self-Evaluation và Feedback.<br>- Kiểm tra menu, liên kết nội bộ, hình ảnh và bảng Markdown.<br>- Chạy `hugo server -D` để kiểm tra website trên trình duyệt cục bộ. | 16/07/2026 | 16/07/2026 | Tài liệu Hugo<br>Tài liệu Markdown<br>Tài liệu chương trình FCAJ |
| 6 | - Tổng hợp mã nguồn, tài liệu và hình ảnh của dự án.<br>- Chuẩn bị nội dung trình bày và demo CloudPay.<br>- Kiểm tra Billing and Cost Management.<br>- Xóa hoặc dừng các tài nguyên AWS không còn sử dụng.<br>- Sao lưu phiên bản hoàn thiện và chuẩn bị bàn giao dự án. | 17/07/2026 | 17/07/2026 | Tài liệu AWS<br>Tài liệu chương trình FCAJ |

### Kết quả đạt được tuần 12:

* Hoàn thiện phiên bản cơ bản của dự án **CloudPay – Hệ thống thanh toán trên AWS**.
* Rà soát thành công các thành phần chính:
  * Amazon S3.
  * Amazon CloudFront.
  * Amazon Cognito.
  * Amazon API Gateway.
  * AWS Lambda.
  * Amazon CloudWatch.
  * AWS IAM.
* Kiểm tra thành công Customer Web và Admin Dashboard.
* Kiểm tra lại luồng đăng nhập và đăng xuất bằng Amazon Cognito.
* Kiểm tra thành công Payment API với route `POST /payments`.
* Xác nhận hệ thống có thể:
  * Nhận `orderId` và `amount`.
  * Kiểm tra dữ liệu đầu vào.
  * Tạo mã giao dịch.
  * Trả về trạng thái `PENDING`.
  * Trả về đường dẫn thanh toán mô phỏng.
* Theo dõi được quá trình xử lý yêu cầu thông qua Amazon CloudWatch Logs.
* Hoàn thiện nội dung Workshop CloudPay bằng tiếng Việt và tiếng Anh.
* Bổ sung đầy đủ các bước triển khai, ảnh minh họa, kết quả kiểm thử và phần dọn dẹp tài nguyên.
* Hoàn thiện các phần chính của website báo cáo thực tập:
  * Worklog.
  * Proposal.
  * Blogs Posted.
  * Events Participated.
  * Workshop.
  * Self-Evaluation.
  * Feedback.
* Kiểm tra thành công menu, liên kết, hình ảnh và định dạng Markdown trên website Hugo.
* Tổng hợp mã nguồn, tài liệu và hình ảnh để chuẩn bị trình bày và bàn giao.
* Kiểm tra chi phí và dọn dẹp các tài nguyên AWS không còn sử dụng.
* Hiểu rõ các giới hạn của phiên bản hiện tại:
  * Chỉ mô phỏng quá trình thanh toán.
  * Chưa kết nối với cổng thanh toán thực tế.
  * Chưa triển khai đầy đủ cơ chế lưu trữ giao dịch và bảo mật nâng cao.
* Tích lũy thêm kinh nghiệm về xây dựng ứng dụng Serverless, triển khai frontend, xác thực người dùng, kiểm thử hệ thống và viết tài liệu kỹ thuật trên AWS.