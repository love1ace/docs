# Byul AI 뉴스 API v2 문서

## 시작하기

Byul AI 뉴스 API는 byul.ai의 실시간 금융 뉴스 콘텐츠에 액세스할 수 있게 해줍니다. 다양한 사용 사례를 위해 REST와 WebSocket 연결을 모두 지원합니다.

### 기본 URL
```
https://api.byul.ai/api/v2
```

### 인증
모든 요청에는 `byul_v2_` 접두사가 있는 API 키가 필요합니다. 요청 헤더에 API 키를 포함하세요:

```
X-API-Key: byul_v2_your_api_key_here
```

## 요금제

### Test (무료)
**API 테스트를 위한**
- **가격**: 무료
- **API 호출**: 100회/월
- **요청 제한**: 30회/분
- **기능**: 실시간 경제 뉴스
- **WebSocket**: ❌
- **지원**: 커뮤니티

### Starter ($19/월)
**개인 투자자 및 개발자를 위한**
- **가격**: $19/월
- **API 호출**: 50,000회/월
- **요청 제한**: 60회/분
- **기능**: 실시간 경제 뉴스
- **WebSocket**: ❌
- **지원**: 이메일 지원

### Pro ($99/월) ⭐ 인기
**소규모 비즈니스 서비스를 위한**
- **가격**: $99/월
- **API 호출**: 무제한
- **요청 제한**: 120회/분
- **기능**: 실시간 경제 뉴스
- **WebSocket**: ✅ (10개 동시 연결)
- **상업적 이용**: 내부 사용 권한
- **지원**: 이메일 지원

### Enterprise (맞춤형)
**성장중인 비즈니스를 위한**
- **가격**: 맞춤 견적
- **API 호출**: 무제한
- **요청 제한**: 120회/분
- **기능**: 실시간 경제 뉴스
- **WebSocket**: ✅ (50개 동시 연결)
- **상업적 이용**: 상업적 이용, 재배포 권한
- **지원**: 이메일 지원
- **추가**: 맞춤형 기능 개발 가능

### 플랜 업그레이드 및 결제
- 모든 유료 플랜은 월간 구독 방식입니다
- 언제든지 플랜 변경이 가능합니다
- 신용카드, 은행 이체 결제 지원
- **플랜 구매**: [요금제 페이지](https://www.byul.ai/api/pricing)에서 결제
- Enterprise 플랜은 [문의하기](https://www.byul.ai/contact)를 통해 견적 요청

## REST API

### 뉴스 조회

byul.ai의 최신 금융 뉴스를 조회합니다.

**엔드포인트:** `GET /news`

**쿼리 매개변수:**

| 매개변수 | 타입 | 설명 | 기본값 |
|---------|------|------|--------|
| `limit` | integer | 반환할 기사 수 (1-100) | 10 |
| `cursor` | string | 다음 페이지를 위한 페이지네이션 커서 | - |
| `sinceId` | string | 이 ID 이후의 기사 가져오기 | - |
| `minImportance` | integer | 최소 중요도 레벨 (1-10) | - |
| `q` | string | 검색 쿼리 | - |
| `symbol` | string | 주식 심볼 필터 | - |

**요청 예시:**
```bash
curl -X GET "https://api.byul.ai/api/v2/news?limit=20&minImportance=5" \
  -H "X-API-Key: byul_v2_your_api_key"
```

**응답 예시:**
```json
{
  "items": [
    {
      "id": "article_123",
      "title": "테슬라 4분기 실적 발표 후 주가 급등",
      "summary": "테슬라가 예상보다 좋은 실적을 발표했습니다...",
      "importance": 8,
      "publishedAt": "2024-01-15T10:30:00Z",
      "url": "https://www.byul.ai/news/tesla-earnings",
      "symbol": "TSLA"
    }
  ],
  "nextCursor": "eyJpZCI6ImFydGljbGVfMTIzIn0",
  "hasMore": true
}
```

### 상태 확인

API 서비스 상태를 확인합니다.

**엔드포인트:** `GET /news/health`

**응답 예시:**
```json
{
  "status": "ok",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

## WebSocket API

실시간 뉴스 업데이트에 연결합니다 (프로/엔터프라이즈 플랜만 가능).

**엔드포인트:** `wss://api.byul.ai/news-v2`

### 연결
```javascript
import { io } from 'socket.io-client';

const socket = io('wss://api.byul.ai/news-v2', {
  auth: {
    apiKey: 'byul_v2_your_api_key'
  }
});
```

### 이벤트

#### 클라이언트 → 서버

**뉴스 구독:**
```javascript
socket.emit('news:subscribe', {
  limit: 20,
  minImportance: 5,
  symbol: 'AAPL'
});
```

**구독 해제:**
```javascript
socket.emit('news:unsubscribe', { type: "news" });
socket.emit('news:unsubscribe:all');
```

#### 서버 → 클라이언트

**인증 성공:**
```javascript
socket.on('auth:success', () => {
  console.log('성공적으로 연결되었습니다');
});
```

**뉴스 데이터:**
```javascript
socket.on('news:data', (articles) => {
  console.log('새로운 기사:', articles);
});
```

**에러:**
```javascript
socket.on('news:error', (error) => {
  console.error('에러:', error.message);
});
```

## HTTP 상태 코드

| 코드 | 설명 |
|------|------|
| 200 | 성공 |
| 400 | 잘못된 요청 - 유효하지 않은 매개변수 |
| 401 | 인증되지 않음 - API 키 필요하거나 유효하지 않음 |
| 402 | 결제 필요 - 플랜 만료됨 |
| 403 | 금지됨 - 플랜에서 사용할 수 없는 기능 |
| 429 | 너무 많은 요청 - 사용량 제한 초과 |
| 500 | 내부 서버 오류 |

## 사용량 제한 헤더

모든 응답에는 사용량 제한 정보가 포함됩니다:

```
X-V2-RateLimit-Limit: 120
X-V2-RateLimit-Remaining: 119
X-V2-RateLimit-Used: 1
X-V2-RateLimit-Reset: 1704628800
```

## 오류 응답

모든 오류는 다음 형식을 따릅니다:

```json
{
  "statusCode": 401,
  "message": "API 키가 필요합니다",
  "error": "Unauthorized",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

## 빠른 시작 예제

```javascript
// REST API 예제
const getNews = async () => {
  const response = await fetch('https://api.byul.ai/api/v2/news?limit=10', {
    headers: {
      'X-API-Key': 'byul_v2_your_api_key'
    }
  });
  
  const data = await response.json();
  console.log('최신 뉴스:', data.items);
};

// WebSocket 예제 (프로/엔터프라이즈)
const socket = io('wss://api.byul.ai/news-v2', {
  auth: { apiKey: 'byul_v2_your_api_key' }
});

socket.on('connect', () => {
  socket.emit('news:subscribe', { minImportance: 7 });
});

socket.on('news:data', (articles) => {
  articles.forEach(article => {
    console.log(`${article.title} - 중요도: ${article.importance}`);
  });
});
```

## 자주 묻는 질문 (FAQ)

### Q1: Byul API는 무엇인가요?
전 세계 경제 뉴스를 지연없이 실시간으로 받을 수 있는 최고의 API 서비스입니다. REST API와 WebSocket을 통해 금융 시장에 영향을 주는 중요한 뉴스를 즉시 받아볼 수 있습니다.

### Q2: 어떤 뉴스를 제공하나요?
전 세계 경제, 금융, 기업 뉴스와 중앙은행 발표, 경제지표 등을 포함한 시장에 영향을 주는 모든 경제 뉴스를 실시간으로 제공합니다. 한국어와 영어로 번역된 글로벌 뉴스를 받아볼 수 있습니다.

### Q3: 뉴스에 지연이 있나요?
테스트 플랜도 지연없이 사용이 가능합니다. Byul은 전 세계 경제 뉴스 속보를 지연없이 최대한 빠르게 제공하는것을 목표로 운영되고 있습니다. 평균 응답 시간은 100ms 미만입니다.

### Q4: 무료로 사용할 수 있나요?
테스트를 위한 무료 플랜이 제공됩니다. 월 100회 API 호출과 분당 30회 요청이 가능하며, 실시간 경제 뉴스에 접근할 수 있습니다.

### Q5: 어떤 데이터 형식을 지원하나요?
JSON 형태로 데이터를 제공하며, REST API와 웹소켓을 지원합니다. 모든 타임스탬프는 ISO 8601 형식(UTC)으로 제공됩니다. 자세한 내용은 위의 API 문서를 참고하시기 바랍니다.

### Q6: API 키는 어떻게 받나요?
회원가입하시면 API 키를 발급받을 수 있습니다. 발급받은 API 키는 `byul_v2_` 접두사로 시작하며, 모든 요청에 `X-API-Key` 헤더로 포함해야 합니다.

## 지원 및 상태 확인

### 서비스 상태
실시간 서비스 상태를 확인하려면 [byul.ai 상태 페이지](https://www.byul.ai/status)를 방문하세요.

### 문의하기
- 기술 지원이나 질문이 있으시면 [문의하기](https://www.byul.ai/contact)를 통해 연락해 주세요
- Enterprise 플랜 문의도 같은 링크를 통해 가능합니다

### 중요 참고사항
- WebSocket은 Pro 또는 Enterprise 플랜이 필요합니다
- 모든 타임스탬프는 ISO 8601 형식(UTC)입니다
- API 키는 안전하게 보관하고 클라이언트 사이드에 노출하지 마세요