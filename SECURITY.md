# 🔒 Security Architecture

**Building Materials Price Intelligence Platform**  
**Security Documentation & Best Practices**

---

## 📋 Overview

Security is a **formal requirement** of this project. This document outlines the comprehensive security architecture, implementation strategies, and best practices employed to protect user data, prevent unauthorized access, and maintain system integrity.

---

## 🔐 Authentication & Authorization

### JWT-Based Authentication

**Implementation Strategy:**

#### Token Structure
```json
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "user_id": "uuid",
    "email": "user@example.com",
    "role": "contractor|admin|analyst",
    "iat": 1708012800,
    "exp": 1708099200
  },
  "signature": "HMACSHA256(base64UrlEncode(header) + '.' + base64UrlEncode(payload), secret)"
}
```

#### Token Lifecycle
1. **Issuance**: Upon successful login, server generates JWT with 24-hour expiry
2. **Storage**: 
   - **Web**: Secure, HttpOnly cookie (prevents XSS access)
   - **Mobile**: Secure device storage (Keychain/Keystore)
3. **Transmission**: Included in `Authorization: Bearer <token>` header
4. **Validation**: Server verifies signature and expiry on each request
5. **Refresh**: Refresh token mechanism for seamless re-authentication
6. **Revocation**: Blacklist in Redis for logout/compromise scenarios

#### Security Benefits
- ✅ **Stateless**: No server-side session storage required
- ✅ **Scalable**: Horizontal scaling without session replication
- ✅ **Tamper-Proof**: HMAC signature prevents modification
- ✅ **Expiration**: Time-limited tokens reduce breach impact

---

### Role-Based Access Control (RBAC)

**User Roles:**

| Role | Permissions | Use Case |
|------|-------------|----------|
| **Contractor** | View prices, forecasts, suppliers; save favorites | Standard user - construction contractors |
| **Analyst** | All contractor permissions + export data, advanced analytics | Business analysts, project managers |
| **Admin** | Full system access, user management, trigger scraping, view metrics | System administrators |
| **Guest** | Read-only access to limited data (demo purposes) | Public showcase, trial users |

#### Implementation
```javascript
// Middleware example
function requireRole(allowedRoles) {
  return (req, res, next) => {
    const userRole = req.user.role;
    if (!allowedRoles.includes(userRole)) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}

// Usage
app.get('/api/admin/metrics', authenticate, requireRole(['admin']), getMetrics);
```

---

### Password Security

**Hashing Strategy:**

#### Choice: bcrypt or Argon2

**bcrypt:**
- Industry standard, widely tested
- Adaptive work factor (cost parameter)
- Recommended cost: 12-14 rounds

**Argon2:**
- Winner of Password Hashing Competition (2015)
- Memory-hard algorithm (resistant to GPU/ASIC attacks)
- Recommended for new projects

#### Implementation
```javascript
// Password hashing (bcrypt example)
const bcrypt = require('bcrypt');
const saltRounds = 12;

// Registration
const hashedPassword = await bcrypt.hash(plainPassword, saltRounds);

// Login verification
const isValid = await bcrypt.compare(plainPassword, hashedPassword);
```

#### Password Policy
- **Minimum Length**: 8 characters
- **Complexity**: Must include uppercase, lowercase, number, special character
- **Breach Prevention**: Check against Have I Been Pwned API
- **No Reuse**: Prevent reuse of last 5 passwords
- **Reset Flow**: Secure token-based password reset (expiry: 1 hour)

---

### JWT Token Blacklist

**Redis-Based Implementation:**

When a user logs out or a token is compromised, it's added to a blacklist to prevent further use.

```javascript
// Logout - add token to blacklist
const token = req.headers.authorization.split(' ')[1];
const decoded = jwt.verify(token, SECRET_KEY);
const ttl = decoded.exp - Math.floor(Date.now() / 1000);

await redisClient.setex(`blacklist:${token}`, ttl, 'true');

// Middleware - check blacklist
async function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  
  // Check if blacklisted
  const isBlacklisted = await redisClient.get(`blacklist:${token}`);
  if (isBlacklisted) {
    return res.status(401).json({ error: 'Token revoked' });
  }
  
  // Verify token
  try {
    const decoded = jwt.verify(token, SECRET_KEY);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
}
```

---

## 🌐 API Security

### HTTPS Encryption

**Requirement:** All API communication must use HTTPS (TLS 1.2+)

#### Implementation
- **Production**: Automatic HTTPS via Fly.io and Vercel
- **Development**: Self-signed certificates or HTTP (localhost only)
- **Certificate Management**: Automatic renewal with Let's Encrypt

#### Benefits
- ✅ Encryption in transit (prevents eavesdropping)
- ✅ Data integrity (prevents tampering)
- ✅ Server authentication (prevents MITM attacks)

---

### Rate Limiting

**Redis-Based Rate Limiting:**

Prevents abuse, brute force attacks, and DDoS attempts.

#### Configuration

| Endpoint Type | Rate Limit | Window | Consequence |
|---------------|-----------|--------|-------------|
| Authentication | 5 attempts | 15 minutes | Account lockout |
| API (Authenticated) | 100 requests | 1 minute | 429 Too Many Requests |
| API (Public) | 20 requests | 1 minute | 429 Too Many Requests |
| Expensive Operations (ML) | 10 requests | 5 minutes | 429 Too Many Requests |

#### Implementation
```javascript
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');

const limiter = rateLimit({
  store: new RedisStore({
    client: redisClient,
    prefix: 'rl:',
  }),
  windowMs: 60 * 1000, // 1 minute
  max: 100, // 100 requests per window
  message: 'Too many requests, please try again later.',
  standardHeaders: true,
  legacyHeaders: false,
});

app.use('/api/', limiter);
```

---

### Input Validation

**Strategy:** Validate all user input on both client and server

#### Validation Rules
1. **Type Checking**: Ensure data types match expectations
2. **Range Validation**: Numeric values within acceptable bounds
3. **Format Validation**: Email, phone numbers, dates conform to patterns
4. **Sanitization**: Remove/escape potentially harmful characters
5. **Whitelist Approach**: Only accept known-good input

#### Implementation Example
```javascript
const { body, validationResult } = require('express-validator');

app.post('/api/materials/search',
  [
    body('material_name').isString().trim().isLength({ min: 2, max: 100 }),
    body('region').optional().isIn(['Gauteng', 'Western Cape', 'KwaZulu-Natal', 'Eastern Cape']),
    body('min_price').optional().isFloat({ min: 0 }),
    body('max_price').optional().isFloat({ min: 0 }),
  ],
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // Process validated input
  }
);
```

---

## 🛡️ Protection Mechanisms

### SQL Injection Prevention

**Strategy:** Parameterized queries and ORM usage

#### Vulnerable Code (DO NOT USE)
```javascript
// ❌ VULNERABLE
const query = `SELECT * FROM prices WHERE material = '${req.params.material}'`;
db.query(query);
```

#### Secure Code (CORRECT)
```javascript
// ✅ SECURE - Parameterized query
const query = 'SELECT * FROM prices WHERE material = $1';
db.query(query, [req.params.material]);

// ✅ SECURE - ORM (e.g., Prisma)
const prices = await prisma.price.findMany({
  where: { material: req.params.material }
});
```

#### Additional Protections
- **Least Privilege**: Database user has minimal required permissions
- **Input Validation**: Reject suspicious input patterns
- **Stored Procedures**: Use where appropriate for complex queries

---

### Cross-Site Scripting (XSS) Protection

**Strategy:** Output encoding and Content Security Policy

#### Server-Side Protections
```javascript
const helmet = require('helmet');

app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'unsafe-inline'"], // Minimize 'unsafe-inline'
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", "data:", "https:"],
    connectSrc: ["'self'", "https://api.buildmaterials.app"],
    fontSrc: ["'self'"],
    objectSrc: ["'none'"],
    upgradeInsecureRequests: [],
  },
}));
```

#### Client-Side Protections (React)
- **Automatic Escaping**: React escapes JSX content by default
- **Avoid dangerouslySetInnerHTML**: Use only with sanitized content
- **DOMPurify**: Sanitize HTML when necessary

```javascript
import DOMPurify from 'dompurify';

// If must render user HTML
const cleanHTML = DOMPurify.sanitize(userInput);
<div dangerouslySetInnerHTML={{ __html: cleanHTML }} />
```

---

### Cross-Site Request Forgery (CSRF) Protection

**Strategy:** CSRF tokens and SameSite cookies

#### Implementation
```javascript
const csrf = require('csurf');
const csrfProtection = csrf({ cookie: true });

// Generate token
app.get('/api/csrf-token', csrfProtection, (req, res) => {
  res.json({ csrfToken: req.csrfToken() });
});

// Validate token on state-changing requests
app.post('/api/materials', csrfProtection, createMaterial);
```

#### Cookie Configuration
```javascript
res.cookie('sessionToken', token, {
  httpOnly: true,      // Not accessible via JavaScript
  secure: true,        // HTTPS only
  sameSite: 'strict',  // CSRF protection
  maxAge: 86400000,    // 24 hours
});
```

#### Mobile API Exception
- CSRF tokens not required for mobile API (JWT-based authentication sufficient)
- Origin validation for web clients

---

## 🔑 Data Protection

### Secure Credential Storage

**Strategy:** Environment variables and secret management

#### Configuration
```bash
# .env file (NEVER commit to Git)
DATABASE_URL=postgresql://user:pass@host:5432/db
REDIS_URL=redis://user:pass@host:6379
JWT_SECRET=<strong-random-secret>
ENCRYPTION_KEY=<32-byte-random-key>
API_KEYS=<third-party-api-keys>
```

#### Access Control
```javascript
// Load environment variables
require('dotenv').config();

// Access securely
const dbUrl = process.env.DATABASE_URL;

// Never log sensitive data
console.log('Connecting to database...'); // ✅
console.log(`DB URL: ${dbUrl}`); // ❌ NEVER DO THIS
```

#### Secret Rotation
- **Frequency**: Rotate secrets every 90 days
- **Process**: Generate new secret → deploy → revoke old secret
- **Emergency**: Immediate rotation if compromise suspected

---

### Environment Variable Isolation

**Strategy:** Separate configurations for dev/staging/production

```
.env.development     # Local development
.env.staging         # Staging environment
.env.production      # Production (stored in hosting platform securely)
```

#### Production Secret Management
- **Fly.io**: `fly secrets set JWT_SECRET=...`
- **Vercel**: Environment variables in project settings
- **Supabase**: API keys in dashboard (never in code)

---

### API Key Management

**Best Practices:**
1. **Separate Keys**: Different keys for dev/production
2. **Rotation**: Regular rotation schedule
3. **Minimal Permissions**: Scope keys to minimum required access
4. **Monitoring**: Log and alert on suspicious API key usage
5. **Revocation**: Immediate revocation process for compromised keys

---

### Logging & Monitoring

**Strategy:** Log security events without exposing sensitive data

#### What to Log
- ✅ Authentication attempts (success/failure)
- ✅ Authorization failures
- ✅ Rate limit violations
- ✅ Unusual API usage patterns
- ✅ Data access by admins
- ✅ Configuration changes

#### What NOT to Log
- ❌ Passwords (plain or hashed)
- ❌ JWT tokens
- ❌ API keys
- ❌ Personal identification numbers
- ❌ Credit card information

#### Implementation
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

// Security event
logger.info('Authentication attempt', {
  event: 'auth_attempt',
  user_email: email, // OK to log
  ip_address: req.ip,
  user_agent: req.headers['user-agent'],
  success: false,
  reason: 'invalid_password',
  // password: password, // ❌ NEVER LOG
});
```

---

## ☁️ Infrastructure Security

### Cloud-Hosted Security

**Supabase (Database):**
- Row-Level Security (RLS) policies
- Automatic encrypted backups
- Connection pooling with SSL
- IP allowlist (production)

**Redis Cloud (Caching):**
- TLS encryption in transit
- Password authentication
- Memory limits prevent DoS
- Automatic failover (paid tiers)

**Fly.io (Backend API):**
- Automatic HTTPS certificates
- Private networking between services
- Secrets encrypted at rest
- Deployment rollback capability

**Vercel (Web Frontend):**
- Automatic HTTPS/TLS
- DDoS protection via CDN
- Preview deployments (isolated)
- Environment variable encryption

---

### CI/CD Pipeline Security

**GitHub Actions Security:**

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Secrets from GitHub Secrets (encrypted)
      - name: Deploy
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          JWT_SECRET: ${{ secrets.JWT_SECRET }}
        run: npm run deploy
```

**Best Practices:**
- ✅ Secrets stored in GitHub Secrets (encrypted)
- ✅ Minimal permissions for workflow tokens
- ✅ Dependency scanning (Dependabot)
- ✅ Code scanning (CodeQL) for vulnerabilities
- ✅ Branch protection (require PR reviews)

---

### Environment-Based Configuration

**Strategy:** Different security postures for dev vs. production

| Feature | Development | Production |
|---------|------------|------------|
| HTTPS | Optional (localhost) | Mandatory |
| Debug Logging | Verbose | Minimal |
| CORS | Permissive | Restricted |
| Rate Limiting | Lenient | Strict |
| Error Messages | Detailed stack traces | Generic messages |
| Authentication | Optional (test users) | Mandatory |

---

## 🔍 Security Audit Plan

### Week 10: Comprehensive Security Audit

**Audit Checklist:**

#### Authentication & Authorization
- [ ] JWT implementation secure (strong secret, proper expiry)
- [ ] Password hashing using bcrypt/Argon2 (cost factor 12+)
- [ ] Token blacklist functional
- [ ] RBAC correctly enforced on all endpoints
- [ ] Session management secure

#### API Security
- [ ] All endpoints use HTTPS (production)
- [ ] Rate limiting active and effective
- [ ] Input validation comprehensive
- [ ] CORS configured correctly
- [ ] Error messages don't leak sensitive info

#### Injection Prevention
- [ ] SQL injection protection (parameterized queries)
- [ ] XSS protection (output encoding, CSP)
- [ ] CSRF protection (tokens for state-changing operations)
- [ ] No command injection vulnerabilities
- [ ] No path traversal vulnerabilities

#### Data Protection
- [ ] Secrets stored securely (not in code)
- [ ] Environment variables properly isolated
- [ ] Logging doesn't expose sensitive data
- [ ] Database encryption at rest
- [ ] TLS for all data in transit

#### Infrastructure
- [ ] Dependencies up to date (no known CVEs)
- [ ] Minimal permissions for service accounts
- [ ] Backups encrypted and tested
- [ ] Monitoring and alerting functional
- [ ] Incident response plan documented

---

### Automated Security Scanning

**Tools:**
1. **npm audit / pip-audit**: Dependency vulnerability scanning
2. **Snyk**: Continuous security monitoring
3. **OWASP ZAP**: Web application security testing
4. **GitLeaks**: Secret detection in commits
5. **SonarQube**: Code quality and security analysis

---

## 📋 Security Best Practices Summary

### Development Practices
1. ✅ **Principle of Least Privilege**: Grant minimum necessary permissions
2. ✅ **Defense in Depth**: Multiple layers of security
3. ✅ **Fail Securely**: Default to deny access on errors
4. ✅ **Separation of Concerns**: Security logic isolated and testable
5. ✅ **Secure by Default**: Security features enabled out of the box

### Code Review Checklist
- [ ] No hardcoded secrets or credentials
- [ ] Input validation on all user data
- [ ] Output encoding to prevent XSS
- [ ] Parameterized queries to prevent SQL injection
- [ ] Authentication required for sensitive operations
- [ ] Authorization checks before data access
- [ ] Error handling doesn't leak sensitive info
- [ ] Logging excludes passwords and tokens

### Deployment Checklist
- [ ] HTTPS enabled and enforced
- [ ] Secrets stored in secure vault (not .env in repo)
- [ ] Rate limiting configured
- [ ] CORS properly restricted
- [ ] CSP headers configured
- [ ] Security headers enabled (Helmet.js)
- [ ] Monitoring and alerting active
- [ ] Backup and recovery tested

---

## 🚨 Incident Response

### Security Incident Process

1. **Detection**: Monitoring alerts or user report
2. **Containment**: Isolate affected systems, revoke compromised credentials
3. **Investigation**: Determine scope and root cause
4. **Remediation**: Fix vulnerability, deploy patch
5. **Communication**: Notify affected users (if applicable)
6. **Post-Mortem**: Document lessons learned, improve processes

### Contact Information
- **Security Issues**: Report via GitHub Security Advisory (private disclosure)
- **Emergency**: Contact project team through academy

---

## 📚 Security Resources

### Industry Standards
- **OWASP Top 10**: https://owasp.org/www-project-top-ten/
- **OWASP API Security**: https://owasp.org/www-project-api-security/
- **CWE Top 25**: https://cwe.mitre.org/top25/

### Training & Awareness
- Team security training: Before production deployment
- Regular security updates: Monthly team meetings
- Vulnerability disclosure program: Post-launch

---

## 🎓 Academic Context

Security requirements fulfill Informatics 3 project objectives:
- ✅ Authentication & authorization implementation
- ✅ API security best practices
- ✅ Data protection mechanisms
- ✅ Industry-standard security architecture
- ✅ Security audit and testing

---

**Last Updated:** March 9, 2026  
**Security Review Schedule:** Weekly during development, comprehensive audit Week 10  
**Next Security Audit:** May 2026 (Post-MVP)

---

**Security is Everyone's Responsibility**  
If you discover a security vulnerability, please report it responsibly through GitHub Security Advisories.
