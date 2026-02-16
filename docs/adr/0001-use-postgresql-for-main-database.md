# ADR-0001: Use PostgreSQL for Main Database

**Status:** Accepted  
**Date:** 2026-03-01  
**Decision Makers:** Backend Team, ML Team  
**Consulted:** All team members  
**Informed:** Project stakeholders

---

## Context

### Problem Statement

The Building Materials Price Intelligence Platform requires a robust database system to store and query historical price data, material information, supplier details, forecasts, and user data. The database must support complex queries, time-series analysis, geolocation features, and maintain ACID properties for financial data integrity.

### Background

**Current Situation:**
- New project, no existing database infrastructure
- Need to support 2000+ materials, 50+ suppliers, 100,000+ price records
- Required features: time-series queries, geolocation search, JSON storage, full-text search

**Constraints:**
- Must use free tier or low-cost hosting for MVP
- Team has experience with SQL databases
- Must support relational data with complex relationships
- Performance requirement: <200ms query response time for price data

**Requirements:**
1. ACID compliance for price and transaction data
2. Support for time-series data and temporal queries
3. Geolocation capabilities (PostGIS)
4. JSON/JSONB support for flexible metadata
5. Full-text search for materials
6. Strong community support and documentation
7. Free tier availability for MVP

### Goals

1. Select a database that meets all technical requirements
2. Minimize learning curve for team
3. Keep costs low during MVP phase
4. Ensure scalability path for future growth
5. Maintain data integrity and security

---

## Decision

**We will use PostgreSQL (via Supabase) as the main database for the Building Materials Price Intelligence Platform.**

### Rationale

**Technical Fit:**
- **Relational Model:** Perfect fit for our structured price data with clear relationships (materials ↔ prices ↔ suppliers)
- **Time-Series Support:** Native support for date/time operations, window functions, and efficient date-range queries
- **PostGIS Extension:** Industry-standard for geolocation queries (supplier nearby search)
- **JSONB:** Flexible storage for material specifications, bulk pricing, and metadata
- **Full-Text Search:** Built-in support for material name/description search

**Operational Benefits:**
- **Supabase Hosting:** Free tier provides 500MB storage, 2GB bandwidth, automatic backups
- **Row-Level Security (RLS):** Built-in multi-tenancy and security features
- **Real-time Subscriptions:** Potential for live price updates (future feature)
- **Mature Ecosystem:** Extensive tools, libraries, and community support

**Team Alignment:**
- Team has SQL experience (database courses, previous projects)
- Well-documented with extensive learning resources
- Standard technology choice in industry

**Research Support:**
- Widely used in production systems
- Proven scalability (can handle millions of rows)
- Active development and long-term support

### Implementation Notes

**Setup Steps:**
1. Create Supabase project (free tier)
2. Design and implement database schema
3. Enable PostGIS extension for geolocation
4. Configure RLS policies for security
5. Set up automated backups
6. Implement connection pooling

**Timeline:**
- Week 1: Schema design and Supabase setup
- Week 2: Initial migrations and data seeding
- Week 3: Query optimization and indexing

**Resource Requirements:**
- Free tier sufficient for MVP (500MB, 2GB bandwidth)
- Upgrade path: $25/month for 8GB storage if needed post-MVP

---

## Alternatives Considered

### Alternative 1: MongoDB (NoSQL)

**Description:** Document-oriented NoSQL database with flexible schema

**Pros:**
- ✅ Flexible schema (JSON-native)
- ✅ Horizontal scalability
- ✅ Free Atlas tier (512MB)

**Cons:**
- ❌ No native geolocation support (requires add-ons)
- ❌ Weaker ACID guarantees (important for price data)
- ❌ Less efficient for relational queries
- ❌ Team less familiar with NoSQL

**Reason for Rejection:** 
The relational nature of our data (materials have many prices from many suppliers) is a better fit for SQL. ACID compliance is critical for financial price data. PostGIS support for geolocation is superior.

### Alternative 2: MySQL

**Description:** Popular open-source relational database

**Pros:**
- ✅ Mature and widely used
- ✅ Good performance
- ✅ Free hosting options available

**Cons:**
- ❌ Weaker JSON support (no JSONB equivalent)
- ❌ Less sophisticated geolocation support
- ❌ Fewer advanced features (window functions, CTEs)
- ❌ Not available on Supabase

**Reason for Rejection:**
PostgreSQL offers superior features for our use case, particularly JSONB, PostGIS, and advanced SQL features. Supabase provides better developer experience with PostgreSQL.

### Alternative 3: SQLite

**Description:** Lightweight, file-based SQL database

**Pros:**
- ✅ Zero configuration
- ✅ Perfect for development
- ✅ No hosting costs

**Cons:**
- ❌ Not suitable for production web applications
- ❌ No concurrent write support
- ❌ No user management
- ❌ No cloud hosting

**Reason for Rejection:**
Not production-ready for a multi-user web application. Would require migration to another database for production deployment.

### Alternative 4: Do Nothing / Defer Decision

**Description:** Start without a database, use file storage

**Cons:**
- ❌ Cannot build any backend features
- ❌ No way to store user accounts
- ❌ No way to cache scraped price data
- ❌ Blocks all development progress

**Reason for Rejection:**
A database is fundamental to the application architecture. Cannot proceed without one.

---

## Consequences

### Positive

- ✅ **Relational Integrity:** Foreign keys ensure data consistency between materials, prices, and suppliers
- ✅ **Query Performance:** Efficient indexing and query optimization for price comparisons and historical analysis
- ✅ **Geolocation Features:** PostGIS enables nearby supplier search with accurate distance calculations
- ✅ **Flexible Metadata:** JSONB allows storing material specifications without rigid schema changes
- ✅ **Free MVP Hosting:** Supabase free tier covers all MVP needs (500MB, sufficient for 100K+ price records)
- ✅ **Team Productivity:** Familiar SQL syntax reduces learning curve
- ✅ **Security Features:** Row-Level Security provides built-in multi-tenancy
- ✅ **Scalability Path:** Can upgrade to paid tiers or self-hosted PostgreSQL as needed

### Negative

- ⚠️ **Vendor Lock-in (Supabase):** Using Supabase-specific features (RLS, real-time) could make migration harder
  - *Mitigation:* Use standard PostgreSQL features where possible, document Supabase-specific usage
- ⚠️ **Schema Rigidity:** Schema changes require migrations (vs. NoSQL flexibility)
  - *Mitigation:* Use JSONB for truly flexible data, plan schema carefully upfront
- ⚠️ **Vertical Scaling Limits:** PostgreSQL scales vertically better than horizontally
  - *Mitigation:* Implement caching layer (Redis) to reduce database load
- ⚠️ **Free Tier Limits:** 500MB storage, 2GB bandwidth could be exceeded with rapid growth
  - *Mitigation:* Monitor usage, upgrade to paid tier if needed ($25/month)

### Neutral

- ℹ️ **SQL Expertise Required:** Team must be proficient in SQL for complex queries
- ℹ️ **Migration Management:** Need to implement database migration strategy (Prisma Migrate)

---

## Compliance

### Security
- ✅ ACID compliance ensures data integrity
- ✅ RLS provides fine-grained access control
- ✅ Supports encrypted connections (SSL/TLS)
- ✅ Automatic backups on Supabase

### Performance
- ✅ Target: <200ms query time for price data
- ✅ Indexes on frequently queried columns
- ✅ Query optimization with EXPLAIN ANALYZE
- ✅ Connection pooling to handle concurrent requests

### Scalability
- ✅ Handles 100K+ price records in free tier
- ✅ Upgrade path to millions of records
- ✅ Vertical scaling via Supabase tiers
- ✅ Read replicas available on paid tiers

### Maintainability
- ✅ Standard SQL reduces maintenance complexity
- ✅ Mature tools (pgAdmin, DBeaver)
- ✅ Extensive documentation
- ✅ Migration framework (Prisma Migrate)

### Cost
- ✅ Free tier for MVP: $0/month
- ✅ First paid tier: $25/month (8GB storage)
- ✅ Cost-effective for long-term operation

---

## Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| Query Response Time | <200ms (95th percentile) | Application monitoring |
| Database Uptime | >99.5% | Supabase dashboard |
| Storage Usage | <400MB (80% of free tier) | Supabase dashboard |
| Connection Pool Usage | <50% capacity | PostgreSQL stats |
| Price Query Hit Rate | >80% from cache | Redis monitoring |

---

## Review Schedule

- **Initial Review:** Week 4 (April 1, 2026) - Post-MVP launch
- **Follow-up Review:** Week 13 (June 9, 2026) - End of project
- **Trigger for Early Review:** 
  - Storage exceeds 400MB (80% of free tier)
  - Query performance degrades below 200ms
  - Significant new requirements emerge

---

## References

1. [PostgreSQL Documentation](https://www.postgresql.org/docs/)
2. [PostGIS Documentation](https://postgis.net/documentation/)
3. [Supabase Documentation](https://supabase.com/docs)
4. [TECHNOLOGY_CHOICES.md](../../TECHNOLOGY_CHOICES.md) - Original technology research
5. Research: "PostgreSQL vs MySQL: Which is Better?" - [Article Link]
6. Research: "Time-Series Data in PostgreSQL" - [Article Link]

---

## Change History

| Date | Change | Author |
|------|--------|--------|
| 2026-03-01 | Initial draft | Backend Team |
| 2026-03-01 | Team review feedback incorporated | Backend Team |
| 2026-03-01 | Status changed to Accepted | All Team |

---

## Notes

- PostGIS extension must be enabled manually: `CREATE EXTENSION IF NOT EXISTS postgis;`
- Consider TimescaleDB extension for time-series optimization if performance becomes an issue (future enhancement)
- Monitor query performance closely during Week 2-3 as data volume increases
- Document all Supabase-specific features used for easier migration path if needed
