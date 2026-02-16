# 🧪 Testing Documentation

**Building Materials Price Intelligence Platform**  
**Comprehensive Testing Strategy**

---

## 📋 Overview

Testing is a critical component of the Building Materials Price Intelligence Platform. This document outlines our comprehensive testing strategy, tools, frameworks, and best practices to ensure high-quality, reliable software delivery.

**Testing Philosophy:** Write tests that provide confidence, not just coverage.

**Target Coverage:** 80%+ overall, 90%+ for critical paths

---

## 🎯 Testing Pyramid

```
                   ┌─────────────┐
                   │   E2E Tests │  (5%)
                   │  (Slow)     │
                   └─────────────┘
               ┌─────────────────────┐
               │ Integration Tests  │  (15%)
               │    (Medium)        │
               └─────────────────────┘
           ┌────────────────────────────┐
           │      Unit Tests           │  (80%)
           │       (Fast)              │
           └────────────────────────────┘
```

---

## 🔧 Testing Tools & Frameworks

### Backend (Node.js/Express)

| Category | Tool | Purpose |
|----------|------|---------|
| **Test Runner** | Jest | Unit and integration testing |
| **API Testing** | Supertest | HTTP endpoint testing |
| **Mocking** | Jest mocks | Function and module mocking |
| **Database** | PostgreSQL Test Instance | Isolated test database |
| **Coverage** | Jest coverage | Code coverage reporting |

### Frontend (React)

| Category | Tool | Purpose |
|----------|------|---------|
| **Test Runner** | Jest | Unit testing |
| **Component Testing** | React Testing Library | Component interaction testing |
| **Mocking** | MSW (Mock Service Worker) | API mocking |
| **Snapshot Testing** | Jest snapshots | UI regression detection |

### Mobile (Flutter)

| Category | Tool | Purpose |
|----------|------|---------|
| **Unit Testing** | flutter_test | Dart unit testing |
| **Widget Testing** | flutter_test | Widget testing |
| **Integration Testing** | integration_test | Full app testing |
| **Mocking** | mockito | Mock generation |

### End-to-End

| Category | Tool | Purpose |
|----------|------|---------|
| **E2E Framework** | Playwright / Cypress | Browser automation |
| **Visual Regression** | Percy / Chromatic | Screenshot comparison |

### Performance Testing

| Category | Tool | Purpose |
|----------|------|---------|
| **Load Testing** | k6 / Artillery | API load testing |
| **Profiling** | Chrome DevTools | Performance profiling |

### Security Testing

| Category | Tool | Purpose |
|----------|------|---------|
| **Dependency Scanning** | npm audit / Snyk | Vulnerability scanning |
| **Static Analysis** | ESLint, SonarQube | Code quality and security |
| **SAST** | CodeQL | Security code analysis |

---

## 📁 Test Organization

### Backend Structure

```
backend/
├── src/
│   ├── routes/
│   │   ├── auth.js
│   │   └── auth.test.js         # Unit tests
│   ├── services/
│   │   ├── price.service.js
│   │   └── price.service.test.js
│   ├── models/
│   │   ├── material.model.js
│   │   └── material.model.test.js
│   └── utils/
│       ├── validators.js
│       └── validators.test.js
├── tests/
│   ├── integration/
│   │   ├── api.test.js          # API integration tests
│   │   └── database.test.js     # Database integration tests
│   ├── e2e/
│   │   └── user-flows.test.js   # E2E scenarios
│   └── fixtures/
│       └── test-data.json       # Test data
└── jest.config.js
```

### Frontend Structure

```
web/
├── src/
│   ├── components/
│   │   ├── PriceCard/
│   │   │   ├── PriceCard.jsx
│   │   │   ├── PriceCard.test.jsx
│   │   │   └── PriceCard.test.jsx.snap
│   ├── hooks/
│   │   ├── usePrices.js
│   │   └── usePrices.test.js
│   └── services/
│       ├── api.service.js
│       └── api.service.test.js
├── tests/
│   ├── integration/
│   │   └── app-flow.test.jsx
│   └── e2e/
│       └── playwright/
│           └── user-journey.spec.js
└── jest.config.js
```

### Mobile Structure

```
mobile/
├── lib/
│   ├── screens/
│   │   └── home_screen.dart
│   ├── widgets/
│   │   └── price_card.dart
│   └── services/
│       └── api_service.dart
├── test/
│   ├── unit/
│   │   └── services/
│   │       └── api_service_test.dart
│   ├── widget/
│   │   └── widgets/
│   │       └── price_card_test.dart
│   └── integration/
│       └── app_test.dart
└── test_driver/
    └── integration_test.dart
```

---

## 🧪 Unit Testing

### Backend Unit Test Example (Jest)

```javascript
// src/services/price.service.test.js
const PriceService = require('./price.service');
const db = require('../config/database');

jest.mock('../config/database');

describe('PriceService', () => {
  let priceService;

  beforeEach(() => {
    priceService = new PriceService();
    jest.clearAllMocks();
  });

  describe('getLatestPrice', () => {
    it('should return latest price for a material', async () => {
      // Arrange
      const mockPrice = {
        material_id: 'mat_001',
        supplier_id: 'sup_001',
        price: 145.50,
        date: '2026-03-15'
      };
      db.query.mockResolvedValue({ rows: [mockPrice] });

      // Act
      const result = await priceService.getLatestPrice('mat_001');

      // Assert
      expect(result).toEqual(mockPrice);
      expect(db.query).toHaveBeenCalledWith(
        expect.stringContaining('SELECT'),
        ['mat_001']
      );
    });

    it('should return null when no price found', async () => {
      // Arrange
      db.query.mockResolvedValue({ rows: [] });

      // Act
      const result = await priceService.getLatestPrice('mat_999');

      // Assert
      expect(result).toBeNull();
    });

    it('should handle database errors', async () => {
      // Arrange
      db.query.mockRejectedValue(new Error('Database error'));

      // Act & Assert
      await expect(
        priceService.getLatestPrice('mat_001')
      ).rejects.toThrow('Database error');
    });
  });
});
```

### Frontend Unit Test Example (React Testing Library)

```javascript
// src/components/PriceCard/PriceCard.test.jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import PriceCard from './PriceCard';

describe('PriceCard', () => {
  const mockPrice = {
    material: 'Portland Cement 42.5N',
    supplier: 'Builders Warehouse',
    price: 145.50,
    unit: '50kg bag'
  };

  it('renders price information correctly', () => {
    // Arrange & Act
    render(<PriceCard price={mockPrice} />);

    // Assert
    expect(screen.getByText('Portland Cement 42.5N')).toBeInTheDocument();
    expect(screen.getByText('R 145.50')).toBeInTheDocument();
    expect(screen.getByText('Builders Warehouse')).toBeInTheDocument();
  });

  it('calls onAddToFavorites when favorite button clicked', async () => {
    // Arrange
    const mockOnAddToFavorites = jest.fn();
    render(
      <PriceCard 
        price={mockPrice} 
        onAddToFavorites={mockOnAddToFavorites} 
      />
    );

    // Act
    await userEvent.click(screen.getByRole('button', { name: /favorite/i }));

    // Assert
    expect(mockOnAddToFavorites).toHaveBeenCalledWith(mockPrice);
  });
});
```

### Mobile Unit Test Example (Flutter)

```dart
// test/unit/services/api_service_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:mockito/mockito.dart';
import 'package:http/http.dart' as http;
import 'package:build_materials/services/api_service.dart';

class MockHttpClient extends Mock implements http.Client {}

void main() {
  group('ApiService', () {
    late ApiService apiService;
    late MockHttpClient mockHttpClient;

    setUp(() {
      mockHttpClient = MockHttpClient();
      apiService = ApiService(client: mockHttpClient);
    });

    test('getMaterial returns Material on successful API call', () async {
      // Arrange
      when(mockHttpClient.get(
        Uri.parse('https://api.buildmaterials.app/api/materials/mat_001'),
        headers: anyNamed('headers'),
      )).thenAnswer((_) async => http.Response(
        '{"id": "mat_001", "name": "Cement", "price": 145.50}',
        200,
      ));

      // Act
      final material = await apiService.getMaterial('mat_001');

      // Assert
      expect(material.id, equals('mat_001'));
      expect(material.name, equals('Cement'));
      expect(material.price, equals(145.50));
    });

    test('getMaterial throws exception on API error', () async {
      // Arrange
      when(mockHttpClient.get(any, headers: anyNamed('headers')))
          .thenAnswer((_) async => http.Response('Not Found', 404));

      // Act & Assert
      expect(
        () => apiService.getMaterial('mat_999'),
        throwsException,
      );
    });
  });
}
```

---

## 🔗 Integration Testing

### Backend API Integration Test

```javascript
// tests/integration/api.test.js
const request = require('supertest');
const app = require('../../src/app');
const db = require('../../src/config/database');
const { setupTestDB, teardownTestDB, seedTestData } = require('../helpers/db');

describe('API Integration Tests', () => {
  beforeAll(async () => {
    await setupTestDB();
  });

  afterAll(async () => {
    await teardownTestDB();
  });

  beforeEach(async () => {
    await seedTestData();
  });

  describe('POST /api/auth/login', () => {
    it('should login with valid credentials', async () => {
      // Act
      const response = await request(app)
        .post('/api/auth/login')
        .send({
          email: 'test@example.com',
          password: 'TestPass123!'
        });

      // Assert
      expect(response.status).toBe(200);
      expect(response.body.success).toBe(true);
      expect(response.body.data.token).toBeDefined();
      expect(response.body.data.user.email).toBe('test@example.com');
    });

    it('should reject invalid credentials', async () => {
      // Act
      const response = await request(app)
        .post('/api/auth/login')
        .send({
          email: 'test@example.com',
          password: 'WrongPassword'
        });

      // Assert
      expect(response.status).toBe(401);
      expect(response.body.error.code).toBe('INVALID_CREDENTIALS');
    });
  });

  describe('GET /api/materials/search', () => {
    let authToken;

    beforeEach(async () => {
      // Get auth token
      const loginResponse = await request(app)
        .post('/api/auth/login')
        .send({
          email: 'test@example.com',
          password: 'TestPass123!'
        });
      authToken = loginResponse.body.data.token;
    });

    it('should search materials with valid token', async () => {
      // Act
      const response = await request(app)
        .get('/api/materials/search?q=cement')
        .set('Authorization', `Bearer ${authToken}`);

      // Assert
      expect(response.status).toBe(200);
      expect(response.body.success).toBe(true);
      expect(response.body.data).toBeInstanceOf(Array);
      expect(response.body.data.length).toBeGreaterThan(0);
    });

    it('should reject request without auth token', async () => {
      // Act
      const response = await request(app)
        .get('/api/materials/search?q=cement');

      // Assert
      expect(response.status).toBe(401);
    });
  });
});
```

---

## 🌐 End-to-End Testing

### Playwright E2E Test Example

```javascript
// tests/e2e/playwright/user-journey.spec.js
const { test, expect } = require('@playwright/test');

test.describe('User Journey: Material Search and Comparison', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('https://localhost:3000');
  });

  test('User can search for materials and view prices', async ({ page }) => {
    // Login
    await page.click('text=Login');
    await page.fill('input[name="email"]', 'test@example.com');
    await page.fill('input[name="password"]', 'TestPass123!');
    await page.click('button[type="submit"]');
    
    // Wait for redirect to dashboard
    await page.waitForURL('**/dashboard');

    // Search for cement
    await page.fill('input[placeholder="Search materials..."]', 'cement');
    await page.click('button:has-text("Search")');

    // Verify search results
    await expect(page.locator('.material-card')).toHaveCount(3, { timeout: 5000 });
    await expect(page.locator('text=Portland Cement 42.5N')).toBeVisible();

    // Click on a material
    await page.click('text=Portland Cement 42.5N');

    // Verify material detail page
    await expect(page.locator('h1')).toContainText('Portland Cement 42.5N');
    await expect(page.locator('.price-comparison')).toBeVisible();
    
    // Verify price data is loaded
    await expect(page.locator('.supplier-price')).toHaveCount(5, { timeout: 5000 });

    // Add to favorites
    await page.click('button:has-text("Add to Favorites")');
    await expect(page.locator('text=Added to favorites')).toBeVisible();

    // Take screenshot for visual regression
    await page.screenshot({ path: 'screenshots/material-detail.png' });
  });

  test('User can view price forecast', async ({ page, context }) => {
    // Assume logged in (could use context storage)
    await context.addCookies([
      { name: 'auth_token', value: 'test_token', domain: 'localhost', path: '/' }
    ]);

    await page.goto('https://localhost:3000/material/mat_001');

    // Click forecast tab
    await page.click('button:has-text("Forecast")');

    // Verify forecast chart is displayed
    await expect(page.locator('.forecast-chart')).toBeVisible();
    await expect(page.locator('text=7-Day Forecast')).toBeVisible();

    // Verify forecast data points
    const dataPoints = page.locator('.forecast-data-point');
    await expect(dataPoints).toHaveCount(7);
  });
});
```

---

## ⚡ Performance Testing

### Load Testing with k6

```javascript
// tests/performance/load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 20 },  // Ramp up to 20 users
    { duration: '1m', target: 50 },   // Ramp up to 50 users
    { duration: '2m', target: 50 },   // Stay at 50 users
    { duration: '30s', target: 0 },   // Ramp down to 0 users
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'], // 95% of requests should be below 500ms
    http_req_failed: ['rate<0.01'],   // Error rate should be below 1%
  },
};

export default function () {
  // Test material search endpoint
  const searchResponse = http.get(
    'https://api.buildmaterials.app/api/materials/search?q=cement',
    {
      headers: { 'Authorization': 'Bearer test_token' },
    }
  );

  check(searchResponse, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });

  sleep(1);

  // Test price comparison endpoint
  const priceResponse = http.get(
    'https://api.buildmaterials.app/api/prices/compare?materialId=mat_001',
    {
      headers: { 'Authorization': 'Bearer test_token' },
    }
  );

  check(priceResponse, {
    'status is 200': (r) => r.status === 200,
    'cache hit': (r) => r.headers['X-Cache-Status'] === 'HIT',
  });

  sleep(1);
}
```

**Run load test:**
```bash
k6 run tests/performance/load-test.js
```

---

## 🔒 Security Testing

### Automated Security Scans

```bash
# Dependency vulnerability scanning
npm audit

# Advanced scanning with Snyk
npm install -g snyk
snyk test

# Static code analysis
npm run lint:security

# CodeQL analysis (via GitHub Actions)
# See .github/workflows/codeql.yml
```

### Manual Security Testing Checklist

- [ ] SQL injection testing
- [ ] XSS vulnerability testing
- [ ] CSRF protection verification
- [ ] Authentication bypass attempts
- [ ] Authorization boundary testing
- [ ] Rate limiting verification
- [ ] Sensitive data exposure checks
- [ ] API security testing (OWASP API Top 10)

---

## 📊 Test Coverage

### Generating Coverage Reports

**Backend:**
```bash
# Run tests with coverage
npm run test:coverage

# View HTML report
open coverage/lcov-report/index.html
```

**Frontend:**
```bash
# Run tests with coverage
npm run test:coverage

# View report
open coverage/lcov-report/index.html
```

**Mobile:**
```bash
# Generate coverage
flutter test --coverage

# View report
genhtml coverage/lcov.info -o coverage/html
open coverage/html/index.html
```

### Coverage Targets

| Component | Target | Critical Paths |
|-----------|--------|----------------|
| **Backend API** | 80%+ | 95%+ |
| **Frontend** | 75%+ | 90%+ |
| **Mobile** | 70%+ | 90%+ |
| **ML Services** | 70%+ | 85%+ |

### Coverage Enforcement

```javascript
// jest.config.js
module.exports = {
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    },
    './src/services/': {
      branches: 90,
      functions: 95,
      lines: 90,
      statements: 90
    }
  }
};
```

---

## 🎯 Test Data Management

### Test Fixtures

```javascript
// tests/fixtures/materials.json
{
  "materials": [
    {
      "id": "mat_test_001",
      "name": "Test Portland Cement",
      "category": "cement",
      "unit": "50kg bag",
      "avgPrice": 145.50
    }
  ]
}
```

### Test Data Seeding

```javascript
// tests/helpers/seed.js
const db = require('../../src/config/database');
const fixtures = require('../fixtures/materials.json');

async function seedTestData() {
  await db.query('TRUNCATE TABLE materials CASCADE');
  
  for (const material of fixtures.materials) {
    await db.query(
      'INSERT INTO materials (id, name, category, unit) VALUES ($1, $2, $3, $4)',
      [material.id, material.name, material.category, material.unit]
    );
  }
}

module.exports = { seedTestData };
```

---

## 🔄 CI/CD Integration

### GitHub Actions Workflow

```yaml
# .github/workflows/test.yml
name: Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  backend-tests:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run unit tests
        run: npm run test:unit
      
      - name: Run integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test_db
      
      - name: Generate coverage
        run: npm run test:coverage
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          flags: backend

  frontend-tests:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
          cache-dependency-path: web/package-lock.json
      
      - name: Install dependencies
        working-directory: ./web
        run: npm ci
      
      - name: Run tests
        working-directory: ./web
        run: npm run test:ci
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./web/coverage/lcov.info
          flags: frontend

  e2e-tests:
    runs-on: ubuntu-latest
    needs: [backend-tests, frontend-tests]
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install Playwright
        run: npx playwright install --with-deps
      
      - name: Run E2E tests
        run: npm run test:e2e
      
      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: playwright-report
          path: playwright-report/
```

---

## 📋 Code Review Testing Checklist

### For Reviewers

- [ ] All new code has corresponding tests
- [ ] Tests are meaningful, not just for coverage
- [ ] Tests follow AAA pattern (Arrange, Act, Assert)
- [ ] Edge cases and error scenarios are tested
- [ ] Tests are isolated and don't depend on external state
- [ ] Test names clearly describe what is being tested
- [ ] Mock external dependencies appropriately
- [ ] No hardcoded test data (use fixtures)
- [ ] Tests run quickly (unit tests < 1s each)
- [ ] Coverage meets threshold requirements

---

## 🎓 Testing Best Practices

### DO

✅ **Write tests before or alongside code (TDD/BDD)**  
✅ **Test behavior, not implementation**  
✅ **Keep tests simple and focused**  
✅ **Use descriptive test names**  
✅ **Mock external dependencies**  
✅ **Test edge cases and error conditions**  
✅ **Run tests locally before pushing**  
✅ **Keep tests fast**  
✅ **Maintain test code quality**  
✅ **Update tests when requirements change**

### DON'T

❌ **Don't skip writing tests**  
❌ **Don't test implementation details**  
❌ **Don't write flaky tests**  
❌ **Don't share state between tests**  
❌ **Don't hardcode test data**  
❌ **Don't ignore failing tests**  
❌ **Don't test third-party libraries**  
❌ **Don't write tests just for coverage**

---

## 📚 Additional Resources

- [Jest Documentation](https://jestjs.io/)
- [React Testing Library](https://testing-library.com/react)
- [Playwright Documentation](https://playwright.dev/)
- [Flutter Testing](https://docs.flutter.dev/testing)
- [k6 Performance Testing](https://k6.io/docs/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [Security Documentation](./SECURITY.md)
- [CI/CD Best Practices](./DEPLOYMENT.md)

---

**Last Updated:** March 9, 2026  
**Testing Framework Version:** Jest 29.x, Playwright 1.40.x  
**Next Review:** Post-MVP (June 2026)
