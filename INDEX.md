````markdown
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

```

```
````
