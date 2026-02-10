# Documentation Index

Complete guide to all Sellapay API documentation files.

## 📋 Structure Overview

```
docs/
├── README.md                      # Main introduction & quick start
├── api-reference.md               # Complete endpoint documentation
├── CHANGELOG.md                   # Version history and updates
├── CONTRIBUTING.md                # Contributing guidelines
├── TROUBLESHOOTING.md             # Common issues and solutions
├── examples/
│   ├── python/README.md           # Python implementation guide
│   ├── javascript/README.md       # JavaScript (Node.js & Browser)
│   ├── php/README.md              # PHP (cURL & Guzzle)
│   └── ruby/README.md             # Ruby (HTTParty & Net::HTTP)
└── setup/
    ├── README.md                  # Setup overview
    ├── sdk-installation.md        # How to install SDKs
    ├── authentication-setup.md    # JWT auth explanation
    └── environment-variables.md   # Env var management
```

---

## 🚀 Getting Started

### For First-Time Users

1. **Start here:** [README.md](./README.md)
   - Quick overview
   - 5-minute quick start
   - Core concepts

2. **Set up environment:** [setup/README.md](./setup/README.md)
   - Prerequisites
   - First request walkthrough
   - Common troubleshooting

3. **Install SDK:** [setup/sdk-installation.md](./setup/sdk-installation.md)
   - Choose your language
   - Installation instructions
   - Verification steps

4. **Set up authentication:** [setup/authentication-setup.md](./setup/authentication-setup.md)
   - Obtain API credentials
   - Authorization flow
   - Token management

5. **Pick your language:** Choose one integration guide:
   - [Python](./examples/python/README.md)
   - [JavaScript](./examples/javascript/README.md)
   - [PHP](./examples/php/README.md)
   - [Ruby](./examples/ruby/README.md)

---

## 📚 Documentation by Purpose

### Authentication & Security

- **JWT Explained:** [setup/authentication-setup.md](./setup/authentication-setup.md)
  - Authentication flow diagram
  - Token validation details
  - Best practices checklist
  - Troubleshooting 401 errors

- **Environment Variables:** [setup/environment-variables.md](./setup/environment-variables.md)
  - Variable names and purposes
  - Language-specific loading
  - Production deployment
  - Credential rotation

- **API Reference:** [api-reference.md](./api-reference.md)
  - Endpoint list
  - All parameters
  - All response codes
  - Bank codes for transfers

### Implementation Guides

**Python:**

- Location: [examples/python/README.md](./examples/python/README.md)
- Features: SellapayClient class, token caching, auto-retry
- Tests: pytest examples + integration tests

**JavaScript:**

- Location: [examples/javascript/README.md](./examples/javascript/README.md)
- Features: Node.js and browser support, axios + fetch
- Tests: Jest examples + integration tests

**PHP:**

- Location: [examples/php/README.md](./examples/php/README.md)
- Features: cURL and Guzzle clients, production checklist
- Tests: PHPUnit examples + integration tests

**Ruby:**

- Location: [examples/ruby/README.md](./examples/ruby/README.md)
- Features: HTTParty and Net::HTTP, Rails integration
- Tests: Minitest examples + integration tests

### Advanced Topics

**Rate Limiting:**

- See: [setup/README.md](./setup/README.md#rate-limiting)
  - Limits per endpoint
  - Monitoring headers
  - Best practices

**Error Handling:**

- See: [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)
  - Status codes explained
  - Common errors by type
  - Debugging checklist

**Deployment:**

- See: [setup/environment-variables.md](./setup/environment-variables.md#production-deployment)
  - Docker containerization
  - Kubernetes setup
  - AWS Lambda configuration
  - Environment management

### Reference Documentation

- **Endpoints:** [api-reference.md](./api-reference.md)
- **Versions:** [CHANGELOG.md](./CHANGELOG.md)
- **Troubleshooting:** [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)

---

## 🔍 File Guide

### Root Level

| File                                       | Purpose                           | Audience     |
| ------------------------------------------ | --------------------------------- | ------------ |
| [README.md](./README.md)                   | Quick overview, getting started   | Everyone     |
| [api-reference.md](./api-reference.md)     | Complete endpoint documentation   | Developers   |
| [CHANGELOG.md](./CHANGELOG.md)             | Version history, breaking changes | Maintenance  |
| [CONTRIBUTING.md](./CONTRIBUTING.md)       | How to contribute                 | Contributors |
| [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) | Common issues, solutions          | Debugging    |

### /setup directory

| File                                                               | Purpose                          |
| ------------------------------------------------------------------ | -------------------------------- |
| [setup/README.md](./setup/README.md)                               | Setup overview & quick links     |
| [setup/sdk-installation.md](./setup/sdk-installation.md)           | How to install for all languages |
| [setup/authentication-setup.md](./setup/authentication-setup.md)   | JWT auth explained               |
| [setup/environment-variables.md](./setup/environment-variables.md) | Env var management & deployment  |

### /examples directory

| File                                                             | Purpose                   |
| ---------------------------------------------------------------- | ------------------------- |
| [examples/python/README.md](./examples/python/README.md)         | Python (3.6+) integration |
| [examples/javascript/README.md](./examples/javascript/README.md) | JavaScript/Node.js (14+)  |
| [examples/php/README.md](./examples/php/README.md)               | PHP (7.4+) integration    |
| [examples/ruby/README.md](./examples/ruby/README.md)             | Ruby (2.7+) integration   |

---

## 🎯 Use Cases

### "I need to integrate the API"

1. Read: [README.md](./README.md#quick-start)
2. Choose language:
   - Python: [examples/python/README.md](./examples/python/README.md)
   - JavaScript: [examples/javascript/README.md](./examples/javascript/README.md)
   - PHP: [examples/php/README.md](./examples/php/README.md)
   - Ruby: [examples/ruby/README.md](./examples/ruby/README.md)
3. Install SDK: [setup/sdk-installation.md](./setup/sdk-installation.md)
4. Set up auth: [setup/authentication-setup.md](./setup/authentication-setup.md)
5. Run your first request

### "I'm getting an error"

1. Go to: [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)
2. Find your error in "Quick Diagnosis"
3. Follow solutions
4. If unresolved, check [api-reference.md](./api-reference.md#error-responses)

### "I need to deploy to production"

1. Check: [setup/environment-variables.md](./setup/environment-variables.md#production-deployment)
2. Choose your platform:
   - Docker
   - Kubernetes
   - AWS Lambda
   - Other
3. Set up variables
4. Deploy and test

### "I want to contribute"

1. Read: [CONTRIBUTING.md](./CONTRIBUTING.md)
2. Follow guidelines
3. See structure in [CONTRIBUTING.md#documentation-organization](./CONTRIBUTING.md#documentation-organization)

### "I need to know about API versions"

1. Visit: [CHANGELOG.md](./CHANGELOG.md#version-history)
2. Check: [api-reference.md](./api-reference.md#versioning)
3. Current: **v1** (Stable)

---

## 🔗 Cross References

### Related Documentation Links

**Authentication:**

- Main guide: [setup/authentication-setup.md](./setup/authentication-setup.md)
- JWT info: [CONTRIBUTING.md#code-standards](./CONTRIBUTING.md#code-standards)
- Troubleshooting 401: [TROUBLESHOOTING.md#authentication-errors-401](./TROUBLESHOOTING.md#authentication-errors-401)

**Endpoints:**

- Full reference: [api-reference.md](./api-reference.md#endpoints)
- Quick start: [README.md#endpoints](./README.md#endpoints)
- Error codes: [api-reference.md#http-status-codes](./api-reference.md#http-status-codes)

**Environment Setup:**

- Installation: [setup/sdk-installation.md](./setup/sdk-installation.md)
- Variables: [setup/environment-variables.md](./setup/environment-variables.md)
- Production: [setup/environment-variables.md#production-deployment](./setup/environment-variables.md#production-deployment)

**Examples:**

- Python: [examples/python/README.md](./examples/python/README.md)
- JavaScript: [examples/javascript/README.md](./examples/javascript/README.md)
- PHP: [examples/php/README.md](./examples/php/README.md)
- Ruby: [examples/ruby/README.md](./examples/ruby/README.md)

---

## 📊 Documentation Statistics

### Coverage

- **Core Endpoints:** 6 documented
  - `/authorize` - JWT token generation
  - `/getBalance` - Account balance
  - `/requestStkPush` - M-Pesa prompt
  - `/sendFundsToMobile` - Mobile transfer
  - `/sendFundsToSellapay` - Internal transfer
  - `/sendFundsToLocalBank` - Bank transfer

- **Programming Languages:** 4 complete examples
  - Python with SellapayClient class
  - JavaScript with Node.js & browser support
  - PHP with cURL & Guzzle
  - Ruby with HTTParty & Net::HTTP

- **Documentation Pages:** 12 comprehensive guides
  - 1 main README
  - 1 API reference
  - 1 changelog
  - 1 troubleshooting guide
  - 1 contributing guide
  - 4 language examples
  - 3 setup guides

### Code Examples

- **Total working examples:** 50+
- Tests and integration examples included
- All examples runnable and tested

---

## 🎓 Learning Path

### Beginner (30 minutes)

1. Read: [README.md](./README.md)
2. Review: [setup/README.md](./setup/README.md)
3. Follow: [setup/authentication-setup.md](./setup/authentication-setup.md)
4. Result: You understand authentication flow

### Intermediate (1-2 hours)

1. Choose language: [examples/](./examples/)
2. Install: [setup/sdk-installation.md](./setup/sdk-installation.md)
3. Implement: Language example
4. Test: Run your first API call
5. Result: Working API integration

### Advanced (2-4 hours)

1. Review: [api-reference.md](./api-reference.md) completely
2. Study: [setup/environment-variables.md](./setup/environment-variables.md)
3. Implement: Production error handling
4. Deploy: Docker/Kubernetes/Lambda setup
5. Test: Full integration test suite
6. Result: Production-ready integration

### Expert (Ongoing)

1. Monitor: [CHANGELOG.md](./CHANGELOG.md)
2. Contribute: [CONTRIBUTING.md](./CONTRIBUTING.md)
3. Track: [GitHub releases](#)
4. Update: SDK and dependencies
5. Optimize: Performance tuning

---

## 🔍 Search Tips

If you're looking for...

| Topic           | File                                                                                                           |
| --------------- | -------------------------------------------------------------------------------------------------------------- |
| Quick start     | [README.md#quick-start](./README.md)                                                                           |
| All endpoints   | [api-reference.md#endpoints](./api-reference.md#endpoints)                                                     |
| Error codes     | [api-reference.md#http-status-codes](./api-reference.md#http-status-codes)                                     |
| JWT explained   | [setup/authentication-setup.md](./setup/authentication-setup.md)                                               |
| 401 error       | [TROUBLESHOOTING.md#authentication-errors-401](./TROUBLESHOOTING.md#authentication-errors-401)                 |
| 402 error       | [TROUBLESHOOTING.md#balance-errors-402](./TROUBLESHOOTING.md#balance-errors-402)                               |
| Phone format    | [api-reference.md#phone-format](./api-reference.md#phone-format)                                               |
| Rate limiting   | [TROUBLESHOOTING.md#rate-limiting](./TROUBLESHOOTING.md#rate-limiting)                                         |
| Environment     | [setup/environment-variables.md](./setup/environment-variables.md)                                             |
| Docker          | [setup/environment-variables.md#docker](./setup/environment-variables.md#docker)                               |
| Python code     | [examples/python/README.md](./examples/python/README.md)                                                       |
| JavaScript code | [examples/javascript/README.md](./examples/javascript/README.md)                                               |
| PHP code        | [examples/php/README.md](./examples/php/README.md)                                                             |
| Ruby code       | [examples/ruby/README.md](./examples/ruby/README.md)                                                           |
| Debugging       | [TROUBLESHOOTING.md#debugging-best-practices](./TROUBLESHOOTING.md#debugging-best-practices)                   |
| Deploy          | [setup/environment-variables.md#production-deployment](./setup/environment-variables.md#production-deployment) |
| Version         | [CHANGELOG.md](./CHANGELOG.md)                                                                                 |
| Contribute      | [CONTRIBUTING.md](./CONTRIBUTING.md)                                                                           |

---

## 📞 Support & Resources

- **Status Page:** https://status.sellapay.africa/
- **Email Support:** api-support@sellapay.africa
- **GitHub Issues:** [Repository Issues](#)
- **Documentation Issues:** See [CONTRIBUTING.md#bug-reports](./CONTRIBUTING.md#bug-reports)

---

## 📄 Document Versions

| Document                       | Version | Last Updated | Status |
| ------------------------------ | ------- | ------------ | ------ |
| README.md                      | 1.0.0   | 2024-01-15   | Stable |
| api-reference.md               | 1.0.0   | 2024-01-15   | Stable |
| CHANGELOG.md                   | 1.0.0   | 2024-01-15   | Stable |
| CONTRIBUTING.md                | 1.0.0   | 2024-01-15   | Stable |
| TROUBLESHOOTING.md             | 1.0.0   | 2024-01-15   | Stable |
| examples/python                | 1.0.0   | 2024-01-15   | Stable |
| examples/javascript            | 1.0.0   | 2024-01-15   | Stable |
| examples/php                   | 1.0.0   | 2024-01-15   | Stable |
| examples/ruby                  | 1.0.0   | 2024-01-15   | Stable |
| setup/README.md                | 1.0.0   | 2024-01-15   | Stable |
| setup/sdk-installation.md      | 1.0.0   | 2024-01-15   | Stable |
| setup/authentication-setup.md  | 1.0.0   | 2024-01-15   | Stable |
| setup/environment-variables.md | 1.0.0   | 2024-01-15   | Stable |

---

## 🎯 Quick Links

### Most Popular

- [Get Balance Example](./examples/python/README.md#get-balance)
- [Send Funds Example](./examples/javascript/README.md#send-funds-to-mobile)
- [Authentication Flow](./setup/authentication-setup.md#authentication-flow)
- [Error Troubleshooting](./TROUBLESHOOTING.md)

### Must Read

- [API Reference](./api-reference.md)
- [Security Best Practices](./setup/authentication-setup.md#best-practices)
- [Production Deployment](./setup/environment-variables.md#production-deployment)

### Get Started

- [README](./README.md)
- [Setup Guide](./setup/README.md)
- [Your Language](./examples/)

---

## 📝 Notes

- All examples use **production URL**: `https://pay.sellapay.africa/api/v1/`
- API version: **v1** (Stable, recommended for production)
- Requires **HTTPS** (HTTP not supported)
- All code examples are **fully working** and **tested**
