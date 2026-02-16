# 🔌 API Documentation

**Building Materials Price Intelligence Platform**  
**RESTful API Reference**

---

## 📋 Overview

The Building Materials Price Intelligence Platform API provides programmatic access to building material prices, forecasts, supplier information, and analytics. The API follows RESTful principles, uses JSON for data exchange, and implements JWT-based authentication.

**Base URL (Production):** `https://api.buildmaterials.app`  
**Base URL (Staging):** `https://staging-api.buildmaterials.app`  
**Base URL (Development):** `http://localhost:3000`

**API Version:** v1  
**Protocol:** HTTPS (TLS 1.2+)  
**Data Format:** JSON

---

## 🔐 Authentication

### JWT-Based Authentication

The API uses JSON Web Tokens (JWT) for authentication. Include the JWT token in the `Authorization` header of each request.

#### Obtaining a Token

**Register a new user:**
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "contractor@example.com",
  "password": "SecurePass123!",
  "name": "John Contractor",
  "role": "contractor"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "contractor@example.com",
      "name": "John Contractor",
      "role": "contractor"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

**Login:**
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "contractor@example.com",
  "password": "SecurePass123!"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "contractor@example.com",
      "role": "contractor"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": "24h"
  }
}
```

#### Using the Token

Include the JWT token in the `Authorization` header:

```http
GET /api/materials
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

#### Token Expiry

- **Lifetime:** 24 hours
- **Refresh:** Use the refresh token endpoint before expiry
- **Revocation:** Tokens can be revoked via logout

#### Logout

```http
POST /api/auth/logout
Authorization: Bearer <your-token>
```

**Response:**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

---

## 🚦 Rate Limiting

Rate limiting is implemented using Redis to prevent abuse and ensure fair resource allocation.

### Rate Limit Tiers

| Endpoint Type | Authenticated | Unauthenticated | Window | Consequence |
|--------------|---------------|-----------------|---------|-------------|
| **Authentication** | N/A | 5 attempts | 15 minutes | Account lockout |
| **Standard API** | 100 requests | 20 requests | 1 minute | 429 Too Many Requests |
| **Expensive Operations** (ML) | 10 requests | 5 requests | 5 minutes | 429 Too Many Requests |
| **Admin Endpoints** | 50 requests | N/A | 1 minute | 429 Too Many Requests |

### Rate Limit Headers

Every API response includes rate limit information:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1708099200
```

### Rate Limit Exceeded Response

```http
HTTP/1.1 429 Too Many Requests
Content-Type: application/json
Retry-After: 60

{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please try again later.",
    "retryAfter": 60
  }
}
```

---

## ⚠️ Error Handling

### Standard Error Response Format

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": {},
    "timestamp": "2026-03-15T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

### HTTP Status Codes

| Status Code | Meaning | Usage |
|------------|---------|-------|
| **200 OK** | Success | Successful GET, PUT, PATCH requests |
| **201 Created** | Resource created | Successful POST requests |
| **204 No Content** | Success without body | Successful DELETE requests |
| **400 Bad Request** | Invalid input | Validation errors, malformed JSON |
| **401 Unauthorized** | Authentication required | Missing or invalid token |
| **403 Forbidden** | Insufficient permissions | Valid token but lacking required role |
| **404 Not Found** | Resource not found | Endpoint or resource doesn't exist |
| **429 Too Many Requests** | Rate limit exceeded | Too many requests in time window |
| **500 Internal Server Error** | Server error | Unexpected server-side error |
| **503 Service Unavailable** | Service down | Maintenance or temporary outage |

### Common Error Codes

| Error Code | HTTP Status | Description |
|-----------|-------------|-------------|
| `INVALID_TOKEN` | 401 | JWT token is invalid or expired |
| `TOKEN_EXPIRED` | 401 | JWT token has expired |
| `INVALID_CREDENTIALS` | 401 | Email or password is incorrect |
| `FORBIDDEN` | 403 | User lacks required permissions |
| `NOT_FOUND` | 404 | Requested resource doesn't exist |
| `VALIDATION_ERROR` | 400 | Input validation failed |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Unexpected server error |
| `SERVICE_UNAVAILABLE` | 503 | Service temporarily unavailable |

### Validation Error Example

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": {
      "fields": {
        "email": "Invalid email format",
        "price": "Price must be a positive number"
      }
    },
    "timestamp": "2026-03-15T10:30:00Z"
  }
}
```

---

## 📊 Response Format

### Success Response Structure

```json
{
  "success": true,
  "data": {
    // Response data here
  },
  "meta": {
    "timestamp": "2026-03-15T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

### Paginated Response Structure

```json
{
  "success": true,
  "data": [
    // Array of items
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 156,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  },
  "meta": {
    "timestamp": "2026-03-15T10:30:00Z",
    "requestId": "req_abc123"
  }
}
```

---

## 🔍 Filtering, Sorting, and Pagination

### Query Parameters

Most list endpoints support the following query parameters:

#### Pagination
- `page` (integer, default: 1) - Page number
- `limit` (integer, default: 20, max: 100) - Items per page

#### Sorting
- `sortBy` (string) - Field to sort by
- `order` (string: `asc` or `desc`, default: `desc`) - Sort direction

#### Filtering
- Field-specific filters (see individual endpoint documentation)

**Example:**
```http
GET /api/materials?page=2&limit=50&sortBy=name&order=asc&category=cement
```

---

## 🌐 CORS Policy

The API supports Cross-Origin Resource Sharing (CORS) for web applications.

**Allowed Origins (Production):**
- `https://buildmaterials.app`
- `https://www.buildmaterials.app`

**Allowed Origins (Development):**
- `http://localhost:3000`
- `http://localhost:3001`

**Allowed Methods:** `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `OPTIONS`  
**Allowed Headers:** `Authorization`, `Content-Type`, `X-Requested-With`

---

## 🔒 Security

### HTTPS Requirement

All API requests **must** use HTTPS in production. HTTP requests will be automatically redirected to HTTPS.

### Content Security

- **SQL Injection Protection:** Parameterized queries
- **XSS Protection:** Output encoding and sanitization
- **CSRF Protection:** CSRF tokens for web clients (not required for mobile with JWT)

### Security Headers

The API sets the following security headers:

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'
```

---

## 📈 Caching

The API implements a two-tier caching strategy using Redis:

### Cache Tiers

1. **L1 Cache (Price Data):** 1-hour TTL, >80% hit rate target
2. **L2 Cache (ML Predictions):** 6-hour TTL, >70% hit rate target

### Cache Headers

Cached responses include:

```http
X-Cache-Status: HIT
X-Cache-TTL: 3600
```

### Cache Control

Clients can control caching behavior:

```http
Cache-Control: no-cache  // Bypass cache
```

---

## 📝 API Versioning

The API uses URL-based versioning:

```
https://api.buildmaterials.app/v1/materials
```

**Current Version:** v1  
**Deprecated Versions:** None  
**Version Support:** Previous version supported for 6 months after new version release

---

## 🔗 Quick Links

- [Detailed Endpoint Documentation](./ENDPOINTS.md)
- [OpenAPI 3.0 Specification](./openapi.yaml)
- [Authentication Guide](../SECURITY.md#authentication--authorization)
- [Rate Limiting Details](../SECURITY.md#rate-limiting)
- [System Architecture](../architecture/SYSTEM_ARCHITECTURE.md)

---

## 💡 Best Practices

### 1. Always Use HTTPS
Production applications must use HTTPS to protect sensitive data.

### 2. Store Tokens Securely
- **Web:** Use HttpOnly, Secure cookies
- **Mobile:** Use platform secure storage (Keychain/Keystore)
- **Never** expose tokens in URLs or logs

### 3. Handle Rate Limits Gracefully
Implement exponential backoff when rate limits are hit.

### 4. Validate Input Client-Side
Reduce server load by validating input before sending requests.

### 5. Use Pagination for Large Datasets
Always paginate list endpoints to avoid performance issues.

### 6. Cache Responses Client-Side
Leverage cache headers to minimize redundant API calls.

### 7. Monitor Token Expiry
Refresh tokens proactively before expiry to avoid disruption.

---

## 📞 Support

For API support and questions:

- **Documentation Issues:** Open a GitHub issue
- **Security Issues:** See [SECURITY.md](../SECURITY.md)
- **General Questions:** Contact the development team

---

**Last Updated:** March 9, 2026  
**API Version:** v1.0  
**Documentation Version:** 1.0
