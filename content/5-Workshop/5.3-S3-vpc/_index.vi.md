---
title: "Triển khai Frontend với S3 và CloudFront"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Lưu trữ và phân phối giao diện CloudPay

Trong phần này, bạn sẽ triển khai giao diện người dùng của hệ thống CloudPay bằng Amazon S3 và Amazon CloudFront.

Hệ thống sử dụng hai giao diện riêng biệt:

- **Customer Web Application**: Dành cho người dùng đăng nhập, tạo yêu cầu thanh toán và xem kết quả giao dịch.
- **Admin Dashboard**: Dành cho Admin theo dõi giao dịch, xem báo cáo và xử lý các giao dịch thất bại.

Các file giao diện như HTML, CSS, JavaScript và hình ảnh được lưu trữ trong Amazon S3. Hai S3 Bucket được giữ ở trạng thái private để ngăn người dùng truy cập trực tiếp.

Amazon CloudFront được đặt phía trước Amazon S3 để phân phối nội dung với độ trễ thấp. CloudFront sử dụng Origin Access Control để đọc nội dung từ private S3 Bucket mà không cần bật quyền truy cập public.

Luồng truy cập frontend:

```text
Customer hoặc Admin
        ↓
      HTTPS
        ↓
Amazon CloudFront
        ↓
Origin Access Control
        ↓
Private Amazon S3 Bucket