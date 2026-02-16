# 📡 API Endpoints Reference

**Building Materials Price Intelligence Platform**  
**Detailed Endpoint Documentation**

---

## Table of Contents

1. [Authentication Endpoints](#authentication-endpoints)
2. [Materials Endpoints](#materials-endpoints)
3. [Prices Endpoints](#prices-endpoints)
4. [Suppliers Endpoints](#suppliers-endpoints)
5. [Admin Endpoints](#admin-endpoints)

---

## Authentication Endpoints

### POST /api/auth/register

Register a new user account.

**Authentication:** None (public endpoint)  
**Rate Limit:** 5 attempts per 15 minutes

#### Request Body

```json
{
  "email": "string (required, valid email)",
  "password": "string (required, min 8 chars)",
  "name": "string (required)",
  "role": "string (optional, default: contractor)"
}
```

**Password Requirements:**
- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character

**Valid Roles:** `contractor`, `analyst`, `admin`, `guest`

#### Response (201 Created)

```json
{
  "success": true,
  "data": {
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "contractor@example.com",
      "name": "John Contractor",
      "role": "contractor",
      "createdAt": "2026-03-15T10:30:00Z"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": "24h"
  }
}
```

#### Example (curl)

```bash
curl -X POST https://api.buildmaterials.app/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "contractor@example.com",
    "password": "SecurePass123!",
    "name": "John Contractor"
  }'
```

#### Error Responses

**400 Bad Request** - Validation error:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": {
      "fields": {
        "email": "Email already registered",
        "password": "Password must contain at least one uppercase letter"
      }
    }
  }
}
```

---

### POST /api/auth/login

Authenticate and obtain a JWT token.

**Authentication:** None (public endpoint)  
**Rate Limit:** 5 attempts per 15 minutes

#### Request Body

```json
{
  "email": "string (required)",
  "password": "string (required)"
}
```

#### Response (200 OK)

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
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": "24h"
  }
}
```

#### Example (curl)

```bash
curl -X POST https://api.buildmaterials.app/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "contractor@example.com",
    "password": "SecurePass123!"
  }'
```

#### Error Responses

**401 Unauthorized** - Invalid credentials:
```json
{
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Email or password is incorrect"
  }
}
```

---

### POST /api/auth/logout

Logout and revoke JWT token.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute

#### Request

```http
POST /api/auth/logout
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

#### Response (200 OK)

```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

#### Example (curl)

```bash
curl -X POST https://api.buildmaterials.app/api/auth/logout \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

---

## Materials Endpoints

### GET /api/materials/search

Search for building materials by name, category, or other criteria.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute  
**Cache:** L1 (1-hour TTL)

#### Query Parameters

- `q` (string, optional) - Search query (material name or description)
- `category` (string, optional) - Filter by category (cement, steel, timber, etc.)
- `region` (string, optional) - Filter by region
- `page` (integer, default: 1) - Page number
- `limit` (integer, default: 20, max: 100) - Items per page

**Valid Categories:** `cement`, `steel`, `timber`, `sand`, `copper`, `pvc`, `bitumen`, `masonry`

**Valid Regions:** `Gauteng`, `Western Cape`, `KwaZulu-Natal`, `Eastern Cape`, `Free State`, `Limpopo`, `Mpumalanga`, `Northern Cape`, `North West`

#### Response (200 OK)

```json
{
  "success": true,
  "data": [
    {
      "id": "mat_001",
      "name": "Portland Cement 42.5N",
      "category": "cement",
      "unit": "50kg bag",
      "description": "High-strength portland cement suitable for general construction",
      "avgPrice": 145.50,
      "priceRange": {
        "min": 138.00,
        "max": 155.00
      },
      "lastUpdated": "2026-03-15T09:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 156,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  }
}
```

#### Example (curl)

```bash
curl -X GET "https://api.buildmaterials.app/api/materials/search?q=cement&category=cement&region=Gauteng" \
  -H "Authorization: Bearer <your-token>"
```

---

### GET /api/materials/:id

Get detailed information about a specific material.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute  
**Cache:** L1 (1-hour TTL)

#### Path Parameters

- `id` (string, required) - Material ID

#### Response (200 OK)

```json
{
  "success": true,
  "data": {
    "id": "mat_001",
    "name": "Portland Cement 42.5N",
    "category": "cement",
    "unit": "50kg bag",
    "description": "High-strength portland cement suitable for general construction",
    "specifications": {
      "strength": "42.5 MPa",
      "standard": "SANS 50197-1",
      "type": "CEM I"
    },
    "avgPrice": 145.50,
    "priceRange": {
      "min": 138.00,
      "max": 155.00
    },
    "priceHistory": {
      "lastMonth": 142.30,
      "lastQuarter": 139.50,
      "lastYear": 130.00
    },
    "suppliers": 15,
    "lastUpdated": "2026-03-15T09:00:00Z"
  }
}
```

#### Example (curl)

```bash
curl -X GET https://api.buildmaterials.app/api/materials/mat_001 \
  -H "Authorization: Bearer <your-token>"
```

#### Error Responses

**404 Not Found** - Material doesn't exist:
```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "Material not found"
  }
}
```

---

### GET /api/materials/favorites

Get user's favorite materials.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute  
**Cache:** No caching (user-specific data)

#### Query Parameters

- `page` (integer, default: 1)
- `limit` (integer, default: 20, max: 100)

#### Response (200 OK)

```json
{
  "success": true,
  "data": [
    {
      "id": "mat_001",
      "name": "Portland Cement 42.5N",
      "category": "cement",
      "avgPrice": 145.50,
      "addedAt": "2026-03-10T14:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 8
  }
}
```

#### Example (curl)

```bash
curl -X GET https://api.buildmaterials.app/api/materials/favorites \
  -H "Authorization: Bearer <your-token>"
```

---

### POST /api/materials/:id/favorite

Add a material to favorites.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute

#### Path Parameters

- `id` (string, required) - Material ID

#### Response (201 Created)

```json
{
  "success": true,
  "message": "Material added to favorites"
}
```

#### Example (curl)

```bash
curl -X POST https://api.buildmaterials.app/api/materials/mat_001/favorite \
  -H "Authorization: Bearer <your-token>"
```

---

### DELETE /api/materials/:id/favorite

Remove a material from favorites.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute

#### Path Parameters

- `id` (string, required) - Material ID

#### Response (200 OK)

```json
{
  "success": true,
  "message": "Material removed from favorites"
}
```

---

## Prices Endpoints

### GET /api/prices/compare

Compare prices for a specific material across suppliers.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute  
**Cache:** L1 (1-hour TTL)

#### Query Parameters

- `materialId` (string, required) - Material ID
- `region` (string, optional) - Filter by region
- `sortBy` (string, optional) - Sort by field (`price`, `distance`, `rating`)
- `order` (string, optional) - Sort order (`asc` or `desc`)

#### Response (200 OK)

```json
{
  "success": true,
  "data": {
    "material": {
      "id": "mat_001",
      "name": "Portland Cement 42.5N",
      "unit": "50kg bag"
    },
    "prices": [
      {
        "supplier": {
          "id": "sup_001",
          "name": "Builders Warehouse Johannesburg",
          "location": "Johannesburg, Gauteng",
          "distance": 5.2,
          "rating": 4.5
        },
        "price": 145.50,
        "bulkPricing": [
          {
            "quantity": 10,
            "pricePerUnit": 140.00
          },
          {
            "quantity": 50,
            "pricePerUnit": 135.00
          }
        ],
        "inStock": true,
        "lastUpdated": "2026-03-15T09:00:00Z"
      }
    ],
    "statistics": {
      "avgPrice": 145.50,
      "minPrice": 138.00,
      "maxPrice": 155.00,
      "priceSpread": 17.00
    }
  }
}
```

#### Example (curl)

```bash
curl -X GET "https://api.buildmaterials.app/api/prices/compare?materialId=mat_001&region=Gauteng&sortBy=price&order=asc" \
  -H "Authorization: Bearer <your-token>"
```

---

### GET /api/prices/history

Get historical price data for a material.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute  
**Cache:** L1 (1-hour TTL)

#### Query Parameters

- `materialId` (string, required) - Material ID
- `startDate` (string, optional) - Start date (ISO 8601)
- `endDate` (string, optional) - End date (ISO 8601)
- `region` (string, optional) - Filter by region
- `supplierId` (string, optional) - Filter by supplier

#### Response (200 OK)

```json
{
  "success": true,
  "data": {
    "material": {
      "id": "mat_001",
      "name": "Portland Cement 42.5N"
    },
    "priceHistory": [
      {
        "date": "2026-03-15",
        "avgPrice": 145.50,
        "minPrice": 138.00,
        "maxPrice": 155.00,
        "dataPoints": 15
      },
      {
        "date": "2026-03-14",
        "avgPrice": 144.80,
        "minPrice": 137.50,
        "maxPrice": 154.00,
        "dataPoints": 15
      }
    ],
    "statistics": {
      "trend": "increasing",
      "changePercent": 2.3,
      "volatility": "low"
    }
  }
}
```

#### Example (curl)

```bash
curl -X GET "https://api.buildmaterials.app/api/prices/history?materialId=mat_001&startDate=2026-02-01&endDate=2026-03-15" \
  -H "Authorization: Bearer <your-token>"
```

---

### GET /api/prices/forecast

Get price forecast for a material.

**Authentication:** Required (JWT token)  
**Rate Limit:** 10 requests per 5 minutes (expensive ML operation)  
**Cache:** L2 (6-hour TTL)

#### Query Parameters

- `materialId` (string, required) - Material ID
- `horizon` (integer, default: 7) - Forecast horizon in days (7 or 30)
- `model` (string, optional) - Specific model to use (`lstm`, `prophet`, `xgboost`, `ensemble`)
- `region` (string, optional) - Filter by region

**Valid Horizons:** 7, 30

#### Response (200 OK)

```json
{
  "success": true,
  "data": {
    "material": {
      "id": "mat_001",
      "name": "Portland Cement 42.5N"
    },
    "currentPrice": 145.50,
    "forecast": [
      {
        "date": "2026-03-16",
        "predictedPrice": 146.20,
        "confidenceInterval": {
          "lower": 143.50,
          "upper": 148.90
        },
        "confidence": 0.85
      },
      {
        "date": "2026-03-17",
        "predictedPrice": 146.80,
        "confidenceInterval": {
          "lower": 143.80,
          "upper": 149.80
        },
        "confidence": 0.83
      }
    ],
    "model": {
      "name": "LSTM Ensemble",
      "accuracy": {
        "mae": 2.45,
        "rmse": 3.12,
        "mape": 1.68
      },
      "trainedAt": "2026-03-15T06:00:00Z"
    },
    "recommendation": {
      "action": "wait",
      "reason": "Price expected to stabilize in 5 days",
      "optimalPurchaseDate": "2026-03-20"
    }
  }
}
```

#### Example (curl)

```bash
curl -X GET "https://api.buildmaterials.app/api/prices/forecast?materialId=mat_001&horizon=30&model=ensemble" \
  -H "Authorization: Bearer <your-token>"
```

---

## Suppliers Endpoints

### GET /api/suppliers/nearby

Find suppliers near a specific location.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute  
**Cache:** L1 (1-hour TTL)

#### Query Parameters

- `lat` (number, required) - Latitude
- `lng` (number, required) - Longitude
- `radius` (number, default: 25) - Search radius in kilometers
- `category` (string, optional) - Filter by material category
- `page` (integer, default: 1)
- `limit` (integer, default: 20, max: 100)

#### Response (200 OK)

```json
{
  "success": true,
  "data": [
    {
      "id": "sup_001",
      "name": "Builders Warehouse Johannesburg",
      "location": {
        "address": "123 Main Street, Johannesburg, Gauteng, 2000",
        "coordinates": {
          "lat": -26.2041,
          "lng": 28.0473
        }
      },
      "distance": 5.2,
      "contact": {
        "phone": "+27 11 123 4567",
        "email": "jburg@builderswarehouse.co.za",
        "website": "https://builderswarehouse.co.za"
      },
      "rating": 4.5,
      "categories": ["cement", "steel", "timber"],
      "openingHours": {
        "monday": "07:00-18:00",
        "tuesday": "07:00-18:00",
        "wednesday": "07:00-18:00",
        "thursday": "07:00-18:00",
        "friday": "07:00-18:00",
        "saturday": "07:00-17:00",
        "sunday": "08:00-14:00"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 12
  }
}
```

#### Example (curl)

```bash
curl -X GET "https://api.buildmaterials.app/api/suppliers/nearby?lat=-26.2041&lng=28.0473&radius=25" \
  -H "Authorization: Bearer <your-token>"
```

---

### GET /api/suppliers/:id

Get detailed information about a specific supplier.

**Authentication:** Required (JWT token)  
**Rate Limit:** 100 requests per minute  
**Cache:** L1 (1-hour TTL)

#### Path Parameters

- `id` (string, required) - Supplier ID

#### Response (200 OK)

```json
{
  "success": true,
  "data": {
    "id": "sup_001",
    "name": "Builders Warehouse Johannesburg",
    "description": "Leading building materials supplier in Gauteng",
    "location": {
      "address": "123 Main Street, Johannesburg, Gauteng, 2000",
      "coordinates": {
        "lat": -26.2041,
        "lng": 28.0473
      }
    },
    "contact": {
      "phone": "+27 11 123 4567",
      "email": "jburg@builderswarehouse.co.za",
      "website": "https://builderswarehouse.co.za"
    },
    "rating": 4.5,
    "reviewCount": 234,
    "categories": ["cement", "steel", "timber", "sand", "copper"],
    "openingHours": {
      "monday": "07:00-18:00",
      "tuesday": "07:00-18:00",
      "wednesday": "07:00-18:00",
      "thursday": "07:00-18:00",
      "friday": "07:00-18:00",
      "saturday": "07:00-17:00",
      "sunday": "08:00-14:00"
    },
    "paymentMethods": ["cash", "card", "eft", "account"],
    "deliveryAvailable": true,
    "lastScraped": "2026-03-15T09:00:00Z"
  }
}
```

#### Example (curl)

```bash
curl -X GET https://api.buildmaterials.app/api/suppliers/sup_001 \
  -H "Authorization: Bearer <your-token>"
```

---

## Admin Endpoints

### GET /api/admin/metrics

Get system metrics and analytics (admin only).

**Authentication:** Required (JWT token with admin role)  
**Rate Limit:** 50 requests per minute

#### Query Parameters

- `startDate` (string, optional) - Start date for metrics
- `endDate` (string, optional) - End date for metrics

#### Response (200 OK)

```json
{
  "success": true,
  "data": {
    "system": {
      "uptime": 432000,
      "version": "1.0.0",
      "environment": "production"
    },
    "database": {
      "totalMaterials": 156,
      "totalSuppliers": 42,
      "totalPrices": 12453,
      "lastUpdate": "2026-03-15T09:00:00Z"
    },
    "cache": {
      "hitRate": 0.76,
      "l1HitRate": 0.82,
      "l2HitRate": 0.71,
      "avgResponseTime": 45,
      "memoryUsage": "24.5 MB"
    },
    "api": {
      "totalRequests": 1543210,
      "requestsPerMinute": 120,
      "avgResponseTime": 120,
      "errorRate": 0.02
    },
    "ml": {
      "modelsDeployed": 4,
      "predictionsToday": 1234,
      "avgPredictionTime": 250,
      "accuracy": {
        "mae": 2.45,
        "rmse": 3.12,
        "mape": 1.68
      }
    },
    "users": {
      "total": 523,
      "active": 87,
      "newToday": 12
    }
  }
}
```

#### Example (curl)

```bash
curl -X GET https://api.buildmaterials.app/api/admin/metrics \
  -H "Authorization: Bearer <admin-token>"
```

#### Error Responses

**403 Forbidden** - Not admin:
```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "Admin access required"
  }
}
```

---

### GET /api/admin/users

List all users (admin only).

**Authentication:** Required (JWT token with admin role)  
**Rate Limit:** 50 requests per minute

#### Query Parameters

- `role` (string, optional) - Filter by role
- `page` (integer, default: 1)
- `limit` (integer, default: 20, max: 100)

#### Response (200 OK)

```json
{
  "success": true,
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "contractor@example.com",
      "name": "John Contractor",
      "role": "contractor",
      "createdAt": "2026-03-10T14:30:00Z",
      "lastLogin": "2026-03-15T10:30:00Z",
      "active": true
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 523
  }
}
```

---

### POST /api/admin/scraping

Trigger manual scraping job (admin only).

**Authentication:** Required (JWT token with admin role)  
**Rate Limit:** 10 requests per minute

#### Request Body

```json
{
  "supplierId": "string (optional)",
  "force": "boolean (optional, default: false)"
}
```

#### Response (202 Accepted)

```json
{
  "success": true,
  "data": {
    "jobId": "job_abc123",
    "status": "queued",
    "estimatedCompletion": "2026-03-15T11:00:00Z"
  }
}
```

#### Example (curl)

```bash
curl -X POST https://api.buildmaterials.app/api/admin/scraping \
  -H "Authorization: Bearer <admin-token>" \
  -H "Content-Type: application/json" \
  -d '{
    "supplierId": "sup_001",
    "force": true
  }'
```

---

### GET /api/admin/scraping/:jobId

Get scraping job status (admin only).

**Authentication:** Required (JWT token with admin role)  
**Rate Limit:** 50 requests per minute

#### Path Parameters

- `jobId` (string, required) - Job ID

#### Response (200 OK)

```json
{
  "success": true,
  "data": {
    "jobId": "job_abc123",
    "status": "completed",
    "supplier": {
      "id": "sup_001",
      "name": "Builders Warehouse Johannesburg"
    },
    "results": {
      "materialsFound": 45,
      "pricesUpdated": 45,
      "errors": 0
    },
    "startedAt": "2026-03-15T10:30:00Z",
    "completedAt": "2026-03-15T10:35:00Z",
    "duration": 300
  }
}
```

---

## 📚 Additional Resources

- [API Overview](./README.md)
- [OpenAPI 3.0 Specification](./openapi.yaml)
- [Authentication Guide](../SECURITY.md)
- [System Architecture](../architecture/SYSTEM_ARCHITECTURE.md)

---

**Last Updated:** March 9, 2026  
**API Version:** v1.0
