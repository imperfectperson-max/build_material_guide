# ADR-0002: Use Redis for Caching Layer

**Status:** Accepted  
**Date:** 2026-03-01  
**Decision Makers:** Backend Team  
**Consulted:** All team members  
**Informed:** Project stakeholders

---

## Context

### Problem Statement

The Building Materials Price Intelligence Platform requires a high-performance caching layer to reduce database load, improve API response times, and support additional features like rate limiting and JWT token blacklisting. Without caching, repeated price queries and ML predictions would overwhelm the database and result in poor user experience.

### Background

**Current Situation:**
- PostgreSQL database selected (ADR-0001)
- Expected high frequency of repeated queries (popular materials)
- ML inference operations are expensive (~250ms per prediction)
- Need for distributed rate limiting across API instances
- JWT token blacklist required for logout functionality

**Constraints:**
- Must use free tier or low-cost service for MVP
- Must be fast (<50ms response time)
- Must support distributed caching (multiple API instances)
- Must handle rate limiting counters
- Must support TTL (time-to-live) expiration

**Requirements:**
1. In-memory performance (<50ms latency)
2. Key-value store with TTL support
3. Support for multiple data structures (strings, sets, sorted sets)
4. Distributed/networked architecture
5. High availability
6. Free tier availability

### Goals

1. Achieve >70% cache hit rate for frequently accessed data
2. Reduce API response time to <50ms for cached queries
3. Reduce database load by 70%+
4. Support rate limiting (100 req/min per user)
5. Enable JWT token blacklist for security
6. Maintain simple, maintainable architecture

---

## Decision

**We will use Redis (via Redis Cloud) as the caching and data structure layer for the Building Materials Price Intelligence Platform.**

### Rationale

**Performance:**
- **In-Memory Speed:** Sub-millisecond data access (typically <10ms)
- **Network Overhead:** Redis Cloud provides low-latency connections
- **Throughput:** Can handle 100,000+ operations per second

**Feature Completeness:**
- **TTL Support:** Automatic expiration for price data (1hr) and forecasts (6hrs)
- **Data Structures:** Strings for cache, Sets for blacklist, Sorted Sets for rate limiting
- **Atomic Operations:** INCR for rate limiting counters
- **Pub/Sub:** Future feature possibility (real-time price updates)

**Two-Tier Caching Strategy:**
- **L1 Cache (Prices):** 1-hour TTL, >80% hit rate target
- **L2 Cache (ML Forecasts):** 6-hour TTL, >70% hit rate target
- **JWT Blacklist:** TTL = token expiry (24 hours)
- **Rate Limiting:** 1-minute sliding window counters

**Operational Benefits:**
- **Redis Cloud Free Tier:** 30MB storage (sufficient for MVP)
- **Managed Service:** No maintenance overhead
- **High Availability:** Built-in replication on paid tiers
- **Monitoring:** Built-in dashboard and metrics

**Research Validation:**
From TECHNOLOGY_CHOICES.md:
> "Redis-based rate limiting and two-tier caching (L1: Prices 1hr TTL, L2: Forecasts 6hr TTL) achieves >70% hit rate and <50ms response time."

### Implementation Notes

**Setup Steps:**
1. Create Redis Cloud account (free tier)
2. Configure Redis client in Node.js application
3. Implement cache-aside pattern for price queries
4. Implement rate limiting middleware
5. Implement JWT blacklist service
6. Set up cache warming for popular materials

**Cache Key Naming Convention:**
```
price:latest:{materialId}:{region}
forecast:{modelName}:{materialId}:{horizon}
blacklist:{jwtToken}
ratelimit:{userId}:{endpoint}
```

**Timeline:**
- Week 2: Redis Cloud setup and basic caching
- Week 4: Complete L1/L2 cache hierarchy
- Week 7: ML prediction caching (6-hour TTL)

---

## Alternatives Considered

### Alternative 1: Memcached

**Description:** Distributed memory caching system

**Pros:**
- ✅ Simple key-value store
- ✅ Fast in-memory caching
- ✅ Mature and stable

**Cons:**
- ❌ No native TTL per key (less flexible)
- ❌ No data structures (only strings)
- ❌ No persistence options
- ❌ Less suitable for rate limiting
- ❌ Cannot handle JWT blacklist efficiently

**Reason for Rejection:**
Lacks the data structure support needed for rate limiting and JWT blacklist. Redis offers all Memcached features plus additional capabilities we need.

### Alternative 2: In-Memory Node.js Cache (node-cache)

**Description:** Simple in-memory caching within Node.js process

**Pros:**
- ✅ No external dependency
- ✅ Zero latency (same process)
- ✅ Free (no hosting cost)

**Cons:**
- ❌ Not distributed (each API instance has separate cache)
- ❌ Cache doesn't persist across restarts
- ❌ Memory limited by Node.js process
- ❌ Cannot share state between instances
- ❌ Difficult to implement rate limiting across instances

**Reason for Rejection:**
Not suitable for distributed systems. When running multiple API instances, each would have its own cache, leading to inconsistency and inefficiency.

### Alternative 3: PostgreSQL Query Results Caching

**Description:** Cache query results in PostgreSQL using materialized views

**Pros:**
- ✅ No additional infrastructure
- ✅ Integrated with existing database

**Cons:**
- ❌ Slower than in-memory cache (disk-based)
- ❌ Refresh overhead (REFRESH MATERIALIZED VIEW)
- ❌ Cannot handle JWT blacklist
- ❌ Not suitable for rate limiting
- ❌ Less flexible TTL management

**Reason for Rejection:**
Materialized views are useful for analytics but not fast enough for real-time API caching. Does not solve rate limiting or JWT blacklist requirements.

### Alternative 4: No Caching

**Description:** Query database directly for every request

**Cons:**
- ❌ High database load (100+ queries/min)
- ❌ Slow response times (150ms+ per query)
- ❌ Poor user experience
- ❌ No way to implement distributed rate limiting
- ❌ No JWT blacklist (security risk)
- ❌ Expensive ML inference on every request

**Reason for Rejection:**
Unacceptable performance and security implications. Caching is essential for the application's success.

---

## Consequences

### Positive

- ✅ **Performance Improvement:** API response time reduced from 150ms to <50ms (3× faster)
- ✅ **Database Load Reduction:** 70-80% reduction in database queries
- ✅ **Scalability:** Can handle 100+ concurrent users without database overload
- ✅ **User Experience:** Near-instant response for frequently accessed materials
- ✅ **ML Cost Savings:** ML predictions cached for 6 hours, reducing inference costs
- ✅ **Rate Limiting:** Distributed rate limiting across API instances
- ✅ **Security:** JWT blacklist prevents compromised token reuse
- ✅ **Free MVP Hosting:** Redis Cloud 30MB free tier sufficient for MVP
- ✅ **Monitoring:** Built-in metrics for cache hit rate and performance

### Negative

- ⚠️ **Cache Invalidation Complexity:** Must invalidate cache when prices are updated
  - *Mitigation:* Use time-based expiration (1hr TTL), acceptable for price data
- ⚠️ **Memory Limitations:** Free tier limited to 30MB
  - *Mitigation:* Monitor usage, implement LRU eviction, upgrade if needed ($5/month for 100MB)
- ⚠️ **Single Point of Failure:** Redis outage affects cache (but not database)
  - *Mitigation:* Graceful fallback to database queries, consider replication on paid tier
- ⚠️ **Network Dependency:** Redis Cloud adds network hop
  - *Mitigation:* Choose region close to API servers (Johannesburg)
- ⚠️ **Stale Data Risk:** Cache may serve outdated data during TTL period
  - *Mitigation:* 1-hour TTL acceptable for price data (prices don't change that frequently)

### Neutral

- ℹ️ **Learning Curve:** Team must learn Redis commands and patterns
- ℹ️ **Operational Complexity:** One more service to monitor and maintain
- ℹ️ **Code Complexity:** Cache-aside pattern adds code complexity

---

## Compliance

### Security
- ✅ TLS encryption for data in transit
- ✅ Password authentication required
- ✅ JWT blacklist prevents token reuse after logout
- ✅ Rate limiting prevents brute force attacks

### Performance
- ✅ Target cache hit rate: >70% overall
- ✅ L1 cache (prices): >80% hit rate, <10ms response
- ✅ L2 cache (forecasts): >70% hit rate, <50ms response
- ✅ Overall API response: <50ms for cached queries

### Scalability
- ✅ Handles 100+ concurrent users
- ✅ 100,000+ operations per second capacity
- ✅ Upgrade path to 100MB+ storage on paid tiers
- ✅ Replication available for high availability

### Maintainability
- ✅ Standard Redis commands (widely documented)
- ✅ Simple key-value model
- ✅ Redis CLI for debugging
- ✅ Built-in monitoring dashboard

### Cost
- ✅ Free tier for MVP: $0/month (30MB)
- ✅ First paid tier: $5/month (100MB)
- ✅ Very cost-effective for performance benefits

---

## Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| Overall Cache Hit Rate | >70% | Redis INFO stats |
| L1 Cache Hit Rate (Prices) | >80% | Application metrics |
| L2 Cache Hit Rate (Forecasts) | >70% | Application metrics |
| Cache Response Time | <50ms | Application monitoring |
| Redis Memory Usage | <25MB (80% of free tier) | Redis Cloud dashboard |
| API Response Time (Cached) | <50ms | Application monitoring |
| Database Load Reduction | >70% | Compare query counts |

---

## Review Schedule

- **Initial Review:** Week 4 (April 1, 2026) - After cache hierarchy implementation
- **Performance Review:** Week 7 (April 22, 2026) - After ML caching
- **Final Review:** Week 11 (May 20, 2026) - Optimization phase
- **Trigger for Early Review:**
  - Cache hit rate <60%
  - Memory usage >25MB (83% of free tier)
  - Redis outages affecting service
  - Performance targets not met

---

## References

1. [Redis Documentation](https://redis.io/documentation)
2. [Redis Cloud Documentation](https://docs.redis.com/latest/rc/)
3. [Cache-Aside Pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/cache-aside)
4. [TECHNOLOGY_CHOICES.md](../../TECHNOLOGY_CHOICES.md) - Caching strategy research
5. [SECURITY.md](../../SECURITY.md) - Rate limiting and JWT blacklist requirements
6. Research: "Redis Best Practices" - [Article Link]
7. Research: "Two-Tier Caching Architecture" - [Article Link]

---

## Change History

| Date | Change | Author |
|------|--------|--------|
| 2026-03-01 | Initial draft | Backend Team |
| 2026-03-01 | Team review feedback incorporated | Backend Team |
| 2026-03-01 | Status changed to Accepted | All Team |

---

## Notes

**Cache Warming Strategy:**
- Identify top 20 most-searched materials (weekly)
- Pre-load their prices into cache every 6 hours
- Monitor cache hit rates and adjust strategy

**TTL Configuration:**
- L1 Cache (Prices): 1 hour (3600 seconds)
- L2 Cache (Forecasts): 6 hours (21600 seconds)
- JWT Blacklist: Token expiry time (24 hours = 86400 seconds)
- Rate Limit Counters: 1 minute (60 seconds)

**Fallback Strategy:**
```javascript
// Graceful fallback to database if Redis fails
async function getPrice(materialId) {
  try {
    const cached = await redis.get(`price:${materialId}`);
    if (cached) return JSON.parse(cached);
  } catch (err) {
    console.error('Redis error, falling back to database:', err);
  }
  
  // Fallback to database
  const price = await db.query('SELECT * FROM prices WHERE material_id = $1', [materialId]);
  return price;
}
```

**Monitoring Commands:**
```bash
# Check cache hit rate
redis-cli INFO stats | grep hit_rate

# Check memory usage
redis-cli INFO memory

# View all cache keys (dev only!)
redis-cli KEYS price:*

# Monitor commands in real-time
redis-cli MONITOR
```
