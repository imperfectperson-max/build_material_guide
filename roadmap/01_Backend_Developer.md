# 👨‍💻 Backend Developer & Security Lead — Detailed Roadmap

**Team Member:** Member 1  
**Primary Tech Stack:** Node.js, PostgreSQL (Supabase), Redis, JWT  
**Timeline:** March 9 – June 9, 2026 (13 Weeks)

---

## 🚀 Getting Started — March 8, 2026

Complete this setup before Week 1 begins on March 9.

### System Requirements
- **OS:** Windows 10/11, macOS 10.15+, or Ubuntu 20.04+
- **RAM:** Minimum 8 GB (16 GB recommended)
- **Storage:** At least 10 GB free

### Step 1 – Install Node.js

```bash
# Download LTS from: https://nodejs.org/  (v20.x recommended)
node --version   # Must be v18.x or v20.x
npm --version    # Must be 9.x+
```

### Step 2 – Install Git

```bash
# macOS
brew install git

# Ubuntu/Debian
sudo apt update && sudo apt install git

git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Step 3 – Create Project Structure

```bash
mkdir -p ~/projects/buildmat-backend
cd ~/projects/buildmat-backend
git init

mkdir -p src/{routes,controllers,services,middleware,config,utils}
mkdir -p tests/{unit,integration}
mkdir -p docs logs
```

### Step 4 – Initialize Node Project & Install Dependencies

```bash
npm init -y

# Core
npm install express dotenv cors helmet
npm install pg @supabase/supabase-js
npm install redis
npm install jsonwebtoken bcryptjs
npm install express-validator
npm install express-rate-limit@7   # pin to v7 — custom store interface changed in v7
npm install rate-limit-redis       # official Redis store for express-rate-limit v7
npm install joi winston axios
npm install response-time swagger-jsdoc swagger-ui-express

# Dev
npm install --save-dev nodemon jest supertest eslint prettier
```

### Step 5 – Environment Variables

```bash
cat > .env.example << 'EOF'
# Database
SUPABASE_URL=your_supabase_url_here
SUPABASE_ANON_KEY=your_supabase_anon_key_here
DATABASE_URL=postgresql://user:password@host:port/database

# Redis
REDIS_URL=redis://default:password@host:6379

# JWT
JWT_SECRET=your_super_secret_jwt_key_minimum_32_chars
JWT_EXPIRY=24h

# Server
PORT=3000
NODE_ENV=development

# ML Service (Member 2's inference endpoint)
ML_SERVICE_URL=http://localhost:8000

# Rate Limiting
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_MAX_REQUESTS=100
EOF

cp .env.example .env
```

### Step 6 – .gitignore

```bash
cat > .gitignore << 'EOF'
node_modules/
.env
.env.local
dist/
build/
coverage/
logs/
*.log
.DS_Store
EOF
```

### Step 7 – package.json Scripts

```bash
npm pkg set scripts.start="node src/server.js"
npm pkg set scripts.dev="nodemon src/server.js"
npm pkg set scripts.test="jest --runInBand"
npm pkg set scripts.lint="eslint src/"
npm pkg set scripts.format="prettier --write 'src/**/*.js'"
```

### Step 8 – Basic Server

```javascript
// src/server.js
require('dotenv').config();
const express  = require('express');
const cors     = require('cors');
const helmet   = require('helmet');

const app  = express();
const PORT = process.env.PORT || 3000;

app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '5mb' }));  // allow bulk-import payloads

app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() });
});

app.listen(PORT, () => {
  console.log(`🚀 Server running on port ${PORT} [${process.env.NODE_ENV}]`);
});

module.exports = app;
```

### Step 9 – Test Setup

```bash
npm run dev
curl http://localhost:3000/health
# Expected: {"status":"OK","timestamp":"..."}
```

### Setup Checklist ✅
- [ ] Node.js v18+ installed
- [ ] Git configured
- [ ] Project structure created
- [ ] All packages installed successfully
- [ ] `.env` created from template
- [ ] Server starts and `/health` returns 200

---

## 🗄️ Database Configuration

```javascript
// src/config/database.js
const { Pool } = require('pg');
require('dotenv').config();

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: process.env.NODE_ENV === 'production'
    ? { rejectUnauthorized: false }
    : false,
  max: 20,               // connection pool size
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

pool.on('error', (err) => {
  console.error('Unexpected pool error:', err);
});

module.exports = pool;
```

```javascript
// src/config/db-helpers.js
const pool = require('./database');

const query = async (text, params) => {
  const start = Date.now();
  const res   = await pool.query(text, params);
  const ms    = Date.now() - start;
  if (ms > 500) console.warn(`Slow query (${ms}ms):`, text.slice(0, 80));
  return res;
};

module.exports = { query, pool };
```

## 🔴 Redis Configuration

```javascript
// src/config/redis.js
const redis = require('redis');
require('dotenv').config();

const client = redis.createClient({
  url: process.env.REDIS_URL,
  socket: {
    reconnectStrategy: (retries) => Math.min(retries * 50, 2000),
  },
});

client.on('error',   (err) => console.error('Redis error:', err));
client.on('connect', ()    => console.log('✅ Redis connected'));

(async () => { await client.connect(); })();

module.exports = client;
```

---

## 📋 Canonical Schema Reference

Every endpoint, table, and cache key in this plan uses the same 16 fields:

```
record_id, date, year, month, material_name, material_category,
supplier_name, region, province, price_zar, unit, price_per_kg_zar,
price_change_mom_pct, price_change_yoy_pct, stock_status, bulk_discount_available
```

Valid enums:
- `stock_status`: `'In Stock'` | `'Low Stock'` | `'Out of Stock'`
- `bulk_discount_available`: `'Yes'` | `'No'`

---

## 📅 WEEK 1 — March 9–15, 2026
### Database Foundation

#### Monday (March 9) — Supabase + Schema Design

1. Create Supabase project at supabase.com
2. Copy `DATABASE_URL` into `.env`
3. Design schema (see Tuesday's SQL)
4. Share schema diagram with team for feedback

**Deliverable:** Database schema design document.

#### Tuesday (March 10) — Full Schema Implementation

```sql
-- schema.sql
-- Run this in Supabase SQL Editor

-- ── Extensions ────────────────────────────────────────────────────────────────
CREATE EXTENSION IF NOT EXISTS postgis;

-- ── Users ─────────────────────────────────────────────────────────────────────
CREATE TABLE users (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email         VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name          VARCHAR(255),
  role          VARCHAR(50)  NOT NULL DEFAULT 'user'
                  CHECK (role IN ('user', 'admin')),
  active        BOOLEAN DEFAULT true,
  last_login    TIMESTAMP,
  created_at    TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at    TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ── Prices ────────────────────────────────────────────────────────────────────
-- Primary data table. Maps directly to the canonical 16-column schema.
-- record_id is the scraper-generated unique key (upsert anchor).
-- No separate materials/suppliers tables — material_name and supplier_name
-- are plain strings, matching the schema contract with Member 4's scrapers
-- and Member 2's ML pipeline.
CREATE TABLE prices (
  -- Core schema fields (16 columns)
  record_id               VARCHAR(50)    PRIMARY KEY,
  date                    DATE           NOT NULL,
  year                    INTEGER        NOT NULL,
  month                   INTEGER        NOT NULL CHECK (month BETWEEN 1 AND 12),
  material_name           VARCHAR(255)   NOT NULL,
  material_category       VARCHAR(100)   NOT NULL,
  supplier_name           VARCHAR(255)   NOT NULL,
  region                  VARCHAR(100)   NOT NULL,
  province                VARCHAR(100)   NOT NULL,
  price_zar               DECIMAL(10,2)  NOT NULL CHECK (price_zar >= 0),
  unit                    VARCHAR(50)    NOT NULL,
  price_per_kg_zar        DECIMAL(10,4)  NOT NULL CHECK (price_per_kg_zar >= 0),
  price_change_mom_pct    DECIMAL(10,2),
  price_change_yoy_pct    DECIMAL(10,2),
  stock_status            VARCHAR(20)    NOT NULL
                            CHECK (stock_status IN ('In Stock','Low Stock','Out of Stock')),
  bulk_discount_available VARCHAR(3)     NOT NULL
                            CHECK (bulk_discount_available IN ('Yes','No')),
  -- Metadata
  created_at              TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at              TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

COMMENT ON TABLE prices IS
  'Price records in the canonical 16-column schema. '
  'record_id is the scraper-generated primary key used for upserts. '
  'material_name and supplier_name are plain strings — no FK to separate tables.';

-- ── Forecasts ─────────────────────────────────────────────────────────────────
-- Uses material_name (string) to stay consistent with the prices table.
-- No FK to a materials table — lookup by name matches how ML outputs work.
CREATE TABLE forecasts (
  id                    BIGSERIAL      PRIMARY KEY,
  material_name         VARCHAR(255)   NOT NULL,
  material_category     VARCHAR(100),
  forecast_date         DATE           NOT NULL,
  predicted_price       DECIMAL(10,2)  NOT NULL,
  confidence_lower      DECIMAL(10,2),   -- lower bound of confidence interval
  confidence_upper      DECIMAL(10,2),   -- upper bound of confidence interval
  confidence_level      DECIMAL(5,4)
                          CHECK (confidence_level BETWEEN 0 AND 1),
  model_name            VARCHAR(100),
  model_version         VARCHAR(50),
  region                VARCHAR(100),    -- NULL = national/all-region forecast
  generated_at          TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_at            TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  -- One forecast per (material, date, model, region)
  UNIQUE (material_name, forecast_date, model_name, region)
);

COMMENT ON TABLE forecasts IS
  'ML price forecasts. Uses material_name (string) for consistency with prices table. '
  'confidence_lower/upper are the interval bounds — not confidence_interval_lower/upper.';

-- ── Favorites ─────────────────────────────────────────────────────────────────
CREATE TABLE favorites (
  user_id       UUID         REFERENCES users(id) ON DELETE CASCADE,
  material_name VARCHAR(255) NOT NULL,
  created_at    TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (user_id, material_name)
);

-- ── Price Alerts ──────────────────────────────────────────────────────────────
CREATE TABLE price_alerts (
  id              BIGSERIAL     PRIMARY KEY,
  user_id         UUID          REFERENCES users(id) ON DELETE CASCADE,
  material_name   VARCHAR(255)  NOT NULL,
  threshold_price DECIMAL(10,2) NOT NULL,
  threshold_type  VARCHAR(20)   NOT NULL CHECK (threshold_type IN ('above','below')),
  region          VARCHAR(100),
  active          BOOLEAN DEFAULT true,
  last_triggered  TIMESTAMP,
  created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ── Anomalies ─────────────────────────────────────────────────────────────────
-- Detected price anomalies. Uses material_name + supplier_name strings
-- to match the prices table — no FK to a separate suppliers table.
CREATE TABLE anomalies (
  id               BIGSERIAL     PRIMARY KEY,
  material_name    VARCHAR(255)  NOT NULL,
  supplier_name    VARCHAR(255)  NOT NULL,
  price_zar        DECIMAL(10,2) NOT NULL,
  expected_price   DECIMAL(10,2),
  deviation_pct    DECIMAL(6,2),
  region           VARCHAR(100),
  date             DATE          NOT NULL,
  detected_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ── Model Performance ─────────────────────────────────────────────────────────
CREATE TABLE model_performance (
  id              BIGSERIAL    PRIMARY KEY,
  model_name      VARCHAR(100) NOT NULL,
  material_name   VARCHAR(255) NOT NULL,
  mae             DECIMAL(10,4),
  rmse            DECIMAL(10,4),
  mape            DECIMAL(10,4),
  evaluation_date DATE         NOT NULL,
  created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ── Scraping Jobs ─────────────────────────────────────────────────────────────
CREATE TABLE scraping_jobs (
  id               VARCHAR(50)  PRIMARY KEY,
  supplier_name    VARCHAR(255),            -- string, no FK
  status           VARCHAR(20)  NOT NULL
                     CHECK (status IN ('pending','running','completed','failed')),
  records_uploaded INTEGER DEFAULT 0,
  errors_count     INTEGER DEFAULT 0,
  error_log        JSONB,
  started_at       TIMESTAMP,
  completed_at     TIMESTAMP,
  created_at       TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

```sql
-- indexes.sql
-- ── Prices indexes ────────────────────────────────────────────────────────────
CREATE INDEX idx_prices_date            ON prices(date DESC);
CREATE INDEX idx_prices_material        ON prices(material_name);
CREATE INDEX idx_prices_supplier        ON prices(supplier_name);
CREATE INDEX idx_prices_region          ON prices(region);
CREATE INDEX idx_prices_province        ON prices(province);
CREATE INDEX idx_prices_category        ON prices(material_category);
CREATE INDEX idx_prices_stock           ON prices(stock_status);
CREATE INDEX idx_prices_year_month      ON prices(year, month);
-- Composite indexes for the most common query patterns
CREATE INDEX idx_prices_mat_region      ON prices(material_name, region);
CREATE INDEX idx_prices_mat_supplier    ON prices(material_name, supplier_name);
CREATE INDEX idx_prices_mat_date        ON prices(material_name, date DESC);

-- ── Forecasts indexes ─────────────────────────────────────────────────────────
CREATE INDEX idx_forecasts_material     ON forecasts(material_name);
CREATE INDEX idx_forecasts_date         ON forecasts(forecast_date);
CREATE INDEX idx_forecasts_region       ON forecasts(region);

-- ── Other indexes ─────────────────────────────────────────────────────────────
CREATE INDEX idx_alerts_user            ON price_alerts(user_id);
CREATE INDEX idx_alerts_active          ON price_alerts(active);
CREATE INDEX idx_anomalies_material     ON anomalies(material_name);
CREATE INDEX idx_anomalies_date         ON anomalies(date DESC);
CREATE INDEX idx_model_perf_name        ON model_performance(model_name);
CREATE INDEX idx_scraping_jobs_status   ON scraping_jobs(status);
```

**Deliverable:** All tables created in Supabase; test inserts pass.

#### Wednesday (March 11) — Express Server + DB Connection

```javascript
// src/server.js (full version)
require('dotenv').config();
const express      = require('express');
const cors         = require('cors');
const helmet       = require('helmet');
const responseTime = require('response-time');
const logger       = require('./config/logger');

const app  = express();
const PORT = process.env.PORT || 3000;

// ── Middleware ─────────────────────────────────────────────────────────────────
app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '5mb' }));
app.use(responseTime((req, res, ms) => {
  if (ms > 200) logger.warn(`Slow response: ${req.method} ${req.path} ${ms.toFixed(0)}ms`);
}));

// ── Routes (registered progressively each week) ────────────────────────────────
app.get('/health', require('./controllers/healthController').healthCheck);
// app.use('/api/prices',   require('./routes/prices'));     // Week 1 Fri
// app.use('/api/data',     require('./routes/data'));       // Week 1 Fri
// app.use('/api/auth',     require('./routes/auth'));       // Week 2
// app.use('/api/forecast', require('./routes/forecasts')); // Week 4
// app.use('/api/admin',    require('./routes/admin'));      // Week 2

// ── Error handler (must be last) ──────────────────────────────────────────────
app.use(require('./middleware/errorHandler'));

app.listen(PORT, () => {
  logger.info(`🚀 Server on port ${PORT} [${process.env.NODE_ENV}]`);
});

module.exports = app;
```

#### Thursday (March 12) — Prices Controller

```javascript
// src/controllers/pricesController.js
const { query } = require('../config/db-helpers');

/**
 * GET /api/prices
 * Returns records in the canonical 16-column schema.
 * Supports: material_name, material_category, supplier_name,
 *           region, province, stock_status, bulk_discount_available,
 *           start_date, end_date, page, per_page, limit
 */
const getPrices = async (req, res) => {
  try {
    const {
      material_name, material_category, supplier_name,
      region, province, stock_status, bulk_discount_available,
      start_date, end_date,
      page = 1, per_page = 30, limit,
    } = req.query;

    const conditions = [];
    const values     = [];
    let   p          = 1;

    if (material_name)           { conditions.push(`material_name = $${p++}`);           values.push(material_name); }
    if (material_category)       { conditions.push(`material_category = $${p++}`);       values.push(material_category); }
    if (supplier_name)           { conditions.push(`supplier_name = $${p++}`);           values.push(supplier_name); }
    if (region)                  { conditions.push(`region = $${p++}`);                  values.push(region); }
    if (province)                { conditions.push(`province = $${p++}`);                values.push(province); }
    if (stock_status)            { conditions.push(`stock_status = $${p++}`);            values.push(stock_status); }
    if (bulk_discount_available) { conditions.push(`bulk_discount_available = $${p++}`); values.push(bulk_discount_available); }
    if (start_date)              { conditions.push(`date >= $${p++}`);                   values.push(start_date); }
    if (end_date)                { conditions.push(`date <= $${p++}`);                   values.push(end_date); }

    const where = conditions.length ? `WHERE ${conditions.join(' AND ')}` : '';

    // Respect explicit ?limit= (used by mobile), otherwise paginate
    const pageLimit  = Math.min(parseInt(limit || per_page) || 30, 500);
    const offset     = (parseInt(page) - 1) * pageLimit;

    const countResult = await query(
      `SELECT COUNT(*) FROM prices ${where}`,
      values
    );
    const total = parseInt(countResult.rows[0].count);

    const dataResult = await query(
      `SELECT record_id, date, year, month, material_name, material_category,
              supplier_name, region, province, price_zar, unit, price_per_kg_zar,
              price_change_mom_pct, price_change_yoy_pct, stock_status, bulk_discount_available
       FROM prices
       ${where}
       ORDER BY date DESC, material_name, supplier_name
       LIMIT $${p} OFFSET $${p + 1}`,
      [...values, pageLimit, offset]
    );

    res.json({
      success: true,
      data: dataResult.rows,
      pagination: {
        page: parseInt(page),
        per_page: pageLimit,
        total,
        total_pages: Math.ceil(total / pageLimit),
      },
    });
  } catch (err) {
    console.error('getPrices error:', err);
    res.status(500).json({ success: false, error: 'Failed to fetch prices' });
  }
};

/**
 * GET /api/prices/compare?material_name=...&region=...
 * Returns the latest price per supplier, sorted cheapest first.
 */
const comparePrices = async (req, res) => {
  try {
    const { material_name, region } = req.query;
    if (!material_name) {
      return res.status(400).json({ success: false,
        error: 'material_name is required' });
    }

    const conditions = ['material_name = $1'];
    const values     = [material_name];
    if (region) { conditions.push('region = $2'); values.push(region); }

    const result = await query(
      `WITH latest AS (
         SELECT *, ROW_NUMBER() OVER (
           PARTITION BY supplier_name ORDER BY date DESC
         ) AS rn
         FROM prices
         WHERE ${conditions.join(' AND ')}
       )
       SELECT record_id, date, year, month, material_name, material_category,
              supplier_name, region, province, price_zar, unit, price_per_kg_zar,
              price_change_mom_pct, price_change_yoy_pct, stock_status, bulk_discount_available
       FROM latest
       WHERE rn = 1
       ORDER BY price_zar ASC`,
      values
    );

    if (!result.rows.length) {
      return res.status(404).json({ success: false,
        error: `No prices found for: ${material_name}` });
    }

    res.json({
      success: true,
      material_name,
      region: region || 'All regions',
      suppliers: result.rows,
    });
  } catch (err) {
    res.status(500).json({ success: false, error: 'Failed to compare prices' });
  }
};

/**
 * GET /api/prices/history?material_name=...&days=30&supplier_name=...
 * Returns daily avg/min/max aggregates for charting.
 */
const getPriceHistory = async (req, res) => {
  try {
    const { material_name, supplier_name, days = 30 } = req.query;
    if (!material_name) {
      return res.status(400).json({ success: false,
        error: 'material_name is required' });
    }

    const conditions = [
      'material_name = $1',
      `date >= CURRENT_DATE - $2::integer`,
    ];
    const values = [material_name, parseInt(days)];

    if (supplier_name) {
      conditions.push(`supplier_name = $3`);
      values.push(supplier_name);
    }

    const result = await query(
      `SELECT date,
              ROUND(AVG(price_zar)::numeric, 2) AS avg_price,
              MIN(price_zar) AS min_price,
              MAX(price_zar) AS max_price,
              COUNT(*)       AS sample_count
       FROM prices
       WHERE ${conditions.join(' AND ')}
       GROUP BY date
       ORDER BY date ASC`,
      values
    );

    res.json({ success: true, material_name, history: result.rows });
  } catch (err) {
    res.status(500).json({ success: false, error: 'Failed to fetch history' });
  }
};

module.exports = { getPrices, comparePrices, getPriceHistory };
```

#### Friday (March 13) — Prices Routes + Bulk Import

```javascript
// src/routes/prices.js
const express = require('express');
const router  = express.Router();
const { cacheMiddleware }                           = require('../middleware/cache');
const { getPrices, comparePrices, getPriceHistory } = require('../controllers/pricesController');

router.get('/',        cacheMiddleware(300),  getPrices);        // 5 min cache
router.get('/compare', cacheMiddleware(300),  comparePrices);
router.get('/history', cacheMiddleware(3600), getPriceHistory);  // 1 hr cache

module.exports = router;
```

```javascript
// src/routes/data.js
/**
 * POST /api/data/bulk-import
 *
 * Receives an array of 16-column schema records from Member 4's scraper.
 * Uses record_id as the upsert key — no duplicate check by material+supplier+date.
 * Writes to the prices table and invalidates relevant cache keys.
 */
const express = require('express');
const router  = express.Router();
const { pool } = require('../config/db-helpers');
const { invalidateCache } = require('../middleware/cache');

const REQUIRED_FIELDS = [
  'record_id', 'date', 'year', 'month', 'material_name', 'material_category',
  'supplier_name', 'region', 'province', 'price_zar', 'unit', 'price_per_kg_zar',
  'price_change_mom_pct', 'price_change_yoy_pct', 'stock_status', 'bulk_discount_available',
];
const VALID_STOCK = new Set(['In Stock', 'Low Stock', 'Out of Stock']);
const VALID_BULK  = new Set(['Yes', 'No']);

router.post('/bulk-import', async (req, res) => {
  const records = req.body;

  if (!Array.isArray(records) || records.length === 0) {
    return res.status(400).json({
      success: false,
      error: 'Payload must be a non-empty JSON array of price records',
    });
  }

  // Validate all records before touching the DB
  for (let i = 0; i < records.length; i++) {
    const r = records[i];
    const missing = REQUIRED_FIELDS.filter((f) => !(f in r));
    if (missing.length) {
      return res.status(400).json({ success: false,
        error: `Record[${i}] missing fields: ${missing.join(', ')}` });
    }
    if (!VALID_STOCK.has(r.stock_status)) {
      return res.status(400).json({ success: false,
        error: `Record[${i}] invalid stock_status: "${r.stock_status}"` });
    }
    if (!VALID_BULK.has(r.bulk_discount_available)) {
      return res.status(400).json({ success: false,
        error: `Record[${i}] invalid bulk_discount_available: "${r.bulk_discount_available}"` });
    }
  }

  const client = await pool.connect();
  try {
    await client.query('BEGIN');

    let imported = 0;
    let updated  = 0;

    for (const r of records) {
      const result = await client.query(
        `INSERT INTO prices (
           record_id, date, year, month, material_name, material_category,
           supplier_name, region, province, price_zar, unit, price_per_kg_zar,
           price_change_mom_pct, price_change_yoy_pct, stock_status, bulk_discount_available
         ) VALUES ($1,$2,$3,$4,$5,$6,$7,$8,$9,$10,$11,$12,$13,$14,$15,$16)
         ON CONFLICT (record_id) DO UPDATE SET
           date                    = EXCLUDED.date,
           year                    = EXCLUDED.year,
           month                   = EXCLUDED.month,
           material_name           = EXCLUDED.material_name,
           material_category       = EXCLUDED.material_category,
           supplier_name           = EXCLUDED.supplier_name,
           region                  = EXCLUDED.region,
           province                = EXCLUDED.province,
           price_zar               = EXCLUDED.price_zar,
           unit                    = EXCLUDED.unit,
           price_per_kg_zar        = EXCLUDED.price_per_kg_zar,
           price_change_mom_pct    = EXCLUDED.price_change_mom_pct,
           price_change_yoy_pct    = EXCLUDED.price_change_yoy_pct,
           stock_status            = EXCLUDED.stock_status,
           bulk_discount_available = EXCLUDED.bulk_discount_available,
           updated_at              = CURRENT_TIMESTAMP
         RETURNING (xmax = 0) AS is_insert`,
        [
          r.record_id, r.date, r.year, r.month,
          r.material_name, r.material_category, r.supplier_name,
          r.region, r.province, r.price_zar, r.unit, r.price_per_kg_zar,
          r.price_change_mom_pct, r.price_change_yoy_pct,
          r.stock_status, r.bulk_discount_available,
        ]
      );
      result.rows[0].is_insert ? imported++ : updated++;
    }

    await client.query('COMMIT');

    // Invalidate cache for every distinct material_name in the batch
    const names = [...new Set(records.map((r) => r.material_name))];
    await Promise.all(names.map((n) => invalidateCache(n)));

    res.json({
      success: true,
      imported,
      updated,
      total: records.length,
    });
  } catch (err) {
    await client.query('ROLLBACK');
    console.error('bulk-import error:', err);
    res.status(500).json({ success: false,
      error: 'Database error during bulk import', details: err.message });
  } finally {
    client.release();
  }
});

module.exports = router;
```

**Deliverable:** `/api/prices`, `/api/prices/compare`, `/api/prices/history`, `/api/data/bulk-import` all working.

---

## 📅 WEEK 2 — March 16–22, 2026
### Redis Caching & Authentication

#### Monday (March 16) — Cache Middleware

```javascript
// src/middleware/cache.js
const redisClient = require('../config/redis');

/**
 * Cache key includes all schema-relevant query params so that
 * material_name=Cement and material_name=Steel never collide.
 */
const buildCacheKey = (req) => {
  const SCHEMA_PARAMS = [
    'material_name', 'material_category', 'supplier_name',
    'region', 'province', 'stock_status', 'bulk_discount_available',
    'start_date', 'end_date', 'page', 'per_page', 'limit',
  ];
  const parts = [req.method, req.path];
  SCHEMA_PARAMS.forEach((k) => {
    if (req.query[k]) parts.push(`${k}:${req.query[k]}`);
  });
  return parts.join('|');
};

const cacheMiddleware = (ttl = 300) => async (req, res, next) => {
  if (req.method !== 'GET') return next();

  const key = buildCacheKey(req);
  try {
    const hit = await redisClient.get(key);
    if (hit) {
      res.setHeader('X-Cache', 'HIT');
      return res.json(JSON.parse(hit));
    }
    res.setHeader('X-Cache', 'MISS');

    const originalJson = res.json.bind(res);
    res.json = (data) => {
      redisClient.setEx(key, ttl, JSON.stringify(data))
        .catch((e) => console.error('Cache write error:', e));
      return originalJson(data);
    };
    next();
  } catch (err) {
    console.error('Cache middleware error:', err);
    next();   // degrade gracefully — never block the request
  }
};

/**
 * Invalidate all cache keys that mention a given material_name.
 * Uses SCAN to avoid blocking Redis with KEYS on large datasets.
 */
const invalidateCache = async (material_name) => {
  try {
    const pattern = `*material_name:${material_name}*`;
    let cursor = 0;
    let deleted = 0;
    do {
      const reply = await redisClient.scan(cursor, { MATCH: pattern, COUNT: 100 });
      cursor = reply.cursor;
      if (reply.keys.length) {
        await redisClient.del(reply.keys);
        deleted += reply.keys.length;
      }
    } while (cursor !== 0);

    if (deleted) console.log(`♻️  Invalidated ${deleted} keys for "${material_name}"`);
  } catch (err) {
    console.error('Cache invalidation error:', err);
  }
};

module.exports = { cacheMiddleware, buildCacheKey, invalidateCache };
```

#### Tuesday (March 17) — Authentication

```javascript
// src/controllers/authController.js
const bcrypt = require('bcryptjs');
const jwt    = require('jsonwebtoken');
const { query } = require('../config/db-helpers');
const { addToBlacklist } = require('../middleware/jwtBlacklist');

const register = async (req, res) => {
  try {
    const { email, password, name } = req.body;

    const existing = await query('SELECT id FROM users WHERE email = $1', [email]);
    if (existing.rows.length) {
      return res.status(409).json({ error: 'Email already registered' });
    }

    const hash   = await bcrypt.hash(password, 12);
    const result = await query(
      'INSERT INTO users (email, password_hash, name, role) VALUES ($1,$2,$3,$4) RETURNING id, email, role',
      [email, hash, name || null, 'user']
    );
    const user  = result.rows[0];
    const token = jwt.sign(
      { userId: user.id, role: user.role },
      process.env.JWT_SECRET,
      { expiresIn: process.env.JWT_EXPIRY || '24h' }
    );
    res.status(201).json({ token, user });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

const login = async (req, res) => {
  try {
    const { email, password } = req.body;

    const result = await query('SELECT * FROM users WHERE email = $1 AND active = true', [email]);
    if (!result.rows.length) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    const user  = result.rows[0];
    const match = await bcrypt.compare(password, user.password_hash);
    if (!match) return res.status(401).json({ error: 'Invalid credentials' });

    await query('UPDATE users SET last_login = NOW() WHERE id = $1', [user.id]);

    const token = jwt.sign(
      { userId: user.id, role: user.role },
      process.env.JWT_SECRET,
      { expiresIn: process.env.JWT_EXPIRY || '24h' }
    );
    res.json({ token, user: { id: user.id, email: user.email, role: user.role } });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

const logout = async (req, res) => {
  try {
    const token = req.headers['authorization']?.split(' ')[1];
    if (token) await addToBlacklist(token);
    res.json({ message: 'Logged out successfully' });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

module.exports = { register, login, logout };
```

#### Wednesday (March 18) — JWT Middleware + Blacklist

```javascript
// src/middleware/auth.js
const jwt = require('jsonwebtoken');
const { isBlacklisted } = require('./jwtBlacklist');

const authenticateToken = async (req, res, next) => {
  const token = req.headers['authorization']?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'Access token required' });

  if (await isBlacklisted(token)) {
    return res.status(401).json({ error: 'Token has been revoked' });
  }

  jwt.verify(token, process.env.JWT_SECRET, (err, payload) => {
    if (err) return res.status(403).json({ error: 'Invalid or expired token' });
    req.user = payload;
    next();
  });
};

const requireRole = (...roles) => (req, res, next) => {
  if (!req.user) return res.status(401).json({ error: 'Authentication required' });
  if (!roles.includes(req.user.role)) {
    return res.status(403).json({ error: 'Insufficient permissions' });
  }
  next();
};

module.exports = { authenticateToken, requireRole };
```

```javascript
// src/middleware/jwtBlacklist.js
const jwt         = require('jsonwebtoken');   // ← required: jwt.decode used below
const redisClient = require('../config/redis');

const BLACKLIST_PREFIX = 'jwt:blacklist:';

const addToBlacklist = async (token) => {
  try {
    const decoded = jwt.decode(token);
    if (!decoded?.exp) return;
    const ttl = decoded.exp - Math.floor(Date.now() / 1000);
    if (ttl > 0) {
      await redisClient.setEx(`${BLACKLIST_PREFIX}${token}`, ttl, '1');
    }
  } catch (err) {
    console.error('Blacklist error:', err);
  }
};

const isBlacklisted = async (token) => {
  try {
    const result = await redisClient.get(`${BLACKLIST_PREFIX}${token}`);
    return result !== null;
  } catch {
    return false;   // on Redis failure, fail open (don't block all requests)
  }
};

module.exports = { addToBlacklist, isBlacklisted };
```

#### Thursday (March 19) — Rate Limiting

```javascript
// src/middleware/rateLimiter.js
const rateLimit      = require('express-rate-limit');   // pinned to v7
const { RedisStore } = require('rate-limit-redis');     // official v7 store
const redisClient    = require('../config/redis');

// 100 requests / minute per IP for general API
const apiLimiter = rateLimit({
  windowMs: 60 * 1000,
  max:      100,
  standardHeaders: true,
  legacyHeaders:   false,
  store: new RedisStore({
    sendCommand: (...args) => redisClient.sendCommand(args),
    prefix: 'rl:api:',
  }),
  message: { error: 'Too many requests — please slow down.' },
});

// 5 attempts / 15 min for auth endpoints
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max:      5,
  standardHeaders: true,
  legacyHeaders:   false,
  store: new RedisStore({
    sendCommand: (...args) => redisClient.sendCommand(args),
    prefix: 'rl:auth:',
  }),
  message: { error: 'Too many login attempts — try again in 15 minutes.' },
});

module.exports = { apiLimiter, authLimiter };
```

#### Friday (March 20) — Error Handler + Validation + Logger

```javascript
// src/config/logger.js
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'logs/error.log',    level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' }),
    ...(process.env.NODE_ENV !== 'production'
      ? [new winston.transports.Console({ format: winston.format.simple() })]
      : []),
  ],
});

module.exports = logger;
```

```javascript
// src/middleware/errorHandler.js
const logger = require('../config/logger');

const errorHandler = (err, req, res, next) => {
  logger.error({ message: err.message, stack: err.stack, path: req.path });

  const status = err.status || 500;
  res.status(status).json({
    error:   err.message || 'Internal Server Error',
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  });
};

module.exports = errorHandler;
```

```javascript
// src/middleware/validation.js
const Joi = require('joi');

const validate = (schema) => (req, res, next) => {
  const { error } = schema.validate(req.body, { abortEarly: false });
  if (error) {
    return res.status(400).json({
      error:   'Validation Error',
      details: error.details.map((d) => d.message),
    });
  }
  next();
};

const schemas = {
  register: Joi.object({
    email:    Joi.string().email().required(),
    password: Joi.string().min(8).required(),
    name:     Joi.string().optional(),
  }),
  login: Joi.object({
    email:    Joi.string().email().required(),
    password: Joi.string().required(),
  }),
};

module.exports = { validate, schemas };
```

```javascript
// src/routes/auth.js
const express              = require('express');
const router               = express.Router();
const { register, login, logout } = require('../controllers/authController');
const { authenticateToken }       = require('../middleware/auth');
const { validate, schemas }       = require('../middleware/validation');
const { authLimiter }             = require('../middleware/rateLimiter');

router.post('/register', authLimiter, validate(schemas.register), register);
router.post('/login',    authLimiter, validate(schemas.login),    login);
router.post('/logout',   authenticateToken,                       logout);

module.exports = router;
```

**Deliverable:** Auth system with JWT blacklist, rate limiting, validation, logging.

---

## 📅 WEEK 3 — March 23–29, 2026
### Health Check + D1 Demo

#### Health Check Controller

```javascript
// src/controllers/healthController.js
const { pool }  = require('../config/db-helpers');
const redis     = require('../config/redis');

const healthCheck = async (req, res) => {
  const health = { status: 'ok', timestamp: new Date(), services: {} };

  // Database
  try {
    await pool.query('SELECT 1');
    health.services.database = 'ok';
  } catch {
    health.services.database = 'error';
    health.status = 'degraded';
  }

  // Redis
  try {
    await redis.ping();
    health.services.redis = 'ok';
  } catch {
    health.services.redis = 'error';
    health.status = 'degraded';
  }

  res.status(health.status === 'ok' ? 200 : 503).json(health);
};

module.exports = { healthCheck };
```

#### Cache Stats (Admin)

```javascript
// src/routes/admin.js
const express = require('express');
const router  = express.Router();
const redis   = require('../config/redis');
const { authenticateToken, requireRole } = require('../middleware/auth');

// All admin routes require admin role
router.use(authenticateToken, requireRole('admin'));

router.get('/cache-stats', async (req, res) => {
  try {
    const hits   = parseInt(await redis.get('cache:hits')   || 0);
    const misses = parseInt(await redis.get('cache:misses') || 0);
    const total  = hits + misses;
    res.json({
      hits, misses, total,
      hit_rate: total ? `${((hits / total) * 100).toFixed(1)}%` : 'n/a',
    });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

router.delete('/cache', async (req, res) => {
  try {
    await redis.flushDb();
    res.json({ message: 'Cache cleared' });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

module.exports = router;
```

#### D1 Demo — Wednesday (March 25) 🎯

Demo script:
1. `GET /health` — show database + Redis both green
2. `POST /api/auth/register` + `POST /api/auth/login` — get JWT
3. `GET /api/prices?material_name=PPC%20Cement%2050kg` — first call (MISS), second call (HIT, instant)
4. `GET /api/prices/compare?material_name=PPC%20Cement%2050kg&region=Gauteng` — supplier ranking
5. `GET /api/admin/cache-stats` — show hit rate climbing

**Deliverable:** Live demo of auth + prices + cache working end-to-end.

---

## 📅 WEEK 4 — March 30–April 5, 2026
### Forecasts + ST1 Presentation

#### ST1 Demo — April 1 🎯

Prepare architecture diagram showing: request → rate limiter → auth middleware → cache check → controller → DB, and the bulk-import scraper flow.

#### Forecasts Controller

```javascript
// src/controllers/forecastsController.js
const { query, pool } = require('../config/db-helpers');
const { invalidateCache } = require('../middleware/cache');

/**
 * POST /api/forecast
 * Member 2 (ML) POSTs predictions here after each training run.
 * Uses material_name (string) — no material_id FK required.
 */
const storeForecast = async (req, res) => {
  try {
    const { material_name, material_category, model_name, model_version, forecasts, region } = req.body;

    if (!material_name || !Array.isArray(forecasts) || !forecasts.length) {
      return res.status(400).json({ error: 'material_name and forecasts[] required' });
    }

    const client = await pool.connect();
    try {
      await client.query('BEGIN');
      let stored = 0;

      for (const f of forecasts) {
        await client.query(
          // Column names match CREATE TABLE exactly: confidence_lower / confidence_upper
          `INSERT INTO forecasts
             (material_name, material_category, forecast_date, predicted_price,
              confidence_lower, confidence_upper, confidence_level,
              model_name, model_version, region)
           VALUES ($1,$2,$3,$4,$5,$6,$7,$8,$9,$10)
           ON CONFLICT (material_name, forecast_date, model_name, region)
           DO UPDATE SET
             predicted_price   = EXCLUDED.predicted_price,
             confidence_lower  = EXCLUDED.confidence_lower,
             confidence_upper  = EXCLUDED.confidence_upper,
             model_version     = EXCLUDED.model_version,
             generated_at      = CURRENT_TIMESTAMP`,
          [
            material_name, material_category || null,
            f.date, f.price, f.lower ?? null, f.upper ?? null,
            f.confidence_level ?? null,
            model_name || null, model_version || null,
            region || null,
          ]
        );
        stored++;
      }

      await client.query('COMMIT');
      await invalidateCache(material_name);

      res.json({ success: true, stored });
    } catch (err) {
      await client.query('ROLLBACK');
      throw err;
    } finally {
      client.release();
    }
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

/**
 * GET /api/forecast?material_name=...&days=7&model_name=...&region=...
 * Returns upcoming predictions for the mobile app.
 */
const getForecasts = async (req, res) => {
  try {
    const { material_name, days = 7, model_name, region } = req.query;
    if (!material_name) {
      return res.status(400).json({ error: 'material_name is required' });
    }

    const conditions = [
      'material_name = $1',
      'forecast_date >= CURRENT_DATE',
      `forecast_date <= CURRENT_DATE + $2::integer`,
    ];
    const values = [material_name, parseInt(days)];
    let   p      = 3;

    if (model_name) { conditions.push(`model_name = $${p++}`); values.push(model_name); }
    if (region)     { conditions.push(`region = $${p++}`);     values.push(region); }

    const result = await query(
      `SELECT forecast_date, predicted_price, confidence_lower, confidence_upper,
              confidence_level, model_name, model_version, region, generated_at
       FROM forecasts
       WHERE ${conditions.join(' AND ')}
       ORDER BY forecast_date ASC`,
      values
    );

    res.json({ success: true, material_name, forecasts: result.rows });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

module.exports = { storeForecast, getForecasts };
```

```javascript
// src/routes/forecasts.js
const express = require('express');
const router  = express.Router();
const { cacheMiddleware }              = require('../middleware/cache');
const { storeForecast, getForecasts } = require('../controllers/forecastsController');
const { authenticateToken, requireRole } = require('../middleware/auth');

router.get('/',  cacheMiddleware(21600), getForecasts);   // 6 hr cache — forecasts rarely change
router.post('/', authenticateToken, requireRole('admin'), storeForecast);

module.exports = router;
```

---

## 📅 WEEK 5 — April 6–12, 2026
### ML Inference API + D2

#### D2 Demo — April 8 🎯

Show live price prediction flowing from the ML service through the backend to the mobile app.

#### ML Inference Controller

```javascript
// src/controllers/mlController.js
const axios       = require('axios');
const redisClient = require('../config/redis');   // ← import required; was missing in original
const { query }   = require('../config/db-helpers');

/**
 * GET /api/ml/predict?material_name=...&days=7&model=xgboost
 * Proxies to Member 2's Python inference service, with Redis caching.
 */
const predictPrice = async (req, res) => {
  try {
    const { material_name, days = 7, model = 'ensemble' } = req.query;
    if (!material_name) {
      return res.status(400).json({ error: 'material_name is required' });
    }

    const cacheKey = `ml:pred:${material_name}:${days}:${model}`;
    const cached   = await redisClient.get(cacheKey);
    if (cached) return res.json(JSON.parse(cached));

    // Fetch recent history to send as context to the ML service
    const history = await query(
      `SELECT date, price_zar, supplier_name, region
       FROM prices
       WHERE material_name = $1
       ORDER BY date DESC
       LIMIT 90`,
      [material_name]
    );

    const mlRes = await axios.post(
      `${process.env.ML_SERVICE_URL}/predict`,
      { material_name, historical_data: history.rows, forecast_days: parseInt(days), model },
      { timeout: 30_000 }
    );

    await redisClient.setEx(cacheKey, 21600, JSON.stringify(mlRes.data));  // 6 hr cache
    res.json(mlRes.data);
  } catch (err) {
    if (err.code === 'ECONNREFUSED') {
      return res.status(503).json({ error: 'ML service unavailable' });
    }
    res.status(500).json({ error: err.message });
  }
};

/**
 * POST /api/ml/batch-predict
 * Predict for multiple materials — runs sequentially to avoid overloading ML service.
 */
const batchPredict = async (req, res) => {
  try {
    const { material_names, days = 7, model = 'ensemble' } = req.body;
    if (!Array.isArray(material_names) || !material_names.length) {
      return res.status(400).json({ error: 'material_names array required' });
    }

    const results = [];
    for (const material_name of material_names) {
      const cacheKey = `ml:pred:${material_name}:${days}:${model}`;
      let   data     = await redisClient.get(cacheKey);

      if (data) {
        results.push({ material_name, prediction: JSON.parse(data), cached: true });
        continue;
      }

      try {
        const history = await query(
          `SELECT date, price_zar FROM prices
           WHERE material_name = $1 ORDER BY date DESC LIMIT 90`,
          [material_name]
        );
        const mlRes = await axios.post(
          `${process.env.ML_SERVICE_URL}/predict`,
          { material_name, historical_data: history.rows, forecast_days: parseInt(days), model },
          { timeout: 30_000 }
        );
        await redisClient.setEx(cacheKey, 21600, JSON.stringify(mlRes.data));
        results.push({ material_name, prediction: mlRes.data, cached: false });
      } catch (err) {
        results.push({ material_name, error: err.message });
      }
    }

    res.json({ success: true, results });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

/**
 * GET /api/ml/performance?model_name=...&material_name=...
 * Returns stored model evaluation metrics.
 */
const getModelPerformance = async (req, res) => {
  try {
    const { model_name, material_name } = req.query;
    const conditions = [];
    const values     = [];
    let   p          = 1;

    if (model_name)    { conditions.push(`model_name = $${p++}`);    values.push(model_name); }
    if (material_name) { conditions.push(`material_name = $${p++}`); values.push(material_name); }

    const where = conditions.length ? `WHERE ${conditions.join(' AND ')}` : '';
    const result = await query(
      `SELECT * FROM model_performance ${where} ORDER BY evaluation_date DESC LIMIT 100`,
      values
    );
    res.json({ success: true, metrics: result.rows });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

module.exports = { predictPrice, batchPredict, getModelPerformance };
```

```javascript
// src/routes/ml.js
const express = require('express');
const router  = express.Router();
const { predictPrice, batchPredict, getModelPerformance } = require('../controllers/mlController');

router.get('/predict',     predictPrice);
router.post('/batch',      batchPredict);
router.get('/performance', getModelPerformance);

module.exports = router;
```

#### Anomaly Detection

```javascript
// src/controllers/anomalyController.js
const { query } = require('../config/db-helpers');

/**
 * GET /api/anomalies?material_name=...&threshold=20&days=7
 * Returns prices deviating >threshold% from the 90-day average.
 * Uses material_name + supplier_name strings — no FK references needed.
 */
const detectAnomalies = async (req, res) => {
  try {
    const { material_name, threshold = 20, days = 7 } = req.query;

    const conditions = ['p.date >= CURRENT_DATE - $2::integer'];
    const values     = [parseFloat(threshold), parseInt(days)];
    let   p          = 3;

    if (material_name) {
      conditions.push(`p.material_name = $${p++}`);
      values.push(material_name);
    }

    const result = await query(
      `WITH averages AS (
         SELECT material_name,
                AVG(price_zar)    AS avg_price,
                STDDEV(price_zar) AS std_price
         FROM prices
         WHERE date >= CURRENT_DATE - 90
         GROUP BY material_name
       )
       SELECT p.record_id, p.date, p.material_name, p.supplier_name,
              p.price_zar, p.region,
              ROUND(a.avg_price::numeric, 2) AS avg_price,
              ROUND(ABS((p.price_zar - a.avg_price) / NULLIF(a.avg_price, 0) * 100)::numeric, 2)
                AS deviation_pct
       FROM prices p
       JOIN averages a ON p.material_name = a.material_name
       WHERE ${conditions.join(' AND ')}
         AND ABS((p.price_zar - a.avg_price) / NULLIF(a.avg_price, 0) * 100) > $1
       ORDER BY deviation_pct DESC
       LIMIT 100`,
      values
    );

    res.json({ success: true, threshold: `${threshold}%`, anomalies: result.rows });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
};

module.exports = { detectAnomalies };
```

---

## 📅 WEEKS 6–8 — April 13–May 3, 2026
### Mobile Support + Cache Warming + MVP

#### Cache Warmer

```javascript
// src/services/cacheWarmer.js
/**
 * Pre-populates Redis with the most common queries so the first
 * mobile app user after a deployment doesn't hit a cold cache.
 * Uses SCAN-safe patterns — no KEYS calls.
 */
const redisClient = require('../config/redis');
const { query }   = require('../config/db-helpers');

const warmCache = async () => {
  console.log('🔥 Cache warming started...');
  try {
    const materials = await query(
      `SELECT DISTINCT material_name FROM prices ORDER BY material_name`
    );

    for (const { material_name } of materials.rows) {
      // Warm the compare endpoint for Gauteng (highest traffic region)
      const result = await query(
        `WITH latest AS (
           SELECT *, ROW_NUMBER() OVER (
             PARTITION BY supplier_name ORDER BY date DESC
           ) AS rn
           FROM prices WHERE material_name = $1 AND region = 'Gauteng'
         )
         SELECT record_id, date, year, month, material_name, material_category,
                supplier_name, region, province, price_zar, unit, price_per_kg_zar,
                price_change_mom_pct, price_change_yoy_pct, stock_status, bulk_discount_available
         FROM latest WHERE rn = 1 ORDER BY price_zar ASC`,
        [material_name]
      );

      // Key must match what buildCacheKey() generates for this route
      const key = `GET|/api/prices/compare|material_name:${material_name}|region:Gauteng`;
      await redisClient.setEx(key, 300, JSON.stringify({
        success: true, material_name, region: 'Gauteng', suppliers: result.rows,
      }));
    }

    console.log(`✅ Warmed cache for ${materials.rows.length} materials`);
  } catch (err) {
    console.error('Cache warming failed:', err);
  }
};

module.exports = { warmCache };
```

Wire into server startup:

```javascript
// In src/server.js — add after app.listen(...)
const { warmCache } = require('./services/cacheWarmer');
warmCache();
setInterval(warmCache, 6 * 60 * 60 * 1000);  // re-warm every 6 hours
```

**D3 (May 1) — MVP Complete** 🎉

---

## 📅 WEEKS 9–13 — May 4–June 9, 2026
### Testing, Performance & Final Presentation

**ST2 (May 6)** — Present: cache hit rate, API p95 response times, bulk-import throughput  
**D4 (May 13)** — Final polish  
**Final (June 10)** — Championship presentation

Focus areas:
- Load testing (target: 100+ concurrent users, cached <50ms, uncached <150ms)
- Security audit against OWASP Top 10
- Cache hit rate tuning (target >70%)
- Database `EXPLAIN ANALYZE` on slow queries and index tuning
- Production deployment to Fly.io or Railway
- Full Swagger/OpenAPI documentation

---

## 🎯 Key Performance Indicators

| Metric | Target | W2 | W4 | W8 | W13 |
|---|---|---|---|---|---|
| Cache hit rate | >70% | 50% | 65% | 75% | 78% |
| Cached response | <50ms | 80ms | 60ms | 45ms | 40ms |
| Uncached response | <150ms | 200ms | 180ms | 140ms | 120ms |
| DB records | 2,000+ | 500 | 800 | 1,500 | 2,500 |
| API endpoints | 20+ | 8 | 12 | 18 | 22 |
| Test coverage | >70% | 20% | 40% | 60% | 75% |

---

## ⚠️ Bugs Fixed in This Version

| Original bug | Fix applied |
|---|---|
| `storeForecast` inserted into `confidence_interval_lower/upper` but `CREATE TABLE` defined `confidence_lower/upper` | Column names aligned — table and INSERT now both use `confidence_lower` / `confidence_upper` |
| Forecasts `UNIQUE` used `COALESCE(region, '')` which conflates NULL and empty string | Changed to `UNIQUE(material_name, forecast_date, model_name, region)` — PostgreSQL treats two NULLs as distinct in unique constraints, which is correct behaviour here |
| `anomalies` table had FK to `suppliers(id)` but the prices table dropped that FK table | `anomalies` now uses `supplier_name VARCHAR` — no FK, matches the schema |
| `dataImportController` checked for duplicates via `material_id + supplier_id + date` | Replaced entirely — single `POST /api/data/bulk-import` uses `ON CONFLICT (record_id)` upsert |
| `mlController` called `redisClient` without importing it | `const redisClient = require('../config/redis')` added at top of file |
| `batchPredict` had `// ... prediction logic` stub — returned `undefined` for all predictions | Fully implemented with per-material cache check + ML service call loop |
| `getPerformanceMetrics` used `redisClient.keys('perf:*')` — blocks Redis on large keyspaces | Removed ad-hoc perf key approach; replaced with `model_performance` DB table + `getModelPerformance` endpoint |
| `cacheWarmer` built keys as `cache:/api/prices?material_id=...` — never matched actual cache middleware keys | Key format aligned with `buildCacheKey()` output: `GET|/api/prices/compare|material_name:...|region:...` |
| `jwtBlacklist` called `jwt.decode()` without importing `jsonwebtoken` | `const jwt = require('jsonwebtoken')` added |
| `RedisStore` in `rateLimiter` implemented v6 interface — incompatible with pinned v7 | Replaced with `rate-limit-redis` package using the official v7 `sendCommand` interface |
| `invalidateCache` used `redisClient.keys(pattern)` — blocks Redis event loop | Replaced with `SCAN`-based cursor loop — safe for production |
| Forecasts table used `material_id` FK to materials table; prices table uses `material_name` string | All tables now use `material_name VARCHAR` — no materials lookup table, no FK mismatch |

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active | **Next Milestone:** D1 — March 25, 2026
