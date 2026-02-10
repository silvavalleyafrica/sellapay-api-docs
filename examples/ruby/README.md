# Sellapay API - Ruby Integration

**Language:** Ruby 2.7+  
**HTTP Library:** `httparty` or `net/http`

## Setup

### 1. Install Dependencies

Using Bundler:

```bash
gem 'httparty'
gem 'dotenv'
```

Or install directly:

```bash
gem install httparty dotenv
```

### 2. Environment Variables

Create `.env` file:

```env
SELLAPAY_API_KEY=your_api_key_here
SELLAPAY_API_SECRET=your_api_secret_here
SELLAPAY_BASE_URL=https://pay.sellapay.africa/api/v1
```

Load environment variables:

```ruby
require 'dotenv'
Dotenv.load
```

## Basic Usage (HTTParty)

### 1. Client Implementation

Create `sellapay_client.rb`:

```ruby
require 'httparty'
require 'dotenv'
require 'time'

Dotenv.load

class SellapayClient
  include HTTParty

  base_uri ENV['SELLAPAY_BASE_URL'] || 'https://pay.sellapay.africa/api/v1'
  default_timeout 10

  def initialize
    @api_key = ENV['SELLAPAY_API_KEY']
    @api_secret = ENV['SELLAPAY_API_SECRET']
    @token = nil
    @token_expiry = nil

    raise 'SELLAPAY_API_KEY and SELLAPAY_API_SECRET are required' unless @api_key && @api_secret
  end

  def authorize
    response = self.class.post(
      '/authorize',
      headers: {
        'X-API-KEY' => @api_key,
        'X-API-SECRET' => @api_secret
      }
    )

    raise "Authorization failed: #{response.body}" unless response.success?

    data = response.parsed_response
    @token = data['access_token']
    @token_expiry = Time.now + data['expires_in']

    data
  end

  def ensure_token
    if @token.nil? || Time.now >= @token_expiry
      authorize
    end
  end

  def make_request(action, method = :get, data = nil)
    ensure_token

    headers = {
      'Authorization' => "Bearer #{@token}"
    }

    options = {
      headers: headers
    }

    options[:body] = data if data && method == :post

    response = self.class.send(method, "/#{action}", options)

    if response.code == 401
      # Token expired, refresh and retry
      @token = nil
      ensure_token
      headers['Authorization'] = "Bearer #{@token}"
      options[:headers] = headers
      response = self.class.send(method, "/#{action}", options)
    end

    raise "API Error (#{response.code}): #{response.body}" unless response.success?

    response.parsed_response
  end

  def get_balance
    make_request('getBalance')
  end

  def request_stk_push(amount, phone, note = '')
    make_request('requestStkPush', :post, {
      amount: amount,
      phone: phone,
      note: note
    })
  end

  def send_funds_to_mobile(amount, phone, note = '')
    make_request('sendFundsToMobile', :post, {
      amount: amount,
      phone: phone,
      note: note
    })
  end

  def send_funds_to_account(amount, to_account, note = '')
    make_request('sendFundsToSellapay', :post, {
      amount: amount,
      to_account: to_account,
      note: note
    })
  end

  def send_funds_to_bank(amount, swift_code, bank_account, note = '')
    make_request('sendFundsToLocalBank', :post, {
      amount: amount,
      swift_code: swift_code,
      bank_account: bank_account,
      note: note
    })
  end
end
```

### 2. Usage Examples

```ruby
require_relative 'sellapay_client'

begin
  client = SellapayClient.new

  # Example 1: Get balance
  balance = client.get_balance
  puts "Balance: #{balance.inspect}"

  # Example 2: Request STK push
  stk = client.request_stk_push(
    100,
    '712345678',
    'Payment for order #1001'
  )
  puts "STK initiated: #{stk.inspect}"

  # Example 3: Send funds to mobile
  result = client.send_funds_to_mobile(
    500,
    '712345678',
    'Worker payout'
  )
  puts "Transfer queued: #{result.inspect}"

rescue StandardError => e
  puts "Error: #{e.message}"
end
```

## Using Net::HTTP

### 1. Native HTTP Client

Create `sellapay_net_client.rb`:

```ruby
require 'net/http'
require 'uri'
require 'json'

class SellapayNetClient
  def initialize
    @api_key = ENV['SELLAPAY_API_KEY']
    @api_secret = ENV['SELLAPAY_API_SECRET']
    @base_url = ENV['SELLAPAY_BASE_URL'] || 'https://pay.sellapay.africa/api/v1'
    @token = nil
    @token_expiry = nil
  end

  def authorize
    uri = URI("#{@base_url}/authorize")

    request = Net::HTTP::Post.new(uri)
    request['X-API-KEY'] = @api_key
    request['X-API-SECRET'] = @api_secret

    http = Net::HTTP.new(uri.host, uri.port)
    http.use_ssl = uri.scheme == 'https'
    http.verify_mode = OpenSSL::SSL::VERIFY_PEER

    response = http.request(request)
    raise "Authorization failed: #{response.body}" unless response.is_a?(Net::HTTPSuccess)

    data = JSON.parse(response.body)
    @token = data['access_token']
    @token_expiry = Time.now + data['expires_in']

    data
  end

  def ensure_token
    if @token.nil? || Time.now >= @token_expiry
      authorize
    end
  end

  def make_request(action, method = :get, data = nil)
    ensure_token

    uri = URI("#{@base_url}/#{action}")

    if method == :get
      request = Net::HTTP::Get.new(uri)
    else
      request = Net::HTTP::Post.new(uri)
      request.body = URI.encode_www_form(data) if data
    end

    request['Authorization'] = "Bearer #{@token}"
    request['Content-Type'] = 'application/x-www-form-urlencoded'

    http = Net::HTTP.new(uri.host, uri.port)
    http.use_ssl = uri.scheme == 'https'
    http.verify_mode = OpenSSL::SSL::VERIFY_PEER
    http.read_timeout = 10

    response = http.request(request)

    if response.code == '401'
      # Token expired, retry
      @token = nil
      ensure_token
      request['Authorization'] = "Bearer #{@token}"
      response = http.request(request)
    end

    raise "API Error (#{response.code}): #{response.body}" unless response.is_a?(Net::HTTPSuccess)

    JSON.parse(response.body)
  end

  def get_balance
    make_request('getBalance')
  end

  def request_stk_push(amount, phone, note = '')
    make_request('requestStkPush', :post, {
      amount: amount,
      phone: phone,
      note: note
    })
  end
end
```

## Error Handling & Retry

```ruby
require 'net/http'

class RetryableSellapayClient < SellapayClient
  MAX_RETRIES = 3

  def make_request_with_retry(action, method = :get, data = nil)
    retries = 0

    begin
      make_request(action, method, data)
    rescue StandardError => e
      if retries < MAX_RETRIES && (e.message.include?('500') || e.message.include?('502'))
        retries += 1
        delay = 2 ** retries  # Exponential backoff
        puts "Retry #{retries}/#{MAX_RETRIES} after #{delay}s..."
        sleep(delay)
        retry
      else
        raise e
      end
    end
  end
end
```

## Rails Integration

### 1. Create Service Class

```ruby
# app/services/sellapay_service.rb

class SellapayService
  def initialize
    @client = SellapayClient.new
  end

  def initiate_payment(amount, phone, description)
    begin
      @client.request_stk_push(amount, phone, description)
    rescue StandardError => e
      Rails.logger.error("Sellapay payment error: #{e.message}")
      raise e
    end
  end

  def get_balance
    @client.get_balance
  end
end
```

### 2. Use in Controller

```ruby
# app/controllers/payments_controller.rb

class PaymentsController < ApplicationController
  def create
    service = SellapayService.new

    begin
      result = service.initiate_payment(
        params[:amount],
        params[:phone],
        params[:description]
      )
      render json: result, status: :ok
    rescue StandardError => e
      render json: { error: e.message }, status: :unprocessable_entity
    end
  end
end
```

## Testing

```ruby
require 'minitest/autorun'
require_relative 'sellapay_client'

class SellapayClientTest < Minitest::Test
  def setup
    @client = SellapayClient.new
  end

  def test_authorize
    result = @client.authorize

    assert result.is_a?(Hash)
    assert result.key?('access_token')
    assert result.key?('expires_in')
  end

  def test_get_balance
    @client.authorize
    result = @client.get_balance

    assert result.is_a?(Hash)
    assert result.key?('balance')
  end
end
```

## Production Checklist

- [ ] API credentials in environment variables
- [ ] HTTPS SSL verification enabled
- [ ] Error handling and logging implemented
- [ ] Token refresh on expiry working
- [ ] Request timeouts configured
- [ ] Exponential backoff implemented
- [ ] Sensitive data not logged
- [ ] Unit tests passing
- [ ] Database serialization if caching tokens

## References

- [Sellapay API Documentation](../README.md)
- [HTTParty Documentation](https://github.com/jnunemaker/httparty)
- [Net::HTTP Documentation](https://ruby-doc.org/stdlib/libdoc/net/http/rdoc/Net/HTTP.html)
- [Environment Variables](../../setup/environment-variables.md)
