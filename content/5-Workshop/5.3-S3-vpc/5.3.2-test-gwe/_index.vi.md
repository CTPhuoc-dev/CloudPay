---
title: "Cấu hình CloudFront và kiểm tra truy cập"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

Trong bước trước, chúng ta đã tạo hai Amazon S3 Bucket private:

```text
cloudpay-customer-web-ACCOUNT_ID
cloudpay-admin-dashboard-ACCOUNT_ID
```

Trong bước này, chúng ta sẽ:

- Tạo CloudFront Distribution cho Customer Web Application.
- Tạo CloudFront Distribution cho Admin Dashboard.
- Cấu hình Origin Access Control.
- Cập nhật S3 Bucket Policy.
- Bắt buộc người dùng sử dụng HTTPS.
- Kiểm tra truy cập trực tiếp vào S3.
- Kiểm tra truy cập thông qua CloudFront.
- Cập nhật nội dung và xóa CloudFront cache.

#### Giới thiệu về Amazon CloudFront

Amazon CloudFront là dịch vụ Content Delivery Network của AWS. CloudFront phân phối nội dung từ các Edge Location để giảm độ trễ khi người dùng truy cập ứng dụng.

Trong hệ thống CloudPay, CloudFront được sử dụng để phân phối:

- Customer Web Application.
- Trang kết quả thanh toán.
- Trang tra cứu trạng thái giao dịch.
- Admin Dashboard.

Hai S3 Bucket vẫn được giữ private. CloudFront sử dụng Origin Access Control để đọc object từ S3.

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
```

{{% notice note %}}
Không bật S3 Static Website Hosting trong kiến trúc này. CloudFront sử dụng S3 Bucket Origin kết hợp với Origin Access Control để truy cập nội dung private.
{{% /notice %}}

#### Tạo CloudFront Distribution cho Customer Web Application

1. Mở [Amazon CloudFront Console](https://console.aws.amazon.com/cloudfront/v4/home).

2. Trong thanh điều hướng bên trái, chọn **Distributions**.

3. Chọn **Create distribution**.

![Mở CloudFront Console](/images/5-Workshop/5.3-S3-vpc/cloudfront-console.png)

4. Trong phần **Origin**, tại trường **Origin domain**, chọn S3 Bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

Không chọn S3 Website Endpoint.

![Chọn Customer S3 Origin](/images/5-Workshop/5.3-S3-vpc/customer-origin.png)

5. Trong phần **Origin access**, chọn:

```text
Origin access control settings (recommended)
```

6. Chọn **Create new OAC**.

7. Nhập tên Origin Access Control:

```text
cloudpay-customer-oac
```

8. Trong phần ký request, giữ nguyên lựa chọn:

```text
Sign requests (recommended)
```

9. Chọn **Create**.

![Tạo Customer OAC](/images/5-Workshop/5.3-S3-vpc/create-customer-oac.png)

#### Cấu hình Cache Behavior

10. Trong phần **Default cache behavior**, cấu hình:

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

- **Origin request policy**: Để trống hoặc sử dụng cấu hình mặc định.

![Cấu hình Customer Cache Behavior](/images/5-Workshop/5.3-S3-vpc/customer-cache-behavior.png)

Việc chọn `Redirect HTTP to HTTPS` đảm bảo request sử dụng HTTP sẽ được chuyển sang HTTPS.

#### Cấu hình Distribution

11. Trong phần **Settings**, tại **Default root object**, nhập:

```text
index.html
```

Khi người dùng truy cập domain CloudFront mà không nhập đường dẫn object, CloudFront sẽ trả về file `index.html`.

12. Trong phần **Web Application Firewall**, giữ WAF chưa được cấu hình trong bước này.

AWS WAF sẽ được thêm ở phần bảo mật và giám sát sau.

13. Trong phần mô tả, có thể nhập:

```text
CloudPay Customer Web Application
```

14. Kiểm tra lại cấu hình và chọn **Create distribution**.

![Tạo Customer Distribution](/images/5-Workshop/5.3-S3-vpc/create-customer-distribution.png)

15. Chờ đến khi Distribution có trạng thái:

```text
Deployed
```

Quá trình triển khai có thể cần một khoảng thời gian để cấu hình được phân phối đến các CloudFront Edge Location.

![Customer Distribution Deployed](/images/5-Workshop/5.3-S3-vpc/customer-distribution-deployed.png)

#### Cập nhật Bucket Policy cho Customer Web Application

16. Mở Distribution vừa tạo.

17. Sao chép **Distribution ID**.

Ví dụ:

```text
E123456EXAMPLE
```

18. Mở Amazon S3 Console.

19. Chọn bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

20. Chọn tab **Permissions**.

21. Trong phần **Bucket policy**, chọn **Edit**.

22. Thêm Bucket Policy sau:

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

Thay các giá trị:

```text
ACCOUNT_ID
CUSTOMER_DISTRIBUTION_ID
```

bằng AWS Account ID và CloudFront Distribution ID của bạn.

Ví dụ:

```text
arn:aws:cloudfront::123456789012:distribution/E123456EXAMPLE
```

23. Chọn **Save changes**.

![Customer Bucket Policy](/images/5-Workshop/5.3-S3-vpc/customer-bucket-policy.png)

{{% notice warning %}}
Bucket Policy chỉ cấp quyền `s3:GetObject` cho đúng CloudFront Distribution. Không sử dụng Principal `"*"` và không tắt Block Public Access.
{{% /notice %}}

#### Kiểm tra Customer Web Application

24. Quay lại CloudFront Distribution.

25. Sao chép **Distribution domain name**.

Ví dụ:

```text
d123example.cloudfront.net
```

26. Mở domain bằng trình duyệt:

```text
https://d123example.cloudfront.net
```

Kết quả mong đợi:

```text
CloudPay Payment System
A secure online payment system built on AWS.
Pay Now
```

![Customer Web thông qua CloudFront](/images/5-Workshop/5.3-S3-vpc/customer-cloudfront-success.png)

27. Thử truy cập trực tiếp S3 Object URL:

```text
https://cloudpay-customer-web-ACCOUNT_ID.s3.ap-southeast-1.amazonaws.com/index.html
```

Kết quả mong đợi:

```text
AccessDenied
```

Điều này xác nhận:

- S3 Bucket vẫn private.
- Người dùng không thể bỏ qua CloudFront.
- CloudFront có quyền đọc object thông qua OAC.

![Customer S3 Access Denied](/images/5-Workshop/5.3-S3-vpc/customer-s3-denied.png)

#### Tạo CloudFront Distribution cho Admin Dashboard

28. Quay lại trang **CloudFront Distributions**.

29. Chọn **Create distribution**.

30. Trong phần **Origin domain**, chọn bucket:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

31. Trong phần **Origin access**, chọn:

```text
Origin access control settings (recommended)
```

32. Chọn **Create new OAC** và nhập:

```text
cloudpay-admin-oac
```

33. Giữ nguyên lựa chọn:

```text
Sign requests (recommended)
```

34. Chọn **Create**.

![Tạo Admin OAC](/images/5-Workshop/5.3-S3-vpc/create-admin-oac.png)

35. Trong phần **Default cache behavior**, cấu hình:

```text
Viewer protocol policy: Redirect HTTP to HTTPS
Allowed HTTP methods: GET, HEAD
Cache policy: CachingOptimized
Restrict viewer access: No
```

36. Trong phần **Settings**, nhập Default Root Object:

```text
index.html
```

37. Trong phần mô tả, nhập:

```text
CloudPay Admin Dashboard
```

38. Chọn **Create distribution**.

39. Chờ Distribution có trạng thái:

```text
Deployed
```

![Admin Distribution Deployed](/images/5-Workshop/5.3-S3-vpc/admin-distribution-deployed.png)

#### Cập nhật Bucket Policy cho Admin Dashboard

40. Sao chép Distribution ID của Admin Dashboard.

41. Mở S3 Bucket:

```text
cloudpay-admin-dashboard-ACCOUNT_ID
```

42. Chọn:

```text
Permissions → Bucket policy → Edit
```

43. Thêm Bucket Policy sau:

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
": "AllowCloudFrontReadAdminDashboard                "StringEquals": {
                    "AWS:SourceArn": "arn:aws:cloudfront::ACCOUNT_ID:distribution/ADMIN_DISTRIBUTION_ID"
                }
            }
        }
    ]
}
```

Thay:

```text
ACCOUNT_ID
ADMIN_DISTRIBUTION_ID
```

bằng thông tin thực tế.

44. Chọn **Save changes**.

![Admin Bucket Policy](/images/5-Workshop/5.3-S3-vpc/admin-bucket-policy.png)

#### Kiểm tra Admin Dashboard

45. Mở CloudFront Distribution của Admin Dashboard.

46. Sao chép Distribution domain name.

47. Mở domain bằng trình duyệt:

```text
https://ADMIN_DISTRIBUTION_DOMAIN
```

Kết quả mong đợi:

```text
CloudPay Admin Dashboard
Transaction management and monitoring portal.
```

Trang có thể hiển thị các chức năng thử nghiệm:

- View transaction history.
- Review failed transactions.
- Retry messages from the DLQ.
- View payment reports.

![Admin Dashboard thông qua CloudFront](/images/5-Workshop/5.3-S3-vpc/admin-cloudfront-success.png)

48. Thử truy cập trực tiếp Admin S3 Object URL:

```text
https://cloudpay-admin-dashboard-ACCOUNT_ID.s3.ap-southeast-1.amazonaws.com/index.html
```

Kết quả mong đợi:

```text
AccessDenied
```

![Admin S3 Access Denied](/images/5-Workshop/5.3-S3-vpc/admin-s3-denied.png)

#### Cập nhật nội dung frontend

49. Mở file `index.html` của Customer Web Application trên máy tính.

50. Thay đổi nội dung:

```html
<p>CloudPay frontend has been updated successfully.</p>
```

51. Upload lại file vào bucket:

```text
cloudpay-customer-web-ACCOUNT_ID
```

52. Khi hệ thống hỏi xác nhận ghi đè object, chọn **Upload**.

53. Mở lại CloudFront domain.

Nội dung cũ vẫn có thể xuất hiện vì CloudFront đang lưu object trong cache.

#### Tạo CloudFront Invalidation

54. Mở Customer CloudFront Distribution.

55. Chọn tab **Invalidations**.

56. Chọn **Create invalidation**.

57. Trong phần **Object paths**, nhập:

```text
/*
```

58. Chọn **Create invalidation**.

![Tạo CloudFront Invalidation](/images/5-Workshop/5.3-S3-vpc/create-invalidation.png)

59. Chờ Invalidation có trạng thái:

```text
Completed
```

60. Tải lại trang CloudFront.

Kết quả mong đợi:

```text
CloudPay frontend has been updated successfully.
```

![CloudFront Updated Content](/images/5-Workshop/5.3-S3-vpc/cloudfront-updated-content.png)

Có thể tạo Invalidation bằng AWS CLI:

```bash
aws cloudfront create-invalidation \
  --distribution-id CUSTOMER_DISTRIBUTION_ID \
  --paths "/*"
```

Thay `CUSTOMER_DISTRIBUTION_ID` bằng Distribution ID thực tế.

#### Xử lý lỗi thường gặp

##### Lỗi AccessDenied khi truy cập CloudFront

Kiểm tra:

- S3 Bucket Policy đã đúng bucket ARN.
- Distribution ID trong `AWS:SourceArn` đã chính xác.
- OAC đã được liên kết với đúng S3 Origin.
- File `index.html` đã tồn tại trong bucket.
- Default Root Object đã được đặt thành `index.html`.
- Distribution đã có trạng thái `Deployed`.

##### Lỗi NoSuchKey

Lỗi này xảy ra khi CloudFront không tìm thấy object.

Kiểm tra:

```text
File name: index.html
Default root object: index.html
```

Tên object có phân biệt chữ hoa và chữ thường.

##### Trang vẫn hiển thị nội dung cũ

Tạo Invalidation với đường dẫn:

```text
/*
```

Sau đó đợi trạng thái chuyển thành `Completed` và tải lại trình duyệt.

##### CloudFront Distribution đang ở trạng thái Deploying

Chờ Distribution chuyển sang:

```text
Deployed
```

Không tiếp tục kiểm thử trước khi quá trình triển khai hoàn tất.

#### Tóm tắt

Trong phần này, bạn đã:

- Tạo CloudFront Distribution cho Customer Web Application.
- Tạo CloudFront Distribution cho Admin Dashboard.
- Cấu hình Origin Access Control.
- Cấp quyền cho CloudFront bằng S3 Bucket Policy.
- Giữ hai S3 Bucket ở trạng thái private.
- Bắt buộc người dùng sử dụng HTTPS.
- Cấu hình `index.html` làm Default Root Object.
- Kiểm tra truy cập Customer Web Application.
- Kiểm tra truy cập Admin Dashboard.
- Xác nhận truy cập trực tiếp S3 bị từ chối.
- Cập nhật frontend và tạo CloudFront Invalidation.

#### Kết quả mong đợi

Sau khi hoàn thành bước này:

- Customer Web Application truy cập được thông qua CloudFront.
- Admin Dashboard truy cập được thông qua CloudFront.
- Hai S3 Bucket vẫn bật Block Public Access.
- Truy cập trực tiếp S3 Object URL trả về `AccessDenied`.
- CloudFront đọc được nội dung thông qua Origin Access Control.
- HTTP request được chuyển sang HTTPS.
- File `index.html` được sử dụng làm trang mặc định.
- Nội dung mới hiển thị sau khi tạo Invalidation.
- Frontend CloudPay đã sẵn sàng để tích hợp Amazon Cognito và API Gateway.