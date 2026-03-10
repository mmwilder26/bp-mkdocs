# Recover Enterprise API

Authentication to the Recover Enterprise API is done using an API key. During the integration phase, Butter will provide you with the necessary API keys to authenticate with the endpoint.

### API Key Example

```none title="text"
curl --location 'https://api.butterpayments.com/recover/payments' \
--header 'x-api-key: <BUTTER_API_KEY>'
```

***

# Merchant Endpoints

Recover supports multiple authentication methods for merchant-provided endpoints, including **Signing Secret&#x20;**(shared by Butter)**, API Keys** and **Basic Authentication**. During integration, merchants must securely share the necessary authorization details with Butter.

!!! note ""
    If you need to Allowlist inbound traffic, Recover IPs can be provided on request.


### **HMAC-SHA256 Signature**

**Webhook Authentication**

For implementation references, please review [**Webhook Signature Verification**](docId:7A93dyRXTWliVJZ_OUoYD).

1. Compute an HMAC with the SHA256 hash function. Use the signing secret (provided by Butter) as the key and `{json_payload}+{str(expiration)}` as the message. The `json_payload` is the request body and `expiration` is the `X-Butter-Webhook-Expiration` header.
2. Compare the signature you generate with `X-Butter-Webhook-Signature` from the headers. They should match exactly.
3. Compare `X-Butter-Webhook-Expiration` to when the webhook was received to ensure it has not expired.

### API Key Example

```none title="text"
curl --location 'https://api.<merchant-url>.com/' \
--header 'x-api-key: <MERCHANT_API_KEY>'
```

### Basic Auth Example

```none title="text"
AUTH=$(echo -ne "$USERNAME:$PASSWORD" | base64 --wrap 0)

curl --location 'https://api.<merchant-url>.com/' \
--header 'Authorization: Basic $AUTH'
```

!!! note "Contact Us"
    Interested in working with Butter to bring more revenue back to your business? Reach out to us here: [**contact@butterpayments.com**](mailto:contact@butterpayments.com)

