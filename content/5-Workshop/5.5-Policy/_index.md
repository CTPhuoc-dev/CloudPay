---
title: "Create the Payment API"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

In this section, you will create a simple API to receive payment requests from the Customer Web Application.

The system uses:

- Amazon API Gateway to receive HTTP requests.
- AWS Lambda to process payment requests.
- Amazon CloudWatch Logs to store execution logs.

The request flow is:

```text
Customer Web Application
          ↓
POST /payments
          ↓
Amazon API Gateway
          ↓
Payment Lambda
          ↓
Transaction ID and Payment URL
```

{{% notice note %}}
This section only simulates the transaction creation process. The Payment URL is not connected to a real MoMo, VNPay, or ZaloPay payment gateway.
{{% /notice %}}

#### Create the Payment Lambda Function

1. Open the AWS Lambda Console.

2. Choose **Create function**.

3. Select:

```text
Author from scratch
```

4. Enter the function name:

```text
cloudpay-create-payment
```

5. Select a Python runtime supported by AWS Lambda.

6. In the Permissions section, select:

```text
Create a new role with basic Lambda permissions
```

7. Choose **Create function**.

#### Add the Payment Processing Code

8. In the **Code source** section, replace the content of `lambda_function.py` with:

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

9. Choose **Deploy**.

#### Test the Lambda Function

10. Choose the **Test** tab.

11. Create a Test Event with the following content:

```json
{
    "body": "{\"orderId\":\"ORDER-001\",\"amount\":100000}"
}
```

12. Choose **Test**.

The result has the following format:

```json
{
    "statusCode": 200,
    "headers": {
        "Content-Type": "application/json"
    },
    "body": "{\"transactionId\":\"EXAMPLE-TRANSACTION-ID\",\"orderId\":\"ORDER-001\",\"amount\":100000.0,\"currency\":\"VND\",\"status\":\"PENDING\",\"paymentUrl\":\"https://sandbox-payment.example.com/pay?transactionId=EXAMPLE-TRANSACTION-ID\",\"createdAt\":\"2026-07-19T10:00:00+00:00\"}"
}
```

The `transactionId` and `createdAt` values will change each time the function runs.

When Lambda is called through API Gateway, the content inside the `body` field is returned to the user as JSON.

#### Create the API Gateway API

13. Open the Amazon API Gateway Console.

14. Choose **Create API**.

15. In the **HTTP API** section, choose **Build**.

16. Enter the API name:

```text
cloudpay-payment-api
```

17. For the integration, select:

```text
Lambda
```

18. Select the following Lambda function:

```text
cloudpay-create-payment
```

19. Choose **Next**.

#### Create the Route

20. Configure the following route:

```text
Method: POST
Resource path: /payments
```

21. Choose **Next**.

22. Use the following stage:

```text
$default
```

23. Review the configuration and choose **Create**.

{{% notice warning %}}
In this basic workshop, the `POST /payments` route does not use a JWT Authorizer. In a real system, this route should be protected by Amazon Cognito and an API Gateway JWT Authorizer.
{{% /notice %}}

#### Configure CORS

24. Open the API that you created.

25. Choose **CORS**.

26. Configure the following values:

```text
Access-Control-Allow-Origin:
https://CUSTOMER_CLOUDFRONT_DOMAIN

Access-Control-Allow-Methods:
POST

Access-Control-Allow-Headers:
Content-Type,Authorization
```

Replace `CUSTOMER_CLOUDFRONT_DOMAIN` with the actual Customer CloudFront domain.

Example:

```text
https://d123customerexample.cloudfront.net
```

27. Choose **Save**.

{{% notice warning %}}
In a production environment, do not use `*` for `Access-Control-Allow-Origin`. Only allow the CloudPay application domain.
{{% /notice %}}

#### Test the Payment API

28. In API Gateway, copy the **Invoke URL**.

Example:

```text
https://abc123.execute-api.ap-southeast-1.amazonaws.com
```

29. Open a Terminal or Command Prompt.

30. On Windows Command Prompt or PowerShell, run:

```bash
curl.exe -X POST "https://API_ID.execute-api.ap-southeast-1.amazonaws.com/payments" -H "Content-Type: application/json" -d "{\"orderId\":\"ORDER-001\",\"amount\":100000}"
```

Replace `API_ID` with the actual API ID.

Expected result:

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

#### Test Invalid Data

31. Send a request with an amount of `0`:

```bash
curl.exe -X POST "https://API_ID.execute-api.ap-southeast-1.amazonaws.com/payments" -H "Content-Type: application/json" -d "{\"orderId\":\"ORDER-002\",\"amount\":0}"
```

Expected result:

```json
{
    "message": "amount must be greater than 0"
}
```

You can also test a request without `orderId`:

```bash
curl.exe -X POST "https://API_ID.execute-api.ap-southeast-1.amazonaws.com/payments" -H "Content-Type: application/json" -d "{\"amount\":100000}"
```

Expected result:

```json
{
    "message": "orderId is required"
}
```

#### Call the API from the Frontend

The Customer Web Application can call the Payment API using JavaScript:

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

Replace `API_ID` with the actual API ID.

{{% notice note %}}
The current Payment URL is only a simulated URL. The frontend displays the URL but does not redirect the user to a payment page.
{{% /notice %}}

#### Check CloudWatch Logs

32. Open the following Lambda function:

```text
cloudpay-create-payment
```

33. Choose the **Monitor** tab.

34. Choose **View CloudWatch logs**.

35. Open the latest Log Stream to review the Lambda execution logs.

You can review:

- The Lambda start time.
- The Request ID.
- The execution duration.
- The amount of Lambda memory used.
- Any errors that occurred during processing.

#### Summary

In this section, you completed the following tasks:

- Created the Payment Lambda function.
- Validated `orderId` and `amount`.
- Generated a Transaction ID.
- Generated a simulated Payment URL.
- Created an HTTP API using Amazon API Gateway.
- Created the `POST /payments` route.
- Configured CORS.
- Tested the API using `curl`.
- Tested invalid requests.
- Called the API from the frontend.
- Reviewed Lambda logs in CloudWatch.

#### Expected Results

After completing this section:

- API Gateway can receive payment requests.
- The Payment Lambda function can process requests.
- Each transaction receives a unique Transaction ID.
- The API returns the `PENDING` status.
- The API returns a simulated Payment URL.
- Invalid requests are rejected.
- The Customer Web Application can call the Payment API.
- Execution logs can be reviewed in CloudWatch.