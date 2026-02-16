# 🚀 Deployment Documentation

**Building Materials Price Intelligence Platform**  
**Comprehensive Deployment Guide**

---

## 📋 Overview

This document provides detailed instructions for deploying the Building Materials Price Intelligence Platform across development, staging, and production environments. It covers infrastructure setup, configuration, CI/CD pipelines, and operational procedures.

**Deployment Philosophy:** Safe, automated, and reversible deployments with zero downtime.

---

## 🌍 Environments

### Environment Overview

| Environment | Purpose | URL | Branch | Auto-Deploy |
|------------|---------|-----|--------|-------------|
| **Development** | Local development | http://localhost:3000 | feature/* | No |
| **Staging** | Pre-production testing | https://staging.buildmaterials.app | develop | Yes |
| **Production** | Live system | https://buildmaterials.app | main | Manual approval |

---

## 🏗️ Infrastructure Setup

### Prerequisites

- **Node.js** 18+ (backend, web)
- **Flutter** 3.x (mobile)
- **PostgreSQL** 14+ (database)
- **Redis** 6+ (caching)
- **Git** (version control)

### Platform Accounts Required

- ✅ **Vercel** (web frontend hosting)
- ✅ **Fly.io** (backend API hosting)
- ✅ **Supabase** (PostgreSQL database)
- ✅ **Redis Cloud** (caching service)
- ✅ **GitHub** (code repository, CI/CD)
- ✅ **Kaggle** (ML training)

---

## 🗄️ Database Setup (Supabase)

### Initial Setup

1. **Create Supabase Project**
   ```bash
   # Visit https://supabase.com/dashboard
   # Click "New Project"
   # Fill in:
   #   - Name: buildmat-production
   #   - Database Password: <strong-password>
   #   - Region: Nearest to users (e.g., AWS ap-southeast-2 for SA)
   ```

2. **Get Connection Details**
   ```bash
   # From Supabase Dashboard > Settings > Database
   # Copy the connection string:
   # postgresql://postgres:[password]@db.xxx.supabase.co:5432/postgres
   ```

3. **Run Initial Migrations**
   ```bash
   # Set database URL
   export DATABASE_URL="postgresql://postgres:..."

   # Run migrations
   npx prisma migrate deploy

   # Verify tables created
   psql $DATABASE_URL -c "\dt"
   ```

4. **Enable PostGIS Extension**
   ```sql
   -- In Supabase SQL Editor
   CREATE EXTENSION IF NOT EXISTS postgis;
   ```

5. **Configure Row-Level Security**
   ```sql
   -- Enable RLS on sensitive tables
   ALTER TABLE users ENABLE ROW LEVEL SECURITY;
   
   -- Create policies (see SECURITY.md for details)
   ```

### Backup Configuration

```bash
# Automatic backups (included in Supabase free tier)
# Daily backups retained for 7 days

# Manual backup
pg_dump $DATABASE_URL > backup_$(date +%Y%m%d_%H%M%S).sql

# Restore from backup
psql $DATABASE_URL < backup_20260315.sql
```

---

## ⚡ Redis Setup (Redis Cloud)

### Initial Setup

1. **Create Redis Cloud Account**
   ```bash
   # Visit https://redis.com/try-free/
   # Sign up for free tier (30MB)
   ```

2. **Create Database**
   ```
   - Name: buildmat-cache
   - Region: Same as application (Johannesburg/nearby)
   - Cloud: AWS
   - Memory: 30MB (free tier)
   - Enable TLS: Yes
   ```

3. **Get Connection String**
   ```bash
   # Format: redis://default:password@endpoint:port
   # Save as REDIS_URL environment variable
   ```

4. **Test Connection**
   ```bash
   redis-cli -u $REDIS_URL ping
   # Expected: PONG
   ```

### Redis Configuration

```javascript
// config/redis.js
const redis = require('redis');

const client = redis.createClient({
  url: process.env.REDIS_URL,
  socket: {
    tls: true,
    rejectUnauthorized: false  // For development only
  }
});

client.on('error', (err) => console.error('Redis error:', err));
client.on('connect', () => console.log('Redis connected'));

module.exports = client;
```

---

## 🖥️ Backend API Deployment (Fly.io)

### Initial Setup

1. **Install Fly CLI**
   ```bash
   # macOS
   brew install flyctl

   # Linux
   curl -L https://fly.io/install.sh | sh

   # Windows
   powershell -Command "iwr https://fly.io/install.ps1 -useb | iex"
   ```

2. **Login to Fly.io**
   ```bash
   flyctl auth login
   ```

3. **Initialize Fly App**
   ```bash
   cd backend
   flyctl launch
   
   # Follow prompts:
   # - App name: buildmat-api
   # - Region: Johannesburg (jnb)
   # - Database: No (using Supabase)
   # - Deploy now: No
   ```

4. **Configure fly.toml**
   ```toml
   # fly.toml
   app = "buildmat-api"
   primary_region = "jnb"

   [build]
     dockerfile = "Dockerfile"

   [env]
     NODE_ENV = "production"
     PORT = "8080"

   [[services]]
     internal_port = 8080
     protocol = "tcp"

     [[services.ports]]
       port = 80
       handlers = ["http"]
       force_https = true

     [[services.ports]]
       port = 443
       handlers = ["tls", "http"]

     [services.concurrency]
       type = "connections"
       hard_limit = 25
       soft_limit = 20

   [[services.http_checks]]
     interval = "30s"
     timeout = "10s"
     grace_period = "5s"
     method = "GET"
     path = "/health"
   ```

5. **Set Secrets**
   ```bash
   # Set environment variables as secrets
   flyctl secrets set \
     DATABASE_URL="postgresql://..." \
     REDIS_URL="redis://..." \
     JWT_SECRET="..." \
     NODE_ENV="production"
   ```

6. **Create Dockerfile**
   ```dockerfile
   # Dockerfile
   FROM node:18-alpine

   WORKDIR /app

   # Copy package files
   COPY package*.json ./

   # Install dependencies
   RUN npm ci --only=production

   # Copy source code
   COPY . .

   # Expose port
   EXPOSE 8080

   # Health check
   HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
     CMD node healthcheck.js

   # Start application
   CMD ["node", "src/server.js"]
   ```

7. **Deploy**
   ```bash
   flyctl deploy
   ```

8. **Verify Deployment**
   ```bash
   # Check status
   flyctl status

   # View logs
   flyctl logs

   # Test endpoint
   curl https://buildmat-api.fly.dev/health
   ```

### Scaling Configuration

```bash
# Scale to 2 instances for high availability
flyctl scale count 2

# Scale VM size (if needed)
flyctl scale vm shared-cpu-1x
```

---

## 🌐 Frontend Deployment (Vercel)

### Initial Setup

1. **Install Vercel CLI**
   ```bash
   npm install -g vercel
   ```

2. **Login to Vercel**
   ```bash
   vercel login
   ```

3. **Link Project**
   ```bash
   cd web
   vercel link
   
   # Follow prompts:
   # - Scope: Your account
   # - Link to existing project: No
   # - Project name: buildmat-web
   ```

4. **Configure vercel.json**
   ```json
   {
     "buildCommand": "npm run build",
     "outputDirectory": "build",
     "devCommand": "npm start",
     "installCommand": "npm install",
     "framework": "create-react-app",
     "env": {
       "REACT_APP_API_URL": "@api_url_production"
     },
     "regions": ["jnb1"],
     "headers": [
       {
         "source": "/(.*)",
         "headers": [
           {
             "key": "X-Content-Type-Options",
             "value": "nosniff"
           },
           {
             "key": "X-Frame-Options",
             "value": "DENY"
           },
           {
             "key": "X-XSS-Protection",
             "value": "1; mode=block"
           }
         ]
       }
     ]
   }
   ```

5. **Set Environment Variables**
   ```bash
   # Production
   vercel env add REACT_APP_API_URL production
   # Enter: https://buildmat-api.fly.dev

   # Staging
   vercel env add REACT_APP_API_URL preview
   # Enter: https://staging-api.fly.dev
   ```

6. **Deploy**
   ```bash
   # Deploy to production
   vercel --prod

   # Deploy to preview (staging)
   vercel
   ```

7. **Configure Custom Domain**
   ```bash
   vercel domains add buildmaterials.app
   
   # Follow DNS instructions
   # Add CNAME record: buildmaterials.app -> cname.vercel-dns.com
   ```

### Build Optimization

```json
// package.json
{
  "scripts": {
    "build": "react-scripts build",
    "build:analyze": "source-map-explorer 'build/static/js/*.js'",
    "postbuild": "npm run build:analyze"
  }
}
```

---

## 📱 Mobile App Deployment

### iOS App Store

1. **Prerequisites**
   - Apple Developer Account ($99/year)
   - Xcode (latest version)
   - Physical iOS device for testing

2. **Configure App**
   ```bash
   cd mobile/ios
   open Runner.xcworkspace
   
   # In Xcode:
   # - Update Bundle Identifier
   # - Configure signing certificates
   # - Set deployment target (iOS 14+)
   ```

3. **Build for Release**
   ```bash
   flutter build ios --release
   ```

4. **Create Archive**
   ```
   # In Xcode:
   # Product > Archive
   # Wait for archive to complete
   ```

5. **Upload to App Store Connect**
   ```
   # In Xcode Organizer:
   # Select archive
   # Click "Distribute App"
   # Choose "App Store Connect"
   # Follow prompts
   ```

6. **Submit for Review**
   ```
   # In App Store Connect:
   # - Add screenshots
   # - Write app description
   # - Set pricing
   # - Submit for review
   ```

### Android Play Store

1. **Prerequisites**
   - Google Play Developer Account ($25 one-time)
   - Android Studio

2. **Generate Signing Key**
   ```bash
   keytool -genkey -v -keystore ~/buildmat-release-key.jks \
     -keyalg RSA -keysize 2048 -validity 10000 \
     -alias buildmat
   ```

3. **Configure Signing**
   ```properties
   # android/key.properties
   storePassword=<password>
   keyPassword=<password>
   keyAlias=buildmat
   storeFile=<path-to-jks>
   ```

   ```gradle
   // android/app/build.gradle
   android {
     signingConfigs {
       release {
         keyAlias keystoreProperties['keyAlias']
         keyPassword keystoreProperties['keyPassword']
         storeFile file(keystoreProperties['storeFile'])
         storePassword keystoreProperties['storePassword']
       }
     }
     buildTypes {
       release {
         signingConfig signingConfigs.release
       }
     }
   }
   ```

4. **Build APK/AAB**
   ```bash
   # Build AAB (recommended for Play Store)
   flutter build appbundle --release

   # Build APK (for direct distribution)
   flutter build apk --release --split-per-abi
   ```

5. **Upload to Play Console**
   ```
   # Visit https://play.google.com/console
   # Select app
   # Production > Create new release
   # Upload AAB file
   # Fill in release notes
   # Review and roll out
   ```

---

## 🔄 CI/CD Pipeline (GitHub Actions)

### Complete Workflow

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches:
      - main
      - develop
  pull_request:
    branches:
      - main

env:
  NODE_VERSION: '18'
  FLUTTER_VERSION: '3.16.0'

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run tests
        run: npm run test:ci
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info

  deploy-backend:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Fly.io CLI
        uses: superfly/flyctl-actions/setup-flyctl@master
      
      - name: Deploy to Fly.io
        run: flyctl deploy --remote-only
        env:
          FLY_API_TOKEN: ${{ secrets.FLY_API_TOKEN }}
        working-directory: ./backend
      
      - name: Verify deployment
        run: |
          sleep 10
          curl -f https://buildmat-api.fly.dev/health || exit 1

  deploy-frontend:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
      
      - name: Install dependencies
        working-directory: ./web
        run: npm ci
      
      - name: Build
        working-directory: ./web
        run: npm run build
        env:
          REACT_APP_API_URL: https://buildmat-api.fly.dev
      
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          working-directory: ./web
          vercel-args: '--prod'

  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Similar to production but deploy to staging
      - name: Deploy backend to staging
        run: flyctl deploy --remote-only --config fly.staging.toml
        env:
          FLY_API_TOKEN: ${{ secrets.FLY_API_TOKEN }}
        working-directory: ./backend
      
      - name: Deploy frontend to staging
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          working-directory: ./web

  notify:
    needs: [deploy-backend, deploy-frontend]
    if: always()
    runs-on: ubuntu-latest
    steps:
      - name: Send notification
        run: |
          # Send Slack/Discord/Email notification
          echo "Deployment completed"
```

---

## 🔐 Environment Variables

### Backend (.env)

```bash
# Database
DATABASE_URL=postgresql://postgres:password@host:5432/dbname

# Redis
REDIS_URL=redis://default:password@host:6379

# Authentication
JWT_SECRET=your-super-secret-jwt-key
JWT_EXPIRY=24h

# API Configuration
PORT=8080
NODE_ENV=production
CORS_ORIGIN=https://buildmaterials.app

# External Services
SCRAPER_USER_AGENT=BuildMat-Bot/1.0
OCR_API_KEY=your-ocr-api-key

# Monitoring
LOG_LEVEL=info
```

### Frontend (.env)

```bash
# API
REACT_APP_API_URL=https://buildmat-api.fly.dev

# Environment
REACT_APP_ENV=production

# Analytics (optional)
REACT_APP_GA_TRACKING_ID=UA-XXXXXXXXX-X
```

### Mobile (.env)

```dart
// lib/config/environment.dart
class Environment {
  static const String apiUrl = String.fromEnvironment(
    'API_URL',
    defaultValue: 'https://buildmat-api.fly.dev'
  );
  
  static const String environment = String.fromEnvironment(
    'ENVIRONMENT',
    defaultValue: 'production'
  );
}
```

---

## 📊 Monitoring & Health Checks

### Health Check Endpoint

```javascript
// backend/src/routes/health.js
const express = require('express');
const router = express.Router();
const db = require('../config/database');
const redis = require('../config/redis');

router.get('/health', async (req, res) => {
  const health = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    version: process.env.npm_package_version,
  };

  try {
    // Check database
    await db.query('SELECT 1');
    health.database = 'connected';
  } catch (error) {
    health.status = 'degraded';
    health.database = 'disconnected';
  }

  try {
    // Check Redis
    await redis.ping();
    health.redis = 'connected';
  } catch (error) {
    health.status = 'degraded';
    health.redis = 'disconnected';
  }

  const statusCode = health.status === 'ok' ? 200 : 503;
  res.status(statusCode).json(health);
});

module.exports = router;
```

### Uptime Monitoring

**Setup Uptime Robot (Free):**
1. Visit https://uptimerobot.com
2. Add HTTP(s) monitor:
   - Name: BuildMat API
   - URL: https://buildmat-api.fly.dev/health
   - Interval: 5 minutes
3. Configure alerts (email/SMS)

---

## 🔄 Rollback Procedures

### Backend Rollback

```bash
# List recent releases
flyctl releases

# Rollback to previous version
flyctl releases rollback

# Rollback to specific version
flyctl releases rollback --version 42
```

### Frontend Rollback

```bash
# List deployments
vercel ls

# Rollback to previous deployment
vercel rollback <deployment-url>

# Promote specific deployment to production
vercel promote <deployment-url> --scope <team-name>
```

### Database Rollback

```bash
# Restore from backup
pg_restore -d $DATABASE_URL backup_20260315.sql

# Or use Supabase dashboard:
# Settings > Database > Point-in-time Recovery
```

---

## 📋 Deployment Checklist

### Pre-Deployment

- [ ] All tests passing (unit, integration, E2E)
- [ ] Code reviewed and approved
- [ ] Database migrations tested on staging
- [ ] Environment variables configured
- [ ] Secrets rotated (if needed)
- [ ] Backup created
- [ ] Deployment plan documented
- [ ] Rollback plan prepared
- [ ] Team notified of deployment

### During Deployment

- [ ] Monitor deployment logs
- [ ] Verify health checks passing
- [ ] Run smoke tests
- [ ] Check error rates
- [ ] Monitor performance metrics
- [ ] Verify database migrations successful

### Post-Deployment

- [ ] Verify application functionality
- [ ] Check all critical user flows
- [ ] Monitor error logs (30 minutes)
- [ ] Review performance metrics
- [ ] Update deployment log
- [ ] Notify team of completion
- [ ] Close deployment window

---

## 🚨 Incident Response

### Severity Levels

| Level | Description | Response Time | Example |
|-------|-------------|---------------|---------|
| **P0 - Critical** | Complete outage | Immediate | API down, database unavailable |
| **P1 - High** | Major feature broken | 1 hour | Login failing, payments broken |
| **P2 - Medium** | Feature degraded | 4 hours | Slow queries, caching issues |
| **P3 - Low** | Minor issue | 24 hours | UI glitch, non-critical bug |

### Incident Response Steps

1. **Detect**: Monitoring alert or user report
2. **Assess**: Determine severity and impact
3. **Notify**: Alert team via Slack/PagerDuty
4. **Investigate**: Review logs and metrics
5. **Mitigate**: Apply hotfix or rollback
6. **Resolve**: Deploy permanent fix
7. **Post-mortem**: Document and learn

---

## 📚 Additional Resources

- [Fly.io Documentation](https://fly.io/docs/)
- [Vercel Documentation](https://vercel.com/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [Redis Cloud Documentation](https://docs.redis.com/latest/rc/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [System Architecture](./docs/architecture/SYSTEM_ARCHITECTURE.md)
- [Security Documentation](./SECURITY.md)
- [Testing Documentation](./TESTING.md)

---

**Last Updated:** March 9, 2026  
**Deployment Platform:** Fly.io + Vercel + Supabase  
**Next Review:** Post-MVP (June 2026)
