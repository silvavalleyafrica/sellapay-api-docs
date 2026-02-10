````markdown
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

## 📖 Examples

### Authorize and Get Balance (cURL)

```bash
# 1. Authorize
TOKEN=$(curl -s -X POST \
  -H "X-API-KEY: YOUR_API_KEY" \
  -H "X-API-SECRET: YOUR_API_SECRET" \
  "https://pay.sellapay.africa/api/v1/authorize" | jq -r '.access_token')

# 2. Get balance using token
curl -H "Authorization: Bearer $TOKEN" \
  "https://pay.sellapay.africa/api/v1/getBalance"
```

### Send M-Pesa STK Push (Python)

```python
import requests

api_key = "YOUR_API_KEY"
api_secret = "YOUR_API_SECRET"

# Authorize
auth_response = requests.post(
    "https://pay.sellapay.africa/api/v1/authorize",
    headers={
        "X-API-KEY": api_key,
        "X-API-SECRET": api_secret
    }
)
token = auth_response.json()["access_token"]

# Request STK push
stk_response = requests.post(
    "https://pay.sellapay.africa/api/v1/requestStkPush",
    headers={"Authorization": f"Bearer {token}"},
    data={
        "amount": 100,
        "phone": "712345678",  # Local format, no country code
        "note": "Payment for invoice #1001"
    }
)
print(stk_response.json())
```

See [examples/](./examples/) directory for more language-specific implementations.

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

```