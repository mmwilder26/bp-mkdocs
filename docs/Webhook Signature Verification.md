# Overview

Code examples that can be used to verify a webhook signature using a signing key.

## Python

```python title="Python"
# Python
import hashlib
import hmac
from time import time

def verify_webhook_signature(body: str, headers: dict, signing_key: str) -> bool:
	now = int(time())
	incoming_signature = headers.get("X-Butter-Webhook-Signature")
	expires_at = headers.get("X-Butter-Webhook-Expiration")
	message = f"{body}+{expires_at}".encode("utf-8")
	signature = hmac.new(signing_key.encode("utf-8"), message, hashlib.sha256).hexdigest()
	if signature != incoming_signature:
		return False
	if expires_at < now:
		return False
	return True

```

## JavaScript

```javascript title="JS"
// JavaScript
const crypto = require('crypto');

function verifyWebhookSignature(body, headers, signingKey) {
	const now = Math.floor(new Date().getTime()/1000);
	const expiresAt = headers["X-Butter-Webhook-Expiration"];
	const incomingSignature = headers["X-Butter-Webhook-Signature"];
	const hmac = crypto.createHmac('SHA256', signingKey);
	const data = hmac.update(Buffer.from(`${body}+${expiresAt}`));
	const signature = data.digest('hex');
	if(signature != incomingSignature) return false;
	if(expiresAt < now) return false;
	return true;
}
```

## Go

```go title="Go"
// Go
package main

import (
	"crypto/hmac"
	"crypto/sha256"
	"encoding/hex"
)

func verifyWebhookSignature(body string, headers map[string]string, signingKey string) bool {
	now := int(time.Now().Unix())

	incomingSignature := headers["X-Butter-Webhook-Signature"]
	expiresAtStr := headers["X-Butter-Webhook-Expiration"]

	expiresAt, err := strconv.Atoi(expiresAtStr)
	if err != nil {
		return false
	}

	message := string(body) + "+" + expiresAtStr

	h := hmac.New(sha256.New, []byte(signingKey))
	h.Write([]byte(message))
	signature := hex.EncodeToString(h.Sum(nil))

	if signature != incomingSignature {
		return false
	}

	if expiresAt < now {
	    return false
	}

	return true
}
```

