# Troubleshooting Guide

## Quick Diagnosis

Use this flowchart to identify and resolve issues:

```
Error occurred?
├─ Status 401? → Check Authentication
├─ Status 402? → Check Balance
├─ Status 400? → Check Request Format
├─ Status 500? → Check Server Status
└─ No response? → Check Network
```

---

## Authentication Errors (401)

### "Invalid or expired access token"

**Symptoms:**

- Request returns `401 Unauthorized`
- Same token worked before
- Been using token for > 1 hour

**Causes:**

- Token expired (3600 second lifetime)
- Token was modified or corrupted
- Token validation failed

**Solutions:**

1. **Obtain new token:**

```python
# Python
token = authorize_client()
```

2. **Check token structure:**

```bash
# Decode token at jwt.io (read-only, don't expose in logs)
# Should have 3 parts: header.payload.signature
```

3. **Verify environment variables:**

```bash
# Check API key and secret are set
echo $API_KEY
echo $API_SECRET
```

4. **Implement automatic refresh:**

```python
# Python
def _ensure_token(self):
    if not self.token or time.time() >= self.token_expiry:
        self.authorize()
```

### "Missing API key or secret code"

**Symptoms:**

- `POST /authorize` returns `400 Bad Request`
- Error message mentions missing credentials

**Causes:**

- Headers not sent
- Header names misspelled
- Headers sent as URL parameters instead

**Solutions:**

1. **Verify headers are correct:**

```bash
curl -v -X POST \
  -H "X-API-KEY: your_api_key" \
  -H "X-API-SECRET: your_api_secret" \
  https://pay.sellapay.africa/api/v1/authorize
```

2. **Check header case sensitivity:**

```
✓ Correct: X-API-KEY
✗ Wrong: x-api-key
✗ Wrong: X_API_KEY
```

3. **Verify headers are in request:**

```python
import requests

# Enable verbose logging
import logging
logging.basicConfig(level=logging.DEBUG)

response = requests.post(url, headers=headers)
```

### "Unauthorized - Invalid API key or secret code"

**Symptoms:**

- `POST /authorize` returns `401 Unauthorized`
- Headers sent correctly
- Same credentials worked before

**Causes:**

- Wrong API key/secret
- Credentials were rotated
- Account suspended or closed
- Account in different environment

**Solutions:**

1. **Verify credentials with account manager:**
   - Check API key starts with correct prefix
   - Confirm secret matches
   - Verify account is active

2. **Regenerate credentials:**
   - Login to dashboard
   - Regenerate API key and secret
   - Update environment variables
   - Restart application

3. **Check if using wrong environment:**
   - Development vs production URL
   - Staging vs production credentials
   - Different business account

---

## Balance Errors (402)

### "Insufficient balance"

**Symptoms:**

- Transaction request returns `402 Payment Required`
- Error shows `required` vs `available` amounts
- Balance was adequate before

**Causes:**

- Your balance is too low
- Concurrent transactions depleted balance
- Fees weren't accounted for
- Wrong currency conversion

**Solutions:**

1. **Check current balance:**

```python
balance = client.get_balance()
print(balance['balance']['KES'])
```

2. **Check transaction cost:**

```json
// Include fees in calculation:
// STK Push: 10 KES
// Mobile transfer: 0-50 KES (varies)
// Bank transfer: 50-100 KES (varies)
Required = Amount + Estimated Fees
```

3. **Top up your account:**
   - Login to dashboard
   - Transfer funds to Sellapay account
   - Wait for deposit to clear (usually < 5 minutes)

4. **Check currency:**

```python
balance = client.get_balance()
# Make sure using correct currency
# STK Push requires KES
```

---

## Format Errors (400)

### "Invalid phone number format"

**Symptoms:**

- `POST /requestStkPush` returns `400 Bad Request`
- Error mentions "Invalid phone number format"

**Causes:**

- Phone includes country code (254)
- Phone too short or too long
- Use wrong prefix (3, 5, 9, etc)
- Extra spaces or special characters

**Correct Format:**

```
✓ 712345678  (7xx format)
✓ 112345678  (1xx format)
✗ 254712345678  (has country code)
✗ +254712345678  (has + sign)
✗ 07 12345678  (has space)
✗ 612345678  (wrong prefix)
```

**Solutions:**

```python
# Clean phone number
phone = "254712345678"  # from user input
phone = phone.replace("+254", "").replace("254", "")  # Remove country code
phone = phone.lstrip("0")  # Remove leading 0
phone = phone.strip()  # Remove spaces

# Should start with 7 or 1
if phone.startswith("7") or phone.startswith("1"):
    # OK to use
    pass
else:
    # ERROR: Invalid prefix
```

### "Invalid amount"

**Symptoms:**

- `POST /requestStkPush` returns `400 Bad Request`
- Error mentions "Invalid amount"

**Causes:**

- Amount is 0 or negative
- Amount too large (> 70,000)
- Amount format invalid (text instead of number)
- Amount has too many decimals

**Solutions:**

```python
# Validate amount
amount = 100.00
if amount <= 0:
    raise ValueError("Amount must be positive")
if amount > 70000:
    raise ValueError("Amount exceeds maximum")
if amount != round(amount, 2):
    raise ValueError("Amount must have max 2 decimals")
```

### "Invalid reference"

**Symptoms:**

- `POST /requestStkPush` returns `400 Bad Request`
- Error mentions "Invalid reference"

**Causes:**

- Reference > 20 characters
- Reference contains special characters
- Reference is not unique (duplicate)

**Solutions:**

```python
# Validate reference
reference = "INV-001"
if len(reference) > 20:
    raise ValueError("Reference must be ≤ 20 chars")
if not reference.replace("-", "").replace("_", "").isalnum():
    raise ValueError("Reference must be alphanumeric")
```

---

## Network Errors

### "Connection refused" / "Cannot connect"

**Symptoms:**

- No response from API
- Connection timeout
- "Connection refused" error

**Causes:**

- API endpoint down
- Network connectivity issue
- Firewall blocking request
- Wrong URL

**Solutions:**

1. **Verify API is up:**

```bash
# Check status page
curl https://status.sellapay.africa/

# Try simple request
curl https://pay.sellapay.africa/api/v1/authorize \
  -H "X-API-KEY: test" \
  -H "X-API-SECRET: test" \
  -v
```

2. **Check network:**

```bash
# Ping endpoint
ping pay.sellapay.africa

# DNS resolution
nslookup pay.sellapay.africa

# Traceroute to endpoint
traceroute pay.sellapay.africa
```

3. **Check firewall:**

```bash
# Port 443 should be open (HTTPS)
# No proxy issues
# No WAF blocking
```

4. **Verify URL:**

```
✓ https://pay.sellapay.africa/api/v1/authorize
✗ http://pay.sellapay.africa (no HTTPS)
✗ https://access.sellapay.africa/api/ (wrong domain)
```

### "SSL certificate error"

**Symptoms:**

- SSL_CERTIFICATE_VERIFY_FAILED
- CERTIFICATE_VERIFY_FAILED
- Certificate pinning failed

**Causes:**

- System clock is wrong
- Certificate chain incomplete
- Expired system certificates
- Antivirus intercepting SSL

**Solutions:**

1. **Update system calendar:**

```bash
# Windows
w32tm /resync

# Mac
date
timedatectl set-ntp true
```

2. **Install certificate:**

```bash
# Windows: Add certificate to trusted store
# Mac: Update certificates via Security.framework
# Linux: Update ca-certificates package
```

3. **For development only - disable verification (NOT production):**

```python
# Python
import ssl
ssl._create_default_https_context = ssl._create_unverified_context

# Node.js
process.env.NODE_TLS_REJECT_UNAUTHORIZED = '0'  # DEV ONLY!

# PHP (cURL)
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);  // DEV ONLY!
```

---

## Rate Limiting

### "Too many requests" / 429 Error

**Symptoms:**

- `429 Too Many Requests` response
- X-RateLimit-Reset header present
- Requests suddenly rejected

**Causes:**

- Hit rate limit (10/min for /authorize, 100/min for others)
- Multiple requests in quick succession
- Polling endpoint too frequently

**Solutions:**

1. **Check rate limits:**

```bash
# Look for these headers
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 45
X-RateLimit-Reset: 1770727500
```

2. **Implement backoff:**

```python
import time
import math

def retry_with_backoff(func, max_attempts=3):
    for attempt in range(max_attempts):
        try:
            return func()
        except RateLimitError:
            if attempt < max_attempts - 1:
                delay = 2 ** attempt  # exponential backoff
                time.sleep(delay)
            else:
                raise
```

3. **Cache tokens:**

```python
# Don't call authorize on every request
# Reuse token until it expires
# Implement token caching with TTL
```

4. **Batch requests:**

```python
# Instead of many small requests
# Batch multiple transactions
# Use /sendFundsToSellapay for bulk transfers
```

---

## Server Errors (500+)

### "Internal Server Error" (500)

**Symptoms:**

- `500 Internal Server Error`
- Same request works sometimes, fails others
- Error in response body is minimal

**Causes:**

- Temporary server issue
- Database connection problem
- Third-party service down (M-Pesa, bank API)
- Request triggered unexpected condition

**Solutions:**

1. **Retry with exponential backoff:**

```python
import time

for attempt in range(3):
    try:
        response = make_request()
        break
    except ServerError as e:
        if attempt < 2:
            time.sleep(2 ** attempt)
        else:
            raise e
```

2. **Check server status:**

```bash
curl https://status.sellapay.africa/
```

3. **Contact support:**
   - Provide request ID (X-Request-ID header)
   - Include timestamp
   - Describe what you were trying to do

### "Service Unavailable" (503)

**Symptoms:**

- `503 Service Unavailable`
- All requests fail
- API completely down

**Causes:**

- Scheduled maintenance
- Server overload
- Database maintenance
- Third-party dependency down

**Solutions:**

1. **Wait and retry:**

```python
import time

while True:
    try:
        response = make_request()
        break
    except ServiceUnavailableError:
        print("Service unavailable, retrying in 30 seconds...")
        time.sleep(30)
```

2. **Check status page:**
   - https://status.sellapay.africa/
   - Email notifications available
   - Follow @sellapay_status on Twitter

3. **Set up monitoring:**

```bash
# Periodically check status
* * * * * curl -f https://pay.sellapay.africa/api/v1/authorize \
  -H "X-API-KEY: $KEY" || send_alert
```

---

## Debugging Best Practices

### 1. Enable Logging

**Python:**

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)
logger.info("Event happened")  # OK
# logger.info(f"Token: {token}")  # NEVER LOG TOKENS!
```

**JavaScript:**

```javascript
// Use console only in development
console.log("Debug info");

// Production: use proper logging library
const logger = require("winston");
logger.info("Event happened");
```

**PHP:**

```php
error_reporting(E_ALL);
ini_set('display_errors', 1);
ini_set('log_errors', 1);
ini_set('error_log', '/var/log/php-errors.log');

error_log('Event happened');
// Don't log: error_log("Token: $token");
```

### 2. Use Request ID

```bash
# Every response includes X-Request-ID header
# Use this when contacting support
curl -v https://pay.sellapay.africa/api/v1/authorize \
  -H "X-API-KEY: test" \
  -H "X-API-SECRET: test" | grep "X-Request-ID"
```

### 3. Test Endpoints in Isolation

```bash
# Test authorize first
curl -X POST \
  -H "X-API-KEY: key" \
  -H "X-API-SECRET: secret" \
  https://pay.sellapay.africa/api/v1/authorize

# Once authorize works, test other endpoints
curl -H "Authorization: Bearer <token>" \
  https://pay.sellapay.africa/api/v1/getBalance
```

### 4. Check Environment Variables

```bash
# Don't print actual values, just verify they exist
[[ -z "$API_KEY" ]] && echo "API_KEY not set"
[[ -z "$API_SECRET" ]] && echo "API_SECRET not set"
```

### 5. Use API Debugging Tools

- **Postman** - GUI for testing endpoints
- **curl** - Command-line HTTP testing
- **Insomnia** - Alternative to Postman
- **Swagger UI** - Interactive API docs
- **request inspector** - Browser network tab

---

## Common Issues Checklist

- [ ] Using HTTPS (not HTTP)
- [ ] API credentials correct and not expired
- [ ] Bearer token format correct
- [ ] Token not expired (< 3600 seconds old)
- [ ] Phone number format correct (no country code)
- [ ] Amount is positive and has max 2 decimals
- [ ] Account has sufficient balance
- [ ] Rate limits not exceeded
- [ ] Network/firewall allows outbound HTTPS
- [ ] System time is accurate (within NTP sync)

---

## Getting Help

- **Documentation:** https://docs.pay.sellapay.africa/
- **Email Support:** api-support@sellapay.africa
- **Status Page:** https://status.sellapay.africa/
- **GitHub Issues:** [GitHub Repository](#)

**When contacting support, include:**

1. Request ID (X-Request-ID header)
2. Timestamp of error
3. Exact endpoint and method
4. Complete error message (no credentials)
5. Steps to reproduce
6. Programming language/version
7. Environment (dev/staging/production)
