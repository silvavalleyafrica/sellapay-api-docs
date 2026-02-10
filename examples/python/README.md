# Sellapay API - Python Integration

**Language:** Python 3.6+  
**HTTP Library:** `requests`

## Setup

### 1. Install Dependencies

```bash
pip install requests
```

### 2. Environment Variables

Create a `.env` file in your project root:

```env
SELLAPAY_API_KEY=your_api_key_here
SELLAPAY_API_SECRET=your_api_secret_here
SELLAPAY_BASE_URL=https://pay.sellapay.africa/api/v1
```

Load environment variables:

```bash
pip install python-dotenv
```

## Basic Usage

### 1. Client Implementation

Create `sellapay_client.py`:

```python
import os
import requests
import time
from typing import Dict, Optional, Any
from dotenv import load_dotenv

load_dotenv()

class SellapayClient:
    def __init__(self):
        self.api_key = os.getenv("SELLAPAY_API_KEY")
        self.api_secret = os.getenv("SELLAPAY_API_SECRET")
        self.base_url = os.getenv("SELLAPAY_BASE_URL", "https://pay.sellapay.africa/api/v1")
        self.token = None
        self.token_expiry = None

        if not self.api_key or not self.api_secret:
            raise ValueError("API_KEY and API_SECRET must be set in environment variables")

    def authorize(self) -> Dict[str, Any]:
        """Obtain JWT access token"""
        response = requests.post(
            f"{self.base_url}/authorize",
            headers={
                "X-API-KEY": self.api_key,
                "X-API-SECRET": self.api_secret
            }
        )
        response.raise_for_status()
        data = response.json()
        self.token = data["access_token"]
        self.token_expiry = time.time() + data["expires_in"]
        return data

    def _ensure_token(self):
        """Refresh token if expired"""
        if not self.token or time.time() >= self.token_expiry:
            self.authorize()

    def _make_request(self, method: str, action: str, data: Optional[Dict] = None) -> Dict[str, Any]:
        """Make authenticated API request"""
        self._ensure_token()

        headers = {
            "Authorization": f"Bearer {self.token}"
        }

        url = f"{self.base_url}/{action}"

        if method.upper() == "GET":
            response = requests.get(url, headers=headers)
        elif method.upper() == "POST":
            response = requests.post(url, headers=headers, data=data)
        else:
            raise ValueError(f"Unsupported HTTP method: {method}")

        if response.status_code == 401:
            # Token expired, refresh and retry
            self.token = None
            self._ensure_token()
            headers["Authorization"] = f"Bearer {self.token}"
            if method.upper() == "GET":
                response = requests.get(url, headers=headers)
            else:
                response = requests.post(url, headers=headers, data=data)

        response.raise_for_status()
        return response.json()

    def get_balance(self) -> Dict[str, Any]:
        """Get account balance"""
        return self._make_request("GET", "getBalance")

    def request_stk_push(self, amount: float, phone: str, note: str = "") -> Dict[str, Any]:
        """Request M-Pesa STK push"""
        return self._make_request("POST", "requestStkPush", {
            "amount": amount,
            "phone": phone,
            "note": note
        })

    def send_funds_to_mobile(self, amount: float, phone: str, note: str = "") -> Dict[str, Any]:
        """Send funds to mobile number"""
        return self._make_request("POST", "sendFundsToMobile", {
            "amount": amount,
            "phone": phone,
            "note": note
        })

    def send_funds_to_account(self, amount: float, to_account: str, note: str = "") -> Dict[str, Any]:
        """Send funds to Sellapay account"""
        return self._make_request("POST", "sendFundsToSellapay", {
            "amount": amount,
            "to_account": to_account,
            "note": note
        })

    def send_funds_to_bank(self, amount: float, swift_code: str, bank_account: str, note: str = "") -> Dict[str, Any]:
        """Send funds to bank account"""
        return self._make_request("POST", "sendFundsToLocalBank", {
            "amount": amount,
            "swift_code": swift_code,
            "bank_account": bank_account,
            "note": note
        })
```

### 2. Usage Examples

```python
from sellapay_client import SellapayClient

# Initialize client
client = SellapayClient()

# Example 1: Get balance
try:
    balance = client.get_balance()
    print(f"Balance: {balance}")
except requests.exceptions.RequestException as e:
    print(f"Error: {e.response.json()}")

# Example 2: Request STK push
try:
    stk = client.request_stk_push(
        amount=100,
        phone="712345678",
        note="Payment for order #1001"
    )
    print(f"STK Push initiated: {stk}")
except requests.exceptions.RequestException as e:
    print(f"Error: {e.response.json()}")

# Example 3: Send funds to mobile
try:
    result = client.send_funds_to_mobile(
        amount=500,
        phone="712345678",
        note="Worker payout"
    )
    print(f"Transfer queued: {result}")
except requests.exceptions.RequestException as e:
    print(f"Error: {e.response.json()}")
```

## Error Handling

```python
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

def create_session_with_retries():
    """Create requests session with automatic retry logic"""
    session = requests.Session()
    retry = Retry(
        total=3,
        backoff_factor=0.5,
        status_forcelist=[500, 502, 503, 504]
    )
    adapter = HTTPAdapter(max_retries=retry)
    session.mount('http://', adapter)
    session.mount('https://', adapter)
    return session

# Usage in SellapayClient
session = create_session_with_retries()
response = session.get(url)
```

## Testing

```python
import unittest
from unittest.mock import patch, MagicMock

class TestSellapayClient(unittest.TestCase):
    @patch.dict(os.environ, {
        'SELLAPAY_API_KEY': 'test_key',
        'SELLAPAY_API_SECRET': 'test_secret'
    })
    def test_authorize(self):
        with patch('requests.post') as mock_post:
            mock_post.return_value.json.return_value = {
                'access_token': 'test_token',
                'expires_in': 3600
            }
            client = SellapayClient()
            result = client.authorize()
            self.assertEqual(result['access_token'], 'test_token')

if __name__ == '__main__':
    unittest.main()
```

## Production Checklist

- [ ] API credentials in environment variables (not hardcoded)
- [ ] HTTPS enforced
- [ ] Error handling and logging implemented
- [ ] Token refresh on expiry implemented
- [ ] Exponential backoff for retries
- [ ] Request timeouts configured
- [ ] API responses validated
- [ ] Sensitive data not logged
- [ ] Unit tests written and passing

## References

- [Sellapay API Documentation](../README.md)
- [Requests Library](https://requests.readthedocs.io/)
- [Environment Variables](../../setup/environment-variables.md)
