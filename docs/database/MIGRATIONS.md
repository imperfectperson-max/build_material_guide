# 🔄 Database Migrations Guide

**Building Materials Price Intelligence Platform**  
**Migration Strategy and Guidelines**

---

## 📋 Overview

This document outlines the database migration strategy, best practices, and procedures for managing schema changes in the Building Materials Price Intelligence Platform. We use a structured approach to ensure safe, reversible, and trackable database schema evolution.

---

## 🎯 Migration Philosophy

### Core Principles

1. **Reversibility**: Every migration must have a rollback plan
2. **Atomicity**: Migrations should be atomic transactions when possible
3. **Zero-Downtime**: Production migrations should not require downtime
4. **Data Safety**: Never lose or corrupt data during migrations
5. **Documentation**: Every migration must be documented with purpose and impact
6. **Testing**: Test migrations on staging before production

---

## 🛠️ Migration Tools

### Primary Tool: Database Migration Framework

**Recommended Tools:**
- **Prisma Migrate** (Node.js/TypeScript)
- **Flyway** (Java-based, language-agnostic)
- **Liquibase** (XML/YAML/SQL based)
- **node-pg-migrate** (Node.js specific)

**Current Choice:** Prisma Migrate (aligns with Node.js backend)

### Installation

```bash
# Install Prisma
npm install -D prisma
npm install @prisma/client

# Initialize Prisma
npx prisma init
```

---

## 📁 Migration File Structure

```
prisma/
├── schema.prisma          # Prisma schema definition
└── migrations/
    ├── 20260301_init/
    │   └── migration.sql
    ├── 20260308_add_forecasts_table/
    │   └── migration.sql
    ├── 20260315_add_geolocation/
    │   └── migration.sql
    └── migration_lock.toml
```

### Naming Convention

Format: `YYYYMMDD_description`

Examples:
- `20260301_init` - Initial schema
- `20260308_add_forecasts_table` - Add forecasts table
- `20260315_add_supplier_rating` - Add rating column to suppliers

---

## 📝 Migration Template

### Forward Migration (Up)

```sql
-- Migration: Add price_alerts table
-- Author: Backend Developer
-- Date: 2026-03-15
-- Description: Create price_alerts table for user notifications

BEGIN;

-- Create table
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

-- Create indexes
CREATE INDEX idx_alerts_user ON price_alerts(user_id);
CREATE INDEX idx_alerts_material ON price_alerts(material_id);
CREATE INDEX idx_alerts_active ON price_alerts(active);

-- Add trigger for updated_at
CREATE TRIGGER update_price_alerts_updated_at 
    BEFORE UPDATE ON price_alerts
    FOR EACH ROW 
    EXECUTE FUNCTION update_updated_at_column();

COMMIT;
```

### Rollback Migration (Down)

```sql
-- Rollback: Remove price_alerts table
-- Date: 2026-03-15

BEGIN;

-- Drop trigger
DROP TRIGGER IF EXISTS update_price_alerts_updated_at ON price_alerts;

-- Drop table (CASCADE will remove dependent objects)
DROP TABLE IF EXISTS price_alerts CASCADE;

COMMIT;
```

---

## 🚀 Migration Workflow

### Development Environment

```bash
# 1. Create a new migration
npx prisma migrate dev --name add_price_alerts

# 2. Review the generated SQL
cat prisma/migrations/20260315_add_price_alerts/migration.sql

# 3. Test the migration
npm run test:migrations

# 4. Commit migration files
git add prisma/migrations/
git commit -m "Add price_alerts table migration"
```

### Staging Environment

```bash
# 1. Deploy migrations to staging
npx prisma migrate deploy

# 2. Verify schema
npx prisma db pull

# 3. Run integration tests
npm run test:integration

# 4. Verify data integrity
psql $DATABASE_URL -c "SELECT COUNT(*) FROM price_alerts;"
```

### Production Environment

```bash
# 1. Backup database
pg_dump $PROD_DATABASE_URL > backup_$(date +%Y%m%d_%H%M%S).sql

# 2. Create maintenance window (optional)
# curl -X POST https://api.buildmaterials.app/admin/maintenance-mode

# 3. Deploy migrations
npx prisma migrate deploy

# 4. Verify migration
psql $PROD_DATABASE_URL -c "\d price_alerts"

# 5. Smoke test
curl https://api.buildmaterials.app/health

# 6. Monitor for errors
tail -f /var/log/api.log

# 7. Exit maintenance mode
# curl -X POST https://api.buildmaterials.app/admin/maintenance-mode -d '{"enabled": false}'
```

---

## 🔄 Common Migration Scenarios

### 1. Adding a New Table

```sql
BEGIN;

CREATE TABLE new_table (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_new_table_name ON new_table(name);

COMMIT;
```

**Rollback:**
```sql
DROP TABLE IF EXISTS new_table CASCADE;
```

---

### 2. Adding a Column (Nullable)

```sql
-- Safe: No downtime required
BEGIN;

ALTER TABLE materials 
ADD COLUMN popularity_score INTEGER DEFAULT 0;

CREATE INDEX idx_materials_popularity ON materials(popularity_score);

COMMIT;
```

**Rollback:**
```sql
BEGIN;
DROP INDEX IF EXISTS idx_materials_popularity;
ALTER TABLE materials DROP COLUMN IF EXISTS popularity_score;
COMMIT;
```

---

### 3. Adding a Column (NOT NULL)

**Two-Phase Approach (Zero-Downtime):**

**Phase 1: Add nullable column with default**
```sql
BEGIN;

ALTER TABLE materials 
ADD COLUMN status VARCHAR(50) DEFAULT 'active';

-- Backfill existing rows
UPDATE materials SET status = 'active' WHERE status IS NULL;

COMMIT;
```

**Phase 2: Make column NOT NULL**
```sql
BEGIN;

ALTER TABLE materials 
ALTER COLUMN status SET NOT NULL;

ALTER TABLE materials
ADD CONSTRAINT check_status CHECK (status IN ('active', 'inactive', 'discontinued'));

COMMIT;
```

---

### 4. Renaming a Column

**Two-Phase Approach (Zero-Downtime):**

**Phase 1: Add new column, copy data**
```sql
BEGIN;

ALTER TABLE materials 
ADD COLUMN description_text TEXT;

UPDATE materials SET description_text = description;

COMMIT;
```

**Deploy application code to use new column**

**Phase 2: Remove old column**
```sql
BEGIN;

ALTER TABLE materials 
DROP COLUMN description;

ALTER TABLE materials
RENAME COLUMN description_text TO description;

COMMIT;
```

---

### 5. Changing Column Type

**Example: Increase VARCHAR size**

```sql
-- Safe: Compatible change
BEGIN;

ALTER TABLE materials 
ALTER COLUMN name TYPE VARCHAR(500);

COMMIT;
```

**Example: Change to incompatible type**

**Two-Phase Approach:**

**Phase 1: Add new column**
```sql
BEGIN;

ALTER TABLE suppliers 
ADD COLUMN rating_new DECIMAL(3,2);

-- Data conversion
UPDATE suppliers 
SET rating_new = CAST(rating AS DECIMAL(3,2));

COMMIT;
```

**Phase 2: Swap columns**
```sql
BEGIN;

ALTER TABLE suppliers DROP COLUMN rating;
ALTER TABLE suppliers RENAME COLUMN rating_new TO rating;

COMMIT;
```

---

### 6. Adding a Foreign Key

```sql
BEGIN;

-- Add column
ALTER TABLE prices 
ADD COLUMN region_id INTEGER;

-- Backfill data (if needed)
UPDATE prices p 
SET region_id = r.id 
FROM regions r 
WHERE p.region = r.name;

-- Add foreign key constraint
ALTER TABLE prices
ADD CONSTRAINT fk_prices_region 
FOREIGN KEY (region_id) REFERENCES regions(id);

-- Add index
CREATE INDEX idx_prices_region_id ON prices(region_id);

COMMIT;
```

---

### 7. Removing a Column

**Two-Phase Approach (Zero-Downtime):**

**Phase 1: Make column nullable, stop using in app**
```sql
BEGIN;

ALTER TABLE materials 
ALTER COLUMN deprecated_field DROP NOT NULL;

COMMIT;
```

**Deploy application code that doesn't use the column**

**Phase 2: Drop column**
```sql
BEGIN;

ALTER TABLE materials 
DROP COLUMN deprecated_field;

COMMIT;
```

---

### 8. Creating an Index (Large Table)

```sql
-- Use CONCURRENTLY to avoid locking table
CREATE INDEX CONCURRENTLY idx_prices_date_material 
ON prices(date, material_id);

-- Note: Cannot be run inside a transaction block
```

---

### 9. Data Migration

```sql
BEGIN;

-- Example: Populate new denormalized field
UPDATE materials m
SET avg_price = (
    SELECT AVG(price)
    FROM prices p
    WHERE p.material_id = m.id
      AND p.date >= CURRENT_DATE - INTERVAL '30 days'
);

COMMIT;
```

---

## ⚠️ Migration Best Practices

### DO

✅ **Test migrations on staging first**
✅ **Backup database before production migration**
✅ **Use transactions for atomic changes**
✅ **Add migrations to version control**
✅ **Document the purpose of each migration**
✅ **Use two-phase approach for risky changes**
✅ **Create indexes CONCURRENTLY on large tables**
✅ **Monitor performance during and after migration**
✅ **Keep migrations small and focused**
✅ **Have a rollback plan**

### DON'T

❌ **Never run untested migrations on production**
❌ **Don't modify existing migration files**
❌ **Avoid large data migrations during peak hours**
❌ **Don't skip backing up production**
❌ **Avoid long-running transactions**
❌ **Don't drop columns without two-phase deployment**
❌ **Never ignore migration errors**
❌ **Don't make breaking schema changes without app deployment coordination**

---

## 🔍 Monitoring Migrations

### Check Migration Status

```bash
# Prisma
npx prisma migrate status

# Manual check
psql $DATABASE_URL -c "
SELECT * FROM _prisma_migrations 
ORDER BY finished_at DESC 
LIMIT 10;"
```

### Check for Long-Running Queries

```sql
SELECT 
    pid,
    now() - pg_stat_activity.query_start AS duration,
    query,
    state
FROM pg_stat_activity
WHERE state != 'idle'
  AND query NOT LIKE '%pg_stat_activity%'
ORDER BY duration DESC;
```

### Kill Long-Running Migration (Emergency)

```sql
-- Find the PID
SELECT pid, query FROM pg_stat_activity 
WHERE query LIKE '%ALTER TABLE%';

-- Terminate the process
SELECT pg_terminate_backend(pid);
```

---

## 🚨 Rollback Procedures

### Standard Rollback

```bash
# 1. Identify the migration to rollback
npx prisma migrate status

# 2. Restore from backup
pg_restore -d $DATABASE_URL backup_20260315.sql

# 3. Run rollback migration (if not using backup)
psql $DATABASE_URL < migrations/20260315_down.sql

# 4. Verify schema
npx prisma db pull

# 5. Deploy previous application version
git checkout previous-version
npm run deploy
```

### Emergency Rollback

```bash
# 1. Stop application traffic
curl -X POST https://api.buildmaterials.app/admin/maintenance-mode

# 2. Quick restore from latest backup
pg_restore --clean -d $DATABASE_URL latest_backup.sql

# 3. Verify database is accessible
psql $DATABASE_URL -c "SELECT COUNT(*) FROM users;"

# 4. Deploy stable application version
git checkout stable
npm run deploy:production

# 5. Resume traffic
curl -X POST https://api.buildmaterials.app/admin/maintenance-mode -d '{"enabled": false}'
```

---

## 📦 Database Seeding

### Seed Data for Development

```sql
-- prisma/seed.sql

-- Insert test users
INSERT INTO users (email, password_hash, name, role) VALUES
('test@example.com', '$2b$12$...', 'Test User', 'contractor'),
('admin@example.com', '$2b$12$...', 'Admin User', 'admin');

-- Insert test materials
INSERT INTO materials (id, name, category, unit, description) VALUES
('mat_test_001', 'Test Cement', 'cement', '50kg bag', 'Test material'),
('mat_test_002', 'Test Steel', 'steel', 'per meter', 'Test material');

-- Insert test suppliers
INSERT INTO suppliers (id, name, region) VALUES
('sup_test_001', 'Test Supplier 1', 'Gauteng'),
('sup_test_002', 'Test Supplier 2', 'Western Cape');

-- Insert test prices
INSERT INTO prices (material_id, supplier_id, price, date) VALUES
('mat_test_001', 'sup_test_001', 145.50, CURRENT_DATE),
('mat_test_002', 'sup_test_002', 50.00, CURRENT_DATE);
```

### Running Seeds

```bash
# Prisma seed
npx prisma db seed

# Manual seed
psql $DATABASE_URL < prisma/seed.sql
```

---

## 🔐 Security Considerations

### Sensitive Data Migrations

```sql
-- Bad: Logging sensitive data
SELECT * FROM users;  -- Don't log passwords

-- Good: Redact sensitive fields
SELECT id, email, role FROM users;

-- Encrypt sensitive columns
UPDATE users 
SET ssn = pgp_sym_encrypt(ssn, 'encryption_key');
```

### Permission Management

```sql
-- Grant minimal permissions for migration user
GRANT CONNECT ON DATABASE buildmat_db TO migration_user;
GRANT USAGE ON SCHEMA public TO migration_user;
GRANT CREATE ON SCHEMA public TO migration_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO migration_user;
```

---

## 📚 Migration Checklist

### Pre-Migration

- [ ] Review migration SQL
- [ ] Test on local database
- [ ] Test on staging database
- [ ] Backup production database
- [ ] Estimate migration duration
- [ ] Schedule maintenance window (if needed)
- [ ] Prepare rollback plan
- [ ] Notify team of migration

### During Migration

- [ ] Enable maintenance mode (if needed)
- [ ] Run migration
- [ ] Monitor for errors
- [ ] Verify migration success
- [ ] Run smoke tests

### Post-Migration

- [ ] Verify data integrity
- [ ] Check application functionality
- [ ] Monitor performance metrics
- [ ] Review logs for errors
- [ ] Disable maintenance mode
- [ ] Document any issues
- [ ] Update migration log

---

## 📊 Performance Impact Assessment

### Estimate Migration Time

```sql
-- Check table size
SELECT 
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size,
    pg_stat_user_tables.n_tup_ins + pg_stat_user_tables.n_tup_upd + pg_stat_user_tables.n_tup_del AS changes
FROM pg_tables
LEFT JOIN pg_stat_user_tables ON pg_tables.tablename = pg_stat_user_tables.relname
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;
```

### Lock Analysis

```sql
-- Check for table locks
SELECT 
    locktype,
    relation::regclass,
    mode,
    granted
FROM pg_locks
WHERE relation IS NOT NULL;
```

---

## 📖 Additional Resources

- [Prisma Migrate Documentation](https://www.prisma.io/docs/concepts/components/prisma-migrate)
- [PostgreSQL ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html)
- [Zero-Downtime Migrations](https://www.braintreepayments.com/blog/safe-operations-for-high-volume-postgresql/)
- [Database Schema](./SCHEMA.md)
- [System Architecture](../architecture/SYSTEM_ARCHITECTURE.md)

---

**Last Updated:** March 9, 2026  
**Migration Tool:** Prisma Migrate  
**Next Review:** Post-MVP (June 2026)
