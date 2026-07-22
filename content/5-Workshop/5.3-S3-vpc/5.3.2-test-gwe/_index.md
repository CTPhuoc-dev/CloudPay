---
title: "Configure CloudFront and Test Access"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

In the previous section, you created two private Amazon S3 buckets:

```text
cloudpay-customer-web-ACCOUNT_ID
cloudpay-admin-dashboard-ACCOUNT_ID
```

In this section, you will:

- Create a CloudFront distribution for the Customer Web Application.
- Create a CloudFront distribution for the Admin Dashboard.
- Configure Origin Access Control.
- Update the S3 bucket policies.
- Redirect HTTP requests to HTTPS.
- Test direct access to Amazon S3.
- Test access through Amazon CloudFront.
- Update frontend content and clear the CloudFront cache.

#### Introduction to Amazon CloudFront

Amazon CloudFront is the AWS Content Delivery Network service. It delivers application content from edge locations to reduce latency for users.

In the CloudPay system, CloudFront is used to deliver:

- The Customer Web Application.
- Payment result pages.
- Transaction tracking pages.
- The Admin Dashboard.

Both S3 buckets remain private. CloudFront uses Origin Access Control to retrieve objects securely from Amazon S3.

The frontend access flow is:

```text
Customer or Administrator
          ↓
        HTTPS
          ↓
  Amazon CloudFront
          ↓
Origin Access Control
          ↓
Private Amazon S3 Bucket
```

{{% notice note %}}
Do not enable S3 Static Website Hosting in this architecture. CloudFront uses the standard S3 bucket origin together with Origin Access Control to retrieve private content.
{{% /notice %}}

#### Create a CloudFront Distribution for the Customer Web Application

1. Open the [Amazon CloudFront Console](https://console.aws.amazon.com/cloudfront/v4/home).

2. In the left navigation pane, choose **Distributions**.

3. Choose **Create distribution**.

![Open CloudFront Console](/images/5-Workshop/5.3-S3-vpc/cloudfront-console.png)

4. In the **Origin** section, for **Origin domain**, select the following S3 bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

Do not select an S3 website endpoint.

![Select Customer S3 Origin](/images/5-Workshop/5.3-S3-vpc/customer-origin.png)

5. In the **Origin access** section, select:

```text
Origin access control settings (recommended)
```

6. Choose **Create new OAC**.

7. Enter the following Origin Access Control name:

```text
cloudpay-customer-oac
```

8. Keep the following request-signing option selected:

```text
Sign requests (recommended)
```

9. Choose **Create**.

![Create Customer OAC](/images/5-Workshop/5.3-S3-vpc/create-customer-oac.png)

#### Configure the Default Cache Behavior

10. In the **Default cache behavior** section, configure the following settings:

- **Viewer protocol policy**:

```text
Redirect HTTP to HTTPS
```

- **Allowed HTTP methods**:

```text
GET, HEAD
```

- **Restrict viewer access**:

```text
No
```

- **Cache policy**:

```text
CachingOptimized
```

- **Origin request policy**: Leave blank or keep the default setting.

![Configure Customer Cache Behavior](/images/5-Workshop/5.3-S3-vpc/customer-cache-behavior.png)

The `Redirect HTTP to HTTPS` option ensures that HTTP requests are automatically redirected to HTTPS.

#### Configure the Distribution

11. In the **Settings** section, enter the following value for **Default root object**:

```text
index.html
```

When users access the CloudFront domain without specifying an object path, CloudFront returns the `index.html` file.

12. In the **Web Application Firewall** section, do not configure AWS WAF at this stage.

AWS WAF will be added later in the security and monitoring section.

13. For the description, you can enter:

```text
CloudPay Customer Web Application
```

14. Review the configuration and choose **Create distribution**.

![Create Customer Distribution](/images/5-Workshop/5.3-S3-vpc/create-customer-distribution.png)

15. Wait until the distribution status becomes:

```text
Deployed
```

CloudFront may require several minutes to deploy the configuration to edge locations.

![Customer Distribution Deployed](/images/5-Workshop/5.3-S3-vpc/customer-distribution-deployed.png)

#### Update the Customer Web Application Bucket Policy

16. Open the CloudFront distribution that you created.

17. Copy the **Distribution ID**.

Example:

```text
E123456EXAMPLE
```

18. Open the Amazon S3 Console.

19. Select the following bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

20. Open the **Permissions** tab.

21. In the **Bucket policy** section, choose **Edit**.

22. Add the following bucket policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCloudFrontReadCustomerWeb",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::cloudpay-customer-web-ACCOUNT_ID/*",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceArn": "arn:aws:cloudfront::ACCOUNT_ID:distribution/CUSTOMER_DISTRIBUTION_ID"
                }
            }
        }
    ]
}
```

Replace the following values:

```text
ACCOUNT_ID
CUSTOMER_DISTRIBUTION_ID
```

with your AWS Account ID and CloudFront Distribution ID.

Example:

```text
arn:aws:cloudfront::123456789012:distribution/E123456EXAMPLE
```

23. Choose **Save changes**.

![Customer Bucket Policy](/images/5-Workshop/5.3-S3-vpc/customer-bucket-policy.png)

{{% notice warning %}}
The bucket policy grants only `s3:GetObject` permission to the specified CloudFront distribution. Do not use `"Principal": "*"` and do not disable S3 Block Public Access.
{{% /notice %}}

#### Test the Customer Web Application

24. Return to the Customer CloudFront distribution.

25. Copy the **Distribution domain name**.

Example:

```text
d123example.cloudfront.net
```

26. Open the domain in a web browser:

```text
https://d123example.cloudfront.net
```

Expected result:

```text
CloudPay Payment System
A secure online payment system built on AWS.
Pay Now
```

![Customer Web through CloudFront](/images/5-Workshop/5.3-S3-vpc/customer-cloudfront-success.png)

27. Try to access the S3 object directly:

```text
https://cloudpay-customer-web-ACCOUNT_ID.s3.ap-southeast-1.amazonaws.com/index.html
```

Expected result:

```text
AccessDenied
```

This confirms that:

- The S3 bucket remains private.
- Users cannot bypass CloudFront.
- CloudFront can read the object through Origin Access Control.

![Customer S3 Access Denied](/images/5-Workshop/5.3-S3-vpc/customer-s3-denied.png)

#### Create a CloudFront Distribution for the Admin Dashboard

28. Return to the **CloudFront Distributions** page.

29. Choose **Create distribution**.

30. In the **Origin domain** field, select:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

31. In the **Origin access** section, select:

```text
Origin access control settings (recommended)
```

32. Choose **Create new OAC**, and enter:

```text
cloudpay-admin-oac
```

33. Keep the following option selected:

```text
Sign requests (recommended)
```

34. Choose **Create**.

![Create Admin OAC](/images/5-Workshop/5.3-S3-vpc/create-admin-oac.png)

35. In the **Default cache behavior** section, configure:

```text
Viewer protocol policy: Redirect HTTP to HTTPS
Allowed HTTP methods: GET, HEAD
Cache policy: CachingOptimized
Restrict viewer access: No
```

36. In the **Settings** section, enter the following Default Root Object:

```text
index.html
```

37. For the description, enter:

```text
CloudPay Admin Dashboard
```

38. Choose **Create distribution**.

39. Wait until the distribution status becomes:

```text
Deployed
```

![Admin Distribution Deployed](/images/5-Workshop/5.3-S3-vpc/admin-distribution-deployed.png)

#### Update the Admin Dashboard Bucket Policy

40. Copy the Distribution ID of the Admin Dashboard distribution.

41. Open the following S3 bucket:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

42. Navigate to:

```text
Permissions → Bucket policy → Edit
```

43. Add the following bucket policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCloudFrontReadAdminDashboard",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::cloudpay-admin-dashboard-ACCOUNT_ID/*",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceArn": "arn:aws:cloudfront::ACCOUNT_ID:distribution/ADMIN_DISTRIBUTION_ID"
                }
            }
        }
    ]
}
```

Replace:

```text
ACCOUNT_ID
ADMIN_DISTRIBUTION_ID
```

with your actual AWS Account ID and Admin CloudFront Distribution ID.

44. Choose **Save changes**.

![Admin Bucket Policy](/images/5-Workshop/5.3-S3-vpc/admin-bucket-policy.png)

#### Test the Admin Dashboard

45. Open the Admin Dashboard CloudFront distribution.

46. Copy the Distribution domain name.

47. Open the domain in a web browser:

```text
https://ADMIN_DISTRIBUTION_DOMAIN
```

Expected result:

```text
CloudPay Admin Dashboard
Transaction management and monitoring portal.
```

The test page may display the following administrative functions:

- View transaction history.
- Review failed transactions.
- Retry messages from the DLQ.
- View payment reports.

![Admin Dashboard through CloudFront](/images/5-Workshop/5.3-S3-vpc/admin-cloudfront-success.png)

48. Try to access the Admin Dashboard S3 object directly:

```text
https://cloudpay-admin-dashboard-ACCOUNT_ID.s3.ap-southeast-1.amazonaws.com/index.html
```

Expected result:

```text
AccessDenied
```

![Admin S3 Access Denied](/images/5-Workshop/5.3-S3-vpc/admin-s3-denied.png)

#### Update the Frontend Content

49. Open the Customer Web Application `index.html` file on your computer.

50. Add or replace the following content:

```html
<p>CloudPay frontend has been updated successfully.</p>
```

51. Upload the updated file to:

```text
cloudpay-customer-web-ACCOUNT_ID
```

52. When Amazon S3 asks for confirmation to overwrite the existing object, choose **Upload**.

53. Open the Customer CloudFront domain again.

The old content may still appear because CloudFront has cached the previous version of the object.

#### Create a CloudFront Invalidation

54. Open the Customer CloudFront distribution.

55. Choose the **Invalidations** tab.

56. Choose **Create invalidation**.

57. In the **Object paths** field, enter:

```text
/*
```

58. Choose **Create invalidation**.

![Create CloudFront Invalidation](/images/5-Workshop/5.3-S3-vpc/create-invalidation.png)

59. Wait until the invalidation status becomes:

```text
Completed
```

60. Reload the CloudFront page.

Expected result:

```text
CloudPay frontend has been updated successfully.
```

![CloudFront Updated Content](/images/5-Workshop/5.3-S3-vpc/cloudfront-updated-content.png)

You can also create an invalidation using AWS CLI:

```bash
aws cloudfront create-invalidation \
  --distribution-id CUSTOMER_DISTRIBUTION_ID \
  --paths "/*"
```

Replace `CUSTOMER_DISTRIBUTION_ID` with the actual CloudFront Distribution ID.

#### Troubleshooting

##### AccessDenied When Accessing CloudFront

Verify the following:

- The S3 bucket ARN in the bucket policy is correct.
- The Distribution ID in `AWS:SourceArn` is correct.
- The OAC is attached to the correct S3 origin.
- The `index.html` file exists in the bucket.
- The Default Root Object is configured as `index.html`.
- The CloudFront distribution status is `Deployed`.

##### NoSuchKey Error

This error occurs when CloudFront cannot find the requested object.

Verify:

```text
File name: index.html
Default root object: index.html
```

Amazon S3 object names are case-sensitive.

##### The Page Still Shows Old Content

Create a CloudFront invalidation using:

```text
/*
```

Wait until the invalidation status becomes `Completed`, and then reload the browser.

##### CloudFront Distribution Status Is Deploying

Wait until the distribution status changes to:

```text
Deployed
```

Do not continue testing before the deployment is complete.

#### Summary

In this section, you completed the following tasks:

- Created a CloudFront distribution for the Customer Web Application.
- Created a CloudFront distribution for the Admin Dashboard.
- Configured Origin Access Control.
- Granted CloudFront access through S3 bucket policies.
- Kept both S3 buckets private.
- Redirected HTTP requests to HTTPS.
- Configured `index.html` as the Default Root Object.
- Tested the Customer Web Application.
- Tested the Admin Dashboard.
- Confirmed that direct access to S3 was denied.
- Updated the frontend and created a CloudFront invalidation.

#### Expected Results

After completing this section:

- The Customer Web Application is accessible through CloudFront.
- The Admin Dashboard is accessible through CloudFront.
- Both S3 buckets still have Block Public Access enabled.
- Direct access through the S3 Object URL returns `AccessDenied`.
- CloudFront can retrieve content through Origin Access Control.
- HTTP requests are redirected to HTTPS.
- The `index.html` file is used as the default page.
- Updated content appears after creating an invalidation.
- The CloudPay frontend is ready to be integrated with Amazon Cognito and Amazon API Gateway.