---
title: "Kiểm tra đăng nhập Cognito"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

Trong phần này, bạn sẽ kiểm tra chức năng đăng nhập và đăng xuất của Customer Web Application và Admin Dashboard.

#### Kiểm tra tài khoản Customer

1. Mở Amazon Cognito Console.

2. Chọn User Pool:

```text
cloudpay-user-pool
```

3. Chọn **Users**.

4. Kiểm tra tài khoản Customer đã được tạo.

Ví dụ:

```text
customer@example.com
```

5. Mở tài khoản Customer và kiểm tra người dùng thuộc nhóm:

```text
Customer
```

![Kiểm tra Customer User](/images/5-Workshop/5.4-S3-onprem/check-customer-user.png)

#### Kiểm tra đăng nhập Customer

6. Mở Customer CloudFront Domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN
```

7. Chọn **Đăng nhập**.

8. Trình duyệt sẽ chuyển đến trang đăng nhập Amazon Cognito.

9. Nhập tài khoản Customer và mật khẩu.

10. Nếu đây là lần đăng nhập đầu tiên, Cognito sẽ yêu cầu đổi mật khẩu tạm thời.

11. Sau khi đăng nhập thành công, Cognito chuyển người dùng về Customer Web Application.

URL có thể có dạng:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/?code=EXAMPLE_CODE
```

Kết quả này cho thấy Cognito đã xác thực tài khoản thành công.

![Customer Login Success](/images/5-Workshop/5.4-S3-onprem/customer-login-success.png)

#### Kiểm tra tài khoản Admin

12. Quay lại Amazon Cognito Console.

13. Trong danh sách Users, kiểm tra tài khoản Admin.

Ví dụ:

```text
admin@example.com
```

14. Mở tài khoản Admin và kiểm tra người dùng thuộc nhóm:

```text
Admin
```

![Kiểm tra Admin User](/images/5-Workshop/5.4-S3-onprem/check-admin-user.png)

#### Kiểm tra đăng nhập Admin

15. Mở Admin CloudFront Domain:

```text
https://ADMIN_CLOUDFRONT_DOMAIN
```

16. Chọn **Đăng nhập**.

17. Nhập tài khoản Admin và mật khẩu.

18. Sau khi đăng nhập thành công, Cognito chuyển người dùng về Admin Dashboard.

URL có thể có dạng:

```text
https://ADMIN_CLOUDFRONT_DOMAIN/?code=EXAMPLE_CODE
```

![Admin Login Success](/images/5-Workshop/5.4-S3-onprem/admin-login-success.png)

#### Kiểm tra đăng xuất

19. Tại Customer Web Application hoặc Admin Dashboard, chọn **Đăng xuất**.

20. Cognito kết thúc phiên đăng nhập và chuyển người dùng về CloudFront Domain.

Kết quả mong đợi:

```text
Người dùng đã đăng xuất khỏi Amazon Cognito.
```

#### Một số lỗi thường gặp

##### Lỗi redirect_mismatch

Kiểm tra Callback URL trong Cognito App Client phải giống hoàn toàn với CloudFront Domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/
```

hoặc:

```text
https://ADMIN_CLOUDFRONT_DOMAIN/
```

##### Không mở được trang đăng nhập

Kiểm tra:

- Cognito Domain đã được tạo.
- App Client đã được cấu hình.
- Callback URL đã được lưu.
- OAuth scope đã chọn `openid`, `email` và `profile`.

##### Trang vẫn hiển thị nội dung cũ

Tạo CloudFront Invalidation với đường dẫn:

```text
/*
```

Sau đó tải lại trang bằng:

```text
Ctrl + Shift + R
```

{{% notice warning %}}
Bước này chỉ kiểm tra chức năng xác thực. Việc phân quyền Customer và Admin sẽ được kiểm tra thêm tại API Gateway và Lambda trong các phần tiếp theo.
{{% /notice %}}

#### Tóm tắt

Trong phần này, bạn đã:

- Kiểm tra tài khoản Customer.
- Kiểm tra tài khoản Admin.
- Kiểm tra nhóm `Customer`.
- Kiểm tra nhóm `Admin`.
- Đăng nhập Customer qua Amazon Cognito.
- Đăng nhập Admin qua Amazon Cognito.
- Kiểm tra chuyển hướng về CloudFront.
- Kiểm tra chức năng đăng xuất.

#### Kết quả mong đợi

Sau khi hoàn thành phần này:

- Customer có thể đăng nhập thành công.
- Admin có thể đăng nhập thành công.
- Cognito chuyển người dùng về đúng CloudFront Domain.
- Chức năng đăng xuất hoạt động.
- Hệ thống xác thực đã sẵn sàng để kết nối với API Gateway.