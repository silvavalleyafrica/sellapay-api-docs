# Changelog

All notable changes to the Sellapay API are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned for v2.0

- Batch transfer API
- Advanced analytics endpoints
- Webhook signature verification
- Multi-account support
- Rate limit increase to 1000 req/min

### Planned for v3.0

- GraphQL endpoint
- Real-time balance updates via WebSocket
- Transaction history with filters
- Detailed transaction reports
- Export functionality (CSV, PDF)

---

## [1.0.0] - 2024-01-15

### Added

#### Authentication

- JWT-based authentication with HMAC-SHA256 signing
- Bearer token support in Authorization header
- 3600-second (1 hour) token expiry
- Automatic token refresh on 401 Unauthorized
- Stateless token validation (no server-side session storage)

#### Core Endpoints

**Authorization**

- `POST /authorize` - Obtain JWT access token using API credentials
- Response includes token, expiry, and account information

**Balance**

- `GET /getBalance` - Retrieve account balance in multiple currencies (KES, USD, EUR, GBP)
- Real-time balance updates

**Mobile Money (M-Pesa)**

- `POST /requestStkPush` - Initiate STK push prompt on customer's phone
- `POST /sendFundsToMobile` - Transfer funds to customer's mobile wallet
- Support for local phone format (7xx, 1xx without country code)

**Bank Transfers**

- `POST /sendFundsToLocalBank` - Send funds to Kenyan bank accounts
- Support for all major Kenyan banks (KCB, Equity, Co-op, etc.)
- 50+ bank codes supported

**Internal Transfers**

- `POST /sendFundsToSellapay` - Transfer funds between Sellapay accounts
- Instant settlement

#### Security

- HMAC-SHA256 token signing
- Timing-safe signature comparison (hash_equals)
- Bearer token in Authorization header (not in URL or body)
- Environment variable management for credentials
- HTTPS-only API (no HTTP allowed)

#### Error Handling

- Comprehensive error responses with error codes and messages
- HTTP status codes:
  - 200 OK - Success
  - 400 Bad Request - Validation error
  - 401 Unauthorized - Authentication error
  - 402 Payment Required - Insufficient balance
  - 404 Not Found - Resource not found
  - 500+ Server errors with retry guidance

#### Documentation

- Comprehensive README with professional structure
- API endpoint reference documentation
- Authentication setup guide with JWT explanation
- Environment variables setup guide
- Multi-language client implementation examples:
  - Python with SellapayClient class
  - JavaScript with Node.js and browser support
  - PHP with cURL and Guzzle implementations
  - Ruby with HTTParty and Net::HTTP support
- SDK installation guide for 7+ languages
- Troubleshooting guides
- Production checklists for all languages

#### Client Libraries

**Python** (docs/examples/python/README.md)

- `SellapayClient` class with automatic token refresh
- Token caching and expiry checking
- Exponential backoff retry logic
- 204 lines of documented code
- PyTest integration examples

**JavaScript** (docs/examples/javascript/README.md)

- `SellapayClient` (Node.js, axios-based)
- `SellapayBrowserClient` (vanilla JavaScript, fetch-based)
- Interceptor-based automatic token refresh
- Exponential backoff with jitter
- Jest testing examples
- 340+ lines of documented code

**PHP** (docs/examples/php/README.md)

- `SellapayClient` (cURL-based)
- `SellapayGuzzleClient` (Guzzle-based)
- Both support automatic token refresh
- 401 retry logic
- PHPUnit testing examples
- 280+ lines of documented code

**Ruby** (docs/examples/ruby/README.md)

- `SellapayClient` (HTTParty with middleware)
- `SellapayNetClient` (Net::HTTP)
- Rails service class integration
- Minitest examples
- 260+ lines of documented code

#### Deployment Guides

- Docker containerization patterns
- Kubernetes deployment configuration
- AWS Lambda serverless integration
- Environment-specific configuration (dev/staging/production)
- Credential rotation best practices
- SSL/TLS certificate verification
- Exponential backoff retry patterns (2^attempt delay)

#### Rate Limiting

- 10 requests/minute for `/authorize` endpoint
- 100 requests/minute for all other endpoints
- Rate limit headers: X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset

#### Versioning

- API version in URL: `/api/v1/{action}`
- Semantic versioning for API stability
- Version clearly documented in all examples

### Changed

- N/A (initial release)

### Deprecated

- N/A (initial release)

### Fixed

- N/A (initial release)

### Security

- Session-based tokenization replaced with stateless JWT
- HMAC-SHA256 signing prevents token tampering
- Bearer token pattern (not in URL) prevents token leakage in logs
- Timing-safe signature comparison prevents timing attacks
- Environment variables required for credentials (never hardcoded)

### Removed

- N/A (initial release)

---

## Version History

### API Stability Policy

| Version | Status   | Support    |
| ------- | -------- | ---------- |
| v1.x.x  | Stable   | Indefinite |
| v2.x.x  | Beta     | TBD        |
| v3.x.x  | Planning | TBD        |

### Backward Compatibility

- v1 maintains full backward compatibility within minor/patch versions
- Endpoint URL format preserved: `/api/v1/{action}`
- Authentication method stable (JWT Bearer token)
- Error response format consistent
- New features added without breaking changes

### Breaking Changes Policy

- Major version bump (v1 → v2) only for breaking changes
- 90-day deprecation notice before breaking change
- Migration guide provided
- New endpoint available during transition period

---

## How to Upgrade

### Check Your Version

All responses include version info in headers and documentation.

### From Legacy (Session-based)

If using old session-based authentication:

1. Get new API credentials
2. Update client to use Bearer token pattern
3. Implement automatic token refresh (3600s expiry)
4. Remove session storage code
5. Test thoroughly

Migration guide: [Authentication Setup](./setup/authentication-setup.md)

### Minor Version Updates (1.0 → 1.1)

- No breaking changes expected
- Update dependencies when available
- Test in staging first
- Deploy to production

---

## Support Timeline

| Version | Released   | Support Until           |
| ------- | ---------- | ----------------------- |
| v1.0.0  | 2024-01-15 | 2025-01-15 (+ extended) |
| v2.0.0  | TBD        | TBD                     |

---

## Feedback & Contributions

- Report bugs: [GitHub Issues](#)
- Feature requests: [GitHub Discussions](#)
- Security issues: [security@sellapay.africa](mailto:security@sellapay.africa)
- Documentation: PRs welcome

---

## Related

- [API Reference](./api-reference.md)
- [Authentication Setup](./setup/authentication-setup.md)
- [Environment Variables](./setup/environment-variables.md)
- [GitHub Repository](#)
