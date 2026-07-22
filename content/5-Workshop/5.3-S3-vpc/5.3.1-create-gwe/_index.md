---
title: "Create S3 Buckets for Web Applications"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

In this step, you will create two Amazon S3 buckets to store the CloudPay frontend applications:

- **Customer Web Application**: The interface used by customers to create and complete payments.
- **Admin Dashboard**: The interface used by administrators to monitor and manage transactions.

Both S3 buckets will remain private. Users will access the applications through Amazon CloudFront instead of accessing the S3 object URLs directly.

#### Create the Customer Web Application Bucket

1. Open the [Amazon S3 console](https://ap-southeast-1.console.aws.amazon.com/s3/home?region=ap-southeast-1).

2. Verify that the selected AWS Region is:

```text
Asia Pacific (Singapore) ap-southeast-1
```

3. In the Amazon S3 console, choose **Create bucket**.

![Open Amazon S3 Console](/images/5-Workshop/5.3-S3-vpc/s3-console.png)

4. In the **General configuration** section, configure the following values:

- **AWS Region**: Asia Pacific (Singapore) `ap-southeast-1`
- **Bucket type**: General purpose
- **Bucket name**:

```text
cloudpay-customer-web-ACCOUNT_ID
```

Replace `ACCOUNT_ID` with your AWS Account ID.

Example:

```text
cloudpay-customer-web-123456789012
```

{{% notice note %}}
Amazon S3 bucket names must be globally unique. If the bucket name already exists, add your AWS Account ID or another unique string to the bucket name.
{{% /notice %}}

![Configure Customer Bucket Name](/images/5-Workshop/5.3-S3-vpc/create-customer-bucket1.png)

5. In the **Object Ownership** section, select:

```text
ACLs disabled (recommended)
Bucket owner enforced
```

This configuration disables ACLs and allows access to be managed through IAM policies and bucket policies.

![Configure Object Ownership](/images/5-Workshop/5.3-S3-vpc/object-ownership.png)

6. In the **Block Public Access settings for this bucket** section, keep the following option enabled:

```text
Block all public access
```

Verify that all four public access settings remain enabled:

- Block public access granted through new ACLs.
- Block public access granted through any ACLs.
- Block public access granted through new public bucket policies.
- Block public and cross-account access through public bucket policies.

{{% notice warning %}}
Do not disable Block Public Access. The CloudPay S3 buckets must remain private. Amazon CloudFront will be granted permission to read the bucket content through Origin Access Control in the next step.
{{% /notice %}}

![Enable Block Public Access](/images/5-Workshop/5.3-S3-vpc/block-public-access.png)

7. In the **Bucket Versioning** section, select:

```text
Enable
```

Bucket Versioning allows you to restore previous versions of frontend files if they are overwritten or updated incorrectly.

For a short workshop environment, you may select `Disable` to reduce storage usage.

8. In the **Default encryption** section, select:

```text
Server-side encryption with Amazon S3 managed keys (SSE-S3)
```

Keep the remaining encryption settings at their default values.

![Configure S3 Encryption](/images/5-Workshop/5.3-S3-vpc/s3-encryption.png)

9. You do not need to add tags for this workshop.

10. Review the configuration and choose **Create bucket**.

![Create Customer Web Bucket](/images/5-Workshop/5.3-S3-vpc/create-customer-bucket2.png)

11. After the bucket is created, verify that it appears in the bucket list:

```text
cloudpay-customer-web-ACCOUNT_ID
```

![Customer Bucket Created](/images/5-Workshop/5.3-S3-vpc/customer-bucket-complete.png)

#### Create the Admin Dashboard Bucket

12. Choose **Create bucket** again to create the second bucket.

13. In the **General configuration** section, enter the following bucket name:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

Example:

```text
cloudpay-admin-dashboard-123456789012
```

14. Use the same configuration as the Customer Web Application bucket:

- **AWS Region**: `ap-southeast-1`
- **Bucket type**: General purpose
- **Object Ownership**: Bucket owner enforced
- **Block Public Access**: Enabled
- **Bucket Versioning**: Enabled or disabled depending on the workshop environment
- **Default encryption**: SSE-S3
- Do not configure public ACLs
- Do not enable Static Website Hosting

15. Choose **Create bucket**.

After completing this step, the S3 bucket list should contain:

```text
cloudpay-customer-web-ACCOUNT_ID
cloudpay-admin-dashboard-ACCOUNT_ID
```

![CloudPay S3 Buckets](/images/5-Workshop/5.3-S3-vpc/cloudpay-buckets.png)

#### Create a Test Customer Web Page

16. On your computer, create a file named:

```text
index.html
```

17. Add the following content:

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
        <p>A secure online payment system built on AWS.</p>

        <button type="button">
            Pay Now
        </button>
    </main>
</body>
</html>
```

18. Open the following bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

19. Choose **Upload**.

20. Choose **Add files**, and then select the `index.html` file.

![Upload Customer index.html](/images/5-Workshop/5.3-S3-vpc/upload-customer-index.png)

21. Keep the default upload settings and choose **Upload**.

22. After the upload is complete, choose **Close**.

![Customer File Upload Complete](/images/5-Workshop/5.3-S3-vpc/upload-customer-complete.png)

#### Create a Test Admin Dashboard Page

23. Create another `index.html` file in a separate folder for the Admin Dashboard.

24. Add the following content:

```html
<!DOCTYPE html>
<html lang="en">
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
        <p>Transaction management and monitoring portal.</p>

        <section>
            <h2>Transaction Management</h2>

            <ul>
                <li>View transaction history</li>
                <li>Review failed transactions</li>
                <li>Retry messages from the DLQ</li>
                <li>View payment reports</li>
            </ul>
        </section>
    </main>
</body>
</html>
```

25. Open the following bucket:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

26. Choose **Upload**, and then upload the Admin Dashboard `index.html` file.

27. After the upload is complete, verify that the file appears in the **Objects** tab.

![Upload Admin Dashboard](/images/5-Workshop/5.3-S3-vpc/upload-admin-index.png)

#### Test Direct S3 Access

28. Open one of the two buckets and select the `index.html` object.

29. In the object details, copy the **Object URL**.

Example:

```text
https://cloudpay-customer-web-ACCOUNT_ID.s3.ap-southeast-1.amazonaws.com/index.html
```

30. Open the object URL in a new browser tab.

Because the S3 bucket is private, the browser should display an error similar to:

```text
AccessDenied
```

This is the expected result because users are not allowed to access the S3 objects directly.

![S3 Access Denied](/images/5-Workshop/5.3-S3-vpc/s3-access-denied.png)

{{% notice note %}}
In the next step, you will create an Amazon CloudFront distribution and configure Origin Access Control. This allows CloudFront to retrieve content from the private S3 buckets without making the buckets public.
{{% /notice %}}

#### Expected Results

After completing this section:

- The Customer Web Application S3 bucket has been created.
- The Admin Dashboard S3 bucket has been created.
- Both buckets are deployed in `ap-southeast-1`.
- Object Ownership is configured as `Bucket owner enforced`.
- Block Public Access is enabled.
- Server-side encryption with SSE-S3 is enabled.
- An `index.html` file has been uploaded to each bucket.
- Direct access through the S3 Object URL is denied.
- Both buckets are ready to be integrated with Amazon CloudFront.