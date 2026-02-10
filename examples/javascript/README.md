# Sellapay API - JavaScript Integration

**Language:** JavaScript (Node.js 14+)  
**HTTP Library:** `axios` (or native `fetch`)

## Setup

### 1. Install Dependencies

Using npm:

```bash
npm install axios dotenv
```

Using yarn:

```bash
yarn add axios dotenv
```

### 2. Environment Variables

Create `.env` file:

```env
SELLAPAY_API_KEY=your_api_key_here
SELLAPAY_API_SECRET=your_api_secret_here
SELLAPAY_BASE_URL=https://pay.sellapay.africa/api/v1
```

Load environment variables:

```javascript
require("dotenv").config();
```

## Basic Usage (Axios)

### 1. Client Implementation

Create `sellapay-client.js`:

```javascript
const axios = require("axios");

class SellapayClient {
  constructor() {
    this.apiKey = process.env.SELLAPAY_API_KEY;
    this.apiSecret = process.env.SELLAPAY_API_SECRET;
    this.baseUrl =
      process.env.SELLAPAY_BASE_URL || "https://pay.sellapay.africa/api/v1";

    this.token = null;
    this.tokenExpiry = null;

    if (!this.apiKey || !this.apiSecret) {
      throw new Error("SELLAPAY_API_KEY and SELLAPAY_API_SECRET are required");
    }

    this.client = axios.create({
      baseURL: this.baseUrl,
      timeout: 10000,
    });

    // Add response interceptor for automatic token refresh
    this.client.interceptors.response.use(
      (response) => response,
      async (error) => {
        if (error.response?.status === 401 && !error.config._retry) {
          error.config._retry = true;
          await this.authorize();
          error.config.headers["Authorization"] = `Bearer ${this.token}`;
          return this.client(error.config);
        }
        return Promise.reject(error);
      },
    );
  }

  async authorize() {
    try {
      const response = await this.client.post(
        "/authorize",
        {},
        {
          headers: {
            "X-API-KEY": this.apiKey,
            "X-API-SECRET": this.apiSecret,
          },
        },
      );

      this.token = response.data.access_token;
      this.tokenExpiry = Date.now() + response.data.expires_in * 1000;

      // Set default authorization header
      this.client.defaults.headers.common["Authorization"] =
        `Bearer ${this.token}`;

      return response.data;
    } catch (error) {
      console.error(
        "Authorization failed:",
        error.response?.data || error.message,
      );
      throw error;
    }
  }

  async ensureToken() {
    if (!this.token || Date.now() >= this.tokenExpiry) {
      await this.authorize();
    }
  }

  async getBalance() {
    await this.ensureToken();
    return this.client.get("/getBalance");
  }

  async requestStkPush(amount, phone, note = "") {
    await this.ensureToken();
    return this.client.post("/requestStkPush", {
      amount,
      phone,
      note,
    });
  }

  async sendFundsToMobile(amount, phone, note = "") {
    await this.ensureToken();
    return this.client.post("/sendFundsToMobile", {
      amount,
      phone,
      note,
    });
  }

  async sendFundsToAccount(amount, toAccount, note = "") {
    await this.ensureToken();
    return this.client.post("/sendFundsToSellapay", {
      amount,
      to_account: toAccount,
      note,
    });
  }

  async sendFundsToBank(amount, swiftCode, bankAccount, note = "") {
    await this.ensureToken();
    return this.client.post("/sendFundsToLocalBank", {
      amount,
      swift_code: swiftCode,
      bank_account: bankAccount,
      note,
    });
  }
}

module.exports = SellapayClient;
```

### 2. Usage Examples

```javascript
const SellapayClient = require("./sellapay-client");

async function main() {
  const client = new SellapayClient();

  try {
    // Example 1: Get balance
    const balanceResponse = await client.getBalance();
    console.log("Balance:", balanceResponse.data);

    // Example 2: Request STK push
    const stkResponse = await client.requestStkPush(
      100, // amount
      "712345678", // phone (local format)
      "Payment for order #1001",
    );
    console.log("STK Push initiated:", stkResponse.data);

    // Example 3: Send funds to mobile
    const mobileResponse = await client.sendFundsToMobile(
      500,
      "712345678",
      "Worker payout",
    );
    console.log("Transfer queued:", mobileResponse.data);
  } catch (error) {
    console.error("Error:", error.response?.data || error.message);
  }
}

main();
```

## Browser Usage (Fetch API)

### 1. Browser Client

Create `sellapay-browser-client.js`:

```javascript
class SellapayBrowserClient {
  constructor(apiKey, apiSecret) {
    this.apiKey = apiKey;
    this.apiSecret = apiSecret;
    this.baseUrl = "https://pay.sellapay.africa/api/v1";
    this.token = null;
    this.tokenExpiry = null;
  }

  async authorize() {
    const response = await fetch(`${this.baseUrl}/authorize`, {
      method: "POST",
      headers: {
        "X-API-KEY": this.apiKey,
        "X-API-SECRET": this.apiSecret,
      },
    });

    if (!response.ok) {
      throw new Error(`Authorization failed: ${response.statusText}`);
    }

    const data = await response.json();
    this.token = data.access_token;
    this.tokenExpiry = Date.now() + data.expires_in * 1000;
    return data;
  }

  async ensureToken() {
    if (!this.token || Date.now() >= this.tokenExpiry) {
      await this.authorize();
    }
  }

  async makeRequest(action, method = "GET", body = null) {
    await this.ensureToken();

    const options = {
      method,
      headers: {
        Authorization: `Bearer ${this.token}`,
        "Content-Type": "application/x-www-form-urlencoded",
      },
    };

    if (body) {
      options.body = new URLSearchParams(body).toString();
    }

    let response = await fetch(`${this.baseUrl}/${action}`, options);

    if (response.status === 401) {
      // Refresh token and retry
      this.token = null;
      await this.ensureToken();
      options.headers["Authorization"] = `Bearer ${this.token}`;
      response = await fetch(`${this.baseUrl}/${action}`, options);
    }

    if (!response.ok) {
      throw new Error(`API error: ${response.statusText}`);
    }

    return response.json();
  }

  async getBalance() {
    return this.makeRequest("getBalance");
  }

  async requestStkPush(amount, phone, note = "") {
    return this.makeRequest("requestStkPush", "POST", {
      amount,
      phone,
      note,
    });
  }
}

// Usage
const client = new SellapayBrowserClient("YOUR_API_KEY", "YOUR_API_SECRET");
client.getBalance().then((balance) => console.log(balance));
```

### 2. HTML Integration

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Sellapay Payment</title>
  </head>
  <body>
    <button onclick="sendPayment()">Send Payment</button>

    <script src="sellapay-browser-client.js"></script>
    <script>
      const client = new SellapayBrowserClient(
        "YOUR_API_KEY",
        "YOUR_API_SECRET",
      );

      async function sendPayment() {
        try {
          const result = await client.requestStkPush(
            100,
            "712345678",
            "Payment for service",
          );
          console.log("Success:", result);
          alert("Payment request sent!");
        } catch (error) {
          console.error("Error:", error);
          alert("Payment failed: " + error.message);
        }
      }
    </script>
  </body>
</html>
```

## Error Handling & Retry Logic

```javascript
async function makeRequestWithRetry(client, fn, maxRetries = 3) {
  let lastError;

  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error;

      if (error.response?.status === 401) {
        // Token expired, don't retry manually (handled by interceptor)
        throw error;
      }

      if (error.response?.status >= 500 && i < maxRetries - 1) {
        // Server error, retry with exponential backoff
        const delay = Math.pow(2, i) * 1000;
        console.log(`Retry attempt ${i + 1}/${maxRetries} after ${delay}ms`);
        await new Promise((resolve) => setTimeout(resolve, delay));
      } else {
        throw error;
      }
    }
  }

  throw lastError;
}

// Usage
const balance = await makeRequestWithRetry(client, () => client.getBalance());
```

## Testing

```javascript
const assert = require("assert");

// Mock example with jest
jest.mock("axios");

test("authorize should set token", async () => {
  const client = new SellapayClient();

  axios.create().post.mockResolvedValue({
    data: {
      access_token: "test_token",
      expires_in: 3600,
    },
  });

  await client.authorize();
  assert.strictEqual(client.token, "test_token");
});
```

## Production Checklist

- [ ] API credentials in environment variables
- [ ] HTTPS enforced (no mixed content)
- [ ] Error handling implemented
- [ ] Token refresh on expiry
- [ ] Exponential backoff for retries
- [ ] Request timeouts configured
- [ ] Sensitive data not logged to console
- [ ] Unit tests written
- [ ] Response validation implemented

## References

- [Sellapay API Documentation](../README.md)
- [Axios Documentation](https://axios-http.com/)
- [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [Environment Variables](../../setup/environment-variables.md)
