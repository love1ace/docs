# Byul AI News API v2 Documentation

## Getting Started

The Byul AI News API provides access to real-time financial news content from byul.ai. It supports both REST and WebSocket connections for various use cases.

### Base URL
```
https://api.byul.ai/api/v2
```

### Authentication
All requests require an API key with the `byul_v2_` prefix. Include the API key in your request headers:

```
X-API-Key: byul_api_key
```

## Pricing Plans

### Test (Free)
**For API testing**
- **Price**: Free
- **API Calls**: 100/month
- **Rate Limit**: 30 requests/min
- **Features**: Real-time economic news
- **WebSocket**: ❌
- **Support**: Community

### Starter ($19/month)
**For individual investors and developers**
- **Price**: $19/month
- **API Calls**: 50,000/month
- **Rate Limit**: 60 requests/min
- **Features**: Real-time economic news
- **WebSocket**: ❌
- **Support**: Email support

### Pro ($99/month) ⭐ Popular
**For small business services**
- **Price**: $99/month
- **API Calls**: Unlimited
- **Rate Limit**: 120 requests/min
- **Features**: Real-time economic news
- **WebSocket**: ✅ (10 concurrent connections)
- **Commercial Use**: Internal use license
- **Support**: Email support

### Enterprise (Custom)
**For growing businesses**
- **Price**: Custom quote
- **API Calls**: Unlimited
- **Rate Limit**: 120 requests/min
- **Features**: Real-time economic news
- **WebSocket**: ✅ (50 concurrent connections)
- **Commercial Use**: Commercial use and redistribution license
- **Support**: Email support
- **Additional**: Custom feature development available

### Plan Upgrades and Payment
- All paid plans are monthly subscriptions
- You can change plans at any time
- Credit card and bank transfer payment supported
- **Plan Purchase**: Subscribe at [Pricing Page](https://www.byul.ai/api/pricing)
- Enterprise plans: Request quotes via [Contact Us](https://www.byul.ai/contact)

## REST API

### Fetch News

Retrieve the latest financial news from byul.ai.

**Endpoint:** `GET /news`

**Query Parameters:**

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| `limit` | integer | Number of articles to return (1-100) | 10 |
| `cursor` | string | Pagination cursor for next page | - |
| `sinceId` | string | Fetch articles after this ID | - |
| `minImportance` | integer | Minimum importance level (1-10) | 1 |
| `q` | string | Search query | - |
| `symbol` | string | Stock symbol filter | - |

**Request Example:**
```bash
curl -X GET "https://api.byul.ai/api/v2/news?limit=20&minImportance=5" \
  -H "X-API-Key: byul_api_key"
```

**Response Structure:**

| Field | Type | Description |
|-------|------|-------------|
| `items` | array | Array of news articles |
| `nextCursor` | string | Cursor for next page (only when hasMore is true, can be null) |
| `hasMore` | boolean | Whether additional pages are available |

**News Article Object (each item in `items` array):**

| Field | Type | Description |
|-------|------|-------------|
| `_id` | string | Unique article ID |
| `title` | string | Article title (primary, usually English) |
| `englishTitle` | string | English title (optional, may be same as title) |
| `koTitle` | string | Korean title (optional) |
| `url` | string | Original article URL |
| `date` | string | Article date (ISO 8601 format) |
| `createdAt` | string | Creation time (ISO 8601 format) |
| `source` | string | News source name (always "byul.ai") |
| `timestamp` | number | Unix timestamp |
| `importanceScore` | number | Market importance (0-10, higher = more important) |
| `category` | string | News category |
| `symbols` | array | Related stock symbols array (optional) |
| `sentiment` | string | Sentiment analysis result ("positive", "negative", "neutral") (optional) |

**Response Example:**
```json
{
  "items": [
    {
      "_id": "67850d7b0123456789abcdef",
      "title": "Tesla Stock Surges After Q4 Earnings Beat",
      "englishTitle": "Tesla Stock Surges After Q4 Earnings Beat",
      "koTitle": "테슬라 4분기 실적 발표 후 주가 급등",
      "url": "https://www.byul.ai/news/tesla-earnings-q4-2024",
      "date": "2024-01-15T10:30:00.000Z",
      "createdAt": "2024-01-15T10:30:00.000Z",
      "source": "byul.ai",
      "timestamp": 1705401000,
      "importanceScore": 8,
      "category": "earnings",
      "symbols": ["TSLA"],
      "sentiment": "positive"
    },
    {
      "_id": "67850d7b0123456789abcde0",
      "title": "Fed Minutes Released, Signal Additional Rate Cuts",
      "englishTitle": "Fed Minutes Released, Signal Additional Rate Cuts",
      "koTitle": "연준 회의록 공개, 추가 금리 인하 시사",
      "url": "https://www.byul.ai/news/fed-fomc-minutes-january",
      "date": "2024-01-15T09:15:00.000Z",
      "createdAt": "2024-01-15T09:15:00.000Z",
      "source": "byul.ai",
      "timestamp": 1705396500,
      "importanceScore": 9,
      "category": "fed",
      "symbols": [],
      "sentiment": "neutral"
    }
  ],
  "nextCursor": "67850d7b0123456789abcde0",
  "hasMore": true
}
```

### Health Check

Check API service status.

**Endpoint:** `GET /news/health`

**Response Structure:**

| Field | Type | Description |
|-------|------|-------------|
| `status` | string | Service status ("healthy", "unhealthy") |
| `hasData` | boolean | Whether news data is available |

**Response Example:**
```json
{
  "status": "healthy",
  "hasData": true
}
```

## WebSocket API

Connect to real-time news updates (Pro/Enterprise plans only).

**Endpoint:** `wss://api.byul.ai/news-v2`

**Note:** Authentication can be provided in multiple ways as shown in the connection example.

### Connection
```javascript
import { io } from 'socket.io-client';

const socket = io('wss://api.byul.ai/news-v2', {
  auth: {
    apiKey: 'byul_api_key',
    token: 'byul_api_key'
  },
  query: {
    api_key: 'byul_api_key'
  },
  extraHeaders: {
    'X-API-Key': 'byul_api_key',
    'Authorization': 'Bearer byul_api_key'
  },
  transports: ['websocket', 'polling'],
  secure: true
});
```

### Events

#### Client → Server

**Subscribe to News:**
```javascript
socket.emit('news:subscribe', {
  limit: 20,
  minImportance: 5,
  symbol: 'AAPL',
  q: 'tesla'
});
```

**Unsubscribe:**
```javascript
socket.emit('news:unsubscribe', { type: "news" });
socket.emit('news:unsubscribe:all');
```

#### Server → Client

**Authentication Success:**
```javascript
socket.on('auth:success', (data) => {
  console.log('Successfully connected', data);
});
```

**Response Data:**
```json
{
  "message": "V2 WebSocket authentication successful",
  "user": {
    "firebase_uid": "abc123...",
    "email": "user@example.com",
    "plan": "pro"
  }
}
```

**News Data:**
```javascript
socket.on('news:data', (response) => {
  console.log('New articles:', response);
});
```

**News Data Response Structure:**

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Event type ("news") |
| `data` | object | News data object containing `news` array, `hasMore`, `nextCursor`, `total` |
| `timestamp` | string | Server transmission time |
| `isRealTimeUpdate` | boolean | Whether this is a real-time update |

**News Data Response Example:**
```json
{
  "type": "news",
  "data": {
    "news": [
      {
        "_id": "67850d7b0123456789abcde1",
        "title": "Breaking: Fed Issues Emergency Statement",
        "englishTitle": "Breaking: Fed Issues Emergency Statement",
        "koTitle": "속보: 연준 긴급성명 발표",
        "url": "https://www.byul.ai/news/fed-emergency-statement",
        "date": "2024-01-15T14:30:00.000Z",
        "createdAt": "2024-01-15T14:30:00.000Z",
        "source": "byul.ai",
        "timestamp": 1705415400,
        "importanceScore": 10,
        "category": "fed",
        "symbols": [],
        "sentiment": "neutral"
      }
    ],
    "hasMore": false,
    "nextCursor": null,
    "total": 1
  },
  "timestamp": "2024-01-15T14:30:15Z",
  "isRealTimeUpdate": true
}
```

**Subscription Confirmation:**
```javascript
socket.on('news:subscribed', (confirmation) => {
  console.log('Subscription confirmed:', confirmation);
});
```

**Subscription Confirmation Response:**
```json
{
  "type": "news",
  "params": {
    "limit": 20,
    "minImportance": 5,
    "symbol": "AAPL"
  },
  "message": "Successfully subscribed to news feed - 실시간 업데이트 활성화됨"
}
```

**Unsubscribe Confirmation:**
```javascript
socket.on('news:unsubscribed', (confirmation) => {
  console.log('Unsubscribed:', confirmation);
});
```

**Unsubscribe Confirmation Response:**
```json
{
  "type": "news",
  "message": "Successfully unsubscribed from v2 news feed"
}
```

**Errors:**
```javascript
socket.on('news:error', (error) => {
  console.error('Error:', error);
});
```

**Error Response Structure:**
```json
{
  "code": "SUBSCRIPTION_FAILED",
  "message": "Minimum importance must be between 1-10",
  "details": {
    "field": "minImportance",
    "value": 15,
    "allowedRange": "1-10"
  },
  "timestamp": "2024-01-15T10:30:00Z"
}
```

**Common WebSocket Error Codes:**

| Code | Description |
|------|-------------|
| `AUTHENTICATION_FAILED` | API key authentication failed |
| `NOT_AUTHENTICATED` | Client not authenticated, reconnection required |
| `SUBSCRIPTION_FAILED` | Subscription setup failed |
| `WEBSOCKET_NOT_SUPPORTED` | WebSocket not available on current plan |

## HTTP Status Codes

<div class="method-pill rounded-lg font-bold px-1.5 py-0.5 text-sm leading-5 bg-green-400/20 dark:bg-green-400/20 text-green-700 dark:text-green-400">200</div> Success

<div class="method-pill rounded-lg font-bold px-1.5 py-0.5 text-sm leading-5 bg-orange-400/20 dark:bg-orange-400/20 text-orange-700 dark:text-orange-400">400</div> Bad Request - Invalid parameters

<div class="method-pill rounded-lg font-bold px-1.5 py-0.5 text-sm leading-5 bg-red-400/20 dark:bg-red-400/20 text-red-700 dark:text-red-400">401</div> Unauthorized - API key required or invalid

<div class="method-pill rounded-lg font-bold px-1.5 py-0.5 text-sm leading-5 bg-yellow-400/20 dark:bg-yellow-400/20 text-yellow-700 dark:text-yellow-400">402</div> Payment Required - Plan expired

<div class="method-pill rounded-lg font-bold px-1.5 py-0.5 text-sm leading-5 bg-red-400/20 dark:bg-red-400/20 text-red-700 dark:text-red-400">403</div> Forbidden - Feature not available on current plan

<div class="method-pill rounded-lg font-bold px-1.5 py-0.5 text-sm leading-5 bg-orange-400/20 dark:bg-orange-400/20 text-orange-700 dark:text-orange-400">429</div> Too Many Requests - Rate limit exceeded

<div class="method-pill rounded-lg font-bold px-1.5 py-0.5 text-sm leading-5 bg-red-400/20 dark:bg-red-400/20 text-red-700 dark:text-red-400">500</div> Internal Server Error

## Rate Limit Headers

All responses include rate limit information:

```
X-V2-RateLimit-Limit: 120
X-V2-RateLimit-Remaining: 119
X-V2-RateLimit-Used: 1
X-V2-RateLimit-Reset: 1704628800
```

## Error Responses

All errors follow this format:

```json
{
  "statusCode": 401,
  "message": "API key is required. Please provide a valid V2 API key in the X-API-Key header or Authorization Bearer token.",
  "error": "Unauthorized",
  "timestamp": "2024-01-15T10:30:00.123Z",
  "path": "/api/v2/news"
}
```

## Quick Start Examples

```javascript
// REST API Example
const getNews = async () => {
  const response = await fetch('https://api.byul.ai/api/v2/news?limit=10', {
    headers: {
      'X-API-Key': 'byul_api_key'
    }
  });
  
  const data = await response.json();
  console.log('Latest news:', data.items);
};

// WebSocket Example (Pro/Enterprise)
const socket = io('wss://api.byul.ai/news-v2', {
  auth: { apiKey: 'byul_api_key' }
});

socket.on('connect', () => {
  socket.emit('news:subscribe', { minImportance: 7 });
});

socket.on('news:data', (response) => {
  response.data.news.forEach(article => {
    console.log(`${article.title} - Importance: ${article.importanceScore}`);
  });
});
```

## Frequently Asked Questions (FAQ)

### Q1: What is the Byul API?
The best API service that provides global economic news in real-time without delay. Get important financial market news instantly via REST API and WebSocket.

### Q2: What kind of news do you provide?
We provide all economic news that affects the market in real-time, including global economic, financial, and corporate news, central bank announcements, and economic indicators. Global news is available in both Korean and English.

### Q3: Is there any delay in the news?
Even the test plan can be used without delay. Byul aims to provide global economic news alerts as fast as possible without delay. Average response time is under 100ms.

### Q4: Is it free to use?
A free plan is available for testing purposes. You get 100 API calls per month and 30 requests per minute, with access to real-time economic news.

### Q5: What data formats do you support?
We provide data in JSON format and support both REST API and WebSocket. All timestamps are in ISO 8601 format (UTC). Please refer to the API documentation above for details.

### Q6: How do I get an API key?
You can get an API key by signing up for an account. The issued API key starts with the `byul_v2_` prefix and must be included in all requests as the `X-API-Key` header.

## Support and Status

### Service Status
Check real-time service status at [byul.ai Status Page](https://www.byul.ai/status).

### Contact Us
- For technical support or questions, please contact us via [Contact Us](https://www.byul.ai/contact)
- Enterprise plan inquiries are also available through the same link

### Important Notes
- WebSocket requires Pro or Enterprise plan
- All timestamps are in ISO 8601 format (UTC)
- Keep your API key secure and do not expose it on the client side