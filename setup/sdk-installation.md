# SDK Installation

Install Sellapay client libraries for your preferred programming language.

## Python (3.6+)

### Option 1: Using pip

```bash
pip install requests
```

### Option 2: pip with requirements.txt

```
requests>=2.28.0
```

Then install:

```bash
pip install -r requirements.txt
```

### Option 3: Using Poetry

```bash
poetry add requests
```

### Option 4: Anaconda/Conda

```bash
conda install -c conda-forge requests
```

**Quick Start:**

```python
import requests

url = "https://pay.sellapay.africa/api/v1/authorize"
headers = {
    "X-API-KEY": "your_api_key",
    "X-API-SECRET": "your_api_secret"
}

response = requests.post(url, headers=headers)
token = response.json()['access_token']
print(token)
```

**See Also:** [Python Integration Guide](../examples/python/README.md)

---

## JavaScript / Node.js (14+)

### Option 1: npm

```bash
npm install axios
```

### Option 2: yarn

```bash
yarn add axios
```

### Option 3: pnpm

```bash
pnpm add axios
```

### Option 4: Bun

```bash
bun add axios
```

### Option 5: No Package Manager (Browser)

```html
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

**Quick Start:**

```javascript
const axios = require("axios");

axios
  .post(
    "https://pay.sellapay.africa/api/v1/authorize",
    {},
    {
      headers: {
        "X-API-KEY": "your_api_key",
        "X-API-SECRET": "your_api_secret",
      },
    },
  )
  .then((response) => {
    console.log(response.data.access_token);
  });
```

**See Also:** [JavaScript Integration Guide](../examples/javascript/README.md)

---

## PHP (7.4+)

### Option 1: Using Composer

```bash
composer require guzzlehttp/guzzle
```

### Option 2: Composer without Guzzle (cURL)

No installation needed. Use native cURL (available in standard PHP):

```bash
# Verify cURL is installed
php -m | grep curl
```

### Option 3: Update Composer

```bash
composer update
```

**Quick Start (Guzzle):**

```php
require 'vendor/autoload.php';

$client = new GuzzleHttp\Client();
$response = $client->post('https://pay.sellapay.africa/api/v1/authorize', [
    'headers' => [
        'X-API-KEY' => 'your_api_key',
        'X-API-SECRET' => 'your_api_secret'
    ]
]);

echo $response->getBody();
```

**Quick Start (cURL):**

```php
$ch = curl_init('https://pay.sellapay.africa/api/v1/authorize');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'X-API-KEY: your_api_key',
    'X-API-SECRET: your_api_secret'
]);
curl_exec($ch);
```

**See Also:** [PHP Integration Guide](../examples/php/README.md)

---

## Ruby (2.7+)

### Option 1: Using Bundler

Add to your `Gemfile`:

```ruby
gem 'httparty'
```

Then install:

```bash
bundle install
```

### Option 2: Without Bundler (Net::HTTP)

No installation needed. Net::HTTP is built-in to Ruby.

**Quick Start (HTTParty):**

```ruby
require 'httparty'

response = HTTParty.post(
  'https://pay.sellapay.africa/api/v1/authorize',
  headers: {
    'X-API-KEY' => 'your_api_key',
    'X-API-SECRET' => 'your_api_secret'
  }
)

puts response['access_token']
```

**Quick Start (Net::HTTP):**

```ruby
require 'net/http'
require 'json'

uri = URI('https://pay.sellapay.africa/api/v1/authorize')
http = Net::HTTP.new(uri.host, uri.port)
http.use_ssl = true

request = Net::HTTP::Post.new(uri.path)
request['X-API-KEY'] = 'your_api_key'
request['X-API-SECRET'] = 'your_api_secret'

response = http.request(request)
puts JSON.parse(response.body)['access_token']
```

**See Also:** [Ruby Integration Guide](../examples/ruby/README.md)

---

## Java (11+)

### Maven

Add to `pom.xml`:

```xml
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
    <version>5.2</version>
</dependency>
```

Then build:

```bash
mvn clean install
```

### Gradle

Add to `build.gradle`:

```gradle
dependencies {
    implementation 'org.apache.httpcomponents.client5:httpclient5:5.2'
}
```

Then build:

```bash
gradle build
```

**Quick Start:**

```java
import org.apache.hc.client5.http.classic.methods.HttpPost;
import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;

CloseableHttpClient httpClient = HttpClients.createDefault();
HttpPost httpPost = new HttpPost("https://pay.sellapay.africa/api/v1/authorize");

httpPost.setHeader("X-API-KEY", "your_api_key");
httpPost.setHeader("X-API-SECRET", "your_api_secret");

httpClient.execute(httpPost, response -> {
    System.out.println(response.getCode());
    return null;
});
```

---

## Go (1.16+)

### Using Go Modules

Create `go.mod`:

```bash
go mod init myapp
```

No external dependencies needed. Use standard library `net/http`.

**Quick Start:**

```go
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    req, _ := http.NewRequest("POST",
        "https://pay.sellapay.africa/api/v1/authorize", nil)

    req.Header.Add("X-API-KEY", "your_api_key")
    req.Header.Add("X-API-SECRET", "your_api_secret")

    resp, _ := http.DefaultClient.Do(req)
    body, _ := ioutil.ReadAll(resp.Body)

    fmt.Println(string(body))
}
```

---

## C# / .NET (6.0+)

### NuGet

```bash
dotnet add package HttpClientFactory
```

Or via Package Manager:

```powershell
Install-Package System.Net.Http
```

**Quick Start:**

```csharp
using System;
using System.Net.Http;

var client = new HttpClient();
client.DefaultRequestHeaders.Add("X-API-KEY", "your_api_key");
client.DefaultRequestHeaders.Add("X-API-SECRET", "your_api_secret");

var response = await client.PostAsync(
    "https://pay.sellapay.africa/api/v1/authorize",
    new StringContent("")
);

var content = await response.Content.ReadAsStringAsync();
Console.WriteLine(content);
```

---

## Verification

### Python

```bash
python -c "import requests; print(requests.__version__)"
```

### Node.js

```bash
npm list axios
```

### PHP

```bash
# Composer
php -r "echo 'Guzzle installed!';" || composer show guzzlehttp/guzzle

# cURL
php -m | grep curl
```

### Ruby

```bash
gem list httparty
```

### Java

```bash
mvn dependency:tree | grep httpcomponents
```

### Go

```bash
go list -m all | grep http
```

### C#

```bash
dotnet list package
```

---

## Updating Dependencies

### Python

```bash
pip install --upgrade requests
```

### Node.js

```bash
npm update axios
```

### PHP

```bash
composer update
```

### Ruby

```bash
bundle update httparty
```

### Java (Maven)

```bash
mvn versions:use-latest-releases
```

### Go

```bash
go get -u all
```

### C#

```bash
dotnet package update --interactive
```

---

## Troubleshooting

### Python: "No module named 'requests'"

```bash
# Install globally
pip install requests

# Or in virtual environment
python -m venv venv
source venv/bin/activate
pip install requests
```

### Node.js: "Cannot find module 'axios'"

```bash
# Clear npm cache
npm cache clean --force

# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install
```

### PHP: "Class not found"

```bash
# Check composer.json includes the dependency
cat composer.json

# Regenerate autoloader
composer dump-autoload

# Reinstall
composer install
```

### Ruby: "Gem not installed"

```bash
# If using Bundler
bundle install

# If not using Bundler
gem install httparty
```

---

## Security Best Practices

1. **Never commit credentials** to version control
2. **Use environment variables** for API keys
3. **Keep dependencies updated** for security patches
4. **Verify HTTPS certificates** in production
5. **Use specific version pins** for stability

See [Environment Variables Setup](./environment-variables.md) for details.

---

## Next Steps

1. Install dependencies for your language
2. Obtain API credentials from dashboard
3. Follow [Authentication Setup](./authentication-setup.md)
4. Check language-specific integration guide:
   - [Python](../examples/python/README.md)
   - [JavaScript](../examples/javascript/README.md)
   - [PHP](../examples/php/README.md)
   - [Ruby](../examples/ruby/README.md)
