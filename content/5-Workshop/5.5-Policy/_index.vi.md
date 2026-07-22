---
title: "Tạo Payment API"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

Trong phần này, bạn sẽ tạo một API đơn giản để tiếp nhận yêu cầu thanh toán từ Customer Web Application.

Hệ thống sử dụng:

- Amazon API Gateway để nhận HTTP request.
- AWS Lambda để xử lý yêu cầu thanh toán.
- Amazon CloudWatch Logs để lưu log thực thi.

Luồng xử lý:

```text
Customer Web Application
          ↓
POST /payments
          ↓
Amazon API Gateway
          ↓
Payment Lambda
          ↓
Transaction ID và Payment URL
```

{{% notice note %}}
Phần này chỉ mô phỏng quá trình tạo giao dịch. Payment URL chưa kết nối với MoMo, VNPay hoặc ZaloPay thật.
{{% /notice %}}

#### Tạo Payment Lambda

1. Mở AWS Lambda Console.

2. Chọn **Create function**.

3. Chọn:

```text
Author from scratch
```

4. Nhập tên Function:

```text
cloudpay-create-payment
```

5. Chọn một Python runtime đang được AWS Lambda hỗ trợ.

6. Trong phần Permissions, chọn:

```text
Create a new role with basic Lambda permissions
```

7. Chọn **Create function**.

#### Thêm mã xử lý thanh toán

8. Trong phần **Code source**, thay nội dung file `lambda_function.py` bằng:

```python
import json
import uuid
from datetime import datetime, timezone


def create_response(status_code, body):
    return {
        "statusCode": status_code,
        "headers": {
            "Content-Type": "application/json"
        },
        "body": json.dumps(body)
    }


def lambda_handler(event, context):
    try:
        request_body = json.loads(
            event.get("body") or "{}"
        )
    except json.JSONDecodeError:
        return create_response(
            400,
            {
                "message": "Invalid JSON request body"
            }
        )

    order_id = request_body.get("orderId")
    amount = request_body.get("amount")

    if not order_id:
        return create_response(
            400,
            {
                "message": "orderId is required"
            }
        )

    try:
        amount = float(amount)
    except (TypeError, ValueError):
        return create_response(
            400,
            {
                "message": "amount must be a number"
            }
        )

    if amount <= 0:
        return create_response(
            400,
            {
                "message": "amount must be greater than 0"
            }
        )

    transaction_id = str(uuid.uuid4())

    payment_url = (
        "https://sandbox-payment.example.com/pay"
        f"?transactionId={transaction_id}"
    )

    return create_response(
        200,
        {
            "transactionId": transaction_id,
            "orderId": order_id,
            "amount": amount,
            "currency": "VND",
            "status": "PENDING",
            "paymentUrl": payment_url,
            "createdAt": datetime.now(
                timezone.utc
            ).isoformat()
        }
    )
```

9. Chọn **Deploy**.

#### Kiểm tra Lambda

10. Chọn tab **Test**.

11. Tạo một Test Event với nội dung:

```json
{
    "body": "{\"orderId\":\"ORDER-001\",\"amount\":100000}"
}
```

12. Chọn **Test**.

Kết quả có dạng:

```json
{
    "statusCode": 200,
    "headers": {
        "Content-Type": "application/json"
    },
    "body": "{\"transactionId\":\"EXAMPLE-TRANSACTION-ID\",\"orderId\":\"ORDER-001\",\"amount\":100000.0,\"currency\":\"VND\",\"status\":\"PENDING\",\"paymentUrl\":\"https://sandbox-payment.example.com/pay?transactionId=EXAMPLE-TRANSACTION-ID\",\"createdAt\":\"2026-07-19T10:00:00+00:00\"}"
}
```

Giá trị `transactionId` và `createdAt` sẽ thay đổi sau mỗi lần chạy.

Khi gọi Lambda thông qua API Gateway, nội dung trong trường `body` sẽ được trả về cho người dùng dưới dạng JSON.

#### Tạo API Gateway

13. Mở Amazon API Gateway Console.

14. Chọn **Create API**.

15. Trong phần **HTTP API**, chọn **Build**.

16. Nhập tên API:

```text
cloudpay-payment-api
```

17. Trong phần Integration, chọn:

```text
Lambda
```

18. Chọn Lambda Function:

```text
cloudpay-create-payment
```

19. Chọn **Next**.

#### Tạo Route

20. Cấu hình Route:

```text
Method: POST
Resource path: /payments
```

21. Chọn **Next**.

22. Sử dụng Stage:

```text
$default
```

23. Kiểm tra cấu hình và chọn **Create**.

{{% notice warning %}}
Trong workshop cơ bản, Route `POST /payments` chưa sử dụng JWT Authorizer. Trong hệ thống thực tế, Route này cần được bảo vệ bằng Amazon Cognito và API Gateway JWT Authorizer.
{{% /notice %}}

#### Cấu hình CORS

24. Mở API vừa tạo.

25. Chọn **CORS**.

26. Cấu hình:

```text
Access-Control-Allow-Origin:
https://CUSTOMER_CLOUDFRONT_DOMAIN

Access-Control-Allow-Methods:
POST

Access-Control-Allow-Headers:
Content-Type,Authorization
```

Thay `CUSTOMER_CLOUDFRONT_DOMAIN` bằng CloudFront Domain thực tế.

Ví dụ:

```text
https://d123customerexample.cloudfront.net
```

27. Chọn **Save**.

{{% notice warning %}}
Trong môi trường production, không nên sử dụng `*` cho `Access-Control-Allow-Origin`. Chỉ nên cho phép domain của ứng dụng CloudPay.
{{% /notice %}}

#### Kiểm tra Payment API

28. Trong API Gateway, sao chép **Invoke URL**.

Ví dụ:

```text
https://abc123.execute-api.ap-southeast-1.amazonaws.com
```

29. Mở Terminal hoặc Command Prompt.

30. Trên Windows Command Prompt hoặc PowerShell, chạy lệnh:

```bash
curl.exe -X POST "https://API_ID.execute-api.ap-southeast-1.amazonaws.com/payments" -H "Content-Type: application/json" -d "{\"orderId\":\"ORDER-001\",\"amount\":100000}"
```

Thay `API_ID` bằng API ID thực tế.

Kết quả dự kiến:

```json
{
    "transactionId": "EXAMPLE-TRANSACTION-ID",
    "orderId": "ORDER-001",
    "amount": 100000.0,
    "currency": "VND",
    "status": "PENDING",
    "paymentUrl": "https://sandbox-payment.example.com/pay?transactionId=EXAMPLE-TRANSACTION-ID",
    "createdAt": "2026-07-19T10:00:00+00:00"
}
```

#### Kiểm tra dữ liệu không hợp lệ

31. Gửi request với số tiền bằng `0`:

```bash
curl.exe -X POST "https://API_ID.execute-api.ap-southeast-1.amazonaws.com/payments" -H "Content-Type: application/json" -d "{\"orderId\":\"ORDER-002\",\"amount\":0}"
```

Kết quả dự kiến:

```json
{
    "message": "amount must be greater than 0"
}
```

Bạn cũng có thể kiểm tra request không có `orderId`:

```bash
curl.exe -X POST "https://API_ID.execute-api.ap-southeast-1.amazonaws.com/payments" -H "Content-Type: application/json" -d "{\"amount\":100000}"
```

Kết quả dự kiến:

```json
{
    "message": "orderId is required"
}
```

#### Gọi API từ Frontend

Customer Web Application có thể gọi Payment API bằng JavaScript:

```javascript
async function createPayment() {
    const response = await fetch(
        "https://API_ID.execute-api.ap-southeast-1.amazonaws.com/payments",
        {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                orderId: "ORDER-001",
                amount: 100000
            })
        }
    );

    const result = await response.json();

    console.log(result);

    if (!response.ok) {
        alert(
            result.message ||
            "Unable to create payment."
        );

        return;
    }

    alert(
        "Payment created successfully!\n" +
        "Transaction ID: " +
        result.transactionId +
        "\nStatus: " +
        result.status +
        "\nPayment URL: " +
        result.paymentUrl
    );
}
```

Thay `API_ID` bằng API ID thực tế.

{{% notice note %}}
Payment URL hiện tại chỉ là URL mô phỏng. Frontend chỉ hiển thị URL và không chuyển người dùng đến trang thanh toán.
{{% /notice %}}

#### Kiểm tra CloudWatch Logs

32. Mở Lambda Function:

```text
cloudpay-create-payment
```

33. Chọn tab **Monitor**.

34. Chọn **View CloudWatch logs**.

35. Mở Log Stream mới nhất để kiểm tra quá trình Lambda được thực thi.

Bạn có thể kiểm tra:

- Thời gian Lambda bắt đầu chạy.
- Request ID.
- Thời gian thực thi.
- Bộ nhớ Lambda đã sử dụng.
- Các lỗi xuất hiện trong quá trình xử lý.

#### Tóm tắt

Trong phần này, bạn đã:

- Tạo Payment Lambda.
- Kiểm tra `orderId` và `amount`.
- Tạo Transaction ID.
- Tạo Payment URL mô phỏng.
- Tạo HTTP API bằng Amazon API Gateway.
- Tạo Route `POST /payments`.
- Cấu hình CORS.
- Kiểm tra API bằng `curl`.
- Kiểm tra request không hợp lệ.
- Gọi API từ frontend.
- Kiểm tra Lambda logs trong CloudWatch.

#### Kết quả mong đợi

Sau khi hoàn thành phần này:

- API Gateway có thể nhận yêu cầu thanh toán.
- Payment Lambda có thể xử lý request.
- Mỗi giao dịch có một Transaction ID riêng.
- API trả về trạng thái `PENDING`.
- API trả về Payment URL mô phỏng.
- Request không hợp lệ bị từ chối.
- Customer Web Application có thể gọi Payment API.
- Log thực thi có thể được kiểm tra trong CloudWatch.