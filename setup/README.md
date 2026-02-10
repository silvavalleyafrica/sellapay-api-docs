# Setup & Installation Guides

## Table of Contents

- [Environment Variables](./environment-variables.md)
- [SDK Installation](./sdk-installation.md)
- [Development Setup](./development-setup.md)
- [Authentication Setup](./authentication-setup.md)

---

## Quick Links

- **[Python Setup](../examples/python/README.md#setup)** — Install dependencies for Python integration
- **[JavaScript Setup](../examples/javascript/README.md#setup)** — Install Node.js dependencies
- **[PHP Setup](../examples/php/README.md#setup)** — PHP configuration
- **[Ruby Setup](../examples/ruby/README.md#setup)** — Ruby/Rails integration

## Prerequisites

Before integrating the Sellapay API, ensure you have:

1. **API Credentials**
   - API Key (`X-API-KEY`)
   - Secret Code (`X-API-SECRET`)
   - Contact: support@sellapay.africa

2. **HTTPS Certificate** (Production)
   - Valid SSL/TLS certificate
   - Certificate verification enabled

3. **Programming Language**
   - Python 3.6+
   - Node.js 14+
   - PHP 7.4+
   - Ruby 2.7+

## Getting Started

### 1. Obtain Credentials

Contact your Sellapay account manager or visit the dashboard to get your API credentials.

### 2. Set Environment Variables

Create a `.env` file in your project root:

```env
SELLAPAY_API_KEY=your_actual_api_key
SELLAPAY_API_SECRET=your_actual_secret
SELLAPAY_BASE_URL=https://pay.sellapay.africa/api/v1
```

### 3. Choose Your Language

Follow the setup guide for your preferred language in the [examples](../examples/) directory.

### 4. Test Integration

Use the provided examples to verify your setup works:

```bash
# Python
python example.py

# Node.js
node example.js

# PHP
php example.php

# Ruby
ruby example.rb
```

## Common Issues

### Issue: "Unauthorized" Response

**Cause:** Invalid or missing API credentials

**Solution:**

- Verify credentials in `.env` file
- Check if credentials have been rotated
- Ensure headers are spelled correctly: `X-API-KEY` and `X-API-SECRET`

### Issue: "Invalid or expired access token"

**Cause:** Token has expired (default 3600 seconds)

**Solution:**

- Implement automatic token refresh
- Call `authorize` endpoint again
- Ensure token is included in subsequent requests

### Issue: SSL Certificate Verification Error

**Cause:** SSL certificate verification is failing

**Solution:**

- Ensure HTTPS is used (not HTTP)
- Verify system certificates are up-to-date
- In development only, you may disable verification (not recommended for production)

## Next Steps

- [Read the full API Reference](../api-reference.md)
- [Review authentication details](./authentication-setup.md)
- [Check your chosen language's examples](../examples/)
