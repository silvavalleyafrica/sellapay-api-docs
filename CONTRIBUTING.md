`````markdown
# Contributing to Sellapay API Documentation

Thank you for your interest in contributing! This guide explains how to contribute to the Sellapay API documentation and examples.

## Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [Bug Reports](#bug-reports)
4. [Submitting Changes](#submitting-changes)
5. [Documentation Standards](#documentation-standards)
6. [Code Standards](#code-standards)
7. [Testing](#testing)
8. [Pull Request Process](#pull-request-process)

---

## Code of Conduct

- Be respectful to other contributors
- Security vulnerabilities are reported privately (not on GitHub)
- No harassment or discrimination
- Professional communication at all times

---

## Getting Started

### Prerequisites

- Git installed
- GitHub account
- Text editor or IDE
- Basic knowledge of the Sellapay API

### Fork and Clone

```bash
# Fork on GitHub (click "Fork" button)

# Clone your fork
git clone https://github.com/YOUR_USERNAME/sellapay.git
cd sellapay

# Add upstream remote
git remote add upstream https://github.com/sellapay/sellapay.git

# Verify remotes
git remote -v
```

### Create Feature Branch

```bash
# Update main first
git checkout main
git pull upstream main

# Create feature branch
git checkout -b docs/your-feature-name
# or
git checkout -b example/python-new-feature
# or
git checkout -b fix/broken-javascript-example
```

**Branch naming convention:**

- `docs/` - Documentation changes
- `example/` - New language examples
- `fix/` - Bug fixes
- `feature/` - New features

---

## Bug Reports

### Before Submitting

1. Check [existing issues](../../issues)
2. Search documentation for solutions
3. Check [Troubleshooting Guide](./TROUBLESHOOTING.md)

### Creating Issue

Include:

```markdown
## Description

[Clear description of the bug]

## Steps to Reproduce

1. [First step]
2. [Second step]
3. [Actual issue]

## Expected Behavior

[What should happen]

## Actual Behavior

[What actually happens]

## Environment

- Language: Python 3.9
- SDK version: 1.0.0
- OS: Ubuntu 20.04
- API version: v1

## Logs/Error Output

[Paste error message or logs]
```

---

## Submitting Changes

### What We Accept

✅ **Good contributions:**

- Documentation fixes and improvements
- New language examples (Python, JavaScript, PHP, Ruby, etc.)
- Bug fixes with tests
- Performance improvements
- Security enhancements
- Typo fixes
- Translation improvements

❌ **We don't accept:**

- API changes (contact Sellapay team)
- Unrelated features
- Breaking changes without discussion
- Credentials or secrets in commits
- Large vendor dependencies without justification

### Contribution Process

1. **Make changes** on your feature branch
2. **Test your changes** thoroughly
3. **Commit with clear messages**
4. **Push to your fork**
5. **Create Pull Request**

---

## Documentation Standards

### Markdown Format

Use standard Markdown:

````markdown
# Main Header

## Sub Header

### Sub-sub Header

**Bold**
_Italic_
`Code`

- Bullet list
- Another item

1. Numbered list
2. Another item

[Link text](https://example.com)

```code block
code here
```
````

````

### Language and Style

- Use **imperative mood** for headings ("Set up authentication" not "Setting up authentication")
- Use **singular you** when addressing user ("you must configure" not "one must configure")
- Keep sentences **short and clear**
- Use **active voice**
- Define **technical terms** on first use

### Structure

Every documentation file should have:

```markdown
# Title

## Overview
[Brief description]

## Prerequisites
[What's needed]

## Step-by-Step Guide
[Clear numbered steps]

## Verification
[How to verify it works]

## Troubleshooting
[Common issues]

## See Also
[Related docs]
````

### Code Examples

Include working code:

```python
# CORRECT: Complete, runnable example
import requests

response = requests.post(
    'https://pay.sellapay.africa/api/v1/authorize',
    headers={
        'X-API-KEY': 'your_api_key',
        'X-API-SECRET': 'your_api_secret'
    }
)
token = response.json()['access_token']
print(token)
```

Don't include:

```python
# WRONG: Incomplete snipped
response = requests.post(..., headers=...)
# ... doesn't convey intent

# WRONG: Made-up code that won't work
response = api.authorize(key, secret)  # This function doesn't exist!
```

### Documentation Checklist

- [ ] Title clearly describes content
- [ ] Overview explains purpose
- [ ] Prerequisites listed
- [ ] Step-by-step with clear progression
- [ ] Code examples are complete and runnable
- [ ] External links are working
- [ ] No credentials or secrets exposed
- [ ] Tested in actual environment
- [ ] Consistent with existing documentation style
- [ ] Proper Markdown formatting
- [ ] Spell-checked

---

## Code Standards

### Python Examples

```python
# Style: PEP 8
# Black formatter with 88 line length
# pylint for linting

# Use type hints (Python 3.6+)
def authorize(self, api_key: str, api_secret: str) -> str:
    """Authorization docstring."""
    pass

# Docstrings for all classes and functions
class SellapayClient:
    """Handles Sellapay API requests with automatic token refresh."""

    def get_balance(self) -> Dict[str, float]:
        """Retrieve account balance in multiple currencies.

        Returns:
            Dict with currency codes as keys, balances as values.

        Raises:
            SellapayError: On API error.
        """
        pass
```

### JavaScript Examples

```javascript
// Style: Airbnb JavaScript Style Guide
// ESLint for linting
// Prettier for formatting

// Use JSDoc comments
/**
 * Initialize Sellapay client.
 * @param {string} apiKey - API key
 * @param {string} apiSecret - API secret
 * @returns {SellapayClient} Client instance
 */
function createClient(apiKey, apiSecret) {
  // implementation
}

// Use async/await
async function getBalance() {
  try {
    const response = await fetch("/getBalance");
    return response.json();
  } catch (error) {
    console.error("Balance fetch failed:", error);
  }
}
```

### PHP Examples

```php
// Style: PSR-12 (PHP Standards Recommendations)
// phpcs for linting
// phpstan for static analysis

/**
 * Get account balance.
 *
 * @return array Account balance by currency.
 * @throws SellapayException On API error.
 */
public function getBalance(): array
{
    // implementation
}

// Use type hints for PHP 7.4+
public function authorize(string $apiKey, string $apiSecret): string
{
    // implementation
}
```

### Ruby Examples

```ruby
# Style: Ruby Style Guide
# RuboCop for linting

# Use YARD documentation
class SellapayClient
  # Initialize client
  #
  # @param api_key [String] API key
  # @param api_secret [String] API secret
  def initialize(api_key, api_secret)
    # implementation
  end

  # Get account balance
  #
  # @return [Hash] Balance by currency
  # @raise [SellapayError] On API error
  def balance
    # implementation
  end
end
```

### Code Review Checklist

- [ ] Code follows style guide
- [ ] No credentials or secrets hardcoded
- [ ] No commented-out code
- [ ] Error handling implemented
- [ ] Comments explain "why" not "what"
- [ ] DRY principle followed
- [ ] No unnecessary dependencies
- [ ] Performance acceptable
- [ ] Security best practices followed
- [ ] Works with latest API version

---

## Testing

### Documentation Tests

Test all code examples work:

```bash
# Python example
python docs/examples/python/README.md

# JavaScript example
node docs/examples/javascript/README.md

# PHP example
php docs/examples/php/README.md
```

### Integration Tests

Test against live API (staging):

```python
# Test with real API
client = SellapayClient(api_key, api_secret)
balance = client.get_balance()
assert isinstance(balance, dict)
assert 'KES' in balance
```

### Link Verification

Check all links are valid:

```bash
# Check links (if available)
htmlproofer docs/
# or manual verification
```

### Spell Check

```bash
# Spell check documentation
aspell check docs/**/*.md
```

---

## Pull Request Process

### Before Submitting PR

1. **Update your branch:**

   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Run tests:**

   ```bash
   # Test code works
   # Check documentation
   # Verify formatting
   ```

3. **Commit with meaningful messages:**

   ```bash
   git commit -m "docs: Add Python retry example

   - Add exponential backoff implementation
   - Includes error handling and logging
   - Tested with real API calls"
   ```

### PR Template

```markdown
## Description

[What changes are made and why]

## Type of Change

- [ ] Bug fix
- [ ] New documentation
- [ ] New example
- [ ] Improvement
- [ ] Other

## Related Issue

Closes #[issue number]

## Checklist

- [ ] Code follows style guide
- [ ] Documentation updated
- [ ] Examples tested
- [ ] No breaking changes
- [ ] No credentials in changes
- [ ] Links verified

## Testing

[How to test these changes]

## Screenshots/Examples

[If applicable]
```

### PR Review

- Maintainers will review within 5 business days
- May request changes
- Address feedback in new commits (don't force-push)
- Once approved, it will be merged

### Merge and Cleanup

After merge:

```bash
# Delete feature branch locally
git branch -d docs/your-feature

# Delete remote branch
git push origin --delete docs/your-feature

# Update local main
git checkout main
git pull upstream main
```

---

## Documentation Organization

```
docs/
├── README.md                 # Main index
├── api-reference.md          # Endpoint documentation
├── CHANGELOG.md              # Version history
├── CONTRIBUTING.md           # This file
├── TROUBLESHOOTING.md        # Common issues
├── examples/
│   ├── python/
│   │   └── README.md         # Python guide
│   ├── javascript/
│   │   └── README.md         # JavaScript guide
│   ├── php/
│   │   └── README.md         # PHP guide
│   └── ruby/
│       └── README.md         # Ruby guide
└── setup/
    ├── README.md             # Setup overview
    ├── environment-variables.md  # Env var guide
    ├── authentication-setup.md   # Auth guide
    └── sdk-installation.md   # Installation guide
```

---

## Release Process

### Versioning

We follow [Semantic Versioning](https://semver.org/):

- MAJOR: Breaking changes
- MINOR: New features (backward compatible)
- PATCH: Bug fixes

### Release Checklist

- [ ] Update version in all files
- [ ] Update CHANGELOG.md
- [ ] Update examples with new features
- [ ] Create GitHub release
- [ ] Tag commit with version

---

## Getting Help

- **Questions?** Open a discussion on GitHub
- **Issues?** Check existing issues first
- **Security?** Email security@sellapay.africa (not public)
- **Feedback?** Create an issue with "discussion" label

---

## License

By contributing, you agree that your contributions are licensed under the same license as the project (typically MIT or Apache 2.0).

---

## Recognition

Contributors are recognized in:

- CONTRIBUTORS.md file
- GitHub contributors page
- Release notes (for significant contributions)

Thank you for contributing! 🎉

```

```
`````
