---
title: "Prepare Authentication Resources"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

In this section, you will prepare the Amazon Cognito resources required for authentication and authorization in the CloudPay system.

The system includes two user groups:

- **Customer**: Signs in, creates payment requests, and views transaction history.
- **Admin**: Monitors transactions, views reports, and handles failed transactions.

The following resources will be created:

- One Amazon Cognito User Pool.
- One App Client for the Customer Web Application.
- One App Client for the Admin Dashboard.
- One Cognito domain for the managed login page.
- A `Customer` user group.
- An `Admin` user group.
- Two test user accounts.
- Callback URLs and sign-out URLs for both applications.

The authentication flow is:

```text
Customer or Administrator
          ↓
   Amazon CloudFront
          ↓
Amazon Cognito Managed Login
          ↓
 User Authentication
          ↓
      JWT Tokens
          ↓
Customer Web Application or Admin Dashboard
          ↓
  Amazon API Gateway
```

#### Prepare the CloudFront Domains

Before creating the App Clients, record the domain names of the two CloudFront distributions created in the previous section.

Customer Web Application domain:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN
```

Example:

```text
https://d123customerexample.cloudfront.net
```

Admin Dashboard domain:

```text
https://ADMIN_DISTRIBUTION_DOMAIN
```

Example:

```text
https://d456adminexample.cloudfront.net
```

These domains will be used to configure:

- Callback URLs.
- Sign-out URLs.
- Redirect URLs after successful authentication.

{{% notice note %}}
Replace `CUSTOMER_DISTRIBUTION_DOMAIN` and `ADMIN_DISTRIBUTION_DOMAIN` with your actual CloudFront distribution domains.
{{% /notice %}}

#### Create an Amazon Cognito User Pool

1. Open the [Amazon Cognito Console](https://ap-southeast-1.console.aws.amazon.com/cognito/v2/home?region=ap-southeast-1).

2. Verify that the selected AWS Region is:

```text
Asia Pacific (Singapore) ap-southeast-1
```

3. Choose **User pools**.

4. Choose **Create user pool** or **Create user directory**, depending on the AWS Console interface.

![Open Amazon Cognito Console](/images/5-Workshop/5.4-S3-onprem/cognito-console.png)

5. For the application name or User Pool name, enter:

```text
cloudpay-user-pool
```

6. Select the following sign-in option:

```text
Email
```

CloudPay users will use their email addresses to sign in.

7. Enable email verification when the option is available:

```text
Send email message, verify email address
```

8. In the user attributes section, select the following required attributes:

```text
email
name
```

9. In the password policy section, use the following recommended configuration:

```text
Minimum password length: 8
Require uppercase letters: Yes
Require lowercase letters: Yes
Require numbers: Yes
Require special characters: Yes
```

10. For the workshop environment, configure MFA as:

```text
MFA: Optional
```

In a production environment, MFA should be required for administrator accounts.

11. In the user registration section, you can enable:

```text
Enable self-registration
```

This option allows customers to register their own CloudPay accounts.

{{% notice warning %}}
Do not allow users to assign themselves administrator permissions. Administrator accounts must be created or added to the `Admin` group by a system administrator.
{{% /notice %}}

12. Review the configuration and choose **Create user pool**.

13. After the User Pool is created, record the **User Pool ID**.

Example:

```text
ap-southeast-1_AbCdEf123
```

![Cognito User Pool Created](/images/5-Workshop/5.4-S3-onprem/user-pool-created.png)

#### Create an App Client for the Customer Web Application

14. Open the following User Pool:

```text
cloudpay-user-pool
```

15. Choose **App clients** or **Applications**.

16. Choose **Create app client**.

17. For the application type, select the option suitable for a browser-based frontend application:

```text
Single-page application
```

18. Enter the following App Client name:

```text
cloudpay-customer-app-client
```

19. Do not create a Client Secret for the browser-based frontend application:

```text
Generate client secret: No
```

{{% notice warning %}}
The Customer Web Application is a public frontend application and cannot securely protect a Client Secret. Do not store a Client Secret in JavaScript files or frontend configuration files.
{{% /notice %}}

20. For the Callback URL, enter:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN/callback
```

Example:

```text
https://d123customerexample.cloudfront.net/callback
```

21. For the Sign-out URL, enter:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN/
```

22. In the OAuth 2.0 grant types section, select:

```text
Authorization code grant
```

23. In the OpenID Connect scopes section, select:

```text
openid
email
profile
```

24. For the Identity Provider, select:

```text
Cognito user pool
```

25. Choose **Create app client**.

26. After the App Client is created, record its **Client ID**.

Example:

```text
4examplecustomerclientid123
```

![Customer App Client](/images/5-Workshop/5.4-S3-onprem/customer-app-client.png)

#### Create an App Client for the Admin Dashboard

27. In the User Pool, choose **Create app client** again.

28. Select the following application type:

```text
Single-page application
```

29. Enter the App Client name:

```text
cloudpay-admin-app-client
```

30. Do not create a Client Secret:

```text
Generate client secret: No
```

31. For the Callback URL, enter:

```text
https://ADMIN_DISTRIBUTION_DOMAIN/callback
```

Example:

```text
https://d456adminexample.cloudfront.net/callback
```

32. For the Sign-out URL, enter:

```text
https://ADMIN_DISTRIBUTION_DOMAIN/
```

33. Select the following OAuth 2.0 grant type:

```text
Authorization code grant
```

34. Select the following OpenID Connect scopes:

```text
openid
email
profile
```

35. Select the following Identity Provider:

```text
Cognito user pool
```

36. Choose **Create app client**.

37. Record the Client ID of the Admin Dashboard App Client.

Example:

```text
7exampleadminclientid456
```

![Admin App Client](/images/5-Workshop/5.4-S3-onprem/admin-app-client.png)

#### Create a Cognito Domain

38. In the User Pool, open:

```text
Branding → Domain
```

Depending on the AWS Console interface, this menu may be displayed as **Domain**, **Managed login**, or **Hosted UI**.

39. Choose the option to create a Cognito domain.

40. Enter the following domain prefix:

```text
cloudpay-ACCOUNT_ID
```

Example:

```text
cloudpay-123456789012
```

The complete domain has the following format:

```text
https://cloudpay-ACCOUNT_ID.auth.ap-southeast-1.amazoncognito.com
```

41. Choose **Create Cognito domain**.

{{% notice note %}}
The domain prefix must be unique within the AWS Region. If the name is already in use, add your AWS Account ID or another unique string.
{{% /notice %}}

42. After the domain is created, record the Cognito domain.

![Cognito Domain](/images/5-Workshop/5.4-S3-onprem/cognito-domain.png)

#### Verify the Managed Login Page

43. In the **App clients** section, select:

```text
cloudpay-customer-app-client
```

44. Open the **Login pages** section.

45. Choose **View login page**.

46. The Amazon Cognito managed login page opens in a new browser tab.

The URL has a format similar to:

```text
https://cloudpay-ACCOUNT_ID.auth.ap-southeast-1.amazoncognito.com/login
```

47. Verify that the login page includes:

- Email.
- Password.
- Sign in.
- Sign up, when self-registration is enabled.
- Forgot password.

![Cognito Login Page](/images/5-Workshop/5.4-S3-onprem/cognito-login-page.png)

#### Create the Customer Group

48. In the User Pool, choose **Groups**.

49. Choose **Create group**.

50. Enter the following group name:

```text
Customer
```

51. Enter the following description:

```text
CloudPay customers who can create payments and view their own transactions.
```

52. For **Precedence**, enter:

```text
10
```

53. You do not need to attach an IAM Role in this workshop.

54. Choose **Create group**.

![Customer Group](/images/5-Workshop/5.4-S3-onprem/customer-group.png)

#### Create the Admin Group

55. Choose **Create group** again.

56. Enter the following group name:

```text
Admin
```

57. Enter the following description:

```text
CloudPay administrators who can monitor transactions and perform retry operations.
```

58. For **Precedence**, enter:

```text
1
```

59. You do not need to attach an IAM Role in this workshop.

60. Choose **Create group**.

![Admin Group](/images/5-Workshop/5.4-S3-onprem/admin-group.png)

After completing these steps, the Groups list should contain:

```text
Admin
Customer
```

#### Create a Test Customer Account

61. In the User Pool, choose **Users**.

62. Choose **Create user**.

63. Enter the following test account information:

```text
Email: customer@example.com
Temporary password: Customer@123
Mark email as verified: Yes
```

Replace `customer@example.com` with your test email address.

64. Choose **Create user**.

65. Open the Customer account that you created.

66. Choose **Add user to group**.

67. Select:

```text
Customer
```

68. Confirm that the user should be added to the group.

![Customer Test User](/images/5-Workshop/5.4-S3-onprem/customer-test-user.png)

#### Create a Test Admin Account

69. Return to the Users list.

70. Choose **Create user**.

71. Enter the following account information:

```text
Email: admin@example.com
Temporary password: Admin@123
Mark email as verified: Yes
```

Replace `admin@example.com` with your actual administrator email address.

72. Choose **Create user**.

73. Open the Admin account that you created.

74. Choose **Add user to group**.

75. Select:

```text
Admin
```

76. Confirm that the user should be added to the group.

![Admin Test User](/images/5-Workshop/5.4-S3-onprem/admin-test-user.png)

{{% notice warning %}}
Do not use the example passwords in a production environment. Users will be required to change their temporary passwords during their first sign-in.
{{% /notice %}}

#### Test Customer Sign-In

77. Open the managed login page for the Customer App Client.

78. Sign in using the Customer test account.

79. Change the temporary password when prompted.

80. After successful authentication, Cognito redirects the user to:

```text
https://CUSTOMER_DISTRIBUTION_DOMAIN/callback
```

When the `/callback` page has not yet been created, CloudFront may display an `AccessDenied` or `404` error.

This can be an expected result during the resource preparation stage. The callback page will be implemented in the frontend integration section.

#### Test Admin Sign-In

81. Open the managed login page for the Admin App Client.

82. Sign in using the Admin test account.

83. Change the temporary password when prompted.

84. After successful authentication, Cognito redirects the administrator to:

```text
https://ADMIN_DISTRIBUTION_DOMAIN/callback
```

#### Verify the User Group in the JWT Token

After successful authentication, Amazon Cognito issues the following tokens:

- ID Token.
- Access Token.
- Refresh Token.

The user group information is included in the following claim:

```text
cognito:groups
```

For a Customer account, the expected claim is:

```json
{
    "cognito:groups": [
        "Customer"
    ]
}
```

For an Admin account, the expected claim is:

```json
{
    "cognito:groups": [
        "Admin"
    ]
}
```

API Gateway and Lambda functions can use this claim to verify user permissions.

Example authorization model:

```text
Customer
→ Create payments
→ View their own transactions

Admin
→ View all transactions
→ View reports
→ Review DLQ messages
→ Perform retry operations
```

#### Record the Configuration Values

Save the following values for use in the next workshop sections:

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

Do not store real user passwords or authentication tokens in a GitHub repository.

#### Expected Results

After completing this section:

- An Amazon Cognito User Pool has been created.
- A Customer App Client has been created.
- An Admin App Client has been created.
- A Cognito domain has been configured.
- Callback URLs and sign-out URLs have been configured.
- OAuth Authorization Code Grant has been enabled.
- The `openid`, `email`, and `profile` scopes have been selected.
- The `Customer` group has been created.
- The `Admin` group has been created.
- A Customer test account has been created.
- An Admin test account has been created.
- The Customer account has been added to the `Customer` group.
- The Admin account has been added to the `Admin` group.
- The Cognito managed login page is accessible.
- The system is ready to integrate Amazon Cognito with the frontend and Amazon API Gateway.