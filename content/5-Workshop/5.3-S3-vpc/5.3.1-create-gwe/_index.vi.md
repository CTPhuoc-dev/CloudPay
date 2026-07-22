---
title: "Tạo S3 Bucket cho Web Application"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

Trong bước này, chúng ta sẽ tạo hai Amazon S3 Bucket để lưu trữ giao diện của hệ thống CloudPay:

- **Customer Web Application**: Giao diện dành cho người dùng thực hiện thanh toán.
- **Admin Dashboard**: Giao diện dành cho Admin theo dõi và quản lý giao dịch.

Các S3 Bucket sẽ được giữ ở trạng thái private. Người dùng sẽ truy cập nội dung thông qua Amazon CloudFront thay vì truy cập trực tiếp bằng URL của Amazon S3.

#### Tạo S3 Bucket cho Customer Web Application

1. Mở [Amazon S3 Console](https://ap-southeast-1.console.aws.amazon.com/s3/home?region=ap-southeast-1).

2. Kiểm tra Region hiện tại là:

```text
Asia Pacific (Singapore) ap-southeast-1
```

3. Trong giao diện Amazon S3, chọn **Create bucket**.

![Mở Amazon S3 Console](/images/5-Workshop/5.3-Frontend/s3-console.png)

4. Trong phần **General configuration**, cấu hình:

- **AWS Region**: Asia Pacific (Singapore) `ap-southeast-1`.
- **Bucket type**: General purpose.
- **Bucket name**:

```text
cloudpay-customer-web-ACCOUNT_ID
```

Thay `ACCOUNT_ID` bằng AWS Account ID của bạn.

Ví dụ:

```text
cloudpay-customer-web-123456789012
```

{{% notice note %}}
Tên Amazon S3 Bucket phải là duy nhất trên toàn bộ AWS. Nếu tên đã tồn tại, hãy thêm AWS Account ID hoặc một chuỗi ký tự riêng vào cuối tên bucket.
{{% /notice %}}

![Cấu hình tên S3 Bucket](/images/5-Workshop/5.3-Frontend/create-customer-bucket1.png)

5. Trong phần **Object Ownership**, chọn:

```text
ACLs disabled (recommended)
Bucket owner enforced
```

Cấu hình này vô hiệu hóa ACL và cho phép quản lý quyền truy cập bằng IAM Policy và Bucket Policy.

![Cấu hình Object Ownership](/images/5-Workshop/5.3-Frontend/object-ownership.png)

6. Trong phần **Block Public Access settings for this bucket**, giữ nguyên tùy chọn:

```text
Block all public access
```

Đảm bảo cả bốn tùy chọn bên dưới đều được bật:

- Block public access granted through new ACLs.
- Block public access granted through any ACLs.
- Block public access granted through new public bucket policies.
- Block public and cross-account access through public bucket policies.

{{% notice warning %}}
Không tắt Block Public Access. S3 Bucket của CloudPay phải được giữ private. Amazon CloudFront sẽ được cấp quyền đọc dữ liệu thông qua Origin Access Control trong bước tiếp theo.
{{% /notice %}}

![Bật Block Public Access](/images/5-Workshop/5.3-Frontend/block-public-access.png)

7. Trong phần **Bucket Versioning**, chọn:

```text
Enable
```

Bucket Versioning giúp lưu lại các phiên bản cũ của frontend khi file bị ghi đè hoặc cập nhật sai.

Để giảm chi phí trong môi trường workshop, bạn cũng có thể chọn `Disable`.

8. Trong phần **Default encryption**, chọn:

```text
Server-side encryption with Amazon S3 managed keys (SSE-S3)
```

Đối với **Bucket Key**, giữ nguyên cấu hình mặc định.

![Cấu hình mã hóa S3](/images/5-Workshop/5.3-Frontend/s3-encryption.png)

9. Không cần thêm Tag trong workshop này.

10. Kiểm tra lại cấu hình và chọn **Create bucket**.

![Tạo Customer Web Bucket](/images/5-Workshop/5.3-Frontend/create-customer-bucket2.png)

11. Sau khi tạo thành công, kiểm tra bucket xuất hiện trong danh sách:

```text
cloudpay-customer-web-ACCOUNT_ID
```

![Customer Bucket đã được tạo](/images/5-Workshop/5.3-Frontend/customer-bucket-complete.png)

#### Tạo S3 Bucket cho Admin Dashboard

12. Tiếp tục chọn **Create bucket** để tạo bucket thứ hai.

13. Trong phần **General configuration**, nhập:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

Ví dụ:

```text
cloudpay-admin-dashboard-123456789012
```

14. Sử dụng cấu hình giống Customer Web Application:

- AWS Region: `ap-southeast-1`.
- Object Ownership: `Bucket owner enforced`.
- Block Public Access: Bật toàn bộ.
- Bucket Versioning: Enable hoặc Disable tùy môi trường.
- Default encryption: SSE-S3.
- Không thêm ACL công khai.
- Không bật Static Website Hosting.

15. Chọn **Create bucket**.

Sau khi hoàn tất, danh sách S3 cần có hai bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
cloudpay-admin-dashboard-ACCOUNT_ID
```

![Hai S3 Bucket của CloudPay](/images/5-Workshop/5.3-Frontend/cloudpay-buckets.png)

#### Tạo giao diện thử nghiệm cho Customer Web Application

16. Trên máy tính, tạo file có tên:

```text
index.html
```

17. Thêm nội dung sau vào file:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta
        name="viewport"
        content="width=device-width, initial-scale=1.0"
    >
    <title>CloudPay</title>
</head>
<body>
    <main>
        <h1>CloudPay Payment System</h1>
        <p>Hệ thống thanh toán trực tuyến trên AWS.</p>

        <button type="button">
            Thanh toán ngay
        </button>
    </main>
</body>
</html>
```

18. Mở bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

19. Chọn **Upload**.

20. Chọn **Add files** và tải file `index.html` lên bucket.

![Upload Customer index.html](/images/5-Workshop/5.3-Frontend/upload-customer-index.png)

21. Giữ nguyên các cấu hình mặc định và chọn **Upload**.

22. Sau khi upload thành công, chọn **Close**.

![Upload thành công](/images/5-Workshop/5.3-Frontend/upload-customer-complete.png)

#### Tạo giao diện thử nghiệm cho Admin Dashboard

23. Tạo một file `index.html` khác trong thư mục riêng dành cho Admin.

24. Thêm nội dung:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta
        name="viewport"
        content="width=device-width, initial-scale=1.0"
    >
    <title>CloudPay Admin Dashboard</title>
</head>
<body>
    <main>
        <h1>CloudPay Admin Dashboard</h1>
        <p>Trang quản trị và theo dõi giao dịch.</p>

        <section>
            <h2>Transaction Management</h2>
            <ul>
                <li>Xem danh sách giao dịch</li>
                <li>Kiểm tra giao dịch thất bại</li>
                <li>Retry giao dịch trong DLQ</li>
                <li>Xem báo cáo thanh toán</li>
            </ul>
        </section>
    </main>
</body>
</html>
```

25. Mở bucket:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

26. Chọn **Upload** và tải file `index.html` của Admin Dashboard lên bucket.

27. Sau khi upload hoàn tất, kiểm tra file xuất hiện trong tab **Objects**.

![Upload Admin Dashboard](/images/5-Workshop/5.3-Frontend/upload-admin-index.png)

#### Kiểm tra quyền truy cập S3

28. Trong một trong hai bucket, chọn file `index.html`.

29. Tại phần **Object URL**, sao chép URL của object.

Ví dụ:

```text
https://cloudpay-customer-web-ACCOUNT_ID.s3.ap-southeast-1.amazonaws.com/index.html
```

30. Mở URL bằng một tab trình duyệt mới.

Do S3 Bucket đang được giữ private, trình duyệt sẽ hiển thị lỗi tương tự:

```text
AccessDenied
```

Đây là kết quả mong đợi vì người dùng không được phép truy cập trực tiếp vào S3 Bucket.

![S3 Access Denied](/images/5-Workshop/5.3-Frontend/s3-access-denied.png)

{{% notice note %}}
Trong bước tiếp theo, chúng ta sẽ tạo Amazon CloudFront Distribution và Origin Access Control để CloudFront có thể đọc nội dung từ S3 mà không cần mở public bucket.
{{% /notice %}}

#### Kết quả mong đợi

Sau khi hoàn thành bước này:

- Đã tạo S3 Bucket cho Customer Web Application.
- Đã tạo S3 Bucket cho Admin Dashboard.
- Hai bucket được triển khai tại Region `ap-southeast-1`.
- Object Ownership được đặt thành `Bucket owner enforced`.
- Block Public Access được bật.
- Nội dung được mã hóa bằng SSE-S3.
- File `index.html` đã được upload vào mỗi bucket.
- Người dùng chưa thể truy cập trực tiếp bằng S3 Object URL.
- Hai bucket đã sẵn sàng để tích hợp với Amazon CloudFront.