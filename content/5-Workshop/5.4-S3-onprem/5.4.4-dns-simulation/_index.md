---
title: "Testing and Integrating the Payment API"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

In this section, you will test the **Payment API** of the CloudPay system and connect it to the **Customer Web Application**.

The Payment API uses:

- **Amazon API Gateway** to receive API requests.
- **AWS Lambda** to process payment logic.
- **Amazon CloudWatch** to store and review execution logs.
- **Amazon CloudFront** to deliver the Customer Web interface.

The system workflow is shown below:

```text
Customer Web
    ↓
Amazon CloudFront
    ↓
Amazon API Gateway
    ↓
AWS Lambda
    ↓
Transaction Response
```

{{% notice note %}}
The current CloudPay version only simulates transaction creation and is not connected to a real payment gateway.
{{% /notice %}}

#### Check the Lambda Function

1. Open the [AWS Lambda Console](https://console.aws.amazon.com/lambda/).

2. From the list of Lambda functions, select:

```text
cloudpay-create-payment
```

3. Review the function status and the deployed source code.

![Check Lambda Function](/images/5-Workshop/5.4-CloudPay/check-lambda.png)

The Lambda function performs the following tasks:

- Receives `orderId` and `amount`.
- Validates the input data.
- Generates a transaction ID.
- Assigns the initial status as `PENDING`.
- Returns a simulated payment URL.

An example response is shown below:

```json
{
  "transactionId": "TRANSACTION_ID",
  "orderId": "ORDER-1001",
  "amount": 250000,
  "status": "PENDING",
  "paymentUrl": "https://payment.cloudpay.demo/TRANSACTION_ID"
}
```

4. Select **Deploy** if the source code has recently been updated.

#### Check the Payment API

1. Open the [Amazon API Gateway Console](https://console.aws.amazon.com/apigateway/).

2. Select the HTTP API used by the CloudPay system.

3. From the left navigation menu, select **Routes**.

4. Verify that the following route exists:

```text
POST /payments
```

![Check Payment Route](/images/5-Workshop/5.4-CloudPay/payment-route.png)

5. Select the `POST /payments` route.

6. Verify that the integration target is connected to the following Lambda function:

```text
cloudpay-create-payment
```

![Check Lambda Integration](/images/5-Workshop/5.4-CloudPay/lambda-integration.png)

7. Select **Stages** and open the following stage:

```text
$default
```

8. Copy the API **Invoke URL**.

Example:

```text
https://API_ID.execute-api.us-east-1.amazonaws.com
```

![Copy Invoke URL](/images/5-Workshop/5.4-CloudPay/invoke-url.png)

#### Check the CORS Configuration

The Customer Web is delivered through Amazon CloudFront. Therefore, API Gateway must allow the frontend application to send requests to the Payment API.

1. In the HTTP API configuration, select **CORS**.

2. Verify the following values:

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

3. Select **Save** to save the configuration.

![Configure CORS](/images/5-Workshop/5.4-CloudPay/configure-cors.png)

{{% notice warning %}}
In a production environment, you should not use `*` for `Access-Control-Allow-Origin` when the API processes sensitive data. Only trusted frontend domains should be allowed.
{{% /notice %}}

#### Test the API Using AWS CloudShell

1. Open **AWS CloudShell** from the AWS Management Console.

2. Run the following command and replace `API_INVOKE_URL` with the Invoke URL copied from API Gateway:

```bash
curl -X POST \
  "API_INVOKE_URL/payments" \
  -H "Content-Type: application/json" \
  -d '{
    "orderId": "ORDER-1001",
    "amount": 250000
  }'
```

3. Review the returned response.

Expected response:

```json
{
  "transactionId": "TRANSACTION_ID",
  "orderId": "ORDER-1001",
  "amount": 250000,
  "status": "PENDING",
  "paymentUrl": "https://payment.cloudpay.demo/TRANSACTION_ID"
}
```

![Successful API Test](/images/5-Workshop/5.4-CloudPay/api-success.png)

This result confirms that:

- API Gateway received the request.
- API Gateway invoked the Lambda function.
- Lambda processed the request successfully.
- The system generated a transaction ID.
- The initial transaction status is `PENDING`.

#### Test Invalid Input Data

1. Test a request without `orderId`:

```bash
curl -X POST \
  "API_INVOKE_URL/payments" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 250000
  }'
```

Expected response:

```json
{
  "message": "orderId is required"
}
```

2. Test a request with an invalid amount:

```bash
curl -X POST \
  "API_INVOKE_URL/payments" \
  -H "Content-Type: application/json" \
  -d '{
    "orderId": "ORDER-1002",
    "amount": -10000
  }'
```

Expected response:

```json
{
  "message": "amount must be greater than 0"
}
```

![Test Invalid Input](/images/5-Workshop/5.4-CloudPay/api-invalid.png)

Input validation prevents the system from creating transactions from invalid requests.

#### Integrate the Payment API with the Customer Web

1. Open the source code of the Customer Web Application.

2. Define the Payment API address:

```javascript
const API_BASE_URL =
  "https://API_ID.execute-api.us-east-1.amazonaws.com";
```

3. Create a function to send payment requests:

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
      "Please enter a valid order ID and amount.";
    return;
  }

  try {
    result.textContent =
      "Creating payment transaction...";

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
        data.message || "Unable to create transaction"
      );
    }

    result.innerHTML = `
      <p><strong>Transaction ID:</strong> ${data.transactionId}</p>
      <p><strong>Order ID:</strong> ${data.orderId}</p>
      <p><strong>Amount:</strong> ${data.amount}</p>
      <p><strong>Status:</strong> ${data.status}</p>
      <p><strong>Payment URL:</strong> ${data.paymentUrl}</p>
    `;
  } catch (error) {
    console.error(error);
    result.textContent = `Error: ${error.message}`;
  }
}
```

4. Add a payment button to the interface:

```html
<button type="button" onclick="createPayment()">
  Create Payment
</button>
```

5. Save the source code and upload the updated files to the Customer Web S3 bucket.

![Update Customer Web](/images/5-Workshop/5.4-CloudPay/update-customer-web.png)

6. Open the Amazon CloudFront distribution used by the Customer Web.

7. Create an invalidation using the following path:

```text
/*
```

8. Wait until the invalidation status changes to **Completed**.

9. Open the Customer CloudFront domain:

```text
https://CUSTOMER_CLOUDFRONT_DOMAIN
```

10. Enter the following test data:

```text
Order ID: ORDER-1003
Amount: 500000
```

11. Select **Create Payment**.

Expected results:

- The Customer Web sends a request to the Payment API.
- API Gateway invokes the Lambda function.
- Lambda generates a transaction ID.
- The transaction information is displayed on the interface.
- The transaction status is `PENDING`.

![Payment Creation Result](/images/5-Workshop/5.4-CloudPay/customer-payment-result.png)

#### Check Amazon CloudWatch Logs

1. Return to the following Lambda function:

```text
cloudpay-create-payment
```

2. Select the **Monitor** tab.

3. Select **View CloudWatch logs**.

4. Open the following Log Group:

```text
/aws/lambda/cloudpay-create-payment
```

5. Select the latest Log Stream.

6. Review the Lambda execution information.

![Check CloudWatch Logs](/images/5-Workshop/5.4-CloudPay/cloudwatch-logs.png)

Amazon CloudWatch Logs can be used to review:

- When the Lambda function was invoked.
- The request data processed by the function.
- The generated transaction ID.
- The execution duration.
- Errors that occurred during processing.

{{% notice warning %}}
Do not write passwords, access tokens, JWT tokens, or sensitive payment information to Amazon CloudWatch Logs.
{{% /notice %}}

#### Common Issues

##### CORS Error

Check the following:

- The Customer CloudFront domain has been added to the CORS configuration.
- The `POST` and `OPTIONS` methods are allowed.
- The `Content-Type` header is allowed.
- The origin URL does not contain an incorrect or unnecessary trailing slash.

##### API Returns a 404 Error

Check the following:

- The `POST /payments` route has been created.
- The correct Invoke URL is being used.
- The `/payments` path has been added to the URL.
- The `$default` stage is active.

##### API Returns a 500 Error

Check the following:

- The Lambda function has been deployed.
- API Gateway has permission to invoke Lambda.
- The Lambda source code does not contain errors.
- Amazon CloudWatch Logs contain information about the execution error.

##### The Website Still Uses Old Source Code

Create a CloudFront invalidation using:

```text
/*
```

Then reload the website using:

```text
Ctrl + Shift + R
```

#### Summary

In this section, you completed the following tasks:

- Checked the payment-processing Lambda function.
- Verified the `POST /payments` route.
- Connected Amazon API Gateway to AWS Lambda.
- Configured CORS for the Customer Web.
- Tested the API with valid input.
- Tested the API with invalid input.
- Integrated the Payment API with the Customer Web.
- Displayed transaction information on the interface.
- Reviewed the payment-processing workflow using Amazon CloudWatch Logs.

#### Expected Results

After completing this section:

- The Customer Web can send payment requests.
- API Gateway can receive the requests.
- Lambda can process `orderId` and `amount`.
- The system can generate a transaction ID.
- The returned transaction status is `PENDING`.
- Transaction information is displayed on the Customer Web.
- Execution logs are stored in Amazon CloudWatch.
- The Payment API is ready to be extended with transaction storage, JWT authentication, and webhook processing.