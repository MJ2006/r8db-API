# R8DB - Movie & TV Show Database API

[![Website](https://img.shields.io/badge/Website-r8db.co.za-blue)](https://r8db.co.za)
[![API Version](https://img.shields.io/badge/API-v1.0-green)](https://r8db.co.za/api)
[![License](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)

> A comprehensive REST API for accessing movie and TV show data, including actor information, ratings, and gallery content.

## 🌟 Features

- **Comprehensive Movie Database**: Access detailed information about movies, TV shows, and actors
- **Advanced Search**: Search across movies, TV shows, and actors with filtering options
- **Rating System**: Get ratings and statistics for content
- **Gallery Support**: Access additional images and media for movies and TV shows
- **Multi-tier Access**: Free, Pro, and Pro Plus subscription plans
- **Rate Limiting**: Built-in rate limiting with plan-based limits
- **Request Signing**: Enhanced security with optional request signing
- **Real-time Analytics**: Usage tracking and analytics for API consumers

## 🚀 Quick Start

### Base URL
```
https://r8db.co.za/api/v1
```

### Authentication

#### Basic Authentication (API Key)
```bash
curl -X GET "https://r8db.co.za/api/v1/movies" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

#### Enhanced Security (Request Signing - Pro/Pro Plus)
```bash
curl -X GET "https://r8db.co.za/api/v1/movies" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "X-API-Timestamp: $(date +%s)" \
  -H "X-API-Signature: YOUR_GENERATED_SIGNATURE"
```

## 📋 Subscription Plans

| Plan | Daily Limit | Monthly Limit | Features |
|------|-------------|---------------|----------|
| **Free** | 1,000 calls | 30,000 calls | Basic access, standard endpoints |
| **Pro** | 50,000 calls | Unlimited | IP whitelisting, request signing, advanced analytics |
| **Pro Plus** | Unlimited | Unlimited | All Pro features + priority support, unlimited calls |

## 📚 API Endpoints

### Movies

#### Get Movies List
```http
GET /movies
```
**Parameters:**
- `page` (optional): Page number (default: 1)
- `per_page` (optional): Items per page (default: 20, max: 100)
- `q` or `query` (optional): Search query
- `genre` (optional): Filter by genre
- `year` (optional): Filter by release year
- `platform` (optional): Filter by platform
- `sort` (optional): Sort field (title, rating, release_date, created_at)
- `order` (optional): Sort order (asc, desc)

**Example:**
```bash
curl "https://r8db.co.za/api/v1/movies?page=1&per_page=10&genre=Action&year=2023" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

#### Get Movie Details
```http
GET /movies/{id}
```

#### Get Movie Gallery
```http
GET /movies/{id}/gallery
```

### TV Shows

#### Get TV Shows List
```http
GET /tvshows
```

#### Get TV Show Details
```http
GET /tvshows/{id}
```

#### Get TV Show Gallery
```http
GET /tvshows/{id}/gallery
```

### Actors

#### Get Actors List
```http
GET /actors
```
**Parameters:**
- `page` (optional): Page number (default: 1)
- `per_page` (optional): Items per page (default: 20, max: 100)
- `q` or `query` (optional): Search query
- `nationality` (optional): Filter by nationality
- `sort` (optional): Sort field (name, rating)
- `order` (optional): Sort order (asc, desc)

#### Get Actor Details
```http
GET /actors/{id}
```

#### Get Actor's Movies
```http
GET /actors/{id}/movies
```

#### Get Actor's TV Shows
```http
GET /actors/{id}/tvshows
```

### Search

#### Unified Search
```http
GET /search
```
**Parameters:**
- `q` or `query` (required): Search query
- `type` (optional): Filter by type (movie, show, actor)
- `genre` (optional): Filter by genre
- `year` (optional): Filter by year
- `platform` (optional): Filter by platform
- `nationality` (optional): Filter by nationality (for actors)

**Example:**
```bash
curl "https://r8db.co.za/api/v1/search?q=action&type=movie&year=2023" \
  -H "Authorization: Bearer pk_your_api_key_here"
```

**Example with Request Signing (Pro/Pro Plus):**
```bash
# Generate timestamp and signature (you would do this in your application)
TIMESTAMP=$(date +%s)
SIGNATURE=$(echo -n "pk_your_api_key_here${TIMESTAMP}GET/search" | openssl dgst -sha256 -hmac $(echo "your_api_secret_here" | base64 -d) | cut -d' ' -f2)

curl "https://r8db.co.za/api/v1/search?q=action&type=movie&year=2023" \
  -H "Authorization: Bearer pk_your_api_key_here" \
  -H "X-API-Timestamp: ${TIMESTAMP}" \
  -H "X-API-Signature: ${SIGNATURE}"
```

### Additional Endpoints

#### Get Genres
```http
GET /genres
```

#### Get Countries
```http
GET /countries
```

#### Get Languages
```http
GET /languages
```

#### Get Ratings Statistics
```http
GET /ratings
```

## 🔐 Authentication & Security

### API Key Management

1. **Sign Up**: Visit [r8db.co.za](https://r8db.co.za) and create an account
2. **Subscribe**: Choose a plan (Free, Pro, or Pro Plus)
3. **Get API Key**: Generate your API key from the dashboard
4. **Optional**: Generate API secret for request signing (Pro/Pro Plus only)

### Request Signing (Pro/Pro Plus)

For enhanced security, you can sign your requests:

```javascript
// JavaScript example for generating signature
const crypto = require('crypto');

function generateSignature(apiKey, timestamp, method, endpoint, body, secret) {
  const payload = apiKey + timestamp + method.toUpperCase() + endpoint + body;
  return crypto.createHmac('sha256', Buffer.from(secret, 'base64'))
               .update(payload)
               .digest('hex');
}

// Usage
const timestamp = Math.floor(Date.now() / 1000); // Unix timestamp in seconds
const signature = generateSignature(
  'pk_your_api_key_here',
  timestamp,
  'GET',
  '/movies',
  '',
  'your_api_secret_here'
);

// Example API call with signature
const axios = require('axios');

async function makeSignedRequest() {
  const response = await axios.get('https://r8db.co.za/api/v1/movies', {
    headers: {
      'Authorization': `Bearer pk_your_api_key_here`,
      'X-API-Timestamp': timestamp,
      'X-API-Signature': signature,
      'Content-Type': 'application/json'
    }
  });

  return response.data;
}
```

### Rate Limiting

Rate limits are enforced based on your subscription plan:

- **Headers included in responses:**
  - `X-RateLimit-Limit`: Your daily limit
  - `X-RateLimit-Remaining`: Remaining calls today
  - `X-RateLimit-Reset`: Time when limits reset (Unix timestamp)

## 📊 Response Format

All API responses follow this standard format:

```json
{
  "success": true,
  "message": "Success",
  "timestamp": "2025-01-20T10:30:00+00:00",
  "api_version": "v1",
  "data": {
    "movies": [
      {
        "id": 1,
        "title": "Movie Title",
        "type": "movie",
        "slug": "movie-title",
        "thumbnail": "https://r8db.co.za/assets/images/uploads/movie.jpg",
        "trailer_url": "https://youtube.com/watch?v=...",
        "release_date": "2023-01-15",
        "genres": ["Action", "Drama"],
        "platform": "Netflix",
        "synopsis": "Movie description...",
        "rating": 4.5,
        "cast": [
          {
            "id": 1,
            "name": "Actor Name",
            "slug": "actor-name",
            "photo": "https://r8db.co.za/assets/images/uploads/actor.jpg",
            "biography": "Actor biography...",
            "date_of_birth": "1990-01-01",
            "nationality": "South Africa",
            "rating": 4.2
          }
        ],
        "director": "Director Name",
        "writer": "Writer Name",
        "duration": 120,
        "airing": false,
        "status": "ended",
        "featured": true,
        "created_at": "2023-01-01T00:00:00+00:00"
      }
    ],
    "pagination": {
      "current_page": 1,
      "per_page": 20,
      "total": 100,
      "total_pages": 5,
      "has_next": true,
      "has_prev": false,
      "next_page": 2,
      "prev_page": null,
      "first_page_url": "https://r8db.co.za/api/v1/movies?page=1",
      "last_page_url": "https://r8db.co.za/api/v1/movies?page=5",
      "next_page_url": "https://r8db.co.za/api/v1/movies?page=2",
      "prev_page_url": null
    }
  }
}
```

## 🚨 Error Responses

```json
{
  "success": false,
  "message": "Error description",
  "timestamp": "2025-01-20T10:30:00+00:00",
  "api_version": "v1",
  "error_code": "ERROR_CODE",
  "documentation_url": "https://r8db.co.za/api/documentation"
}
```

### Common HTTP Status Codes

- `200` - Success
- `400` - Bad Request
- `401` - Unauthorized (invalid API key)
- `403` - Forbidden (subscription inactive/expired)
- `404` - Not Found
- `429` - Rate Limit Exceeded
- `500` - Internal Server Error

## 💻 Code Examples

### JavaScript (Node.js)
```javascript
const axios = require('axios');

const API_KEY = 'your_api_key_here';
const BASE_URL = 'https://r8db.co.za/api/v1';

async function getMovies() {
  try {
    const response = await axios.get(`${BASE_URL}/movies`, {
      headers: {
        'Authorization': `Bearer ${API_KEY}`,
        'Content-Type': 'application/json'
      }
    });

    console.log(response.data);
  } catch (error) {
    console.error('Error:', error.response.data);
  }
}
```

### Python
```python
import requests
import time
import hmac
import hashlib
import base64

API_KEY = 'pk_your_api_key_here'
API_SECRET = 'your_api_secret_here'  # Only for Pro/Pro Plus
BASE_URL = 'https://r8db.co.za/api/v1'

# Basic request (Free/Pro)
def get_movies_basic():
    headers = {
        'Authorization': f'Bearer {API_KEY}',
        'Content-Type': 'application/json'
    }

    response = requests.get(f'{BASE_URL}/movies', headers=headers)

    if response.status_code == 200:
        data = response.json()
        return data
    else:
        print(f'Error: {response.status_code}')
        print(response.json())
        return None

# Signed request (Pro/Pro Plus only)
def generate_signature(api_key, timestamp, method, endpoint, body, secret):
    payload = f"{api_key}{timestamp}{method.upper()}{endpoint}{body}"
    signature = hmac.new(
        base64.b64decode(secret),
        payload.encode('utf-8'),
        hashlib.sha256
    ).hexdigest()
    return signature

def get_movies_signed():
    timestamp = str(int(time.time()))

    signature = generate_signature(
        API_KEY,
        timestamp,
        'GET',
        '/movies',
        '',
        API_SECRET
    )

    headers = {
        'Authorization': f'Bearer {API_KEY}',
        'X-API-Timestamp': timestamp,
        'X-API-Signature': signature,
        'Content-Type': 'application/json'
    }

    response = requests.get(f'{BASE_URL}/movies', headers=headers)

    if response.status_code == 200:
        data = response.json()
        return data
    else:
        print(f'Error: {response.status_code}')
        print(response.json())
        return None

# Usage
movies = get_movies_basic()  # For basic authentication
# movies = get_movies_signed()  # For signed requests (Pro/Pro Plus)
```

### PHP
```php
<?php

$apiKey = 'pk_your_api_key_here';
$apiSecret = 'your_api_secret_here'; // Only for Pro/Pro Plus
$baseUrl = 'https://r8db.co.za/api/v1';

// Function to generate signature (Pro/Pro Plus only)
function generateSignature($apiKey, $timestamp, $method, $endpoint, $body, $secret) {
    $payload = $apiKey . $timestamp . strtoupper($method) . $endpoint . $body;
    return hash_hmac('sha256', $payload, base64_decode($secret));
}

// Basic request (Free/Pro)
function getMoviesBasic($apiKey, $baseUrl) {
    $ch = curl_init();

    curl_setopt_array($ch, [
        CURLOPT_URL => $baseUrl . '/movies',
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_HTTPHEADER => [
            'Authorization: Bearer ' . $apiKey,
            'Content-Type: application/json'
        ]
    ]);

    $response = curl_exec($ch);
    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);

    curl_close($ch);

    if ($httpCode === 200) {
        $data = json_decode($response, true);
        return $data;
    } else {
        echo "Error: $httpCode\n";
        echo $response;
        return null;
    }
}

// Signed request (Pro/Pro Plus only)
function getMoviesSigned($apiKey, $apiSecret, $baseUrl) {
    $timestamp = time(); // Unix timestamp

    $signature = generateSignature(
        $apiKey,
        $timestamp,
        'GET',
        '/movies',
        '',
        $apiSecret
    );

    $ch = curl_init();

    curl_setopt_array($ch, [
        CURLOPT_URL => $baseUrl . '/movies',
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_HTTPHEADER => [
            'Authorization: Bearer ' . $apiKey,
            'X-API-Timestamp: ' . $timestamp,
            'X-API-Signature: ' . $signature,
            'Content-Type: application/json'
        ]
    ]);

    $response = curl_exec($ch);
    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);

    curl_close($ch);

    if ($httpCode === 200) {
        $data = json_decode($response, true);
        return $data;
    } else {
        echo "Error: $httpCode\n";
        echo $response;
        return null;
    }
}

// Usage
$movies = getMoviesBasic($apiKey, $baseUrl); // For basic authentication
// $movies = getMoviesSigned($apiKey, $apiSecret, $baseUrl); // For signed requests (Pro/Pro Plus)

if ($movies) {
    print_r($movies);
}
```

## 🛠️ Development & Testing

### API Explorer
Test the API endpoints live using our interactive API Explorer:
- **URL**: [https://r8db.co.za/api/documentation/explorer](https://r8db.co.za/api/documentation/explorer)
- **Features**: Live endpoint testing, request/response examples, code snippets

### API Documentation
Complete API documentation is available at:
- **URL**: [https://r8db.co.za/api/documentation](https://r8db.co.za/api/documentation)

### Testing Endpoints
- **Movies**: `GET /movies?page=1&per_page=5`
- **TV Shows**: `GET /tvshows?page=1&per_page=5`
- **Actors**: `GET /actors?page=1&per_page=5`
- **Search**: `GET /search?q=batman`

## 📞 Support

### Getting Help
- **Documentation**: [https://r8db.co.za/api/documentation](https://r8db.co.za/api/documentation)
- **API Explorer**: [https://r8db.co.za/api/documentation/explorer](https://r8db.co.za/api/documentation/explorer)
- **Dashboard**: [https://r8db.co.za/api/dashboard](https://r8db.co.za/api/dashboard) (for API key management)

### Rate Limits & Issues
- Check response headers for rate limit information
- Monitor your usage in the API dashboard
- Contact support for plan upgrades or technical issues

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Built with PHP and MySQL
- RESTful API design
- Comprehensive security implementation
- Multi-tier subscription system

---

**Website**: [https://r8db.co.za](https://r8db.co.za)  
**API Base URL**: [https://r8db.co.za/api/v1](https://r8db.co.za/api/v1)  
**Documentation**: [https://r8db.co.za/api/documentation](https://r8db.co.za/api/documentation)
