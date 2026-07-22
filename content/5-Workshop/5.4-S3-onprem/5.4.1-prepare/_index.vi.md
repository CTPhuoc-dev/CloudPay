---
title: "Chuẩn bị tài nguyên xác thực"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

Trong phần này, bạn sẽ chuẩn bị các tài nguyên Amazon Cognito để xác thực và phân quyền cho hệ thống CloudPay.

Hệ thống có hai nhóm người dùng:

- **Customer**: Đăng nhập, tạo yêu cầu thanh toán và xem lịch sử giao dịch.
- **Admin**: Theo dõi giao dịch, xem báo cáo và xử lý các giao dịch thất bại.

Các tài nguyên sẽ được tạo gồm:

- Một Amazon Cognito User Pool.
- Một App Client cho Customer Web Application.
- Một App Client cho Admin Dashboard.
- Một Cognito domain cho trang đăng nhập.
- Nhóm người dùng `Customer`.
- Nhóm người dùng `Admin`.
- Hai tài khoản kiểm thử.
- Callback URL và Sign-out URL cho hai ứng dụng.

Luồng xác thực của hệ thống:

```text
Customer hoặc Admin
        ↓
Amazon CloudFront
        ↓
Amazon Cognito Managed Login
        ↓
Xác thực tài khoản
        ↓
JWT Token
        ↓
Customer Web Application hoặc Admin Dashboard
        ↓
Amazon API Gateway
```

#### Chuẩn bị CloudFront Domain

Trước khi tạo App Client, hãy ghi lại domain của hai CloudFront Distribution đã tạo ở phần trước.

Domain của Customer Web Application:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN
```

Ví dụ:

```text
https://d123customerexample.cloudfront.net
```

Domain của Admin Dashboard:

```text
https://ADMIN_DISTRIBUTION_DOMAIN
```

Ví dụ:

```text
https://d456adminexample.cloudfront.net
```

Các domain này sẽ được sử dụng để cấu hình:

- Callback URL.
- Sign-out URL.
- Redirect URL sau khi đăng nhập.

{{% notice note %}}
Thay các giá trị `CUSTOMER_DISTRIBUTION_DOMAIN` và `ADMIN_DISTRIBUTION_DOMAIN` bằng CloudFront domain thực tế của bạn.
{{% /notice %}}

#### Tạo Amazon Cognito User Pool

1. Mở [Amazon Cognito Console](https://ap-southeast-1.console.aws.amazon.com/cognito/v2/home?region=ap-southeast-1).

2. Kiểm tra Region hiện tại là:

```text
Asia Pacific (Singapore) ap-southeast-1
```

3. Chọn **User pools**.

4. Chọn **Create user pool** hoặc **Create user directory** tùy theo giao diện AWS Console.

![Mở Amazon Cognito Console](/images/5-Workshop/5.4-S3-onprem/cognito-console.png)

5. Trong phần cấu hình tên ứng dụng hoặc User Pool, nhập:

```text
cloudpay-user-pool
```

6. Chọn phương thức đăng nhập bằng:

```text
Email
```

Người dùng sẽ sử dụng địa chỉ email để đăng nhập vào CloudPay.

7. Bật tùy chọn xác minh email nếu được hiển thị:

```text
Send email message, verify email address
```

8. Trong phần thuộc tính người dùng, chọn các thuộc tính cần thiết:

```text
email
name
```

9. Trong phần chính sách mật khẩu, sử dụng cấu hình đề xuất:

```text
Minimum password length: 8
Require uppercase letters: Yes
Require lowercase letters: Yes
Require numbers: Yes
Require special characters: Yes
```

10. Trong môi trường workshop, có thể cấu hình MFA:

```text
MFA: Optional
```

Trong môi trường production, nên yêu cầu MFA đối với tài khoản Admin.

11. Trong phần đăng ký người dùng, có thể bật:

```text
Enable self-registration
```

Tùy chọn này cho phép Customer tự đăng ký tài khoản.

{{% notice warning %}}
Không cho phép người dùng tự gán quyền Admin. Tài khoản Admin phải được tạo hoặc thêm vào nhóm `Admin` bởi người quản trị hệ thống.
{{% /notice %}}

12. Kiểm tra lại cấu hình và chọn **Create user pool**.

13. Sau khi tạo thành công, ghi lại **User Pool ID**.

Ví dụ:

```text
ap-southeast-1_AbCdEf123
```

![Cognito User Pool đã tạo](/images/5-Workshop/5.4-S3-onprem/user-pool-created.png)

#### Tạo App Client cho Customer Web Application

14. Mở User Pool:

```text
cloudpay-user-pool
```

15. Chọn menu **App clients** hoặc **Applications**.

16. Chọn **Create app client**.

17. Trong phần Application type, chọn loại ứng dụng phù hợp với ứng dụng web chạy trên trình duyệt:

```text
Single-page application
```

18. Nhập tên App Client:

```text
cloudpay-customer-app-client
```

19. Không tạo Client Secret cho ứng dụng frontend chạy trên trình duyệt.

```text
Generate client secret: No
```

{{% notice warning %}}
Customer Web Application là ứng dụng frontend công khai nên không thể bảo vệ Client Secret một cách an toàn. Không đưa Client Secret vào JavaScript hoặc file cấu hình frontend.
{{% /notice %}}

20. Trong phần Callback URL, nhập:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN/callback
```

Ví dụ:

```text
https://d123customerexample.cloudfront.net/callback
```

21. Trong phần Sign-out URL, nhập:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN/
```

22. Trong phần OAuth 2.0 grant types, chọn:

```text
Authorization code grant
```

23. Trong phần OpenID Connect scopes, chọn:

```text
openid
email
profile
```

24. Trong phần Identity providers, chọn:

```text
Cognito user pool
```

25. Chọn **Create app client**.

26. Sau khi tạo thành công, ghi lại **Client ID**.

Ví dụ:

```text
4examplecustomerclientid123
```

![Customer App Client](/images/5-Workshop/5.4-S3-onprem/customer-app-client.png)

#### Tạo App Client cho Admin Dashboard

27. Trong User Pool, tiếp tục chọn **Create app client**.

28. Chọn Application type:

```text
Single-page application
```

29. Nhập tên:

```text
cloudpay-admin-app-client
```

30. Không tạo Client Secret:

```text
Generate client secret: No
```

31. Trong phần Callback URL, nhập:

```text
https://ADMIN_DISTRIBUTION_DOMAIN/callback
```

Ví dụ:

```text
https://d456adminexample.cloudfront.net/callback
```

32. Trong phần Sign-out URL, nhập:

```text
https://ADMIN_DISTRIBUTION_DOMAIN/
```

33. Chọn OAuth 2.0 grant type:

```text
Authorization code grant
```

34. Chọn các OpenID Connect scopes:

```text
openid
email
profile
```

35. Chọn Identity provider:

```text
Cognito user pool
```

36. Chọn **Create app client**.

37. Ghi lại Client ID của Admin Dashboard.

Ví dụ:

```text
7exampleadminclientid456
```

![Admin App Client](/images/5-Workshop/5.4-S3-onprem/admin-app-client.png)

#### Tạo Cognito Domain

38. Trong User Pool, mở phần:

```text
Branding → Domain
```

Tên menu có thể hiển thị là **Domain**, **Managed login** hoặc **Hosted UI** tùy theo giao diện AWS Console.

39. Chọn tạo Cognito domain.

40. Nhập domain prefix:

```text
cloudpay-ACCOUNT_ID
```

Ví dụ:

```text
cloudpay-123456789012
```

Domain hoàn chỉnh có dạng:

```text
https://cloudpay-ACCOUNT_ID.auth.ap-southeast-1.amazoncognito.com
```

41. Chọn **Create Cognito domain**.

{{% notice note %}}
Domain prefix phải là duy nhất trong Region. Nếu tên đã tồn tại, hãy thêm AWS Account ID hoặc một chuỗi ký tự riêng.
{{% /notice %}}

42. Sau khi tạo thành công, ghi lại Cognito domain.

![Cognito Domain](/images/5-Workshop/5.4-S3-onprem/cognito-domain.png)

#### Kiểm tra cấu hình Login Page

43. Trong phần **App clients**, chọn:

```text
cloudpay-customer-app-client
```

44. Mở phần **Login pages**.

45. Chọn **View login page**.

46. Trang đăng nhập Amazon Cognito sẽ được mở trong một tab mới.

URL có dạng:

```text
https://cloudpay-ACCOUNT_ID.auth.ap-southeast-1.amazoncognito.com/login
```

47. Kiểm tra trang có các trường:

- Email.
- Password.
- Sign in.
- Sign up nếu self-registration được bật.
- Forgot password.

![Cognito Login Page](/images/5-Workshop/5.4-S3-onprem/cognito-login-page.png)

#### Tạo nhóm Customer

48. Trong User Pool, chọn menu **Groups**.

49. Chọn **Create group**.

50. Nhập tên nhóm:

```text
Customer
```

51. Nhập mô tả:

```text
CloudPay customers who can create payments and view their own transactions.
```

52. Trong phần Precedence, nhập:

```text
10
```

53. Không cần gắn IAM Role trong workshop này.

54. Chọn **Create group**.

![Customer Group](/images/5-Workshop/5.4-S3-onprem/customer-group.png)

#### Tạo nhóm Admin

55. Tiếp tục chọn **Create group**.

56. Nhập tên nhóm:

```text
Admin
```

57. Nhập mô tả:

```text
CloudPay administrators who can monitor transactions and perform retry operations.
```

58. Trong phần Precedence, nhập:

```text
1
```

59. Không cần gắn IAM Role trong workshop này.

60. Chọn **Create group**.

![Admin Group](/images/5-Workshop/5.4-S3-onprem/admin-group.png)

Sau khi hoàn thành, danh sách Groups cần có:

```text
Admin
Customer
```

#### Tạo tài khoản Customer kiểm thử

61. Trong User Pool, chọn menu **Users**.

62. Chọn **Create user**.

63. Nhập thông tin:

```text
Email: customer@example.com
Temporary password: Customer@123
Mark email as verified: Yes
```

Thay `customer@example.com` bằng email kiểm thử của bạn.

64. Chọn **Create user**.

65. Mở tài khoản Customer vừa tạo.

66. Chọn **Add user to group**.

67. Chọn nhóm:

```text
Customer
```

68. Xác nhận thêm người dùng vào nhóm.

![Customer Test User](/images/5-Workshop/5.4-S3-onprem/customer-test-user.png)

#### Tạo tài khoản Admin kiểm thử

69. Quay lại danh sách Users.

70. Chọn **Create user**.

71. Nhập:

```text
Email: admin@example.com
Temporary password: Admin@123
Mark email as verified: Yes
```

Thay `admin@example.com` bằng email Admin thực tế của bạn.

72. Chọn **Create user**.

73. Mở tài khoản Admin vừa tạo.

74. Chọn **Add user to group**.

75. Chọn nhóm:

```text
Admin
```

76. Xác nhận thêm người dùng vào nhóm.

![Admin Test User](/images/5-Workshop/5.4-S3-onprem/admin-test-user.png)

{{% notice warning %}}
Không sử dụng các mật khẩu mẫu trong môi trường production. Người dùng sẽ được yêu cầu đổi mật khẩu tạm thời trong lần đăng nhập đầu tiên.
{{% /notice %}}

#### Kiểm tra đăng nhập Customer

77. Mở Login Page của Customer App Client.

78. Đăng nhập bằng tài khoản Customer.

79. Nếu được yêu cầu, thay đổi mật khẩu tạm thời.

80. Sau khi đăng nhập thành công, Cognito chuyển hướng người dùng về:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN/callback
```

Nếu trang `/callback` chưa được tạo, CloudFront có thể hiển thị lỗi `AccessDenied` hoặc `404`. Đây là kết quả có thể xảy ra ở giai đoạn chuẩn bị.

Trang callback sẽ được xây dựng trong phần tích hợp frontend.

#### Kiểm tra đăng nhập Admin

81. Mở Login Page của Admin App Client.

82. Đăng nhập bằng tài khoản Admin.

83. Thay đổi mật khẩu tạm thời nếu được yêu cầu.

84. Sau khi đăng nhập thành công, Cognito chuyển hướng về:

```text
https://ADMIN_DISTRIBUTION_DOMAIN/callback
```

#### Kiểm tra nhóm người dùng trong JWT Token

Sau khi đăng nhập thành công, Amazon Cognito cấp các token:

- ID Token.
- Access Token.
- Refresh Token.

Thông tin nhóm người dùng được lưu trong claim:

```text
cognito:groups
```

Đối với Customer, claim dự kiến:

```json
{
    "cognito:groups": [
        "Customer"
    ]
}
```

Đối với Admin, claim dự kiến:

```json
{
    "cognito:groups": [
        "Admin"
    ]
}
```

API Gateway và Lambda có thể sử dụng claim này để kiểm tra quyền truy cập.

Ví dụ:

```text
Customer
→ Tạo thanh toán
→ Xem giao dịch của chính mình

Admin
→ Xem toàn bộ giao dịch
→ Xem báo cáo
→ Kiểm tra DLQ
→ Thực hiện retry
```

#### Ghi lại thông tin cấu hình

Lưu các giá trị sau để sử dụng trong các bước tiếp theo:

```text
AWS_REGION=ap-southeast-1

COGNITO_USER_POOL_ID=<USER_POOL_ID>

CUSTOMER_CLIENT_ID=<CUSTOMER_APP_CLIENT_ID>
ADMIN_CLIENT_ID=<ADMIN_APP_CLIENT_ID>

COGNITO_DOMAIN=<COGNITO_DOMAIN>

CUSTOMER_CALLBACK_URL=https://CUSTOMER_DISTRIBUTION_DOMAIN/callback
CUSTOMER_LOGOUT_URL=https://CUSTOMER_DISTRIBUTION_DOMAIN/

ADMIN_CALLBACK_URL=https://ADMIN_DISTRIBUTION_DOMAIN/callback
ADMIN_LOGOUT_URL=https://ADMIN_DISTRIBUTION_DOMAIN/
```

Không lưu mật khẩu người dùng hoặc token thật trong GitHub Repository.

#### Kết quả mong đợi

Sau khi hoàn thành phần này:

- Amazon Cognito User Pool đã được tạo.
- Customer App Client đã được tạo.
- Admin App Client đã được tạo.
- Cognito domain đã được cấu hình.
- Callback URL và Sign-out URL đã được cấu hình.
- OAuth Authorization Code Grant đã được bật.
- Các scopes `openid`, `email` và `profile` đã được chọn.
- Nhóm `Customer` đã được tạo.
- Nhóm `Admin` đã được tạo.
- Tài khoản Customer kiểm thử đã được tạo.
- Tài khoản Admin kiểm thử đã được tạo.
- Customer đã được thêm vào nhóm `Customer`.
- Admin đã được thêm vào nhóm `Admin`.
- Trang đăng nhập Cognito có thể truy cập.
- Hệ thống đã sẵn sàng để tích hợp Cognito với frontend và API Gateway.