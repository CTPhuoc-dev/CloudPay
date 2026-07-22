---
title: "Connect Cognito to the Frontend"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

In the previous section, you created:

- A Cognito User Pool.
- A Customer App Client.
- An Admin App Client.
- The `Customer` and `Admin` groups.
- Test user accounts.

In this section, you will connect the Amazon Cognito login page to the Customer Web Application and Admin Dashboard.

#### Prepare the Configuration Information

Record the following information:

```text
COGNITO_DOMAIN
CUSTOMER_CLIENT_ID
ADMIN_CLIENT_ID
CUSTOMER_CLOUDFRONT_DOMAIN
ADMIN_CLOUDFRONT_DOMAIN
```

Example Cognito domain:

```text
https://cloudpay-123456789012.auth.ap-southeast-1.amazoncognito.com
```

#### Update the Callback URLs

1. Open the Amazon Cognito Console.

2. Select the following User Pool:

```text
cloudpay-user-pool
```

3. Select the Customer App Client:

```text
cloudpay-customer-app-client
```

4. For the Callback URL, enter:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/
```

5. For the Sign-out URL, enter:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/
```

6. Choose **Save changes**.

7. Select the Admin App Client:

```text
cloudpay-admin-app-client
```

8. Configure both the Callback URL and Sign-out URL:

```text
https://ADMIN_CLOUDFRONT_DOMAIN/
```

![Update Callback URLs](/images/5-Workshop/5.4-S3-onprem/update-callback-url.png)

#### Create the Customer Login Page

9. Open the Customer Web Application `index.html` file.

10. Replace its content with:

```html
<!DOCTYPE html>
<html lang="en">
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
            Sign in to create payments and view your
            transaction history.
        </p>

        <button onclick="login()">
            Sign in
        </button>

        <button onclick="logout()">
            Sign out
        </button>
    </main>

    <script>
        const cognitoDomain =
            "COGNITO_DOMAIN";

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

11. Replace:

```text
COGNITO_DOMAIN
CUSTOMER_CLIENT_ID
```

with your actual Cognito information.

Example:

```javascript
const cognitoDomain =
    "https://cloudpay-123456789012.auth.ap-southeast-1.amazoncognito.com";

const clientId =
    "4examplecustomerclientid123";
```

{{% notice note %}}
Do not add `/` at the end of the Cognito domain.
{{% /notice %}}

#### Upload the Customer Web Application

12. Open the Amazon S3 Console.

13. Select the following bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

14. Upload the updated file:

```text
index.html
```

15. Open the Customer CloudFront distribution.

16. Create an invalidation using:

```text
/*
```

17. Wait until the invalidation status becomes:

```text
Completed
```

#### Test Customer Sign-In

18. Open the Customer CloudFront domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN
```

19. Choose **Sign in**.

20. The browser redirects you to the Amazon Cognito login page.

21. Sign in using the Customer test account.

22. After successful authentication, Cognito redirects the user back to the Customer Web Application.

The URL may contain an authorization code:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/?code=EXAMPLE_CODE
```

This result shows that Cognito has authenticated the user successfully.

![Customer Login Success](/images/5-Workshop/5.4-S3-onprem/customer-login-success.png)

#### Create the Admin Login Page

23. Open the Admin Dashboard `index.html` file.

24. Use the same content as the Customer Web Application, but replace the Client ID with:

```javascript
const clientId =
    "ADMIN_CLIENT_ID";
```

You can also change the page content to:

```html
<h1>CloudPay Admin Dashboard</h1>

<p>
    Sign in with an Admin account to manage transactions.
</p>
```

25. Upload the updated `index.html` file to:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

26. Create a CloudFront invalidation using:

```text
/*
```

#### Test Admin Sign-In

27. Open the Admin CloudFront domain:

```text
https://ADMIN_CLOUDFRONT_DOMAIN
```

28. Choose **Sign in**.

29. Sign in using an account that belongs to the following group:

```text
Admin
```

30. After successful authentication, Cognito redirects the user back to the Admin Dashboard.

![Admin Login Success](/images/5-Workshop/5.4-S3-onprem/admin-login-success.png)

#### Test Sign-Out

31. On the Customer Web Application or Admin Dashboard, choose **Sign out**.

32. Cognito ends the login session and redirects the user back to the CloudFront domain.

#### Summary

In this section, you completed the following tasks:

- Updated the Callback URL for the Customer App Client.
- Updated the Callback URL for the Admin App Client.
- Added a sign-in button to the Customer Web Application.
- Added a sign-in button to the Admin Dashboard.
- Redirected users to the Cognito login page.
- Tested Customer sign-in.
- Tested Admin sign-in.
- Tested the sign-out function.

#### Expected Results

After completing this section:

- Customers can open the Cognito login page.
- Administrators can open the Cognito login page.
- Cognito can authenticate the test accounts.
- Users are redirected to the correct CloudFront domain.
- The sign-out function works.
- The frontend is ready to connect to Amazon API Gateway.