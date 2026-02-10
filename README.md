# Sellapay API Documentation

**Current API Version:** `v1` (Stable)

**Available Versions:** `v1`, `v2`, `v3`

**Base Endpoint:** `https://pay.sellapay.africa/api/`

---

Welcome to the Sellapay API documentation. This guide provides comprehensive resources for integrating payment and fund transfer functionality into your application.

## 📋 Documentation Index

- **[API Reference](./api-reference.md)** — Complete endpoint specification, request/response formats, error codes
- **[Authentication](./authentication.md)** — JWT token-based authentication and security best practices
- **[Setup & Installation](./setup/)** — Language-specific setup guides

## 🚀 Quick Start

### 1. Obtain API Credentials

Contact your Sellapay account manager to receive:

- `X-API-KEY` — Your API key
- `X-API-SECRET` — Your secret code

### 2. Choose Your Language

Select an example implementation:

- **[Python](./examples/python/README.md)** — Using `requests` library
- **[JavaScript](./examples/javascript/README.md)** — Node.js and browser examples
- **[PHP](./examples/php/README.md)** — Native and with cURL
- **[Ruby](./examples/ruby/README.md)** — Using `Net::HTTP`

### 3. Get Your First Token

```bash
curl -X POST \
  -H "X-API-KEY: YOUR_API_KEY" \
  -H "X-API-SECRET: YOUR_API_SECRET" \
  "https://pay.sellapay.africa/api/v1/authorize"
```

**Response:**

```json
{
  "message": "User authorized",
  "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 3600
}
```

## 📚 Core Concepts

### Authentication

The API uses JWT (JSON Web Token) authentication with HMAC-SHA256 signing:

- Tokens are cryptographically signed and cannot be forged
- Tokens expire after 3600 seconds by default
- Always use `Authorization: Bearer <token>` header for authenticated requests
- Tokens are stateless (no server-side session storage)

### Actions Supported

| Action                 | Method | Description                  |
| ---------------------- | ------ | ---------------------------- |
| `authorize`            | POST   | Obtain JWT access token      |
| `getBalance`           | GET    | Retrieve account balance     |
| `requestStkPush`       | POST   | Initiate M-Pesa STK push     |
| `sendFundsToMobile`    | POST   | Send money to mobile number  |
| `sendFundsToSellapay`  | POST   | Transfer to Sellapay account |
| `sendFundsToLocalBank` | POST   | Transfer to bank account     |

## 🔒 Security Best Practices

1. **Protect API Credentials**
   - Treat `X-API-KEY` and `X-API-SECRET` as root credentials
   - Never commit to version control
   - Rotate credentials regularly
   - Use environment variables in production

2. **Token Management**
   - Cache tokens securely (memory, secure storage)
   - Implement automatic refresh on expiry
   - Discard tokens on 401 responses
   - Never log tokens

3. **HTTPS Only**
   - Always use HTTPS in production
   - Verify SSL certificates
   - Never transmit credentials over unencrypted connections

4. **Error Handling**
   - Implement exponential backoff on failures
   - Log errors securely (mask sensitive values)
   - Monitor for anomalous activity

## 📖 Endpoint Examples

### 1. Authorize — Get Access Token

**Request:**

```bash
curl -X POST \
  -H "X-API-KEY: YOUR_API_KEY" \
  -H "X-API-SECRET: YOUR_API_SECRET" \
  -H "Content-Type: application/json" \
  -d '{}' \
  "https://pay.sellapay.africa/api/v1/authorize"
```

**Response:**

```json
{
  "message": "User authorized",
  "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 3600
}
```

---

### 2. Get Balance — Check Account Balance

**Request:**

```bash
curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  "https://pay.sellapay.africa/api/v1/getBalance"
```

**Response:**

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

---

### 3. Request STK Push — Initiate M-Pesa Payment

**Request:**

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "712345678",
    "amount": 100,
    "reference": "INV-001",
    "description": "Payment for services"
  }' \
  "https://pay.sellapay.africa/api/v1/requestStkPush"
```

**Response:**

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

**Request Parameters:**

- `phone` (required): Customer phone number in local format (7xx or 1xx, no country code)
- `amount` (required): Amount in KES
- `reference` (optional): Your transaction reference
- `description` (optional): Description visible to customer

---

### 4. Send Funds to Mobile — Initiate Mobile Transfer

**Request:**

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "712345678",
    "amount": 100,
    "reference": "REF-001",
    "narrative": "Refund"
  }' \
  "https://pay.sellapay.africa/api/v1/sendFundsToMobile"
```

**Response:**

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

**Request Parameters:**

- `phone` (required): Recipient phone number (local format, no country code)
- `amount` (required): Amount to send in KES
- `reference` (optional): Your unique reference
- `narrative` (optional): Description visible to recipient

---

### 5. Send Funds to Sellapay Account — Account-to-Account Transfer

**Request:**

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "account": "fbsotk4jH1L7FiO2z2qkqsPiqVHJSaVmpgBu8a7iMWGaba97RH",
    "amount": 1000,
    "reference": "REF-12345",
    "narrative": "Payment invoice 001"
  }' \
  "https://pay.sellapay.africa/api/v1/sendFundsToSellapay"
```

**Response:**

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

**Request Parameters:**

- `account` (required): Recipient Sellapay account ID
- `amount` (required): Amount to transfer
- `reference` (optional): Your unique reference
- `narrative` (optional): Transaction description

---

### 6. Send Funds to Bank — Bank Account Transfer

**Request:**

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "swift_code=EQBLKENA&bank_account=0123456789&amount=5000&note=Payment+for+services" \
  "https://pay.sellapay.africa/api/v1/sendFundsToLocalBank"
```

**Response:**

```json
{
  "message": "Funds sent successfully",
  "transaction_id": "TXN-1770727500-88888",
  "bank": "Equity Bank",
  "bank_account": "0123456789",
  "amount": 5000.0,
  "status": "pending",
  "timestamp": 1770727500,
  "estimated_arrival": "2 hours"
}
```

**Request Parameters:**

- `swift_code` (required): Bank SWIFT code (e.g., "EQBLKENA" for Equity Bank Kenya)
- `bank_account` (required): Recipient bank account number
- `amount` (required): Amount in KES
- `note` (optional): Transaction description

**Common Kenya Bank SWIFT Codes:**

```
KCBLKENA   = KCB Group Limited
BARCKENA   = Barclays Bank Kenya
COBAKENAN  = Co-operative Bank Kenya
SCBLKENA   = Standard Chartered Kenya
CITIKENA   = Citibank Kenya
DTBLKENA   = Diamond Trust Bank Kenya
NBKEKENA   = National Bank of Kenya
EQBLKENA   = Equity Bank Kenya
HOUBEKEA   = Housing Finance Bank
CBKEKENA   = Consolidated Bank Kenya
```

---

See [examples/](./examples/) directory for complete language-specific implementations with error handling and token refresh logic.

## 🌐 API Versions

| Version | Status   | Deprecation Date | Notes                                  |
| ------- | -------- | ---------------- | -------------------------------------- |
| v1      | Stable   | N/A              | Recommended for all new integrations   |
| v2      | Beta     | TBD              | Enhanced features, backward compatible |
| v3      | Planning | TBD              | Future improvements                    |

## 📞 Support

For integration assistance, technical issues, or questions:

- **Email:** support@sellapay.africa
- **Documentation:** https://docs.sellapay.africa
- **API Status:** https://status.sellapay.africa

## 📝 Changelog

### Version 1.0.0 (Current - Feb 10, 2026)

- ✅ JWT token-based authentication
- ✅ Stateless API design
- ✅ HMAC-SHA256 token signing
- ✅ Multi-language support
- ✅ Comprehensive error handling

---

**Last Updated:** February 10, 2026  
**Documentation Version:** 1.0.0
