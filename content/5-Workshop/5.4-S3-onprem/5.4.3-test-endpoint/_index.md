---
title: "Test Cognito Sign-In"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

In this section, you will test the sign-in and sign-out functions of the Customer Web Application and Admin Dashboard.

#### Check the Customer Account

1. Open the Amazon Cognito Console.

2. Select the following User Pool:

```text
cloudpay-user-pool
```

3. Choose **Users**.

4. Verify that the Customer account has been created.

Example:

```text
customer@example.com
```

5. Open the Customer account and verify that the user belongs to the following group:

```text
Customer
```

![Check Customer User](/images/5-Workshop/5.4-S3-onprem/check-customer-user.png)

#### Test Customer Sign-In

6. Open the Customer CloudFront domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN
```

7. Choose **Sign in**.

8. The browser redirects you to the Amazon Cognito login page.

9. Enter the Customer email address and password.

10. For the first sign-in, Cognito may require the user to change the temporary password.

11. After successful authentication, Cognito redirects the user back to the Customer Web Application.

The URL may have the following format:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/?code=EXAMPLE_CODE
```

This result shows that Cognito has authenticated the Customer account successfully.

![Customer Login Success](/images/5-Workshop/5.4-S3-onprem/customer-login-success.png)

#### Check the Admin Account

12. Return to the Amazon Cognito Console.

13. In the Users list, verify that the Admin account has been created.

Example:

```text
admin@example.com
```

14. Open the Admin account and verify that the user belongs to the following group:

```text
Admin
```

![Check Admin User](/images/5-Workshop/5.4-S3-onprem/check-admin-user.png)

#### Test Admin Sign-In

15. Open the Admin CloudFront domain:

```text
https://ADMIN_CLOUDFRONT_DOMAIN
```

16. Choose **Sign in**.

17. Enter the Admin email address and password.

18. After successful authentication, Cognito redirects the user back to the Admin Dashboard.

The URL may have the following format:

```text
https://ADMIN_CLOUDFRONT_DOMAIN/?code=EXAMPLE_CODE
```

![Admin Login Success](/images/5-Workshop/5.4-S3-onprem/admin-login-success.png)

#### Test Sign-Out

19. On the Customer Web Application or Admin Dashboard, choose **Sign out**.

20. Cognito ends the login session and redirects the user back to the CloudFront domain.

Expected result:

```text
The user has signed out of Amazon Cognito.
```

#### Common Errors

##### redirect_mismatch Error

Verify that the Callback URL in the Cognito App Client exactly matches the CloudFront domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN/
```

or:

```text
https://ADMIN_CLOUDFRONT_DOMAIN/
```

##### The Login Page Does Not Open

Verify that:

- The Cognito domain has been created.
- The App Client has been configured.
- The Callback URL has been saved.
- The `openid`, `email`, and `profile` scopes have been selected.

##### The Page Still Shows Old Content

Create a CloudFront invalidation using:

```text
/*
```

Then reload the page using:

```text
Ctrl + Shift + R
```

{{% notice warning %}}
This step only tests user authentication. Customer and Admin authorization will also be checked by API Gateway and AWS Lambda in the next sections.
{{% /notice %}}

#### Summary

In this section, you completed the following tasks:

- Checked the Customer account.
- Checked the Admin account.
- Verified the `Customer` group.
- Verified the `Admin` group.
- Signed in as a Customer through Amazon Cognito.
- Signed in as an Admin through Amazon Cognito.
- Tested the redirect to the correct CloudFront domain.
- Tested the sign-out function.

#### Expected Results

After completing this section:

- The Customer can sign in successfully.
- The Admin can sign in successfully.
- Cognito redirects each user to the correct CloudFront domain.
- The sign-out function works.
- The authentication system is ready to connect to Amazon API Gateway.