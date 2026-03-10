# Recover Enterprise API

The Recover Enterprise API offers an endpoint for sending payment events—both successful and failed. This endpoint is designed to be source-agnostic, working seamlessly with any payment service provider.

Please review the [**Authentication**](docId\:x_CpA5gD9Fcb6RydARZOv) documentation for how to securly connect to Recover APIs.

## POST: Payments

The Payments API is an HTTP endpoint that handles standardized payment events forwarded from a merchant's payment processing system.&#x20;

Please check the [**Recover Payment Object Properties**](docId\:J6sVYE_NzGbcoP9aV0ua-) for additional descriptions of required and optional properties.&#x20;

[**Payments**](docId\:LIvADqN50DNN5R1jhv5nM)&#x20;

**Example URL**

`https://api.butterpayments.com/payments`

**Example Request Body**

```json title="json"
{
    "customer": {
        "id": "11223344", // REQUIRED
        "psp_id": "cus_12345",
        "created_at": "2023-09-25T03:57:26Z",
        "postal_code": "04102"
    },
    "subscription": {
        "id": "sub_1234", // REQUIRED
        "start_date": "2023-09-25T03:57:26Z", // REQUIRED
        "end_date": "2023-10-25T03:57:26Z",
        "discounted_or_free_period": false,
        "product_id": "sku_020901",
        "billing": {
            "period": "month",
            "frequency": 1
        }
    },
    "invoice": {
        "id": "inv_12345", // REQUIRED
        "created_at": "2023-09-25T03:57:26Z" // REQUIRED
    },
    "transactions": [
        {
            "id": "ts_12345", // REQUIRED
            "psp_transaction_id": "ts_12345", // REQUIRED
            "acquirer_country": "US", // REQUIRED
            "amount": 12.23, // REQUIRED
            "psp": "stripe", // REQUIRED
            "currency": "USD", // REQUIRED
            "funding_source": "CREDIT", // REQUIRED
            "payment_method_id": "pm_12345", // REQUIRED
            "psp_transaction_created_at": "2023-10-25T03:57:26Z", // REQUIRED
            "outcome": {
                "raw_response_message": "insufficient_funds", // REQUIRED
                "psp_response_message": "The customer has insufficient funds.",
                "processor_response_code": 2000,
                "network_status": "psp_block",
                "response_type": "soft_decline"
            },
            "network_transaction_id": "PCQVZZWBGA0912",
            "mid": "8224137",
            "reporting_group": "Pepsi",
            "billing_postal_code": "04102",
            "auth": true,
            "bin": "112233",
            "captured": false,
            "issuer_country": "US",
            "refunded": false,
            "scheme": "visa",
            "success": false
        }
    ]
}
```

**Example Response Body**

```json title="json"
{
  "message": "Request submitted for processing",
  "request_id": "2c941653-b229-4528-b576-70fcfd388543:49645839701979566120793328447151049151317978254208401410:000000000000"
}
```

## POST: Payment Method Update

This endpoint allows merchants to notify Recover of updates to a customer's payment method. Whenever a payment method is added, updated, or removed, the event should be forwarded to this endpoint. These updates are crucial as changes in payment methods influence the calculation of the optimal retry time.

[**API Reference**](docId\:oylR8swr-HN9dqZOscpcw)&#x20;

**Example URL**

`https://api.butterpayments.com/payment_method_updates`

**Example Request Body**

```json title="json"
{
  "customer_id": "11223344",
  "psp_customer_id": "cust_12345",
  "payment_method_id": "pm_12345",
  "reason": "network_updated"
}
```

**Example Response Body&#x20;**

```json title="json"
{
  "message": "Request submitted for processing",
  "request_id": "2c941653-b229-4528-b576-70fcfd388543:49645839701979566120793328447151049151317978254208401410:000000000000"
}
```

***

# Merchant Endpoints

To integrate with the Recover Enterprise API effectively, merchants must provision specific endpoints. These endpoints are essential for maintaining consistency in payment recovery and ensuring accurate payment reconciliation.

Please review the [**Authentication**](docId\:x_CpA5gD9Fcb6RydARZOv) section for details on what schemes Butter systems currently support.&#x20;

## POST: RetryStatus

The RetryStatus endpoint, provided by the merchant, determines whether an invoice is eligible for retry. Before initiating a retry, the Recover service calls this endpoint as a final security check prior to submitting the transaction.

This check is crucial because various changes can occur to an invoice between the time it is sent to Recover and when a retry is attempted. Depending on what Recover's underlying models determine to be optimal, this interval may range from hours to days. One of the most common reasons to cancel a retry during this period is if the user cancels their subscription.

&#x20;[**Retry Status**](docId\:idcldp2JT0GOpaQuO-xyx)&#x20;

**Example URL**

`https://api.<merchant-url>/retry/<invoice-id>`

**Example Request Body**

```json title="json"
{
// Optional Custom Filter Paramaters: 
// {brand: Pepsi}
// Merchant to define Filter Parameters, if required
}
```

**Example Response Body**

```json title="json"
{
  "retry": false,
  "reason": "User Cancellation",
  "amount": 19.99,
  "metadata": {},
  "payment_methods": [
    {
      "id": "pm_12345",
      "network_transaction_id": "QKSX6RP250903",
      "bin": "621035",
      "token": {
        "id": "4457010000000009",
        "expiration_month": "02",
        "expiration_year": "27",
        "card_validation_number": "445"
      },
      "billing_address": {
        "name": "John Smith",
        "city": "Boston",
        "state": "MA",
        "zip": "02108",
        "phone": "555-309-9999"
      }
    }
  ]
}
```

## POST: RetryResults

The RetryResults endpoint acts as a webhook consumer for Retry events, providing real-time visibility into the operations of the Recover system. These events deliver detailed information about each retry attempt, including its outcome, enabling merchants to reconcile their systems efficiently.

- If payment is successfully recovered on a retry attempt, `"success":true` will be returned.
- If payment is *not* successfully recovered on a retry attempt, `"success":false` will be returned.

[**Retry Results**](docId\:apHOqKSlqpUbkR7vO46X3)&#x20;

**Example URL**

`https://api.<merchant-url>/retry-results`

**Example Request Body**

```json title="json"
{
   "timestamp":"2025-01-22T22:31:08.893377",
   "butter_id":"1040432066964226052",
   "customer_id":"cust_1234",
   "subscription_id":"sub_12345",
   "invoice_id":"inv_12345",
   "transaction_id":"txn_12345",
   "original_psp_transaction_id":"9472768581",
   "psp_transactions":[
      {
         "psp_transaction_id":"9472768583",
         "network_transaction_id":null,
         "merchant_payment_method_id":"pm_12345",
         "success":true
      }
   ],
   "original_amount":50,
   "amount_collected":50,
   "currency":"USD",
   "success":true,
   "dunning_complete":true,
   "custom": {}
}
```

**Example Response Body**

```json title="json"

{
  "detail": "Event delivered successfully"
}
```

## POST: EndOfDunning

Recover will send webhooks to this endpoint via HTTP POST requests when the dunning process is complete. This signifies the end of the retry window, after which the Recover service will cease any further payment retry attempts.

[**End of Dunning**](docId:_xPgIsw24GH-d43oZIXvg)&#x20;

**Example URL**

`https://api.<merchant-url>/end-of-dunning`

**Example Request Body**

```json title="json"
{
  "timestamp": "2025-04-05T19:41:01.396550",
  "invoice_id": "inv_12345",
  "recovered": true
  "custom":null
}
```

**Example Response Body**

```json title="json"
{
  "detail": "Event delivered successfully"
}
```

!!! note "Contact Us"
    Interested in working with Butter to bring more revenue back to your business? Reach out to us here: [**contact@butterpayments.com**](mailto:contact@butterpayments.com).


<swagger-ui src="/api/openapi.yaml"/>
