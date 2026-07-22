---
title: "Kết nối Cognito với Frontend"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

Trong phần trước, bạn đã tạo:

- Cognito User Pool.
- Customer App Client.
- Admin App Client.
- Nhóm `Customer` và `Admin`.
- Tài khoản kiểm thử.

Trong phần này, chúng ta sẽ kết nối trang đăng nhập Amazon Cognito với Customer Web Application và Admin Dashboard.

#### Chuẩn bị thông tin

Ghi lại các thông tin sau:

```text
COGNITO_DOMAIN
CUSTOMER_CLIENT_ID
ADMIN_CLIENT_ID
CUSTOMER_CLOUDFRONT_DOMAIN
ADMIN_CLOUDFRONT_DOMAIN
```

Ví dụ Cognito Domain:

```text
https://cloudpay-123456789012.auth.ap-southeast-1.amazoncognito.com
```

#### Cập nhật Callback URL

1. Mở Amazon Cognito Console.

2. Chọn User Pool:

```text
cloudpay-user-pool
```

3. Chọn Customer App Client:

```text
cloudpay-customer-app-client
```

4. Trong phần Callback URL, nhập:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/
```

5. Trong phần Sign-out URL, nhập:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/
```

6. Chọn **Save changes**.

7. Tiếp tục chọn Admin App Client:

```text
cloudpay-admin-app-client
```

8. Cấu hình Callback URL và Sign-out URL:

```text
https://ADMIN_CLOUDFRONT_DOMAIN/
```

![Cập nhật Callback URL](/images/5-Workshop/5.4-S3-onprem/update-callback-url.png)

#### Tạo trang đăng nhập Customer

9. Mở file `index.html` của Customer Web Application.

10. Thay nội dung bằng:

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

        <p>
            Đăng nhập để thực hiện thanh toán và xem
            lịch sử giao dịch.
        </p>

        <button onclick="login()">
            Đăng nhập
        </button>

        <button onclick="logout()">
            Đăng xuất
        </button>
    </main>

    <script>
        const cognitoDomain =
            "https://COGNITO_DOMAIN";

        const clientId =
            "CUSTOMER_CLIENT_ID";

        const redirectUri =
            window.location.origin + "/";

        function login() {
            const loginUrl =
                cognitoDomain +
                "/login" +
                "?client_id=" +
                clientId +
                "&response_type=code" +
                "&scope=openid+email+profile" +
                "&redirect_uri=" +
                encodeURIComponent(redirectUri);

            window.location.href = loginUrl;
        }

        function logout() {
            const logoutUrl =
                cognitoDomain +
                "/logout" +
                "?client_id=" +
                clientId +
                "&logout_uri=" +
                encodeURIComponent(redirectUri);

            window.location.href = logoutUrl;
        }
    </script>
</body>
</html>
```

11. Thay:

```text
COGNITO_DOMAIN
CUSTOMER_CLIENT_ID
```

bằng thông tin Cognito thực tế.

Ví dụ:

```javascript
const cognitoDomain =
    "https://cloudpay-123456789012.auth.ap-southeast-1.amazoncognito.com";

const clientId =
    "4examplecustomerclientid123";
```

{{% notice note %}}
Không thêm dấu `/` ở cuối Cognito Domain.
{{% /notice %}}

#### Upload Customer Web Application

12. Mở Amazon S3 Console.

13. Chọn bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

14. Upload lại file:

```text
index.html
```

15. Mở Customer CloudFront Distribution.

16. Tạo Invalidation với đường dẫn:

```text
/*
```

17. Chờ trạng thái chuyển thành:

```text
Completed
```

#### Kiểm tra đăng nhập Customer

18. Mở Customer CloudFront Domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN
```

19. Chọn **Đăng nhập**.

20. Trình duyệt sẽ chuyển đến trang đăng nhập Amazon Cognito.

21. Đăng nhập bằng tài khoản Customer đã tạo.

22. Sau khi đăng nhập thành công, Cognito chuyển người dùng về Customer Web Application.

URL có thể chứa Authorization Code:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/?code=EXAMPLE_CODE
```

Điều này cho thấy Cognito đã xác thực người dùng thành công.

![Customer Login Success](/images/5-Workshop/5.4-S3-onprem/customer-login-success.png)

#### Tạo trang đăng nhập Admin

23. Mở file `index.html` của Admin Dashboard.

24. Sử dụng nội dung tương tự Customer Web Application nhưng thay:

```javascript
const clientId =
    "ADMIN_CLIENT_ID";
```

Tiêu đề trang có thể đổi thành:

```html
<h1>CloudPay Admin Dashboard</h1>

<p>
    Đăng nhập bằng tài khoản Admin để quản lý giao dịch.
</p>
```

25. Upload file `index.html` lên bucket:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

26. Tạo CloudFront Invalidation:

```text
/*
```

#### Kiểm tra đăng nhập Admin

27. Mở Admin CloudFront Domain:

```text
https://ADMIN_CLOUDFRONT_DOMAIN
```

28. Chọn **Đăng nhập**.

29. Đăng nhập bằng tài khoản thuộc nhóm:

```text
Admin
```

30. Sau khi đăng nhập thành công, Cognito chuyển người dùng về Admin Dashboard.

![Admin Login Success](/images/5-Workshop/5.4-S3-onprem/admin-login-success.png)

#### Kiểm tra đăng xuất

31. Tại Customer Web hoặc Admin Dashboard, chọn **Đăng xuất**.

32. Cognito kết thúc phiên đăng nhập và chuyển người dùng về CloudFront Domain.

#### Tóm tắt

Trong phần này, bạn đã:

- Cập nhật Callback URL cho Customer App Client.
- Cập nhật Callback URL cho Admin App Client.
- Thêm nút đăng nhập vào Customer Web Application.
- Thêm nút đăng nhập vào Admin Dashboard.
- Chuyển người dùng đến trang đăng nhập Cognito.
- Kiểm tra đăng nhập Customer.
- Kiểm tra đăng nhập Admin.
- Kiểm tra chức năng đăng xuất.

#### Kết quả mong đợi

Sau khi hoàn thành phần này:

- Customer có thể mở trang đăng nhập Cognito.
- Admin có thể mở trang đăng nhập Cognito.
- Cognito xác thực tài khoản thành công.
- Người dùng được chuyển về đúng CloudFront Domain.
- Chức năng đăng xuất hoạt động.
- Frontend đã sẵn sàng để kết nối với API Gateway.