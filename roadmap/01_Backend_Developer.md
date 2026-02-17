# 👨‍💻 Backend Developer & Security Lead - Detailed Roadmap

**Team Member:** Member 1  
**Primary Tech Stack:** Node.js, PostgreSQL (Supabase), Redis, JWT  
**Timeline:** March 9 - June 9, 2026 (13 weeks)

---

## 🚀 Getting Started - March 8, 2026

**Before Week 1 begins, complete this setup to hit the ground running on March 9!**

### System Requirements
- **Operating System:** Windows 10/11, macOS 10.15+, or Ubuntu 20.04+
- **RAM:** Minimum 8GB (16GB recommended)
- **Storage:** At least 10GB free space
- **Internet:** Stable connection for package downloads

### Step 1: Install Node.js and npm
```bash
# Download and install Node.js LTS (v18.x or v20.x)
# Visit: https://nodejs.org/

# Verify installation
node --version  # Should show v18.x.x or v20.x.x
npm --version   # Should show 9.x.x or higher
```

### Step 2: Install Git
```bash
# Windows: Download from https://git-scm.com/
# macOS: Install via Homebrew
brew install git

# Linux (Ubuntu/Debian)
sudo apt update
sudo apt install git

# Verify installation
git --version
```

### Step 3: Install Development Tools
```bash
# Install VS Code (recommended)
# Download from: https://code.visualstudio.com/

# Install VS Code extensions (recommended)
# - ESLint
# - Prettier - Code formatter
# - REST Client
# - GitLens
# - PostgreSQL (by Chris Kolkman)
```

### Step 4: Install PostgreSQL Client (Optional - for local testing)
```bash
# macOS
brew install postgresql

# Ubuntu/Debian
sudo apt install postgresql-client

# Windows: Download from https://www.postgresql.org/download/windows/
```

### Step 5: Create Development Directory Structure
```bash
# Create project directory
mkdir -p ~/projects/buildmat-backend
cd ~/projects/buildmat-backend

# Initialize Git repository
git init
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Create basic structure
mkdir -p src/{routes,controllers,services,middleware,config,utils}
mkdir -p tests/{unit,integration}
mkdir -p docs
```

### Step 6: Initialize Node.js Project
```bash
# Initialize package.json
npm init -y

# Install core dependencies
npm install express dotenv cors helmet
npm install pg @supabase/supabase-js
npm install redis ioredis
npm install jsonwebtoken bcryptjs
npm install express-validator express-rate-limit

# Install development dependencies
npm install --save-dev nodemon jest supertest eslint prettier
```

### Step 7: Set Up Environment Variables Template
```bash
# Create .env.example file
cat > .env.example << 'EOF'
# Database Configuration
SUPABASE_URL=your_supabase_url_here
SUPABASE_ANON_KEY=your_supabase_anon_key_here
DATABASE_URL=postgresql://user:password@host:port/database

# Redis Configuration
REDIS_HOST=your_redis_host_here
REDIS_PORT=6379
REDIS_PASSWORD=your_redis_password_here

# JWT Configuration
JWT_SECRET=your_super_secret_jwt_key_here
JWT_EXPIRY=24h

# Server Configuration
PORT=3000
NODE_ENV=development

# API Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
EOF

# Create actual .env file (don't commit this!)
cp .env.example .env
```

### Step 8: Create .gitignore File
```bash
cat > .gitignore << 'EOF'
# Dependencies
node_modules/
npm-debug.log
yarn-error.log

# Environment files
.env
.env.local
.env.*.local

# IDE files
.vscode/
.idea/
*.swp
*.swo
*~

# OS files
.DS_Store
Thumbs.db

# Build outputs
dist/
build/
coverage/

# Logs
logs/
*.log
EOF
```

### Step 9: Set Up ESLint and Prettier
```bash
# Initialize ESLint
npx eslint --init

# Create .prettierrc
cat > .prettierrc << 'EOF'
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2
}
EOF
```

### Step 10: Create Basic Server File
```bash
# Create src/server.js
cat > src/server.js << 'EOF'
require('dotenv').config();
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(helmet());
app.use(cors());
app.use(express.json());

// Health check endpoint
app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() });
});

// Start server
app.listen(PORT, () => {
  console.log(`🚀 Backend server running on port ${PORT}`);
  console.log(`Environment: ${process.env.NODE_ENV}`);
});

module.exports = app;
EOF
```

### Step 11: Update package.json Scripts
```bash
# Add these scripts to package.json
npm pkg set scripts.start="node src/server.js"
npm pkg set scripts.dev="nodemon src/server.js"
npm pkg set scripts.test="jest"
npm pkg set scripts.lint="eslint src/"
npm pkg set scripts.format="prettier --write 'src/**/*.js'"
```

### Step 12: Test Your Setup
```bash
# Test the server
npm run dev

# In another terminal, test the health endpoint
curl http://localhost:3000/health

# Expected output:
# {"status":"OK","timestamp":"2026-03-08T..."}
```

### Checklist - Confirm Everything Works ✅
- [ ] Node.js and npm installed and working
- [ ] Git installed and configured
- [ ] VS Code or preferred IDE installed
- [ ] Project directory structure created
- [ ] npm packages installed successfully
- [ ] .env file created with template
- [ ] Basic server runs without errors
- [ ] Health endpoint returns 200 OK
- [ ] ESLint and Prettier configured

### Troubleshooting Common Issues

**Issue: npm install fails**
```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and package-lock.json
rm -rf node_modules package-lock.json

# Reinstall
npm install
```

**Issue: Port 3000 already in use**
```bash
# Find process using port 3000
# macOS/Linux:
lsof -ti:3000 | xargs kill -9

# Windows:
netstat -ano | findstr :3000
taskkill /PID <process_id> /F
```

**Issue: Permission denied errors on Linux/macOS**
```bash
# Don't use sudo with npm! Instead, fix npm permissions:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### Ready for Week 1! 🎉
Once you complete this setup, you'll be ready to start Week 1 on Monday, March 9, 2026.

---

## 🎯 Role Overview

As the Backend Developer and Security Lead, you are responsible for building the entire server-side infrastructure that powers the platform. Your work forms the foundation that all other team members depend on.

**Core Responsibilities:**
- Database architecture and management
- RESTful API development
- Redis caching layer implementation
- Authentication and authorization
- Security hardening
- API rate limiting
- Model inference endpoint integration

---

## 📅 WEEK 1: March 9-15, 2026
### Theme: Database Foundation

#### Monday (March 9)
**Morning (2-3 hours):**
1. Create Supabase project account
2. Set up PostgreSQL database instance
3. Document connection string and credentials
4. Test connection from local machine

**Afternoon (2-3 hours):**
1. Design initial database schema:
   - `materials` table (id, name, category, unit, description)
   - `suppliers` table (id, name, location, contact, website)
   - `prices` table (id, material_id, supplier_id, price, date, region)
   - `users` table (id, email, password_hash, role, created_at)
2. Create schema diagram using dbdiagram.io or draw.io
3. Share schema with team for feedback

**Deliverable:** Database schema design document

#### Tuesday (March 10)
**Full Day (4-5 hours):**
1. Implement database schema in Supabase (example):
   ```sql
   -- Enable PostGIS extension for geographic data support
   CREATE EXTENSION IF NOT EXISTS postgis;

   -- Users table: Authentication and user management
   CREATE TABLE users (
     id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
     email VARCHAR(255) UNIQUE NOT NULL,
     password_hash VARCHAR(255) NOT NULL,
     name VARCHAR(255),
     role VARCHAR(50) NOT NULL DEFAULT 'user' CHECK (role IN ('user', 'admin')),
     active BOOLEAN DEFAULT true,
     email_verified BOOLEAN DEFAULT false,
     last_login TIMESTAMP,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );

   -- Materials table: Building materials catalog
   CREATE TABLE materials (
     id VARCHAR(50) PRIMARY KEY,  -- e.g., 'CEMENT-001'
     name VARCHAR(255) NOT NULL,
     category VARCHAR(100),
     unit VARCHAR(50),
     description TEXT,
     specifications JSONB,  -- Detailed material specs
     keywords TEXT[],  -- Search keywords
     active BOOLEAN DEFAULT true,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );

   -- Suppliers table: Supplier information and details
   CREATE TABLE suppliers (
     id VARCHAR(50) PRIMARY KEY,  -- e.g., 'SUP-001'
     name VARCHAR(255) NOT NULL,
     website VARCHAR(255),
     location_address TEXT,
     location_coordinates GEOGRAPHY(POINT, 4326),  -- WGS84 coordinates (latitude/longitude)
     region VARCHAR(100),
     contact_phone VARCHAR(50),
     contact_email VARCHAR(255),
     rating DECIMAL(3,2) CHECK (rating >= 0.00 AND rating <= 5.00),  -- 0.00 to 5.00
     review_count INTEGER DEFAULT 0,
     categories TEXT[],  -- Supplier categories
     opening_hours JSONB,  -- Store hours
     payment_methods TEXT[],  -- Accepted payment methods
     delivery_available BOOLEAN DEFAULT false,
     active BOOLEAN DEFAULT true,
     last_scraped TIMESTAMP,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );

   -- Prices table: Material prices from various suppliers
   CREATE TABLE prices (
     id BIGSERIAL PRIMARY KEY,
     material_id VARCHAR(50) REFERENCES materials(id) ON DELETE CASCADE,
     supplier_id VARCHAR(50) REFERENCES suppliers(id) ON DELETE CASCADE,
     price DECIMAL(10,2) NOT NULL,
     quantity INTEGER,
     unit VARCHAR(50),
     region VARCHAR(100),
     in_stock BOOLEAN DEFAULT true,
     bulk_pricing JSONB,
     metadata JSONB,  -- Additional scraper data
     date DATE NOT NULL,
     scraped_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );

   -- Favorites table: User favorite materials
   CREATE TABLE favorites (
     user_id UUID REFERENCES users(id) ON DELETE CASCADE,
     material_id VARCHAR(50) REFERENCES materials(id) ON DELETE CASCADE,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     PRIMARY KEY (user_id, material_id)
   );

   -- Price Alerts table: User price threshold alerts
   CREATE TABLE price_alerts (
     id BIGSERIAL PRIMARY KEY,
     user_id UUID REFERENCES users(id) ON DELETE CASCADE,
     material_id VARCHAR(50) REFERENCES materials(id) ON DELETE CASCADE,
     threshold_price DECIMAL(10,2) NOT NULL,
     threshold_type VARCHAR(20) NOT NULL CHECK (threshold_type IN ('above', 'below')),
     active BOOLEAN DEFAULT true,
     last_triggered TIMESTAMP,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );

   -- Scraping Jobs table: Track web scraping jobs
   -- Note: supplier_id uses ON DELETE SET NULL to preserve historical job records
   -- even when suppliers are removed, useful for debugging and auditing
   CREATE TABLE scraping_jobs (
     id VARCHAR(50) PRIMARY KEY,
     supplier_id VARCHAR(50) REFERENCES suppliers(id) ON DELETE SET NULL,
     status VARCHAR(20) NOT NULL CHECK (status IN ('pending', 'running', 'completed', 'failed')),
     materials_found INTEGER DEFAULT 0,
     prices_updated INTEGER DEFAULT 0,
     errors_count INTEGER DEFAULT 0,
     error_log JSONB,
     started_at TIMESTAMP,
     completed_at TIMESTAMP,
     duration_seconds INTEGER,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );
   ```

2. Add indexes for common queries:
   ```sql
   -- Materials indexes
   CREATE INDEX idx_materials_category ON materials(category);
   CREATE INDEX idx_materials_active ON materials(active);
   CREATE INDEX idx_materials_keywords ON materials USING GIN(keywords);

   -- Suppliers indexes
   CREATE INDEX idx_suppliers_region ON suppliers(region);
   CREATE INDEX idx_suppliers_active ON suppliers(active);
   CREATE INDEX idx_suppliers_categories ON suppliers USING GIN(categories);

   -- Prices indexes
   CREATE INDEX idx_prices_material ON prices(material_id);
   CREATE INDEX idx_prices_supplier ON prices(supplier_id);
   CREATE INDEX idx_prices_date ON prices(date);
   CREATE INDEX idx_prices_region ON prices(region);
   CREATE INDEX idx_prices_scraped_at ON prices(scraped_at);
   CREATE INDEX idx_prices_in_stock ON prices(in_stock);

   -- Price Alerts indexes
   CREATE INDEX idx_price_alerts_user ON price_alerts(user_id);
   CREATE INDEX idx_price_alerts_material ON price_alerts(material_id);
   CREATE INDEX idx_price_alerts_active ON price_alerts(active);

   -- Scraping Jobs indexes
   CREATE INDEX idx_scraping_jobs_supplier ON scraping_jobs(supplier_id);
   CREATE INDEX idx_scraping_jobs_status ON scraping_jobs(status);
   CREATE INDEX idx_scraping_jobs_created ON scraping_jobs(created_at);
   ```

3. Test all tables with sample data inserts

**Deliverable:** Functional database with schema

#### Wednesday (March 11)
**Morning (2-3 hours):**
1. Initialize Node.js backend project:
   ```bash
   mkdir backend
   cd backend
   npm init -y
   npm install express pg dotenv cors helmet
   npm install --save-dev nodemon
   ```

2. Create project structure:
   ```
   backend/
   ├── src/
   │   ├── config/
   │   │   └── database.js
   │   ├── routes/
   │   ├── controllers/
   │   ├── middleware/
   │   └── server.js
   ├── .env
   ├── .gitignore
   └── package.json
   ```

3. Set up database connection:
   ```javascript
   // src/config/database.js
   const { Pool } = require('pg');
   require('dotenv').config();

   const pool = new Pool({
     connectionString: process.env.DATABASE_URL,
     ssl: { rejectUnauthorized: false }
   });

   module.exports = pool;
   ```

**Afternoon (2-3 hours):**
1. Create basic Express server:
   ```javascript
   // src/server.js
   const express = require('express');
   const cors = require('cors');
   const helmet = require('helmet');
   
   const app = express();
   
   app.use(helmet());
   app.use(cors());
   app.use(express.json());
   
   app.get('/health', (req, res) => {
     res.json({ status: 'ok', timestamp: new Date() });
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. Test server startup
3. Create `.gitignore` and commit to GitHub

**Deliverable:** Basic Express server running

#### Thursday (March 12)
**Morning (2-3 hours):**
1. Create database helper functions:
   ```javascript
   // src/config/db-helpers.js
   const pool = require('./database');

   const query = async (text, params) => {
     const start = Date.now();
     const res = await pool.query(text, params);
     const duration = Date.now() - start;
     console.log('Executed query', { text, duration, rows: res.rowCount });
     return res;
   };

   module.exports = { query };
   ```

2. Create materials controller with basic CRUD:
   ```javascript
   // src/controllers/materialsController.js
   const { query } = require('../config/db-helpers');

   const getAllMaterials = async (req, res) => {
     try {
       const result = await query('SELECT * FROM materials ORDER BY name');
       res.json(result.rows);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };

   const getMaterialById = async (req, res) => {
     try {
       const { id } = req.params;
       const result = await query('SELECT * FROM materials WHERE id = $1', [id]);
       
       if (result.rows.length === 0) {
         return res.status(404).json({ error: 'Material not found' });
       }
       
       res.json(result.rows[0]);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };

   module.exports = { getAllMaterials, getMaterialById };
   ```

**Afternoon (2-3 hours):**
1. Create routes for materials:
   ```javascript
   // src/routes/materials.js
   const express = require('express');
   const router = express.Router();
   const { getAllMaterials, getMaterialById } = require('../controllers/materialsController');

   router.get('/', getAllMaterials);
   router.get('/:id', getMaterialById);

   module.exports = router;
   ```

2. Register routes in server:
   ```javascript
   // In server.js
   const materialsRoutes = require('./routes/materials');
   app.use('/api/materials', materialsRoutes);
   ```

3. Test endpoints with Postman or curl

**Deliverable:** Working materials API endpoints

#### Friday (March 13)
**Full Day (4-5 hours):**
1. Create suppliers controller and routes (similar pattern to materials)
2. Create prices controller with query parameters:
   ```javascript
   // src/controllers/pricesController.js
   const getPrices = async (req, res) => {
     try {
       const { material_id, supplier_id, region, start_date, end_date } = req.query;
       
       let queryText = `
         SELECT p.*, m.name as material_name, s.name as supplier_name
         FROM prices p
         JOIN materials m ON p.material_id = m.id
         JOIN suppliers s ON p.supplier_id = s.id
         WHERE 1=1
       `;
       const params = [];
       let paramIndex = 1;

       if (material_id) {
         queryText += ` AND p.material_id = $${paramIndex++}`;
         params.push(material_id);
       }

       if (supplier_id) {
         queryText += ` AND p.supplier_id = $${paramIndex++}`;
         params.push(supplier_id);
       }

       if (region) {
         queryText += ` AND p.region = $${paramIndex++}`;
         params.push(region);
       }

       if (start_date) {
         queryText += ` AND p.date >= $${paramIndex++}`;
         params.push(start_date);
       }

       if (end_date) {
         queryText += ` AND p.date <= $${paramIndex++}`;
         params.push(end_date);
       }

       queryText += ' ORDER BY p.date DESC LIMIT 100';

       const result = await query(queryText, params);
       res.json(result.rows);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

3. Create routes for prices
4. Test all query parameter combinations:
   ```bash
   # Test with curl - Basic price query
   curl http://localhost:3000/api/prices
   
   # Test with material filter
   curl "http://localhost:3000/api/prices?material_id=1"
   
   # Test with date range
   curl "http://localhost:3000/api/prices?start_date=2026-01-01&end_date=2026-03-01"
   
   # Test with multiple filters
   curl "http://localhost:3000/api/prices?material_id=1&region=Gauteng&start_date=2026-01-01"
   
   # Test with formatted output (using jq)
   curl -s http://localhost:3000/api/prices | jq '.[] | {material: .material_name, price: .price, date: .date}'
   ```

5. Create Postman collection for API testing:
   ```json
   {
     "info": {
       "name": "BuildMat API",
       "description": "Building Materials Price Intelligence API",
       "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
     },
     "item": [
       {
         "name": "Health Check",
         "request": {
           "method": "GET",
           "header": [],
           "url": {
             "raw": "{{base_url}}/health",
             "host": ["{{base_url}}"],
             "path": ["health"]
           }
         }
       },
       {
         "name": "Get All Materials",
         "request": {
           "method": "GET",
           "header": [],
           "url": {
             "raw": "{{base_url}}/api/materials",
             "host": ["{{base_url}}"],
             "path": ["api", "materials"]
           }
         }
       },
       {
         "name": "Get Prices with Filters",
         "request": {
           "method": "GET",
           "header": [],
           "url": {
             "raw": "{{base_url}}/api/prices?material_id=1&region=Gauteng",
             "host": ["{{base_url}}"],
             "path": ["api", "prices"],
             "query": [
               {
                 "key": "material_id",
                 "value": "1"
               },
               {
                 "key": "region",
                 "value": "Gauteng"
               }
             ]
           }
         }
       }
     ],
     "variable": [
       {
         "key": "base_url",
         "value": "http://localhost:3000"
       }
     ]
   }
   ```
   Save this to `tests/postman/buildmat-api.json` and import into Postman.

**Deliverable:** Prices API with filtering

#### Weekend (Optional - 2-3 hours)
1. Write API documentation for existing endpoints
2. Create Postman collection for testing
3. Prepare for Week 2 Redis setup

---

## 📅 WEEK 2: March 16-22, 2026
### Theme: Redis Caching & Authentication Setup

#### Monday (March 16)
**Morning (2-3 hours):**
1. Create Redis Cloud account (free 30MB tier)
2. Note down Redis connection details
3. Install Redis client in backend:
   ```bash
   npm install redis
   ```

4. Create Redis connection module:
   ```javascript
   // src/config/redis.js
   const redis = require('redis');
   require('dotenv').config();

   const client = redis.createClient({
     url: process.env.REDIS_URL,
     socket: {
       reconnectStrategy: (retries) => Math.min(retries * 50, 500)
     }
   });

   client.on('error', (err) => console.error('Redis Client Error', err));
   client.on('connect', () => console.log('Redis Client Connected'));

   (async () => {
     await client.connect();
   })();

   module.exports = client;
   ```

**Afternoon (2-3 hours):**
1. Create cache middleware:
   ```javascript
   // src/middleware/cache.js
   const redisClient = require('../config/redis');

   const cacheMiddleware = (duration) => {
     return async (req, res, next) => {
       if (req.method !== 'GET') {
         return next();
       }

       const key = `cache:${req.originalUrl}`;

       try {
         const cachedData = await redisClient.get(key);

         if (cachedData) {
           console.log('Cache HIT:', key);
           return res.json(JSON.parse(cachedData));
         }

         console.log('Cache MISS:', key);

         // Store original res.json
         const originalJson = res.json.bind(res);

         // Override res.json
         res.json = (body) => {
           redisClient.setEx(key, duration, JSON.stringify(body));
           originalJson(body);
         };

         next();
       } catch (error) {
         console.error('Cache error:', error);
         next();
       }
     };
   };

   module.exports = cacheMiddleware;
   ```

2. Apply caching to materials endpoint:
   ```javascript
   // In routes/materials.js
   const cacheMiddleware = require('../middleware/cache');
   
   router.get('/', cacheMiddleware(300), getAllMaterials); // 5 min cache
   router.get('/:id', cacheMiddleware(300), getMaterialById);
   ```

3. Test cache hits/misses with console logs

**Deliverable:** Basic Redis caching working

#### Tuesday (March 17)
**Morning (2-3 hours):**
1. Implement cache statistics tracking:
   ```javascript
   // src/middleware/cacheStats.js
   const redisClient = require('../config/redis');

   const trackCacheStats = async (req, res, next) => {
     const originalJson = res.json.bind(res);

     res.json = async (body) => {
       const isFromCache = res.locals.cacheHit || false;
       const statKey = isFromCache ? 'cache:hits' : 'cache:misses';
       
       await redisClient.incr(statKey);
       originalJson(body);
     };

     next();
   };

   const getCacheStats = async (req, res) => {
     try {
       const hits = await redisClient.get('cache:hits') || 0;
       const misses = await redisClient.get('cache:misses') || 0;
       const total = parseInt(hits) + parseInt(misses);
       const hitRate = total > 0 ? (parseInt(hits) / total * 100).toFixed(2) : 0;

       res.json({
         hits: parseInt(hits),
         misses: parseInt(misses),
         total,
         hitRate: `${hitRate}%`
       });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };

   module.exports = { trackCacheStats, getCacheStats };
   ```

2. Create admin route for cache stats:
   ```javascript
   // src/routes/admin.js
   const express = require('express');
   const router = express.Router();
   const { getCacheStats } = require('../middleware/cacheStats');

   router.get('/cache-stats', getCacheStats);

   module.exports = router;
   ```

**Afternoon (2-3 hours):**
1. Install JWT and bcrypt:
   ```bash
   npm install jsonwebtoken bcryptjs
   ```

2. Create auth controller:
   ```javascript
   // src/controllers/authController.js
   const bcrypt = require('bcryptjs');
   const jwt = require('jsonwebtoken');
   const { query } = require('../config/db-helpers');

   const register = async (req, res) => {
     try {
       const { email, password } = req.body;

       // Check if user exists
       const existingUser = await query(
         'SELECT * FROM users WHERE email = $1',
         [email]
       );

       if (existingUser.rows.length > 0) {
         return res.status(400).json({ error: 'User already exists' });
       }

       // Hash password
       const salt = await bcrypt.genSalt(10);
       const passwordHash = await bcrypt.hash(password, salt);

       // Create user
       const result = await query(
         'INSERT INTO users (email, password_hash, role) VALUES ($1, $2, $3) RETURNING id, email, role',
         [email, passwordHash, 'user']
       );

       // Generate JWT
       const token = jwt.sign(
         { userId: result.rows[0].id, role: result.rows[0].role },
         process.env.JWT_SECRET,
         { expiresIn: '24h' }
       );

       res.status(201).json({
         token,
         user: result.rows[0]
       });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };

   const login = async (req, res) => {
     try {
       const { email, password } = req.body;

       // Find user
       const result = await query(
         'SELECT * FROM users WHERE email = $1',
         [email]
       );

       if (result.rows.length === 0) {
         return res.status(401).json({ error: 'Invalid credentials' });
       }

       const user = result.rows[0];

       // Verify password
       const isMatch = await bcrypt.compare(password, user.password_hash);

       if (!isMatch) {
         return res.status(401).json({ error: 'Invalid credentials' });
       }

       // Generate JWT
       const token = jwt.sign(
         { userId: user.id, role: user.role },
         process.env.JWT_SECRET,
         { expiresIn: '24h' }
       );

       res.json({
         token,
         user: {
           id: user.id,
           email: user.email,
           role: user.role
         }
       });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };

   module.exports = { register, login };
   ```

3. Create auth routes and test

**Deliverable:** Working authentication system

#### Wednesday (March 18)
**Morning (2-3 hours):**
1. Create JWT verification middleware:
   ```javascript
   // src/middleware/auth.js
   const jwt = require('jsonwebtoken');

   const authenticateToken = (req, res, next) => {
     const authHeader = req.headers['authorization'];
     const token = authHeader && authHeader.split(' ')[1];

     if (!token) {
       return res.status(401).json({ error: 'Access token required' });
     }

     jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
       if (err) {
         return res.status(403).json({ error: 'Invalid or expired token' });
       }

       req.user = user;
       next();
     });
   };

   const requireRole = (...roles) => {
     return (req, res, next) => {
       if (!req.user) {
         return res.status(401).json({ error: 'Authentication required' });
       }

       if (!roles.includes(req.user.role)) {
         return res.status(403).json({ error: 'Insufficient permissions' });
       }

       next();
     };
   };

   module.exports = { authenticateToken, requireRole };
   ```

2. Protect admin routes:
   ```javascript
   // In routes/admin.js
   const { authenticateToken, requireRole } = require('../middleware/auth');

   router.get('/cache-stats', authenticateToken, requireRole('admin'), getCacheStats);
   ```

**Afternoon (2-3 hours):**
1. Implement JWT blacklist using Redis:
   ```javascript
   // src/middleware/jwtBlacklist.js
   const redisClient = require('../config/redis');

   const addToBlacklist = async (token) => {
     const decoded = jwt.decode(token);
     const expiry = decoded.exp - Math.floor(Date.now() / 1000);
     
     if (expiry > 0) {
       await redisClient.setEx(`blacklist:${token}`, expiry, 'true');
     }
   };

   const isBlacklisted = async (token) => {
     const result = await redisClient.get(`blacklist:${token}`);
     return result !== null;
   };

   const checkBlacklist = async (req, res, next) => {
     const authHeader = req.headers['authorization'];
     const token = authHeader && authHeader.split(' ')[1];

     if (token && await isBlacklisted(token)) {
       return res.status(401).json({ error: 'Token has been revoked' });
     }

     next();
   };

   module.exports = { addToBlacklist, isBlacklisted, checkBlacklist };
   ```

2. Create logout endpoint:
   ```javascript
   // In authController.js
   const { addToBlacklist } = require('../middleware/jwtBlacklist');

   const logout = async (req, res) => {
     try {
       const authHeader = req.headers['authorization'];
       const token = authHeader && authHeader.split(' ')[1];

       if (token) {
         await addToBlacklist(token);
       }

       res.json({ message: 'Logged out successfully' });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Deliverable:** JWT authentication with blacklist

#### Thursday (March 19)
**Morning (2-3 hours):**
1. Implement rate limiting:
   ```bash
   npm install express-rate-limit
   ```

2. Create rate limit middleware:
   ```javascript
   // src/middleware/rateLimiter.js
   const rateLimit = require('express-rate-limit');
   const redisClient = require('../config/redis');

   // Redis store for rate limiting
   class RedisStore {
     constructor(options) {
       this.client = redisClient;
       this.prefix = options.prefix || 'rl:';
       this.resetExpiryOnChange = options.resetExpiryOnChange || false;
     }

     async increment(key) {
       const prefixedKey = this.prefix + key;
       const current = await this.client.incr(prefixedKey);
       
       if (current === 1) {
         await this.client.expire(prefixedKey, 60); // 1 minute window
       }
       
       return {
         totalHits: current,
         resetTime: new Date(Date.now() + 60000)
       };
     }

     async decrement(key) {
       const prefixedKey = this.prefix + key;
       await this.client.decr(prefixedKey);
     }

     async resetKey(key) {
       const prefixedKey = this.prefix + key;
       await this.client.del(prefixedKey);
     }
   }

   // API rate limiter - 100 requests per minute per IP
   const apiLimiter = rateLimit({
     store: new RedisStore({ prefix: 'rl:api:' }),
     windowMs: 60 * 1000, // 1 minute
     max: 100,
     message: 'Too many requests from this IP, please try again later.',
     standardHeaders: true,
     legacyHeaders: false,
   });

   // Auth rate limiter - 5 attempts per 15 minutes
   const authLimiter = rateLimit({
     store: new RedisStore({ prefix: 'rl:auth:' }),
     windowMs: 15 * 60 * 1000, // 15 minutes
     max: 5,
     message: 'Too many authentication attempts, please try again later.',
   });

   module.exports = { apiLimiter, authLimiter };
   ```

3. Apply rate limiters:
   ```javascript
   // In server.js
   const { apiLimiter } = require('./middleware/rateLimiter');
   
   app.use('/api/', apiLimiter);
   
   // In auth routes
   const { authLimiter } = require('../middleware/rateLimiter');
   
   router.post('/login', authLimiter, login);
   router.post('/register', authLimiter, register);
   ```

**Afternoon (2-3 hours):**
1. Create error handling middleware:
   ```javascript
   // src/middleware/errorHandler.js
   const errorHandler = (err, req, res, next) => {
     console.error('Error:', err);

     if (err.name === 'ValidationError') {
       return res.status(400).json({
         error: 'Validation Error',
         details: err.message
       });
     }

     if (err.name === 'UnauthorizedError') {
       return res.status(401).json({
         error: 'Unauthorized',
         message: 'Invalid or expired token'
       });
     }

     res.status(err.status || 500).json({
       error: err.message || 'Internal Server Error',
       ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
     });
   };

   module.exports = errorHandler;
   ```

2. Add to server:
   ```javascript
   // In server.js (add at the end, after all routes)
   const errorHandler = require('./middleware/errorHandler');
   app.use(errorHandler);
   ```

3. Create input validation helpers:
   ```bash
   npm install joi
   ```

   ```javascript
   // src/middleware/validation.js
   const Joi = require('joi');

   const validateRequest = (schema) => {
     return (req, res, next) => {
       const { error } = schema.validate(req.body);
       
       if (error) {
         return res.status(400).json({
           error: 'Validation Error',
           details: error.details.map(d => d.message)
         });
       }
       
       next();
     };
   };

   // Example schemas
   const schemas = {
     register: Joi.object({
       email: Joi.string().email().required(),
       password: Joi.string().min(8).required()
     }),
     login: Joi.object({
       email: Joi.string().email().required(),
       password: Joi.string().required()
     })
   };

   module.exports = { validateRequest, schemas };
   ```

**Deliverable:** Rate limiting and validation

#### Friday (March 20)
**Full Day (4-5 hours):**
1. Create comprehensive API documentation:
   - Endpoint list with descriptions
   - Request/response examples
   - Authentication requirements
   - Rate limiting info

2. Create health check endpoint with detailed status:
   ```javascript
   // src/controllers/healthController.js
   const redisClient = require('../config/redis');
   const pool = require('../config/database');

   const healthCheck = async (req, res) => {
     const health = {
       status: 'ok',
       timestamp: new Date(),
       services: {}
     };

     // Check database
     try {
       await pool.query('SELECT 1');
       health.services.database = 'ok';
     } catch (error) {
       health.services.database = 'error';
       health.status = 'degraded';
     }

     // Check Redis
     try {
       await redisClient.ping();
       health.services.redis = 'ok';
     } catch (error) {
       health.services.redis = 'error';
       health.status = 'degraded';
     }

     res.status(health.status === 'ok' ? 200 : 503).json(health);
   };

   module.exports = { healthCheck };
   ```

3. Prepare for D1 demo (Week 3):
   - Test all endpoints
   - Verify cache performance
   - Document API usage

**Deliverable:** Production-ready API foundation

#### Weekend (Optional - 2-3 hours)
1. Load testing with basic tools (Apache Bench or similar)
2. Performance optimization review
3. Security audit of implemented features

---

## 📅 WEEK 3: March 23-29, 2026
### Theme: D1 Demo Preparation & Price History API

#### Monday (March 23)
**Morning (2-3 hours):**
1. Create price history endpoint with aggregations:
   ```javascript
   // src/controllers/pricesController.js
   const getPriceHistory = async (req, res) => {
     try {
       const { material_id, supplier_id, days = 30 } = req.query;
       
       const queryText = `
         SELECT 
           DATE(date) as price_date,
           AVG(price) as avg_price,
           MIN(price) as min_price,
           MAX(price) as max_price,
           COUNT(*) as sample_count
         FROM prices
         WHERE material_id = $1
           AND date >= CURRENT_DATE - $2::integer
           ${supplier_id ? 'AND supplier_id = $3' : ''}
         GROUP BY DATE(date)
         ORDER BY price_date DESC
       `;
       
       const params = supplier_id 
         ? [material_id, days, supplier_id]
         : [material_id, days];
       
       const result = await query(queryText, params);
       res.json(result.rows);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

2. Add caching with 1-hour TTL:
   ```javascript
   router.get('/history', cacheMiddleware(3600), getPriceHistory);
   ```

**Afternoon (2-3 hours):**
1. Create price comparison endpoint:
   ```javascript
   const comparePrices = async (req, res) => {
     try {
       const { material_id, region } = req.query;
       
       const queryText = `
         SELECT 
           s.name as supplier_name,
           s.location,
           p.price,
           p.date,
           p.bulk_pricing
         FROM prices p
         JOIN suppliers s ON p.supplier_id = s.id
         WHERE p.material_id = $1
           AND p.date = (
             SELECT MAX(date) 
             FROM prices 
             WHERE material_id = $1 
               AND supplier_id = p.supplier_id
               ${region ? 'AND region = $2' : ''}
           )
           ${region ? 'AND p.region = $2' : ''}
         ORDER BY p.price ASC
       `;
       
       const params = region ? [material_id, region] : [material_id];
       const result = await query(queryText, params);
       
       res.json({
         material_id,
         region,
         suppliers: result.rows
       });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Deliverable:** Price analysis endpoints

#### Tuesday (March 24)
**Full Day (4-5 hours):**
1. Work with Member 4 to integrate scraped data:
   - Create data import endpoint
   - Validate data format
   - Handle duplicates

2. Create bulk insert functionality:
   ```javascript
   // src/controllers/dataImportController.js
   const { authenticateToken, requireRole } = require('../middleware/auth');
   
   const bulkImportPrices = async (req, res) => {
     try {
       const { prices } = req.body; // Array of price objects
       
       // Validate data
       if (!Array.isArray(prices)) {
         return res.status(400).json({ error: 'Prices must be an array' });
       }
       
       // Begin transaction
       const client = await pool.connect();
       
       try {
         await client.query('BEGIN');
         
         let imported = 0;
         let skipped = 0;
         
         for (const price of prices) {
           // Check for duplicates
           const existing = await client.query(
             'SELECT id FROM prices WHERE material_id = $1 AND supplier_id = $2 AND date = $3',
             [price.material_id, price.supplier_id, price.date]
           );
           
           if (existing.rows.length > 0) {
             skipped++;
             continue;
           }
           
           // Insert
           await client.query(
             'INSERT INTO prices (material_id, supplier_id, price, date, region, bulk_pricing) VALUES ($1, $2, $3, $4, $5, $6)',
             [price.material_id, price.supplier_id, price.price, price.date, price.region, price.bulk_pricing]
           );
           
           imported++;
         }
         
         await client.query('COMMIT');
         
         res.json({
           success: true,
           imported,
           skipped,
           total: prices.length
         });
         
       } catch (error) {
         await client.query('ROLLBACK');
         throw error;
       } finally {
         client.release();
       }
       
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   
   module.exports = { bulkImportPrices };
   ```

3. Test with sample data from Member 4

**Deliverable:** Data import pipeline

#### Wednesday (March 25) - **D1 DEMO DAY** 🎯
**Morning (2-3 hours):**
1. Final testing of all endpoints
2. Verify cache statistics are working
3. Ensure database has 500+ records
4. Prepare demo script:
   - Show API endpoints in Postman
   - Demonstrate cache hit vs miss (with timing)
   - Show database schema
   - Display cache statistics

**Afternoon (2-3 hours):**
1. D1 Presentation
2. Address any feedback
3. Document lessons learned

**Deliverable:** Successful D1 demo

#### Thursday (March 26)
**Morning (2-3 hours):**
1. Implement response time monitoring:
   ```javascript
   // src/middleware/responseTime.js
   const responseTime = require('response-time');
   
   const trackResponseTime = responseTime((req, res, time) => {
     const stat = (req.route && req.route.path) || req.path;
     console.log(`[${stat}] ${time.toFixed(2)}ms`);
     
     // Store in Redis for analytics
     const key = `perf:${stat}:${Date.now()}`;
     redisClient.setEx(key, 3600, time.toString());
   });
   
   module.exports = trackResponseTime;
   ```

2. Add to server for performance tracking

**Afternoon (2-3 hours):**
1. Create analytics endpoint:
   ```javascript
   const getPerformanceMetrics = async (req, res) => {
     try {
       // Get recent performance data
       const keys = await redisClient.keys('perf:*');
       const times = await Promise.all(
         keys.map(key => redisClient.get(key))
       );
       
       const numericTimes = times.map(Number).filter(n => !isNaN(n));
       
       const avg = numericTimes.reduce((a, b) => a + b, 0) / numericTimes.length;
       const max = Math.max(...numericTimes);
       const min = Math.min(...numericTimes);
       
       res.json({
         average: avg.toFixed(2),
         max: max.toFixed(2),
         min: min.toFixed(2),
         samples: numericTimes.length
       });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Deliverable:** Performance monitoring system

#### Friday (March 27)
**Full Day (4-5 hours):**
1. Optimize database queries with EXPLAIN ANALYZE
2. Add database connection pooling optimization
3. Review and optimize cache TTL values
4. Prepare for ST1 presentation (Week 4)

**Deliverable:** Optimized backend

---

## 📅 WEEK 4: March 30-April 5, 2026
### Theme: ML Integration & ST1 Presentation

#### Monday (March 30)
**Morning (2-3 hours):**
1. Create forecasts table for ML predictions:
   ```sql
   CREATE TABLE forecasts (
     id BIGSERIAL PRIMARY KEY,
     material_id VARCHAR(50) REFERENCES materials(id) ON DELETE CASCADE,
     forecast_date DATE NOT NULL,
     predicted_price DECIMAL(10,2) NOT NULL,
     confidence_lower DECIMAL(10,2),
     confidence_upper DECIMAL(10,2),
     confidence_level DECIMAL(5,4) CHECK (confidence_level >= 0 AND confidence_level <= 1),  -- e.g., 0.9500 for 95% confidence
     model_name VARCHAR(100),
     model_version VARCHAR(50),
     region VARCHAR(100),  -- NULL region represents global forecasts
     generated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     UNIQUE(material_id, forecast_date, model_name, COALESCE(region, ''))
   );
   
   CREATE INDEX idx_forecasts_material ON forecasts(material_id);
   CREATE INDEX idx_forecasts_date ON forecasts(forecast_date);
   CREATE INDEX idx_forecasts_region ON forecasts(region);
   ```

2. Create forecast storage endpoint for ML team:
   ```javascript
   // src/controllers/forecastsController.js
   const storeForecast = async (req, res) => {
     try {
       const {
         material_id,
         forecasts, // Array of {date, price, lower, upper}
         model_name,
         model_version
       } = req.body;
       
       const client = await pool.connect();
       
       try {
         await client.query('BEGIN');
         
         for (const forecast of forecasts) {
           await client.query(
             `INSERT INTO forecasts 
              (material_id, forecast_date, predicted_price, confidence_interval_lower, 
               confidence_interval_upper, model_name, model_version)
              VALUES ($1, $2, $3, $4, $5, $6, $7)
              ON CONFLICT (material_id, forecast_date, model_name)
              DO UPDATE SET 
                predicted_price = EXCLUDED.predicted_price,
                confidence_interval_lower = EXCLUDED.confidence_interval_lower,
                confidence_interval_upper = EXCLUDED.confidence_interval_upper,
                model_version = EXCLUDED.model_version,
                generated_at = CURRENT_TIMESTAMP`,
             [material_id, forecast.date, forecast.price, forecast.lower, 
              forecast.upper, model_name, model_version]
           );
         }
         
         await client.query('COMMIT');
         
         res.json({ success: true, stored: forecasts.length });
       } catch (error) {
         await client.query('ROLLBACK');
         throw error;
       } finally {
         client.release();
       }
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Afternoon (2-3 hours):**
1. Create forecast retrieval endpoint:
   ```javascript
   const getForecasts = async (req, res) => {
     try {
       const { material_id, days = 7, model_name } = req.query;
       
       let queryText = `
         SELECT 
           forecast_date,
           predicted_price,
           confidence_interval_lower,
           confidence_interval_upper,
           model_name,
           model_version
         FROM forecasts
         WHERE material_id = $1
           AND forecast_date >= CURRENT_DATE
           AND forecast_date <= CURRENT_DATE + $2::integer
       `;
       
       const params = [material_id, days];
       
       if (model_name) {
         queryText += ' AND model_name = $3';
         params.push(model_name);
       }
       
       queryText += ' ORDER BY forecast_date ASC';
       
       const result = await query(queryText, params);
       res.json(result.rows);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

2. Add caching with 6-hour TTL (forecasts don't change often):
   ```javascript
   router.get('/', cacheMiddleware(21600), getForecasts); // 6 hours
   ```

**Deliverable:** ML integration endpoints

#### Tuesday (March 31) - **ST1 DEMO DAY** 🎯
**Morning (2-3 hours):**
1. Create comprehensive architecture diagram showing:
   - Request flow
   - Cache layers (L1: prices, L2: forecasts, JWT blacklist)
   - Database relationships
   - Security components

2. Prepare ST1 presentation materials:
   - Architecture slides
   - Cache performance metrics
   - Security features overview
   - Live demo script

**Afternoon (2-3 hours):**
1. ST1 Presentation
2. Q&A preparation
3. Note feedback for improvements

**Deliverable:** Successful ST1 presentation

#### Wednesday (April 2)
**Morning (2-3 hours):**
1. Implement cache warming for frequently accessed data:
   ```javascript
   // src/services/cacheWarmer.js
   const redisClient = require('../config/redis');
   const { query } = require('../config/db-helpers');

   const warmCache = async () => {
     try {
       console.log('Starting cache warming...');
       
       // Get all materials
       const materials = await query('SELECT id FROM materials');
       
       // Pre-cache recent prices for each material
       for (const material of materials.rows) {
         const prices = await query(
           `SELECT * FROM prices 
            WHERE material_id = $1 
            AND date >= CURRENT_DATE - 30 
            ORDER BY date DESC`,
           [material.id]
         );
         
         const cacheKey = `cache:/api/prices?material_id=${material.id}&days=30`;
         await redisClient.setEx(cacheKey, 3600, JSON.stringify(prices.rows));
       }
       
       console.log('Cache warming complete');
     } catch (error) {
       console.error('Cache warming failed:', error);
     }
   };

   module.exports = { warmCache };
   ```

2. Schedule cache warming on server start and every 6 hours

**Afternoon (2-3 hours):**
1. Create cache invalidation helpers:
   ```javascript
   // src/services/cacheInvalidation.js
   const invalidateMaterialCache = async (materialId) => {
     const pattern = `cache:/api/prices?material_id=${materialId}*`;
     const keys = await redisClient.keys(pattern);
     
     if (keys.length > 0) {
       await redisClient.del(keys);
       console.log(`Invalidated ${keys.length} cache entries for material ${materialId}`);
     }
   };

   const invalidateForecastCache = async (materialId) => {
     const pattern = `cache:/api/forecasts?material_id=${materialId}*`;
     const keys = await redisClient.keys(pattern);
     
     if (keys.length > 0) {
       await redisClient.del(keys);
     }
   };
   ```

2. Call invalidation when new prices are added

**Deliverable:** Advanced cache management

#### Thursday (April 3)
**Full Day (4-5 hours):**
1. Create anomaly alert system:
   ```sql
   CREATE TABLE anomalies (
     id BIGSERIAL PRIMARY KEY,
     material_id VARCHAR(50) REFERENCES materials(id) ON DELETE CASCADE,
     supplier_id VARCHAR(50) REFERENCES suppliers(id) ON DELETE CASCADE,
     price DECIMAL(10,2) NOT NULL,
     expected_price DECIMAL(10,2),
     deviation_percent DECIMAL(5,2),
     date DATE NOT NULL,
     detected_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );
   ```

2. Create anomaly detection endpoint:
   ```javascript
   const detectAnomalies = async (req, res) => {
     try {
       const { material_id, threshold = 20 } = req.query; // 20% deviation
       
       const queryText = `
         WITH avg_prices AS (
           SELECT 
             material_id,
             AVG(price) as avg_price,
             STDDEV(price) as std_price
           FROM prices
           WHERE date >= CURRENT_DATE - 90
           GROUP BY material_id
         )
         SELECT 
           p.id,
           p.material_id,
           p.supplier_id,
           p.price,
           p.date,
           a.avg_price,
           ABS((p.price - a.avg_price) / a.avg_price * 100) as deviation_percent
         FROM prices p
         JOIN avg_prices a ON p.material_id = a.material_id
         WHERE p.date >= CURRENT_DATE - 7
           ${material_id ? 'AND p.material_id = $2' : ''}
           AND ABS((p.price - a.avg_price) / a.avg_price * 100) > $1
         ORDER BY deviation_percent DESC
       `;
       
       const params = material_id ? [threshold, material_id] : [threshold];
       const result = await query(queryText, params);
       
       res.json({
         threshold: `${threshold}%`,
         anomalies: result.rows
       });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Deliverable:** Anomaly detection system

#### Friday (April 4)
**Full Day (4-5 hours):**
1. Create comprehensive logging system:
   ```bash
   npm install winston
   ```

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
       new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
       new winston.transports.File({ filename: 'logs/combined.log' })
     ]
   });

   if (process.env.NODE_ENV !== 'production') {
     logger.add(new winston.transports.Console({
       format: winston.format.simple()
     }));
   }

   module.exports = logger;
   ```

2. Replace console.log statements with logger
3. Implement request logging middleware

**Deliverable:** Production logging

#### Weekend (Optional)
1. Review Weeks 1-4 accomplishments
2. Document any technical debt
3. Plan for Weeks 5-6

---

## 📅 WEEK 5: April 6-12, 2026
### Theme: ML Inference API & D2 Preparation

#### Monday (April 6)
**Morning (2-3 hours):**
1. Create ML model endpoint for predictions:
   ```javascript
   // src/controllers/mlController.js
   const axios = require('axios');
   
   const predictPrice = async (req, res) => {
     try {
       const { material_id, days = 7, model = 'xgboost' } = req.query;
       
       // Check cache first
       const cacheKey = `ml:prediction:${material_id}:${days}:${model}`;
       const cached = await redisClient.get(cacheKey);
       
       if (cached) {
         return res.json(JSON.parse(cached));
       }
       
       // Get historical data
       const history = await query(
         `SELECT price, date FROM prices 
          WHERE material_id = $1 
          AND date >= CURRENT_DATE - 90
          ORDER BY date ASC`,
         [material_id]
       );
       
       // Call ML service (Member 2's model endpoint)
       const mlResponse = await axios.post(
         process.env.ML_SERVICE_URL + '/predict',
         {
           material_id,
           historical_data: history.rows,
           forecast_days: days,
           model: model
         }
       );
       
       // Cache for 6 hours
       await redisClient.setEx(cacheKey, 21600, JSON.stringify(mlResponse.data));
       
       res.json(mlResponse.data);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Afternoon (2-3 hours):**
1. Create batch prediction endpoint:
   ```javascript
   const batchPredict = async (req, res) => {
     try {
       const { material_ids, days = 7, model = 'xgboost' } = req.body;
       
       const predictions = await Promise.all(
         material_ids.map(async (material_id) => {
           const cacheKey = `ml:prediction:${material_id}:${days}:${model}`;
           let prediction = await redisClient.get(cacheKey);
           
           if (!prediction) {
             // Fetch and cache
             // ... prediction logic
           } else {
             prediction = JSON.parse(prediction);
           }
           
           return { material_id, prediction };
         })
       );
       
       res.json({ predictions });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Deliverable:** ML inference endpoints

#### Tuesday (April 7)
**Full Day (4-5 hours):**
1. Coordinate with Member 2 to test ML integration
2. Create model performance tracking:
   ```sql
   CREATE TABLE model_performance (
     id BIGSERIAL PRIMARY KEY,
     model_name VARCHAR(100) NOT NULL,
     material_id VARCHAR(50) REFERENCES materials(id) ON DELETE CASCADE,
     mae DECIMAL(10,4),
     rmse DECIMAL(10,4),
     mape DECIMAL(10,4),
     evaluation_date DATE NOT NULL,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );
   ```

3. Create endpoint to retrieve model metrics:
   ```javascript
   const getModelPerformance = async (req, res) => {
     try {
       const { model_name, material_id } = req.query;
       
       let queryText = 'SELECT * FROM model_performance WHERE 1=1';
       const params = [];
       let paramIndex = 1;
       
       if (model_name) {
         queryText += ` AND model_name = $${paramIndex++}`;
         params.push(model_name);
       }
       
       if (material_id) {
         queryText += ` AND material_id = $${paramIndex++}`;
         params.push(material_id);
       }
       
       queryText += ' ORDER BY evaluation_date DESC LIMIT 100';
       
       const result = await query(queryText, params);
       res.json(result.rows);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   };
   ```

**Deliverable:** ML performance tracking

#### Wednesday (April 8) - **D2 DEMO DAY** 🎯
**Morning (2-3 hours):**
1. Final testing of ML endpoints
2. Verify predictions are being cached
3. Prepare demo showing:
   - Real-time price prediction
   - Cache performance metrics
   - Database integration
   - Model performance comparison

**Afternoon (2-3 hours):**
1. D2 Presentation
2. Showcase working intelligence layer
3. Address feedback

**Deliverable:** Successful D2 demo

#### Thursday-Friday (April 9-11)
**Full Days (8-10 hours total):**
1. Optimize API performance based on feedback
2. Add API documentation using Swagger:
   ```bash
   npm install swagger-jsdoc swagger-ui-express
   ```

3. Create webhook system for price alerts:
   ```javascript
   // src/services/webhooks.js
   const notifyPriceChange = async (material_id, old_price, new_price) => {
     const subscribers = await query(
       'SELECT webhook_url FROM subscriptions WHERE material_id = $1',
       [material_id]
     );
     
     for (const sub of subscribers.rows) {
       await axios.post(sub.webhook_url, {
         event: 'price_change',
         material_id,
         old_price,
         new_price,
         change_percent: ((new_price - old_price) / old_price * 100).toFixed(2)
       });
     }
   };
   ```

**Deliverable:** Enhanced API features

---

## 📅 WEEKS 6-7: April 13-26, 2026
### Theme: Mobile Support & Advanced Features

**Focus Areas:**
1. Mobile-optimized API endpoints
2. Image upload for receipts/invoices
3. Regional pricing analytics
4. Advanced caching strategies
5. Performance optimization

**Key Tasks:**
- Create compressed response endpoints for mobile
- Implement pagination for large datasets
- Add GraphQL layer (optional)
- Optimize database indexes
- Implement database read replicas (if needed)

---

## 📅 WEEK 8: April 27-May 3, 2026
### Theme: MVP Polish & D3 Preparation

**D3 (May 1) - MVP COMPLETE** 🎉

**Final Week Sprint:**
1. Bug fixes from integration testing
2. Performance tuning (target: <50ms for cached queries)
3. Security audit completion
4. Load testing and capacity planning
5. Production deployment to Fly.io
6. Documentation finalization
7. Demo preparation

---

## 📅 WEEKS 9-10: May 4-17, 2026
### Theme: Testing & Final Polish

**ST2 (May 6)** - Testing Results  
**D4 (May 13)** - Production Polish

**Testing Focus:**
1. Load testing (100+ concurrent users)
2. Security penetration testing
3. Cache hit rate optimization (>75%)
4. API response time verification (<50ms)
5. Database query optimization
6. Redis memory usage optimization

---

## 📅 WEEKS 11-13: May 18-June 9, 2026
### Theme: Presentation & Final Refinements

**Activities:**
1. Final performance tuning
2. Backup and recovery testing
3. Documentation polish
4. Demo video recording
5. Presentation rehearsals
6. Q&A preparation

**Final Presentation (June 10)** 🏆

---

## 🎯 Key Performance Indicators (Weekly Tracking)

| Metric | Target | Week 2 | Week 4 | Week 8 | Week 13 |
|--------|--------|--------|--------|--------|---------|
| Cache Hit Rate | >70% | 50% | 65% | 75% | 78% |
| API Response (cached) | <50ms | 80ms | 60ms | 45ms | 40ms |
| API Response (uncached) | <150ms | 200ms | 180ms | 140ms | 120ms |
| Database Records | 2000+ | 500 | 800 | 1500 | 2500 |
| API Endpoints | 20+ | 8 | 12 | 18 | 22 |
| Test Coverage | >70% | 20% | 40% | 60% | 75% |

---

## 🚨 Red Flags to Watch

**Week 2-3:**
- Redis not connecting properly
- Cache hit rate below 40%
- Database performance issues

**Week 4-5:**
- ML integration failures
- API response times >200ms
- Authentication bugs

**Week 6-8:**
- Mobile API issues
- Load testing failures
- Security vulnerabilities

**Action Plan:** Report issues immediately in weekly sync, escalate blockers to team lead.

---

## 📚 Learning Resources

**Backend Development:**
- Express.js Best Practices: https://expressjs.com/en/advanced/best-practice-performance.html
- PostgreSQL Performance: https://www.postgresql.org/docs/current/performance-tips.html
- Redis Caching Patterns: https://redis.io/docs/manual/patterns/

**Security:**
- OWASP Top 10: https://owasp.org/www-project-top-ten/
- JWT Best Practices: https://tools.ietf.org/html/rfc8725
- Node.js Security Checklist: https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_Security_Cheat_Sheet.html

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active | **Next Milestone:** D1 (March 25, 2026)
