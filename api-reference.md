# API Reference

Complete endpoint documentation for Sellapay API v1.

## Base URL

```
https://pay.sellapay.africa/api/v1
```

## Authentication

All endpoints (except `/authorize`) require Bearer token authentication:

```bash
Authorization: Bearer <access_token>
```

## Endpoints

### 1. Authorize

Obtain an access token using API credentials.

**Endpoint:** `POST /authorize`

**Request Headers:**

```
X-API-KEY: your_api_key
X-API-SECRET: your_api_secret
Content-Type: application/json
```

**Request Body:**

```json
{}
```

**Response (200 OK):**

```json
{
  "message": "User authorized",
  "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhY2NvdW50IjoiZm...",
  "expires_in": 3600
}
```

**Error Responses:**

- `400 Bad Request` - Missing credentials

```json
{
  "error": "Missing API key or secret code"
}
```

- `401 Unauthorized` - Invalid credentials

```json
{
  "error": "Unauthorized",
  "message": "Invalid API key or secret code"
}
```

**Token Lifetime:** 3600 seconds (1 hour)

**Usage:**

```bash
curl -X POST \
  -H "X-API-KEY: your_api_key" \
  -H "X-API-SECRET: your_api_secret" \
  https://pay.sellapay.africa/api/v1/authorize
```

---

### 2. Get Balance

Retrieve your account balance in all supported currencies.

**Endpoint:** `GET /getBalance`

**Authentication:** Required (Bearer token)

**Response (200 OK):**

```json
{
  "message": "Balance retrieved",
  "balance": {
    "KES": 50000.0,
    "USD": 425.53,
    "EUR": 389.95,
    "GBP": 338.45
  },
  "primary_currency": "KES",
  "timestamp": 1770727500
}
```

**Error Responses:**

- `401 Unauthorized` - Invalid or expired token

```json
{
  "error": "Invalid or expired access token",
  "message": "Please re-authorize to get a new token"
}
```

**Usage:**

```bash
curl -H "Authorization: Bearer <token>" \
  https://pay.sellapay.africa/api/v1/getBalance
```

---

### 3. Request STK Push

Initiate an M-Pesa STK prompt on a customer's phone.

**Endpoint:** `POST /requestStkPush`

**Authentication:** Required (Bearer token)

**Request Body:**

```json
{
  "phone": "712345678",
  "amount": 100.0,
  "reference": "INV-12345",
  "description": "Payment for services"
}
```

**Request Parameters:**

| Field         | Type   | Required | Description                                                       |
| ------------- | ------ | -------- | ----------------------------------------------------------------- |
| `phone`       | string | Yes      | Customer phone number (local format: 7xx or 1xx, no country code) |
| `amount`      | float  | Yes      | Amount to charge (in KES)                                         |
| `reference`   | string | No       | Your unique transaction reference (20 chars max)                  |
| `description` | string | No       | Transaction description visible to customer                       |

**Response (200 OK):**

```json
{
  "message": "STK push initiated",
  "transaction_id": "TXN-1770727500-12345",
  "phone": "254712345678",
  "amount": 100.0,
  "status": "pending",
  "description": "Payment for services"
}
```

**Error Responses:**

- `400 Bad Request` - Invalid parameters

```json
{
  "error": "Validation error",
  "message": "Invalid phone number format",
  "details": {
    "field": "phone",
    "reason": "Phone must start with 7 or 1"
  }
}
```

- `401 Unauthorized` - Invalid token

```json
{
  "error": "Invalid or expired access token",
  "message": "Please re-authorize to get a new token"
}
```

- `402 Payment Required` - Insufficient balance

```json
{
  "error": "Insufficient balance",
  "message": "Your account balance is too low",
  "required": 100.0,
  "available": 45.5
}
```

**Phone Format:**

- **Local format (Safaricom MPESA)**: `712345678` or `112345678`
- **Only 9 digits after first digit**
- **Must start with 7 or 1**
- **No country code (254 added by server)**

**Usage:**

```bash
curl -X POST \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "712345678",
    "amount": 100,
    "reference": "INV-001"
  }' \
  https://pay.sellapay.africa/api/v1/requestStkPush
```

---

### 4. Send Funds to Mobile

Transfer funds to a customer's mobile wallet.

**Endpoint:** `POST /sendFundsToMobile`

**Authentication:** Required (Bearer token)

**Request Body:**

```json
{
  "phone": "712345678",
  "amount": 100.0,
  "reference": "REF-12345",
  "narrative": "Refund"
}
```

**Request Parameters:**

| Field       | Type   | Required | Description                                     |
| ----------- | ------ | -------- | ----------------------------------------------- |
| `phone`     | string | Yes      | Recipient phone (local format, no country code) |
| `amount`    | float  | Yes      | Amount to send (in KES)                         |
| `reference` | string | No       | Your unique reference                           |
| `narrative` | string | No       | Visible to recipient                            |

**Response (200 OK):**

```json
{
  "message": "Funds sent successfully",
  "transaction_id": "TXN-1770727500-54321",
  "phone": "254712345678",
  "amount": 100.0,
  "status": "completed",
  "timestamp": 1770727500
}
```

**Error Responses:**

- `400 Bad Request` - Invalid parameters
- `401 Unauthorized` - Invalid token
- `402 Payment Required` - Insufficient balance
- `500 Internal Server Error` - Transfer failed

**Usage:**

```bash
curl -X POST \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "712345678",
    "amount": 100,
    "reference": "REF-001"
  }' \
  https://pay.sellapay.africa/api/v1/sendFundsToMobile
```

---

### 5. Send Funds to Sellapay Account

Transfer funds to another Sellapay user.

**Endpoint:** `POST /sendFundsToSellapay`

**Authentication:** Required (Bearer token)

**Request Body:**

```json
{
  "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
  "amount": 1000.0,
  "reference": "REF-12345",
  "narrative": "Payment invoice 001"
}
```

**Request Parameters:**

| Field       | Type   | Required | Description             |
| ----------- | ------ | -------- | ----------------------- |
| `account`   | string | Yes      | Recipient account ID    |
| `amount`    | float  | Yes      | Amount to send          |
| `reference` | string | No       | Your unique reference   |
| `narrative` | string | No       | Transaction description |

**Response (200 OK):**

```json
{
  "message": "Funds sent successfully",
  "transaction_id": "TXN-1770727500-99999",
  "recipient_account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
  "amount": 1000.0,
  "status": "completed",
  "timestamp": 1770727500
}
```

**Error Responses:**

- `400 Bad Request` - Invalid account or amount
- `401 Unauthorized` - Invalid token
- `404 Not Found` - Recipient account doesn't exist
- `402 Payment Required` - Insufficient balance

**Usage:**

```bash
curl -X POST \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
    "amount": 1000
  }' \
  https://pay.sellapay.africa/api/v1/sendFundsToSellapay
```

---

### 6. Send Funds to Bank

Transfer funds to a bank account.

**Endpoint:** `POST /sendFundsToLocalBank`

**Authentication:** Required (Bearer token)

**Request Body:**

```json
{
  "bank_code": "001",
  "account_number": "1234567890",
  "amount": 5000.0,
  "account_name": "John Doe",
  "reference": "REF-12345"
}
```

**Request Parameters:**

| Field            | Type   | Required | Description                           |
| ---------------- | ------ | -------- | ------------------------------------- |
| `bank_code`      | string | Yes      | Kenya bank code (e.g., "001" for KCB) |
| `account_number` | string | Yes      | Recipient account number              |
| `amount`         | float  | Yes      | Amount to send (KES)                  |
| `account_name`   | string | Yes      | Recipient name                        |
| `reference`      | string | No       | Your reference                        |

**Common Bank Codes:**

```
001 = KCB Group Limited
002 = Barclays Bank
006 = Co-operative Bank
007 = Standard Chartered
008 = Citibank Kenya
011 = Diamond Trust Bank
014 = National Bank of Kenya
015 = Equity Bank
031 = Housing Finance
032 = Consolidated Bank Kenya
```

**Response (200 OK):**

```json
{
  "message": "Funds sent successfully",
  "transaction_id": "TXN-1770727500-88888",
  "bank": "KCB Group Limited",
  "account_number": "1234567890",
  "amount": 5000.0,
  "status": "pending",
  "timestamp": 1770727500,
  "estimated_arrival": "2 hours"
}
```

**Error Responses:**

- `400 Bad Request` - Invalid bank details
- `401 Unauthorized` - Invalid token
- `402 Payment Required` - Insufficient balance
- `500 Internal Server Error` - Bank transfer failed

**Usage:**

```bash
curl -X POST \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "bank_code": "015",
    "account_number": "0123456789",
    "amount": 5000,
    "account_name": "John Doe"
  }' \
  https://pay.sellapay.africa/api/v1/sendFundsToLocalBank
```

---

## HTTP Status Codes

| Code  | Meaning             | Action                         |
| ----- | ------------------- | ------------------------------ |
| `200` | OK                  | Request succeeded              |
| `400` | Bad Request         | Fix request parameters         |
| `401` | Unauthorized        | Re-authorize to get new token  |
| `402` | Payment Required    | Check account balance          |
| `404` | Not Found           | Verify resource exists         |
| `500` | Server Error        | Retry with exponential backoff |
| `503` | Service Unavailable | Retry later                    |

## Common Request Headers

```
Authorization: Bearer <access_token>
Content-Type: application/json
Accept: application/json
User-Agent: MyApp/1.0
```

## Common Response Headers

```
Content-Type: application/json
Date: Mon, 01 Jan 2024 12:00:00 GMT
X-Process-Time: 123ms
X-Request-ID: req_12345678
```

## Rate Limiting

- **Authorize:** 10 requests per minute (per IP)
- **All other endpoints:** 100 requests per minute (per token)
- **Headers returned:**
  - `X-RateLimit-Limit` - Request limit
  - `X-RateLimit-Remaining` - Requests remaining
  - `X-RateLimit-Reset` - Reset timestamp

## Pagination

Some list endpoints support pagination with:

- `?page=1` - Page number (1-indexed)
- `?limit=10` - Results per page (max 100)

## Error Handling

All error responses include:

```json
{
  "error": "Error code",
  "message": "Human readable message",
  "details": {}
}
```

**Always check:**

1. HTTP status code
2. `error` field for error type
3. `message` field for details
4. `details` object for field-specific errors

## Versioning

Current API version: **v1** (Stable)

Future versions may have breaking changes. Version in URL: `/api/v1/`

See [CHANGELOG](../CHANGELOG.md) for updates.

## Support

- **Documentation:** https://docs.pay.sellapay.africa/
- **Status Page:** https://status.sellapay.africa/
- **Email:** api-support@sellapay.africa
