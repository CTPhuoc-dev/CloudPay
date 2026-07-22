---
title: "Kiểm tra và tích hợp Payment API"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

Trong phần này, bạn sẽ kiểm tra **Payment API** của hệ thống CloudPay và kết nối API với **Customer Web Application**.

Payment API sử dụng:

- **Amazon API Gateway** để tiếp nhận yêu cầu.
- **AWS Lambda** để xử lý logic thanh toán.
- **Amazon CloudWatch** để lưu và kiểm tra log.
- **Amazon CloudFront** để phân phối giao diện Customer Web.

Luồng hoạt động của hệ thống:

```text
Customer Web
    ↓
Amazon CloudFront
    ↓
Amazon API Gateway
    ↓
AWS Lambda
    ↓
Kết quả giao dịch
```

{{% notice note %}}
CloudPay hiện chỉ mô phỏng quá trình tạo giao dịch và chưa kết nối với cổng thanh toán thực tế.
{{% /notice %}}

#### Kiểm tra Lambda Function

1. Truy cập [AWS Lambda Console](https://console.aws.amazon.com/lambda/).

2. Trong danh sách Lambda Function, chọn:

```text
cloudpay-create-payment
```

3. Kiểm tra trạng thái và mã nguồn đang được triển khai.

![Kiểm tra Lambda Function](/images/5-Workshop/5.4-CloudPay/check-lambda.png)

Lambda Function thực hiện các chức năng:

- Nhận `orderId` và `amount`.
- Kiểm tra dữ liệu đầu vào.
- Tạo mã giao dịch.
- Gán trạng thái ban đầu là `PENDING`.
- Trả về đường dẫn thanh toán mô phỏng.

Ví dụ kết quả trả về:

```json
{
  "transactionId": "TRANSACTION_ID",
  "orderId": "ORDER-1001",
  "amount": 250000,
  "status": "PENDING",
  "paymentUrl": "https://payment.cloudpay.demo/TRANSACTION_ID"
}
```

4. Chọn **Deploy** nếu mã nguồn vừa được cập nhật.

#### Kiểm tra Payment API

1. Truy cập [Amazon API Gateway Console](https://console.aws.amazon.com/apigateway/).

2. Chọn HTTP API của hệ thống CloudPay.

3. Trong menu bên trái, chọn **Routes**.

4. Kiểm tra route:

```text
POST /payments
```

![Kiểm tra Payment Route](/images/5-Workshop/5.4-CloudPay/payment-route.png)

5. Chọn route `POST /payments`.

6. Kiểm tra Integration Target được kết nối với Lambda Function:

```text
cloudpay-create-payment
```

![Kiểm tra Lambda Integration](/images/5-Workshop/5.4-CloudPay/lambda-integration.png)

7. Chọn **Stages** và mở stage:

```text
$default
```

8. Sao chép **Invoke URL** của API.

Ví dụ:

```text
https://API_ID.execute-api.us-east-1.amazonaws.com
```

![Sao chép Invoke URL](/images/5-Workshop/5.4-CloudPay/invoke-url.png)

#### Kiểm tra cấu hình CORS

Customer Web được triển khai qua Amazon CloudFront nên API Gateway cần cho phép frontend gửi yêu cầu đến API.

1. Trong HTTP API, chọn **CORS**.

2. Kiểm tra các giá trị sau:

```text
Access-Control-Allow-Origin:
https://CUSTOMER_CLOUDFRONT_DOMAIN
```

```text
Access-Control-Allow-Headers:
Content-Type,Authorization
```

```text
Access-Control-Allow-Methods:
POST,OPTIONS
```

3. Chọn **Save** để lưu cấu hình.

![Cấu hình CORS](/images/5-Workshop/5.4-CloudPay/configure-cors.png)

{{% notice warning %}}
Trong môi trường thực tế, không nên sử dụng `*` cho `Access-Control-Allow-Origin` đối với API xử lý dữ liệu nhạy cảm. Chỉ nên cho phép domain frontend của hệ thống.
{{% /notice %}}

#### Kiểm tra API bằng AWS CloudShell

1. Mở **AWS CloudShell** trên AWS Management Console.

2. Chạy lệnh sau và thay `API_INVOKE_URL` bằng Invoke URL đã sao chép:

```bash
curl -X POST \
  "API_INVOKE_URL/payments" \
  -H "Content-Type: application/json" \
  -d '{
    "orderId": "ORDER-1001",
    "amount": 250000
  }'
```

3. Kiểm tra kết quả trả về.

Kết quả mong đợi:

```json
{
  "transactionId": "TRANSACTION_ID",
  "orderId": "ORDER-1001",
  "amount": 250000,
  "status": "PENDING",
  "paymentUrl": "https://payment.cloudpay.demo/TRANSACTION_ID"
}
```

![Kiểm tra API thành công](/images/5-Workshop/5.4-CloudPay/api-success.png)

Kết quả trên cho thấy:

- API Gateway đã nhận yêu cầu.
- API Gateway đã gọi Lambda Function.
- Lambda đã xử lý dữ liệu thành công.
- Hệ thống đã tạo mã giao dịch.
- Trạng thái giao dịch ban đầu là `PENDING`.

#### Kiểm tra dữ liệu không hợp lệ

1. Kiểm tra trường hợp thiếu `orderId`:

```bash
curl -X POST \
  "API_INVOKE_URL/payments" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 250000
  }'
```

Kết quả mong đợi:

```json
{
  "message": "orderId is required"
}
```

2. Kiểm tra trường hợp số tiền không hợp lệ:

```bash
curl -X POST \
  "API_INVOKE_URL/payments" \
  -H "Content-Type: application/json" \
  -d '{
    "orderId": "ORDER-1002",
    "amount": -10000
  }'
```

Kết quả mong đợi:

```json
{
  "message": "amount must be greater than 0"
}
```

![Kiểm tra dữ liệu không hợp lệ](/images/5-Workshop/5.4-CloudPay/api-invalid.png)

Việc kiểm tra dữ liệu đầu vào giúp ngăn hệ thống tạo giao dịch từ các yêu cầu không hợp lệ.

#### Tích hợp Payment API với Customer Web

1. Mở mã nguồn của Customer Web Application.

2. Khai báo địa chỉ Payment API:

```javascript
const API_BASE_URL =
  "https://API_ID.execute-api.us-east-1.amazonaws.com";
```

3. Tạo hàm gửi yêu cầu thanh toán:

```javascript
async function createPayment() {
  const orderId = document
    .getElementById("orderId")
    .value
    .trim();

  const amount = Number(
    document.getElementById("amount").value
  );

  const result =
    document.getElementById("paymentResult");

  if (!orderId || amount <= 0) {
    result.textContent =
      "Vui lòng nhập mã đơn hàng và số tiền hợp lệ.";
    return;
  }

  try {
    result.textContent = "Đang tạo giao dịch...";

    const response = await fetch(
      `${API_BASE_URL}/payments`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          orderId,
          amount
        })
      }
    );

    const data = await response.json();

    if (!response.ok) {
      throw new Error(
        data.message || "Không thể tạo giao dịch"
      );
    }

    result.innerHTML = `
      <p><strong>Mã giao dịch:</strong> ${data.transactionId}</p>
      <p><strong>Mã đơn hàng:</strong> ${data.orderId}</p>
      <p><strong>Số tiền:</strong> ${data.amount}</p>
      <p><strong>Trạng thái:</strong> ${data.status}</p>
      <p><strong>Đường dẫn thanh toán:</strong> ${data.paymentUrl}</p>
    `;
  } catch (error) {
    console.error(error);
    result.textContent = `Lỗi: ${error.message}`;
  }
}
```

4. Thêm nút tạo thanh toán vào giao diện:

```html
<button type="button" onclick="createPayment()">
  Tạo thanh toán
</button>
```

5. Lưu lại mã nguồn và tải các file mới lên S3 Bucket của Customer Web.

![Cập nhật Customer Web](/images/5-Workshop/5.4-CloudPay/update-customer-web.png)

6. Mở Amazon CloudFront Distribution của Customer Web.

7. Tạo Invalidation với đường dẫn:

```text
/*
```

8. Chờ trạng thái Invalidation chuyển thành **Completed**.

9. Mở Customer CloudFront Domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN
```

10. Nhập dữ liệu thử nghiệm:

```text
Mã đơn hàng: ORDER-1003
Số tiền: 500000
```

11. Chọn **Tạo thanh toán**.

Kết quả mong đợi:

- Customer Web gửi yêu cầu đến Payment API.
- API Gateway gọi Lambda Function.
- Lambda tạo mã giao dịch.
- Giao diện hiển thị thông tin giao dịch.
- Trạng thái giao dịch là `PENDING`.

![Kết quả tạo thanh toán](/images/5-Workshop/5.4-CloudPay/customer-payment-result.png)

#### Kiểm tra CloudWatch Logs

1. Quay lại Lambda Function:

```text
cloudpay-create-payment
```

2. Chọn tab **Monitor**.

3. Chọn **View CloudWatch logs**.

4. Mở Log Group:

```text
/aws/lambda/cloudpay-create-payment
```

5. Chọn Log Stream mới nhất.

6. Kiểm tra thông tin thực thi của Lambda.

![Kiểm tra CloudWatch Logs](/images/5-Workshop/5.4-CloudPay/cloudwatch-logs.png)

CloudWatch Logs cho phép kiểm tra:

- Thời điểm Lambda được gọi.
- Dữ liệu yêu cầu được xử lý.
- Mã giao dịch được tạo.
- Thời gian thực thi.
- Lỗi xảy ra trong quá trình xử lý.

{{% notice warning %}}
Không ghi mật khẩu, Access Token, JWT Token hoặc thông tin thanh toán nhạy cảm vào Amazon CloudWatch Logs.
{{% /notice %}}

#### Một số lỗi thường gặp

##### Lỗi CORS

Kiểm tra:

- Customer CloudFront Domain đã được thêm vào CORS.
- Phương thức `POST` và `OPTIONS` đã được cho phép.
- Header `Content-Type` đã được cho phép.
- Origin URL không bị sai hoặc thừa dấu `/`.

##### API trả về lỗi 404

Kiểm tra:

- Route `POST /payments` đã được tạo.
- Invoke URL được sử dụng đúng.
- Đường dẫn `/payments` đã được thêm vào URL.
- Stage `$default` đang hoạt động.

##### API trả về lỗi 500

Kiểm tra:

- Lambda Function đã được Deploy.
- API Gateway có quyền gọi Lambda.
- Mã nguồn Lambda không bị lỗi.
- CloudWatch Logs có ghi nhận lỗi thực thi hay không.

##### Website vẫn sử dụng mã nguồn cũ

Tạo CloudFront Invalidation:

```text
/*
```

Sau đó tải lại website bằng:

```text
Ctrl + Shift + R
```

#### Tóm tắt

Trong phần này, bạn đã:

- Kiểm tra Lambda Function xử lý thanh toán.
- Kiểm tra route `POST /payments`.
- Kết nối Amazon API Gateway với AWS Lambda.
- Cấu hình CORS cho Customer Web.
- Kiểm tra API với dữ liệu hợp lệ.
- Kiểm tra API với dữ liệu không hợp lệ.
- Tích hợp Payment API với Customer Web.
- Hiển thị thông tin giao dịch trên giao diện.
- Kiểm tra quá trình xử lý bằng Amazon CloudWatch Logs.

#### Kết quả mong đợi

Sau khi hoàn thành phần này:

- Customer Web có thể gửi yêu cầu thanh toán.
- API Gateway tiếp nhận được yêu cầu.
- Lambda xử lý được `orderId` và `amount`.
- Hệ thống tạo được mã giao dịch.
- Trạng thái giao dịch được trả về là `PENDING`.
- Thông tin giao dịch được hiển thị trên Customer Web.
- Log thực thi được ghi nhận trong Amazon CloudWatch.
- Payment API đã sẵn sàng để mở rộng thêm chức năng lưu trữ giao dịch, xác thực JWT và xử lý webhook.