# 🗄️ Database Schema Documentation

**Building Materials Price Intelligence Platform**  
**PostgreSQL Database Schema**

---

## 📋 Overview

The database schema is designed to support price tracking, forecasting, user management, and supplier information for building materials in South Africa. The schema uses PostgreSQL 14+ on Supabase with row-level security (RLS) enabled for multi-tenant data isolation.

**Database:** PostgreSQL 14+  
**Hosting:** Supabase (Managed PostgreSQL)  
**Features:**
- Row-Level Security (RLS)
- JSONB for flexible metadata storage
- PostGIS extension for geolocation queries
- Automatic timestamp tracking
- Foreign key constraints
- Indexes for query optimization

---

## 📊 Entity Relationship Diagram

See [ERD.png](./ERD.png) for visual representation of table relationships.

---

## 📚 Table Definitions

### 1. users

Stores user account information and authentication data.

**Purpose:** User management, authentication, and authorization

#### Schema

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL DEFAULT 'contractor',
    active BOOLEAN DEFAULT TRUE,
    email_verified BOOLEAN DEFAULT FALSE,
    last_login TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    CONSTRAINT valid_email CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$'),
    CONSTRAINT valid_role CHECK (role IN ('contractor', 'analyst', 'admin', 'guest'))
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_users_active ON users(active);
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | UUID | PRIMARY KEY | Unique user identifier |
| `email` | VARCHAR(255) | UNIQUE, NOT NULL | User email address (login) |
| `password_hash` | VARCHAR(255) | NOT NULL | Bcrypt hashed password |
| `name` | VARCHAR(255) | NOT NULL | User's full name |
| `role` | VARCHAR(50) | NOT NULL, DEFAULT 'contractor' | User role (RBAC) |
| `active` | BOOLEAN | DEFAULT TRUE | Account active status |
| `email_verified` | BOOLEAN | DEFAULT FALSE | Email verification status |
| `last_login` | TIMESTAMP | NULL | Last login timestamp |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Account creation timestamp |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Last update timestamp |

#### Sample Data

```sql
INSERT INTO users (email, password_hash, name, role) VALUES
('john@contractor.com', '$2b$12$...', 'John Smith', 'contractor'),
('admin@buildmat.com', '$2b$12$...', 'Admin User', 'admin'),
('analyst@buildmat.com', '$2b$12$...', 'Data Analyst', 'analyst');
```

#### Common Queries

```sql
-- Find user by email (login)
SELECT id, email, password_hash, role 
FROM users 
WHERE email = 'john@contractor.com' AND active = TRUE;

-- Get active users by role
SELECT id, email, name, last_login 
FROM users 
WHERE role = 'contractor' AND active = TRUE 
ORDER BY created_at DESC;

-- Update last login
UPDATE users 
SET last_login = NOW(), updated_at = NOW() 
WHERE id = 'uuid-here';
```

---

### 2. materials

Catalog of building materials tracked by the system.

**Purpose:** Material information, categories, specifications

#### Schema

```sql
CREATE TABLE materials (
    id VARCHAR(50) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    category VARCHAR(50) NOT NULL,
    unit VARCHAR(50) NOT NULL,
    description TEXT,
    specifications JSONB,
    keywords TEXT[],
    active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    CONSTRAINT valid_category CHECK (category IN ('cement', 'steel', 'timber', 'sand', 'copper', 'pvc', 'bitumen', 'masonry'))
);

CREATE INDEX idx_materials_category ON materials(category);
CREATE INDEX idx_materials_name ON materials USING GIN(to_tsvector('english', name));
CREATE INDEX idx_materials_keywords ON materials USING GIN(keywords);
CREATE INDEX idx_materials_active ON materials(active);
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | VARCHAR(50) | PRIMARY KEY | Material ID (e.g., 'mat_001') |
| `name` | VARCHAR(255) | NOT NULL | Material name |
| `category` | VARCHAR(50) | NOT NULL | Material category |
| `unit` | VARCHAR(50) | NOT NULL | Unit of measurement |
| `description` | TEXT | NULL | Detailed description |
| `specifications` | JSONB | NULL | Technical specifications |
| `keywords` | TEXT[] | NULL | Search keywords |
| `active` | BOOLEAN | DEFAULT TRUE | Active in catalog |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Creation timestamp |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Last update timestamp |

#### Sample Data

```sql
INSERT INTO materials (id, name, category, unit, description, specifications, keywords) VALUES
('mat_001', 'Portland Cement 42.5N', 'cement', '50kg bag', 
 'High-strength portland cement suitable for general construction',
 '{"strength": "42.5 MPa", "standard": "SANS 50197-1", "type": "CEM I"}',
 ARRAY['cement', 'portland', 'construction', '42.5']),
('mat_002', 'Steel Rebar 12mm', 'steel', 'per meter', 
 'High-tensile steel reinforcement bar',
 '{"diameter": "12mm", "grade": "450W", "standard": "SANS 920"}',
 ARRAY['steel', 'rebar', 'reinforcement', '12mm']);
```

#### Common Queries

```sql
-- Search materials by name
SELECT id, name, category, unit 
FROM materials 
WHERE to_tsvector('english', name) @@ to_tsquery('english', 'cement')
  AND active = TRUE;

-- Get materials by category
SELECT id, name, unit, specifications 
FROM materials 
WHERE category = 'cement' AND active = TRUE 
ORDER BY name;

-- Full-text search with keywords
SELECT id, name, category 
FROM materials 
WHERE keywords && ARRAY['cement', 'portland']
  AND active = TRUE;
```

---

### 3. suppliers

Information about building material suppliers.

**Purpose:** Supplier catalog, contact info, geolocation

#### Schema

```sql
CREATE EXTENSION IF NOT EXISTS postgis;

CREATE TABLE suppliers (
    id VARCHAR(50) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    website VARCHAR(500),
    location_address TEXT,
    location_coordinates GEOGRAPHY(POINT, 4326),
    region VARCHAR(100),
    contact_phone VARCHAR(50),
    contact_email VARCHAR(255),
    rating DECIMAL(3,2) CHECK (rating >= 0 AND rating <= 5),
    review_count INTEGER DEFAULT 0,
    categories TEXT[],
    opening_hours JSONB,
    payment_methods TEXT[],
    delivery_available BOOLEAN DEFAULT FALSE,
    active BOOLEAN DEFAULT TRUE,
    last_scraped TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_suppliers_region ON suppliers(region);
CREATE INDEX idx_suppliers_categories ON suppliers USING GIN(categories);
CREATE INDEX idx_suppliers_location ON suppliers USING GIST(location_coordinates);
CREATE INDEX idx_suppliers_active ON suppliers(active);
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | VARCHAR(50) | PRIMARY KEY | Supplier ID (e.g., 'sup_001') |
| `name` | VARCHAR(255) | NOT NULL | Supplier name |
| `website` | VARCHAR(500) | NULL | Website URL |
| `location_address` | TEXT | NULL | Physical address |
| `location_coordinates` | GEOGRAPHY | NULL | GPS coordinates (PostGIS) |
| `region` | VARCHAR(100) | NULL | South African region |
| `contact_phone` | VARCHAR(50) | NULL | Phone number |
| `contact_email` | VARCHAR(255) | NULL | Email address |
| `rating` | DECIMAL(3,2) | NULL | Average rating (0-5) |
| `review_count` | INTEGER | DEFAULT 0 | Number of reviews |
| `categories` | TEXT[] | NULL | Material categories sold |
| `opening_hours` | JSONB | NULL | Opening hours by day |
| `payment_methods` | TEXT[] | NULL | Accepted payment methods |
| `delivery_available` | BOOLEAN | DEFAULT FALSE | Delivery service available |
| `active` | BOOLEAN | DEFAULT TRUE | Active supplier |
| `last_scraped` | TIMESTAMP | NULL | Last scraping timestamp |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Creation timestamp |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Last update timestamp |

#### Sample Data

```sql
INSERT INTO suppliers (id, name, website, location_address, location_coordinates, region, contact_phone, categories, delivery_available) VALUES
('sup_001', 'Builders Warehouse Johannesburg', 'https://builderswarehouse.co.za',
 '123 Main Street, Johannesburg, Gauteng, 2000',
 ST_SetSRID(ST_MakePoint(28.0473, -26.2041), 4326),
 'Gauteng', '+27 11 123 4567',
 ARRAY['cement', 'steel', 'timber', 'sand'],
 TRUE);
```

#### Common Queries

```sql
-- Find nearby suppliers (within 25km)
SELECT id, name, location_address,
       ST_Distance(location_coordinates, ST_SetSRID(ST_MakePoint(28.0473, -26.2041), 4326)) / 1000 AS distance_km
FROM suppliers
WHERE ST_DWithin(location_coordinates, ST_SetSRID(ST_MakePoint(28.0473, -26.2041), 4326), 25000)
  AND active = TRUE
ORDER BY distance_km;

-- Get suppliers by category
SELECT id, name, region, rating 
FROM suppliers 
WHERE 'cement' = ANY(categories) AND active = TRUE 
ORDER BY rating DESC;
```

---

### 4. prices

Historical price records for materials from suppliers.

**Purpose:** Price tracking, historical analysis, trend detection

#### Schema

```sql
CREATE TABLE prices (
    id BIGSERIAL PRIMARY KEY,
    material_id VARCHAR(50) NOT NULL REFERENCES materials(id),
    supplier_id VARCHAR(50) NOT NULL REFERENCES suppliers(id),
    price DECIMAL(10,2) NOT NULL CHECK (price >= 0),
    quantity INTEGER DEFAULT 1,
    unit VARCHAR(50),
    region VARCHAR(100),
    in_stock BOOLEAN DEFAULT TRUE,
    bulk_pricing JSONB,
    metadata JSONB,
    date DATE NOT NULL,
    scraped_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    CONSTRAINT unique_price_record UNIQUE (material_id, supplier_id, date)
);

CREATE INDEX idx_prices_material ON prices(material_id);
CREATE INDEX idx_prices_supplier ON prices(supplier_id);
CREATE INDEX idx_prices_date ON prices(date DESC);
CREATE INDEX idx_prices_material_date ON prices(material_id, date DESC);
CREATE INDEX idx_prices_region ON prices(region);

-- Partition by date (monthly) for performance
CREATE TABLE prices_2026_03 PARTITION OF prices
    FOR VALUES FROM ('2026-03-01') TO ('2026-04-01');
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | BIGSERIAL | PRIMARY KEY | Unique price record ID |
| `material_id` | VARCHAR(50) | FK to materials | Material reference |
| `supplier_id` | VARCHAR(50) | FK to suppliers | Supplier reference |
| `price` | DECIMAL(10,2) | NOT NULL, CHECK >= 0 | Price in ZAR |
| `quantity` | INTEGER | DEFAULT 1 | Quantity for price |
| `unit` | VARCHAR(50) | NULL | Unit of measurement |
| `region` | VARCHAR(100) | NULL | Regional pricing |
| `in_stock` | BOOLEAN | DEFAULT TRUE | Stock availability |
| `bulk_pricing` | JSONB | NULL | Bulk pricing tiers |
| `metadata` | JSONB | NULL | Additional metadata |
| `date` | DATE | NOT NULL | Price effective date |
| `scraped_at` | TIMESTAMP | DEFAULT NOW() | Scraping timestamp |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Creation timestamp |

#### Sample Data

```sql
INSERT INTO prices (material_id, supplier_id, price, unit, region, date, bulk_pricing) VALUES
('mat_001', 'sup_001', 145.50, '50kg bag', 'Gauteng', '2026-03-15',
 '[{"quantity": 10, "price_per_unit": 140.00}, {"quantity": 50, "price_per_unit": 135.00}]'),
('mat_001', 'sup_002', 148.00, '50kg bag', 'Gauteng', '2026-03-15', NULL);
```

#### Common Queries

```sql
-- Get latest prices for a material
SELECT s.name AS supplier, p.price, p.date
FROM prices p
JOIN suppliers s ON p.supplier_id = s.id
WHERE p.material_id = 'mat_001'
  AND p.date = (SELECT MAX(date) FROM prices WHERE material_id = 'mat_001')
ORDER BY p.price;

-- Price history for a material
SELECT date, AVG(price) as avg_price, MIN(price) as min_price, MAX(price) as max_price
FROM prices
WHERE material_id = 'mat_001'
  AND date >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY date
ORDER BY date;

-- Detect price anomalies (>10% change)
WITH price_changes AS (
    SELECT 
        material_id, 
        date, 
        price,
        LAG(price) OVER (PARTITION BY material_id, supplier_id ORDER BY date) as prev_price
    FROM prices
)
SELECT material_id, date, price, prev_price,
       ((price - prev_price) / prev_price * 100) as pct_change
FROM price_changes
WHERE ABS((price - prev_price) / prev_price) > 0.10
ORDER BY ABS(pct_change) DESC;
```

---

### 5. forecasts

ML model predictions for future material prices.

**Purpose:** Store ML forecasts, model metadata, confidence intervals

#### Schema

```sql
CREATE TABLE forecasts (
    id BIGSERIAL PRIMARY KEY,
    material_id VARCHAR(50) NOT NULL REFERENCES materials(id),
    forecast_date DATE NOT NULL,
    predicted_price DECIMAL(10,2) NOT NULL CHECK (predicted_price >= 0),
    confidence_lower DECIMAL(10,2),
    confidence_upper DECIMAL(10,2),
    confidence_level DECIMAL(5,4) CHECK (confidence_level >= 0 AND confidence_level <= 1),
    model_name VARCHAR(100) NOT NULL,
    model_version VARCHAR(50),
    region VARCHAR(100),
    generated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    CONSTRAINT unique_forecast UNIQUE (material_id, forecast_date, model_name, model_version)
);

CREATE INDEX idx_forecasts_material ON forecasts(material_id);
CREATE INDEX idx_forecasts_date ON forecasts(forecast_date);
CREATE INDEX idx_forecasts_model ON forecasts(model_name);
CREATE INDEX idx_forecasts_generated ON forecasts(generated_at DESC);
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | BIGSERIAL | PRIMARY KEY | Unique forecast ID |
| `material_id` | VARCHAR(50) | FK to materials | Material reference |
| `forecast_date` | DATE | NOT NULL | Date of prediction |
| `predicted_price` | DECIMAL(10,2) | NOT NULL | Predicted price |
| `confidence_lower` | DECIMAL(10,2) | NULL | Lower confidence bound |
| `confidence_upper` | DECIMAL(10,2) | NULL | Upper confidence bound |
| `confidence_level` | DECIMAL(5,4) | NULL | Confidence level (0-1) |
| `model_name` | VARCHAR(100) | NOT NULL | ML model name |
| `model_version` | VARCHAR(50) | NULL | Model version |
| `region` | VARCHAR(100) | NULL | Regional forecast |
| `generated_at` | TIMESTAMP | DEFAULT NOW() | Forecast generation time |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Creation timestamp |

#### Sample Data

```sql
INSERT INTO forecasts (material_id, forecast_date, predicted_price, confidence_lower, confidence_upper, confidence_level, model_name, model_version) VALUES
('mat_001', '2026-03-16', 146.20, 143.50, 148.90, 0.85, 'LSTM', 'v1.0'),
('mat_001', '2026-03-17', 146.80, 143.80, 149.80, 0.83, 'LSTM', 'v1.0');
```

#### Common Queries

```sql
-- Get latest forecast for a material
SELECT forecast_date, predicted_price, confidence_lower, confidence_upper, model_name
FROM forecasts
WHERE material_id = 'mat_001'
  AND forecast_date >= CURRENT_DATE
  AND generated_at = (SELECT MAX(generated_at) FROM forecasts WHERE material_id = 'mat_001')
ORDER BY forecast_date;

-- Compare model performance
SELECT model_name, model_version,
       AVG(confidence_level) as avg_confidence,
       COUNT(*) as forecast_count
FROM forecasts
WHERE generated_at >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY model_name, model_version
ORDER BY avg_confidence DESC;
```

---

### 6. favorites

User-saved favorite materials.

**Purpose:** User preferences, quick access

#### Schema

```sql
CREATE TABLE favorites (
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    material_id VARCHAR(50) NOT NULL REFERENCES materials(id) ON DELETE CASCADE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    PRIMARY KEY (user_id, material_id)
);

CREATE INDEX idx_favorites_user ON favorites(user_id);
CREATE INDEX idx_favorites_material ON favorites(material_id);
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `user_id` | UUID | FK to users | User reference |
| `material_id` | VARCHAR(50) | FK to materials | Material reference |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Favorited timestamp |

#### Sample Data

```sql
INSERT INTO favorites (user_id, material_id) VALUES
('user-uuid-1', 'mat_001'),
('user-uuid-1', 'mat_002'),
('user-uuid-2', 'mat_001');
```

#### Common Queries

```sql
-- Get user's favorites with current prices
SELECT m.id, m.name, m.category, AVG(p.price) as avg_price
FROM favorites f
JOIN materials m ON f.material_id = m.id
LEFT JOIN prices p ON p.material_id = m.id 
    AND p.date = CURRENT_DATE
WHERE f.user_id = 'user-uuid-1'
GROUP BY m.id, m.name, m.category
ORDER BY f.created_at DESC;
```

---

### 7. price_alerts

User-configured price threshold alerts.

**Purpose:** Price monitoring, user notifications

#### Schema

```sql
CREATE TABLE price_alerts (
    id BIGSERIAL PRIMARY KEY,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    material_id VARCHAR(50) NOT NULL REFERENCES materials(id) ON DELETE CASCADE,
    threshold_price DECIMAL(10,2) NOT NULL CHECK (threshold_price >= 0),
    threshold_type VARCHAR(20) NOT NULL CHECK (threshold_type IN ('above', 'below')),
    active BOOLEAN DEFAULT TRUE,
    last_triggered TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_alerts_user ON price_alerts(user_id);
CREATE INDEX idx_alerts_material ON price_alerts(material_id);
CREATE INDEX idx_alerts_active ON price_alerts(active);
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | BIGSERIAL | PRIMARY KEY | Unique alert ID |
| `user_id` | UUID | FK to users | User reference |
| `material_id` | VARCHAR(50) | FK to materials | Material reference |
| `threshold_price` | DECIMAL(10,2) | NOT NULL | Alert threshold |
| `threshold_type` | VARCHAR(20) | NOT NULL | 'above' or 'below' |
| `active` | BOOLEAN | DEFAULT TRUE | Alert active status |
| `last_triggered` | TIMESTAMP | NULL | Last trigger time |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Creation timestamp |
| `updated_at` | TIMESTAMP | DEFAULT NOW() | Last update timestamp |

#### Sample Data

```sql
INSERT INTO price_alerts (user_id, material_id, threshold_price, threshold_type) VALUES
('user-uuid-1', 'mat_001', 140.00, 'below'),
('user-uuid-1', 'mat_002', 50.00, 'above');
```

#### Common Queries

```sql
-- Check for triggered alerts
SELECT pa.id, pa.user_id, m.name, pa.threshold_price, pa.threshold_type, p.price as current_price
FROM price_alerts pa
JOIN materials m ON pa.material_id = m.id
JOIN prices p ON p.material_id = pa.material_id
WHERE pa.active = TRUE
  AND p.date = CURRENT_DATE
  AND (
    (pa.threshold_type = 'below' AND p.price < pa.threshold_price) OR
    (pa.threshold_type = 'above' AND p.price > pa.threshold_price)
  );
```

---

### 8. scraping_jobs

Tracking of web scraping jobs.

**Purpose:** Job monitoring, error logging, performance tracking

#### Schema

```sql
CREATE TABLE scraping_jobs (
    id VARCHAR(50) PRIMARY KEY,
    supplier_id VARCHAR(50) REFERENCES suppliers(id),
    status VARCHAR(50) NOT NULL CHECK (status IN ('queued', 'running', 'completed', 'failed')),
    materials_found INTEGER DEFAULT 0,
    prices_updated INTEGER DEFAULT 0,
    errors_count INTEGER DEFAULT 0,
    error_log JSONB,
    started_at TIMESTAMP WITH TIME ZONE,
    completed_at TIMESTAMP WITH TIME ZONE,
    duration_seconds INTEGER,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_jobs_supplier ON scraping_jobs(supplier_id);
CREATE INDEX idx_jobs_status ON scraping_jobs(status);
CREATE INDEX idx_jobs_created ON scraping_jobs(created_at DESC);
```

#### Columns

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | VARCHAR(50) | PRIMARY KEY | Job ID (e.g., 'job_abc123') |
| `supplier_id` | VARCHAR(50) | FK to suppliers | Supplier reference |
| `status` | VARCHAR(50) | NOT NULL | Job status |
| `materials_found` | INTEGER | DEFAULT 0 | Materials discovered |
| `prices_updated` | INTEGER | DEFAULT 0 | Prices updated |
| `errors_count` | INTEGER | DEFAULT 0 | Error count |
| `error_log` | JSONB | NULL | Error details |
| `started_at` | TIMESTAMP | NULL | Job start time |
| `completed_at` | TIMESTAMP | NULL | Job completion time |
| `duration_seconds` | INTEGER | NULL | Job duration |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Creation timestamp |

#### Sample Data

```sql
INSERT INTO scraping_jobs (id, supplier_id, status, materials_found, prices_updated, started_at, completed_at, duration_seconds) VALUES
('job_abc123', 'sup_001', 'completed', 45, 45, NOW() - INTERVAL '5 minutes', NOW(), 300);
```

#### Common Queries

```sql
-- Get recent scraping history
SELECT id, supplier_id, status, materials_found, prices_updated, duration_seconds
FROM scraping_jobs
ORDER BY created_at DESC
LIMIT 10;

-- Get scraping success rate by supplier
SELECT supplier_id, 
       COUNT(*) as total_jobs,
       SUM(CASE WHEN status = 'completed' THEN 1 ELSE 0 END) as successful_jobs,
       AVG(duration_seconds) as avg_duration
FROM scraping_jobs
WHERE created_at >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY supplier_id;
```

---

## 🔧 Database Functions & Triggers

### Auto-update timestamp trigger

```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Apply to tables with updated_at column
CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_materials_updated_at BEFORE UPDATE ON materials
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_suppliers_updated_at BEFORE UPDATE ON suppliers
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

---

## 🎯 Performance Optimization

### Materialized Views

```sql
-- Material price statistics (updated daily)
CREATE MATERIALIZED VIEW material_price_stats AS
SELECT 
    m.id,
    m.name,
    m.category,
    AVG(p.price) as avg_price,
    MIN(p.price) as min_price,
    MAX(p.price) as max_price,
    STDDEV(p.price) as price_volatility,
    COUNT(DISTINCT p.supplier_id) as supplier_count,
    MAX(p.date) as last_updated
FROM materials m
LEFT JOIN prices p ON m.id = p.material_id
WHERE p.date >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY m.id, m.name, m.category;

CREATE UNIQUE INDEX ON material_price_stats(id);

-- Refresh daily
REFRESH MATERIALIZED VIEW CONCURRENTLY material_price_stats;
```

---

## 🔒 Security

### Row-Level Security (RLS)

```sql
-- Enable RLS on users table
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Users can only see their own data
CREATE POLICY users_select_own ON users
    FOR SELECT
    USING (auth.uid() = id);

-- Users can update their own data
CREATE POLICY users_update_own ON users
    FOR UPDATE
    USING (auth.uid() = id);
```

---

## 📚 Additional Resources

- [Entity Relationship Diagram](./ERD.png)
- [Migration Strategy](./MIGRATIONS.md)
- [System Architecture](../architecture/SYSTEM_ARCHITECTURE.md)
- [API Documentation](../api/README.md)

---

**Last Updated:** March 9, 2026  
**Database Version:** PostgreSQL 14+  
**Schema Version:** 1.0
