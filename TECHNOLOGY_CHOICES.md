# 🛠️ Technology Choices & Justifications

**Building Materials Price Intelligence Platform**  
**Technical Stack Documentation**

---

## 📑 Overview

This document provides comprehensive justification for all technology choices made in the Building Materials Price Intelligence Platform. Each decision is grounded in research, cost considerations, performance requirements, and the specific needs of the South African construction market context.

---

## 🌐 Data Acquisition Layer

### Web Scraping Strategy

**Choice:** Custom web scraping solution with modern scraping tools

#### Rationale
1. **Real-Time Market Intelligence**
   - South African building material suppliers lack standardized APIs
   - Manual price checking is inefficient and error-prone
   - Construction companies that monitor supplier websites gain competitive advantage (research: 45% transitioning to advanced analytics)

2. **Cost Analysis**
   - Modern web scraping services: **$0.005 per page scrape**
   - 10 suppliers × 50 materials × 3 times/week = 150 scrapes/week
   - Monthly cost: ~$3 for 600 scrapes
   - Dramatically cheaper than manual labor or premium data services

3. **South African Supplier Landscape**
   - Fragmented market with regional pricing variations
   - Many suppliers publish prices online but don't offer API access
   - Enables dataset creation where none previously existed

#### Implementation Approach
- **Scheduled Collection**: Automated scraping 3× weekly to balance freshness vs. cost
- **PDF Extraction**: OCR capabilities for suppliers publishing PDF price lists
- **Historical Backfill**: Wayback Machine integration for historical data reconstruction
- **Data Validation**: Multi-step cleaning pipeline to ensure data quality

#### Research Foundation
> "Web scraping enables construction companies to transform into agile entities by monitoring suppliers' websites and tracking material availability and pricing fluctuations." — Datamam (2025)

---

## 🗄️ Backend Technology Stack

### Database: PostgreSQL (via Supabase)

**Choice:** PostgreSQL on Supabase platform

#### Rationale
1. **Relational Data Structure**
   - Price history is inherently relational (materials → suppliers → prices → dates)
   - Complex queries requiring JOINs across multiple dimensions
   - Strong ACID guarantees for financial data integrity

2. **Time-Series Optimization**
   - Excellent support for time-series queries (critical for price forecasting)
   - Efficient indexing strategies for date-range queries
   - Native support for window functions (moving averages, rolling calculations)

3. **Supabase Benefits**
   - **Free Tier**: 500MB database, 2GB bandwidth (sufficient for MVP)
   - Built-in authentication and row-level security
   - Real-time subscriptions for live price updates
   - Hosted PostgreSQL with automatic backups
   - No institutional funding dependency

#### Performance Characteristics
- **Query Performance**: Sub-100ms for typical price queries
- **Scalability**: Vertical scaling path available post-MVP
- **Reliability**: 99.9% uptime SLA on paid tiers

---

### Caching Layer: Redis

**Choice:** Redis on Redis Cloud (30MB free tier)

#### Rationale
1. **Performance Optimization**
   - **Target Metrics**:
     - Cache hit rate: **>70%** (stretch goal: 75%)
     - Response time: **<50ms** for cached queries
     - Uncached query baseline: ~150ms
   - **Performance Gain**: 3× speed improvement for cached requests

2. **Multi-Purpose Caching Architecture**
   - **L1 Cache**: Frequently accessed price data (materials, suppliers)
   - **L2 Cache**: ML model predictions (6-hour TTL)
   - **JWT Blacklist**: Token revocation for security
   - **Rate Limiting**: Request tracking per IP/user

3. **Cache Strategy Design**
   ```
   L1 Cache (Prices):         TTL = 1 hour,  Hit Rate Target: >80%
   L2 Cache (Forecasts):      TTL = 6 hours, Hit Rate Target: >70%
   JWT Blacklist:             TTL = Token expiry
   Rate Limiting Counters:    TTL = 1 minute
   ```

4. **Cost Efficiency**
   - Redis Cloud free tier: **30MB storage**
   - Sufficient for MVP with 2000+ price records
   - In-memory performance critical for real-time dashboards

#### Research-Backed Approach
Industry best practices for API caching:
- Short TTL for volatile data (prices)
- Longer TTL for expensive computations (ML predictions)
- Warming strategies for common queries

---

### API Architecture: RESTful Design

**Choice:** RESTful API with JWT authentication

#### Rationale
1. **REST Principles**
   - **Stateless**: Each request contains all necessary context (JWT token)
   - **Resource-Based**: Clear endpoints (`/api/materials`, `/api/forecasts`)
   - **Standard HTTP Methods**: GET, POST, PUT, DELETE for CRUD operations
   - **Cacheable**: HTTP caching headers leverage Redis layer

2. **JWT Authentication Benefits**
   - **Stateless Sessions**: No server-side session storage required
   - **Scalability**: Horizontal scaling without session replication
   - **Mobile-Friendly**: Token stored locally, works offline
   - **Cross-Platform**: Works identically for web and mobile clients

3. **Security Implementation**
   - **Token Expiry**: 24-hour lifespan with refresh mechanism
   - **Blacklist**: Redis-based revocation for logout/compromise
   - **Payload**: User ID, role, expiry (encrypted and signed)

#### API Endpoint Structure
```
Authentication:
  POST   /api/auth/login
  POST   /api/auth/register
  POST   /api/auth/logout

Data Access:
  GET    /api/materials
  GET    /api/materials/:id/prices
  GET    /api/suppliers
  GET    /api/suppliers/:id

Forecasting:
  GET    /api/forecast/:material_id?horizon=7
  POST   /api/forecast/batch

Admin:
  POST   /api/admin/scrape
  GET    /api/admin/metrics
```

---

## 🤖 Machine Learning Models

### Baseline Models

#### 1. Naïve Forecast
**Method:** Previous price persistence (t+1 = t)

**Rationale:** Establishes minimum performance baseline. Any ML model must outperform this trivial approach.

#### 2. Moving Average
**Method:** Simple moving average over 7-day or 30-day windows

**Rationale:** Smooths short-term volatility, common in construction industry forecasting.

#### 3. ARIMA (AutoRegressive Integrated Moving Average)
**Method:** Traditional statistical time-series model

**Rationale:**
- Industry standard for price forecasting
- **Benchmark for comparison**: Target 59% improvement over ARIMA
- Provides interpretable coefficients
- Established in construction economics literature

---

### Advanced Machine Learning Models

#### 1. XGBoost (Extreme Gradient Boosting)

**Choice Justification:**
1. **Complex Relationships**
   - Construction material prices influenced by multiple factors (supply chain, seasonality, regional differences)
   - Gradient boosting excels at finding non-linear patterns
   - Handles interaction effects between features automatically

2. **Regularization**
   - Built-in L1/L2 regularization prevents overfitting
   - Critical with limited historical data in early project stages
   - Automatic handling of missing values

3. **Research Foundation**
   > "Gradient boosting algorithms like XGBoost can effectively handle the non-linearity of time series data with efficient self-learning abilities." — Frifra et al. (2024)

4. **Performance**
   - Fast training on CPU (important for Kaggle free tier)
   - Efficient inference for real-time predictions
   - Feature importance analysis for interpretability

---

#### 2. Random Forest Regression

**Choice Justification:**
1. **Ensemble Robustness**
   - Combines multiple decision trees to reduce variance
   - Less sensitive to outliers than single models
   - Provides confidence intervals through tree variance

2. **Varied Dataset Performance**
   - South African market has regional pricing variations
   - Different materials exhibit different volatility patterns
   - Random Forest maintains accuracy across diverse scenarios

3. **Research Foundation**
   > "Random Forest with differentiated inputs proved to be the best performing method across scenarios with varying complexity, including situations with jumps, random walks, or combinations of both." — Comparative ML Study

4. **Operational Benefits**
   - No extensive hyperparameter tuning required
   - Naturally handles categorical features (regions, suppliers)
   - Robust to feature scaling issues

---

#### 3. Prophet (Facebook's Time-Series Model)

**Choice Justification:**
1. **Seasonality Modeling**
   - Construction industry has strong seasonal patterns
   - Summer vs. winter demand variations
   - Quarterly fiscal cycle effects on procurement

2. **Trend Detection**
   - Captures long-term price trends (inflation, supply chain shifts)
   - Handles structural breaks (e.g., COVID-19 supply shocks)
   - Automatic changepoint detection

3. **Holiday/Event Effects**
   - South African public holidays affect construction activity
   - End-of-financial-year purchasing spikes
   - Easily incorporates custom regressors

4. **Practical Advantages**
   - Intuitive hyperparameters for domain experts
   - Handles missing data gracefully
   - Fast fitting even on large datasets

---

#### 4. LSTM (Long Short-Term Memory Neural Networks)

**Choice Justification:**
1. **Time-Series with Long-Term Dependencies**
   - Captures patterns spanning weeks or months
   - Remembers historical price shocks and their durations
   - Gating mechanism preserves relevant long-term information

2. **Research Foundation — Key Result**
   > "LSTM models achieved RMSE values as low as 1.390 with MAPE values of 0.957, representing improvements of **up to 59% over traditional ARIMA models** for construction material price forecasting." — Lyu et al. (2025)

3. **Non-Linear Pattern Recognition**
   - Models complex relationships between multiple materials (steel price affects concrete demand)
   - Captures feedback loops in construction supply chain
   - Superior to linear models in volatile markets

4. **Implementation Strategy**
   - Training on Kaggle GPU (free tier: Tesla P100)
   - Pre-trained model deployment for inference
   - 6-hour caching to minimize compute costs

#### LSTM Architecture Considerations
- **Input Sequence Length**: 30-60 days of historical prices
- **Hidden Layers**: 2-3 LSTM layers with 50-100 units
- **Dropout**: 0.2-0.3 to prevent overfitting
- **Output**: 7-day and 30-day forecasts

---

### Model Evaluation Strategy

#### Metrics
1. **MAE (Mean Absolute Error)**
   - Interpretable: Average price prediction error in Rands
   - Robust to outliers
   - Primary metric for contractor decision-making

2. **RMSE (Root Mean Squared Error)**
   - Penalizes large errors more heavily
   - Standard in academic literature
   - Useful for comparing models

3. **MAPE (Mean Absolute Percentage Error)**
   - Scale-independent: Allows comparison across materials
   - Industry standard in forecasting
   - Percentage error intuitive for stakeholders

#### Comparative Evaluation
- **Baseline Comparison**: All models evaluated against ARIMA
- **Cross-Validation**: Time-series split (no future data leakage)
- **Material-Specific**: Separate evaluations for high-volatility vs. stable materials
- **Horizon Analysis**: Performance at 7-day vs. 30-day forecasts

---

## 🌐 Frontend Technology Stack

### Web Application: React Ecosystem

**Choice:** React with modern tooling

#### Rationale
1. **Component Reusability**
   - Price cards, charts, supplier comparisons all componentized
   - Maintains consistency across dashboard
   - Easier testing and maintenance

2. **State Management: React Query**
   - **Automatic Caching**: Complements backend Redis caching
   - **Stale-While-Revalidate**: Instant UI updates with background refresh
   - **Request Deduplication**: Multiple components requesting same data → single API call
   - **Optimistic Updates**: Snappy user experience

3. **Rich Ecosystem**
   - Recharts for data visualization (responsive, interactive)
   - Tailwind CSS for rapid styling without custom CSS
   - React Router for client-side routing
   - Large community and extensive documentation

#### Styling: Tailwind CSS

**Rationale:**
- **Utility-First**: Rapid prototyping without context switching
- **Responsive Design**: Mobile-first breakpoints built-in
- **Consistency**: Design system enforced through configuration
- **Bundle Size**: PurgeCSS removes unused styles in production

#### Visualization: Recharts

**Rationale:**
- **React-Native**: Seamless integration with React components
- **Responsive**: Automatic resizing for different screen sizes
- **Interactive**: Tooltips, zoom, pan for exploring price trends
- **Customizable**: Matches brand/design system easily

---

### Mobile Application: Flutter

**Choice:** Flutter for cross-platform mobile development

#### Rationale
1. **Cross-Platform Efficiency**
   - **Single Codebase**: iOS + Android from one source
   - **Cost Reduction**: 1 mobile developer instead of 2
   - **Consistent UX**: Identical experience across platforms
   - **Faster Iteration**: Changes deployed to both platforms simultaneously

2. **Performance**
   - Compiles to native ARM code (not web view)
   - 60fps animations standard
   - Smooth scrolling for long material lists
   - Fast startup time critical for field use

3. **Offline-First Design**
   - **Hive Database**: Lightweight, NoSQL, no native dependencies
   - **Local Storage**: Price data cached for offline access
   - **Sync Strategy**: Background sync when connectivity restored
   - Essential for construction sites with poor connectivity

4. **Rich Feature Set**
   - **ML Kit Integration**: Barcode scanning for quick material lookup
   - **Geolocation**: Find nearby suppliers automatically
   - **Push Notifications**: Price alert delivery
   - **Biometric Auth**: Fingerprint/Face ID for secure access

#### Flutter Advantages for Construction Context
- **Rugged UI**: Responsive even with work gloves (larger touch targets)
- **Bright Environments**: High-contrast theme for outdoor visibility
- **Battery Efficiency**: Important for all-day field use
- **Fast App Launch**: Quick access when comparing supplier quotes on-site

---

## ☁️ Infrastructure & Deployment

### Free-Tier Architecture Philosophy

**Strategic Decision:** Prioritize free-tier services to eliminate institutional funding dependency

#### Component Choices

1. **Supabase (Backend Database)**
   - **Free Tier**: 500MB database, 2GB bandwidth
   - **Sufficient For**: MVP with 2000+ price records
   - **Upgrade Path**: $25/month for production scale

2. **Redis Cloud (Caching)**
   - **Free Tier**: 30MB storage
   - **Sufficient For**: MVP caching needs
   - **Upgrade Path**: $5/month for 100MB

3. **Vercel (Web Hosting)**
   - **Free Tier**: Unlimited personal projects, 100GB bandwidth
   - **Benefits**: Automatic CI/CD, CDN distribution, preview deployments
   - **Performance**: Edge network for global low-latency

4. **Fly.io (Backend API Hosting)**
   - **Free Tier**: 3 shared VMs, 160GB bandwidth
   - **Benefits**: Deploy near users (Johannesburg region available)
   - **Scalability**: Easy upgrade to dedicated VMs

5. **Kaggle (ML Training)**
   - **Free Tier**: 30 hours/week GPU time (Tesla P100)
   - **Storage**: 20GB datasets, unlimited private notebooks
   - **Community**: Access to pre-trained models and datasets

---

### Infrastructure Scalability Considerations

#### Current Architecture (MVP)
```
User Request → Vercel CDN (Web) → Fly.io API → Redis Cache
                                            ↓ (miss)
                                      Supabase PostgreSQL
```

#### Future Scaling Path
- **Database**: Upgrade Supabase or migrate to managed PostgreSQL
- **Caching**: Redis cluster for high availability
- **API**: Horizontal scaling with load balancer on Fly.io
- **ML Inference**: Dedicated GPU instance or cloud ML service
- **CDN**: CloudFlare or AWS CloudFront for global distribution

---

### CI/CD Pipeline

**Strategy:** Automated deployment with GitHub Actions

#### Benefits
1. **Automated Testing**: Run tests before deployment
2. **Preview Deployments**: Each PR gets preview URL (Vercel)
3. **Rollback Capability**: Quick revert if issues detected
4. **Environment Management**: Separate dev/staging/production configs

#### Pipeline Stages
```
Git Push → Tests → Build → Deploy to Staging → Manual Approval → Production
```

---

## 🔒 Security Technology Choices

See [SECURITY.md](SECURITY.md) for comprehensive security architecture.

**Key Technologies:**
- **bcrypt/Argon2**: Password hashing
- **JWT**: Stateless authentication
- **Redis**: Token blacklist and rate limiting
- **HTTPS**: Encryption in transit
- **CORS**: Cross-origin resource control
- **Helmet.js**: HTTP header security (web)

---

## 📊 Monitoring & Observability

### Logging
- **Backend**: Structured JSON logging (Winston/Pino)
- **Frontend**: Error tracking (Sentry free tier)
- **Mobile**: Crash reporting (Firebase Crashlytics)

### Analytics
- **User Behavior**: Google Analytics 4 (free)
- **API Metrics**: Custom middleware for request tracking
- **Cache Performance**: Redis INFO commands for hit rate monitoring

### Alerting
- **Uptime**: Uptime Robot (free for 50 monitors)
- **Error Threshold**: Email/Slack notifications for error spikes
- **Performance**: Slow query detection and alerts

---

## 🎯 Technology Decision Summary

| Component | Technology | Key Reason | Cost (MVP) |
|-----------|-----------|------------|------------|
| Database | PostgreSQL (Supabase) | Relational structure, time-series queries | Free |
| Caching | Redis Cloud | Performance (3× faster), multi-purpose | Free |
| Backend API | RESTful + JWT | Stateless, scalable, mobile-friendly | Free |
| ML Models | XGBoost, RF, Prophet, LSTM | Research-backed 59% improvement | Free (Kaggle) |
| Web Frontend | React + Tailwind | Component reuse, rapid development | Free |
| Web Hosting | Vercel | CDN, auto-deploy, preview environments | Free |
| Mobile | Flutter + Hive | Cross-platform, offline-first | Free |
| API Hosting | Fly.io | Geographic proximity, easy scaling | Free |
| Charts | Recharts | React-native, interactive, responsive | Free |
| ML Training | Kaggle GPU | Free P100 access, 30hrs/week | Free |

**Total Infrastructure Cost (MVP Phase):** **$0/month**

---

## 🔬 Research-Backed Decisions

### Key Research Findings Influencing Choices

1. **LSTM Superiority** (Lyu et al., 2025)
   - 59% improvement over ARIMA → Justifies deep learning investment

2. **Web Scraping Viability** (Datamam, 2025)
   - 45% of construction companies transitioning to analytics → Market validation

3. **XGBoost Effectiveness** (Frifra et al., 2024)
   - Non-linear pattern recognition → Critical for volatile SA market

4. **Random Forest Robustness** (Comparative Studies)
   - Handles varied scenarios → Suitable for diverse material types

5. **Prophet for Seasonality** (Facebook Research)
   - Construction industry seasonal patterns → Natural fit

---

## 🚀 Future Technology Considerations

### Post-MVP Enhancements
1. **GraphQL API**: Replace REST for flexible queries
2. **WebSockets**: Real-time price updates without polling
3. **Serverless ML**: AWS Lambda or Google Cloud Functions for inference
4. **Time-Series DB**: TimescaleDB for optimized price history queries
5. **Advanced Caching**: Redis Cluster with read replicas
6. **Message Queue**: RabbitMQ/Redis Streams for async tasks

---

**Last Updated:** March 9, 2026  
**Tech Stack Version:** 1.0 (MVP)  
**Review Schedule:** After each major milestone
