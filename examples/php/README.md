# Sellapay API - PHP Integration

**Language:** PHP 7.4+  
**HTTP Library:** cURL (built-in) or Guzzle HTTP Client

## Setup

### 1. Install Dependencies (Optional - Guzzle)

```bash
composer require guzzlehttp/guzzle
```

### 2. Environment Variables

Create `.env` file:

```env
SELLAPAY_API_KEY=your_api_key_here
SELLAPAY_API_SECRET=your_api_secret_here
SELLAPAY_BASE_URL=https://pay.sellapay.africa/api/v1
```

Load environment variables:

```php
require_once __DIR__ . '/vendor/autoload.php';
$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
$dotenv->load();
```

## Basic Usage (cURL)

### 1. Client Implementation

Create `SellapayClient.php`:

```php
<?php

class SellapayClient
{
    private $apiKey;
    private $apiSecret;
    private $baseUrl;
    private $token;
    private $tokenExpiry;

    public function __construct()
    {
        $this->apiKey = getenv('SELLAPAY_API_KEY');
        $this->apiSecret = getenv('SELLAPAY_API_SECRET');
        $this->baseUrl = getenv('SELLAPAY_BASE_URL') ?: 'https://pay.sellapay.africa/api/v1';

        if (!$this->apiKey || !$this->apiSecret) {
            throw new Exception('SELLAPAY_API_KEY and SELLAPAY_API_SECRET are required');
        }
    }

    public function authorize()
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, "{$this->baseUrl}/authorize");
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, [
            'X-API-KEY: ' . $this->apiKey,
            'X-API-SECRET: ' . $this->apiSecret,
        ]);
        curl_setopt($ch, CURLOPT_TIMEOUT, 10);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);

        $response = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        $error = curl_error($ch);
        curl_close($ch);

        if ($error) {
            throw new Exception("cURL Error: $error");
        }

        $data = json_decode($response, true);

        if ($httpCode !== 200) {
            throw new Exception("Authorization failed: " . json_encode($data));
        }

        $this->token = $data['access_token'];
        $this->tokenExpiry = time() + $data['expires_in'];

        return $data;
    }

    private function ensureToken()
    {
        if (!$this->token || time() >= $this->tokenExpiry) {
            $this->authorize();
        }
    }

    private function makeRequest($action, $method = 'GET', $data = null)
    {
        $this->ensureToken();

        $ch = curl_init();
        $url = "{$this->baseUrl}/{$action}";

        if ($method === 'GET' && $data) {
            $url .= '?' . http_build_query($data);
        }

        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, [
            'Authorization: Bearer ' . $this->token,
            'Content-Type: application/x-www-form-urlencoded',
        ]);
        curl_setopt($ch, CURLOPT_TIMEOUT, 10);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);

        if ($method === 'POST') {
            curl_setopt($ch, CURLOPT_POST, 1);
            if ($data) {
                curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($data));
            }
        }

        $response = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        $error = curl_error($ch);
        curl_close($ch);

        if ($error) {
            throw new Exception("cURL Error: $error");
        }

        $decoded = json_decode($response, true);

        if ($httpCode === 401) {
            // Token expired, refresh and retry
            $this->token = null;
            $this->ensureToken();
            return $this->makeRequest($action, $method, $data);
        }

        if ($httpCode >= 400) {
            throw new Exception("API Error ($httpCode): " . json_encode($decoded));
        }

        return $decoded;
    }

    public function getBalance()
    {
        return $this->makeRequest('getBalance', 'GET');
    }

    public function requestStkPush($amount, $phone, $note = '')
    {
        return $this->makeRequest('requestStkPush', 'POST', [
            'amount' => $amount,
            'phone' => $phone,
            'note' => $note
        ]);
    }

    public function sendFundsToMobile($amount, $phone, $note = '')
    {
        return $this->makeRequest('sendFundsToMobile', 'POST', [
            'amount' => $amount,
            'phone' => $phone,
            'note' => $note
        ]);
    }

    public function sendFundsToAccount($amount, $toAccount, $note = '')
    {
        return $this->makeRequest('sendFundsToSellapay', 'POST', [
            'amount' => $amount,
            'to_account' => $toAccount,
            'note' => $note
        ]);
    }

    public function sendFundsToBank($amount, $swiftCode, $bankAccount, $note = '')
    {
        return $this->makeRequest('sendFundsToLocalBank', 'POST', [
            'amount' => $amount,
            'swift_code' => $swiftCode,
            'bank_account' => $bankAccount,
            'note' => $note
        ]);
    }
}
?>
```

### 2. Usage Examples

```php
<?php

require_once 'SellapayClient.php';

try {
    $client = new SellapayClient();

    // Example 1: Get balance
    $balance = $client->getBalance();
    echo "Balance: " . json_encode($balance) . "\n";

    // Example 2: Request STK push
    $stk = $client->requestStkPush(
        100,
        '712345678',
        'Payment for order #1001'
    );
    echo "STK initiated: " . json_encode($stk) . "\n";

    // Example 3: Send funds to mobile
    $result = $client->sendFundsToMobile(
        500,
        '712345678',
        'Worker payout'
    );
    echo "Transfer queued: " . json_encode($result) . "\n";

} catch (Exception $e) {
    echo "Error: " . $e->getMessage() . "\n";
}
?>
```

## Using Guzzle HTTP Client

### 1. Guzzle Client Implementation

```php
<?php

use GuzzleHttp\Client;
use GuzzleHttp\Exception\RequestException;

class SellapayGuzzleClient
{
    private $client;
    private $apiKey;
    private $apiSecret;
    private $token;
    private $tokenExpiry;

    public function __construct()
    {
        $this->apiKey = getenv('SELLAPAY_API_KEY');
        $this->apiSecret = getenv('SELLAPAY_API_SECRET');

        if (!$this->apiKey || !$this->apiSecret) {
            throw new Exception('API credentials required');
        }

        $this->client = new Client([
            'base_uri' => getenv('SELLAPAY_BASE_URL') ?: 'https://pay.sellapay.africa/api/v1',
            'timeout' => 10,
            'verify' => true,
        ]);
    }

    public function authorize()
    {
        try {
            $response = $this->client->post('/authorize', [
                'headers' => [
                    'X-API-KEY' => $this->apiKey,
                    'X-API-SECRET' => $this->apiSecret,
                ]
            ]);

            $data = json_decode($response->getBody(), true);
            $this->token = $data['access_token'];
            $this->tokenExpiry = time() + $data['expires_in'];

            return $data;
        } catch (RequestException $e) {
            throw new Exception("Authorization failed: " . $e->getMessage());
        }
    }

    private function ensureToken()
    {
        if (!$this->token || time() >= $this->tokenExpiry) {
            $this->authorize();
        }
    }

    private function makeRequest($action, $method = 'GET', $data = null)
    {
        $this->ensureToken();

        $options = [
            'headers' => [
                'Authorization' => 'Bearer ' . $this->token,
            ]
        ];

        if ($data && $method === 'POST') {
            $options['form_params'] = $data;
        }

        try {
            $response = $this->client->request($method, "/$action", $options);
            return json_decode($response->getBody(), true);
        } catch (RequestException $e) {
            if ($e->getResponse()->getStatusCode() === 401) {
                $this->token = null;
                $this->ensureToken();
                return $this->makeRequest($action, $method, $data);
            }
            throw new Exception("API Error: " . $e->getMessage());
        }
    }

    public function getBalance()
    {
        return $this->makeRequest('getBalance');
    }

    public function requestStkPush($amount, $phone, $note = '')
    {
        return $this->makeRequest('requestStkPush', 'POST', compact('amount', 'phone', 'note'));
    }
}
?>
```

## Error Handling

```php
<?php

function handleApiError($exception)
{
    $message = $exception->getMessage();

    if (strpos($message, '401') !== false) {
        // Token expired
        error_log('Token expired, need re-authorization');
    } elseif (strpos($message, '400') !== false) {
        // Bad request
        error_log('Invalid request parameters');
    } elseif (strpos($message, '5') !== false) {
        // Server error - try retry with exponential backoff
        error_log('Server error, retrying...');
    }

    return [
        'status' => 'error',
        'message' => $message
    ];
}
?>
```

## Testing

```php
<?php

class SellapayClientTest extends PHPUnit_Framework_TestCase
{
    public function testAuthorize()
    {
        $client = new SellapayClient();
        $result = $client->authorize();

        $this->assertIsArray($result);
        $this->assertArrayHasKey('access_token', $result);
        $this->assertArrayHasKey('expires_in', $result);
    }

    public function testGetBalance()
    {
        $client = new SellapayClient();
        $result = $client->getBalance();

        $this->assertIsArray($result);
        $this->assertArrayHasKey('balance', $result);
    }
}
?>
```

## Production Checklist

- [ ] API credentials in environment variables
- [ ] SSL/TLS certificate validation enabled
- [ ] Error handling and logging implemented
- [ ] Token refresh on expiry working
- [ ] Timeout configured appropriately
- [ ] Request/response validation implemented
- [ ] Sensitive data not logged
- [ ] Unit tests passing
- [ ] Array injection protection (use prepared statements for DB)

## References

- [Sellapay API Documentation](../README.md)
- [PHP cURL Documentation](https://www.php.net/manual/en/book.curl.php)
- [Guzzle Documentation](https://docs.guzzlephp.org/)
- [Environment Variables](../../setup/environment-variables.md)
