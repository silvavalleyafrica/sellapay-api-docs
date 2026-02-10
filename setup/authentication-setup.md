# Authentication Setup Guide

## Overview

The Sellapay API uses JWT (JSON Web Token) authentication with HMAC-SHA256 signing. This guide explains how to implement authentication in your integration.

## Authentication Flow

```
1. Client sends API credentials (X-API-KEY, X-API-SECRET)
                    ↓
2. Server validates credentials against database
                    ↓
3. Server generates signed JWT token
                    ↓
4. Client receives token and stores it securely
                    ↓
5. Client includes token in Authorization header
                    ↓
6. Server validates token signature and expiry
                    ↓
7. Request is processed or returns 401 if invalid
```

## Step-by-Step Implementation

### Step 1: Obtain API Credentials

Contact your Sellapay account manager or access the dashboard to get:

- `X-API-KEY` — Your unique API key
- `X-API-SECRET` — Your secret code

**Security:** Store these securely, never hardcode or commit to version control.

### Step 2: Authorize (Obtain Token)

Send a POST request to `/authorize` with your credentials:

**Request:**

```bash
curl -X POST \
  -H "X-API-KEY: YOUR_API_KEY" \
  -H "X-API-SECRET: YOUR_API_SECRET" \
  https://pay.sellapay.africa/api/v1/authorize
```

**Response:**

```json
{
  "message": "User authorized",
  "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhY2NvdW50IjoiZmJzb3RrNGpIMUw3RmlPMnoycWtxc1BpcVZISlNhVm1wZ0J1OGE3aU1XR2FiYTk3UkgiLCJidXNpbmVzc19pZCI6InpYUEI0elMyR3poVWU4YmdKVTgyekpESm13UngyekE3T3l1bG1OaFNhY3ZhTm9MRDFiIiwiaWF0IjoxNzcwNzI3NDQwLCJleHAiOjE3NzA3MzEwNDB9.m6EJnKICZdfSrPB0e8CSXaZShdxc7LUidBEe6xsIrFY",
  "expires_in": 3600
}
```

**Token Contents (decoded):**

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
.
{
  "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
  "business_id": "zXPB4zS2GzhUe8bgJU82zJDjmwRx2zA7OyulmNhSacvaNqLD1b",
  "iat": 1770727440,
  "exp": 1770731040
}
```

**Token Fields:**

- `iat` — Issued at (Unix timestamp)
- `exp` — Expires at (Unix timestamp, 3600 seconds after `iat`)
- `account` — Your account ID
- `business_id` — Your business ID

### Step 3: Store Token

Store the token securely:

- In-memory (fastest, cleared on restart)
- Secure cache (Redis with encryption)
- Database (encrypted)
- Session (for web applications)

**Never:**

- Store in plaintext files
- Log tokens
- Send over unencrypted connections
- Expose in URLs or query parameters

### Step 4: Use Token in Requests

Include the token in the `Authorization: Bearer` header:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  https://pay.sellapay.africa/api/v1/getBalance
```

### Step 5: Handle Token Expiry

When you receive a `401 Unauthorized` response:

```json
{
  "error": "Invalid or expired access token",
  "message": "Please re-authorize to get a new token"
}
```

**Action:** Call `/authorize` again to get a new token.

## Token Validation

### Token Structure

Every JWT has three parts separated by dots: `header.payload.signature`

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9  <- Header (base64url)
.
eyJhY2NvdW50IjoiZm...                    <- Payload (base64url)
.
m6EJnKICZdfSrPB0e8CSXaZShdxc7LU...     <- Signature (base64url HMAC-SHA256)
```

### Signature Verification

The server signs the token using HMAC-SHA256 with a secret key:

```
signature = HMAC-SHA256(header.payload, JWT_SECRET)
```

If the token is modified, the signature will not match and validation fails.

## Best Practices

### 1. Automatic Refresh

Implement automatic token refresh before expiry:

```python
import time

def check_token_expiry():
    if token_expiry - time.time() < 300:  # Less than 5 minutes left
        refresh_token()
```

### 2. Exponential Backoff

Retry failed requests with increasing delays:

```python
import time

for attempt in range(3):
    try:
        response = make_request()
        break
    except RequestException as e:
        if attempt < 2:
            delay = 2 ** attempt
            time.sleep(delay)
        else:
            raise e
```

### 3. Error Handling

Handle authentication errors gracefully:

```python
try:
    response = requests.get(
        url,
        headers={'Authorization': f'Bearer {token}'}
    )
    response.raise_for_status()
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 401:
        # Token expired
        token = authorize_client()
        # Retry request
    else:
        # Other HTTP error
        raise e
```

### 4. Logging

**Log this:**

- Authentication attempts (without credentials)
- Token refresh events
- API request status

**Never log:**

- API keys or secrets
- Access tokens
- Full request/response bodies containing sensitive data

```python
import logging

logger = logging.getLogger(__name__)
logger.info('Token refreshed, new expiry: ' + str(token_expiry))
logger.error(f'Authorization failed: {error}')
# Don't do: logger.error(f'Token: {token}')
```

## Troubleshooting

### "Missing API key or secret code"

**Cause:** Headers not sent or misspelled

**Solution:**

- Check header names: `X-API-KEY` and `X-API-SECRET` (case-sensitive)
- Verify headers are not URL-encoded
- Use the correct HTTP method (POST for authorize)

### "Invalid or expired access token"

**Cause:** Token missing, malformed, or expired

**Solution:**

- Verify token is included in `Authorization: Bearer <token>` header
- Check token hasn't expired (valid for 3600 seconds)
- Verify token wasn't modified
- Get a new token if necessary

### "Unauthorized"

**Cause:** Credentials are invalid or account doesn't exist

**Solution:**

- Verify API key and secret with your account manager
- Check if credentials have been rotated
- Ensure account is active and in good standing

## Security Checklist

- [ ] Credentials stored in environment variables
- [ ] Tokens never logged or exposed
- [ ] Automatic token refresh implemented
- [ ] Exponential backoff on retries
- [ ] HTTPS only (no HTTP)
- [ ] SSL certificate verification enabled
- [ ] Error messages don't leak sensitive info
- [ ] Credentials rotated regularly
- [ ] Access logs monitored

## References

- [JWT Introduction](https://jwt.io/introduction)
- [HMAC-SHA256](https://en.wikipedia.org/wiki/HMAC)
- [Environment Variables Setup](./environment-variables.md)
- [API Reference](../api-reference.md)
