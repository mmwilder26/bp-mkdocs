Detailed table views of all required and available properties to be sent to the Recover [API Reference](api-reference.md).

## **Customer Properties**

| Property | Required | Example | Description |
| --- | --- | --- | --- |
| `id` | TRUE | `11223344` | The internal identifier of the customer from your subscription management platform. |
| `psp_id` | FALSE | `cus_12345` | The internal identifier of the customer from your payment service provider. |
| `created_at` | FALSE | `2023-09-25T03:57:26Z` | Timestamp of when the customer was created in your subscription management platform. |
| `postal_code` | FALSE | `94530` | The postal code of the customer. |

## **Subscription Properties**

| Property | Required | Example | Description |
| --- | --- | --- | --- |
| `id` | TRUE | `sub_12345` | The internal identifier from your subscription management platform used to designate the subscription. |
| `start_date` | TRUE | `2023-09-25T03:57:26Z` | Timestamp of when the subscription started in your subscription management platform. |
| `end_date` | FALSE | `2024-09-25T03:57:26Z` | Timestamp of when the subscription ended/will end in your subscription management platform. |
| `discounted_or_free_period` | FALSE | `true` / `false` | Boolean to designate a free trial or discounted billing. |
| `product_id` | FALSE | `SKU_1234` | The unique identifier of the product. |
| `billing.period` | FALSE | `Day`, `Week`, `Month`, `Year` | The subscription billing period. Must be set to one of the specified values or absent. |
| `billing.frequency` | FALSE | `1` | The number of payments per billing period. Must be a positive integer or absent. |

## **Invoice Properties**

| Property | Required | Example | Description |
| --- | --- | --- | --- |
| `id` | TRUE | `inv_12345` | The internal identifier of the invoice from your subscription management platform. |
| `created_at` | TRUE | `2023-09-25T03:57:26Z` | Timestamp of when the invoice was created. |

## **Transaction Properties**

Provided as an array of transactions. At least one transaction is required. If your retry strategy includes multiple payment methods, list each of them here rather than sending an additional event for each transaction.

| Property | Required | Example | Description |
| --- | --- | --- | --- |
| `id` | TRUE | `ts_12345` | The internal identifier of the transaction from your subscription management platform. |
| `psp_id` | TRUE | `ts_12345` | The internal identifier of the transaction from your payment service provider. |
| `psp` | TRUE | `stripe`, `braintree`, `worldpay`, `recharge`, `recurly`, `adyen` | The name of the PSP the transaction originated from. Must be one of the listed values. |
| `acquirer_country` | TRUE | `US` | The country in which the payment was processed and acquired. |
| `amount` | TRUE | `10.00` | The amount of the transaction, as a float. |
| `currency` | TRUE | `USD` | The currency of the transaction in ISO standard format. |
| `funding_source` | TRUE | `CREDIT`, `DEBIT`, `MIXED`, `PAYPAL`, `PREPAID`, `UNCATEGORIZED`, `UNKNOWN` | The account type funding the card. Must be one of the specified values. |
| `payment_method_id` | TRUE | `pm_12345` | The ID from the PSP of the payment method attempted. |
| `psp_transaction_created_at` | TRUE | `2023-09-25T03:57:26Z` | Timestamp of when the transaction was created on the PSP. |
| `scheme` | TRUE | `visa` | Network of the payment card (e.g. `visa`, `mastercard`). |
| `success` | TRUE | `true` / `false` | Boolean to represent if the transaction was successfully processed. |
| `outcome.raw_response_message` | TRUE | `insufficient_funds` | The issuer approval or decline message provided in the authorization response. |
| `outcome.psp_response_message` | FALSE | `The customer has insufficient funds.` | The response description provided by the payment processor. |
| `outcome.processor_response_code` | FALSE | `2000` | The response code returned by the payment processor. |
| `outcome.network_status` | FALSE | `psp_block` | Additional network data returned by certain payment processors, if applicable. |
| `outcome.response_type` | FALSE | `hard_decline` | Category of decline code (`soft_decline`, `hard_decline`). |
| `network_transaction_id` | FALSE | `PCQVZWBGA0912` | The original transaction ID from the network. Required for WorldPay. |
| `mid` | FALSE | `8224137` | The MID (merchant identification number) used to process a transaction with the PSP. Required for Worldpay integrations. |
| `reporting_group` | FALSE | `Pepsi` | The identifier used to categorize transaction data. Commonly used for reporting and analysis, identifying business units, branches, currencies, etc. |
| `billing_postal_code` | FALSE | `94530` | The customer's billing address postal code. |
| `auth` | FALSE | `true` / `false` | Boolean to represent if this is a pre-authorization or final capture. |
| `bin` | FALSE | `112233` | The 6-digit Bank Identification Number of the payment card used. |
| `captured` | FALSE | `true` / `false` | Boolean to represent if the payment has been captured. |
| `issuer_country` | FALSE | `US` | The country in which the payment card was issued. |
| `refunded` | FALSE | `true` / `false` | Boolean to represent if the payment has been refunded. |
| `enhanced_data.level_2` | FALSE | — | Level 2 processing includes extra details such as customer code (often a purchase order number), sales tax amount, and merchant tax ID. Including this data often qualifies the merchant for lower processing fees, particularly for corporate or government transactions. |
| `enhanced_data.level_3` | FALSE | — | Level 3 processing includes line-item details such as item descriptions, quantities, unit prices, and product codes — similar to an itemized invoice. Typically used for large corporate and government transactions, it can yield even lower interchange rates due to reduced risk and enhanced information. |
