# UML Diagrams Overview

## Building Materials Price Intelligence Platform

This document provides a visual overview of all UML diagrams created for the system.

---

## 📊 Use Case Diagram

The use case diagram captures all system interactions between actors and the platform.

### Key Actors:
1. **Contractor/User** - Primary end user (contractors, developers)
2. **Admin** - System administrator
3. **Data Scraper** - Automated data collection system
4. **ML Engine** - Machine learning inference system
5. **External Supplier Websites** - Data sources

### Use Case Categories:
- **Authentication & Authorization** (4 use cases)
  - Register Account, Login, Manage Profile, Reset Password
  
- **Price Comparison & Search** (6 use cases)
  - Search Materials, Compare Prices, View History, Filter by Region, Favorites, Barcode Scanning
  
- **Price Forecasting & Analytics** (4 use cases)
  - View Forecasts (7-30 days), Anomaly Alerts, Trend Analysis, Export Reports
  
- **Mobile Features** (4 use cases)
  - Find Nearby Suppliers, Push Notifications, Offline Access, Biometric Auth
  
- **Administration** (4 use cases)
  - User Management, System Monitoring, Data Source Configuration, Analytics Dashboard
  
- **Data Processing** (4 use cases)
  - Data Scraping, Validation, Storage, Caching
  
- **Machine Learning** (4 use cases)
  - Model Training, Prediction Generation, Anomaly Detection, Performance Evaluation

**Total Use Cases: 30**

---

## 🔄 Activity Diagrams

### 1. User Authentication Activity Diagram
**Purpose:** Documents the complete user login and authentication process

**Key Flows:**
- Credential validation (username/email + password)
- Password verification using bcrypt/Argon2
- JWT token generation and Redis storage
- Biometric authentication setup (mobile only)
- Failed login handling with account locking
- Rate limiting and security measures

**Decision Points:**
- Input validation
- User existence check
- Password match verification
- Mobile vs web platform
- Biometric acceptance
- Max login attempts exceeded

---

### 2. Price Comparison Activity Diagram
**Purpose:** Shows the workflow for comparing building material prices across suppliers

**Key Flows:**
- Material type selection
- JWT token validation
- Redis cache checking (< 50ms for cache hits)
- PostgreSQL database queries
- Price statistics calculation
- Regional filtering and favorites
- Mobile geolocation features
- Export to PDF/CSV

**Performance Optimization:**
- Cache-first strategy
- 1-hour cache TTL for price queries
- Parallel data fetching

---

### 3. Price Forecasting Activity Diagram
**Purpose:** Illustrates the ML-powered price prediction workflow

**Key Flows:**
- Forecast period selection (7-day or 30-day)
- Rate limiting checks
- Redis cache management (6-hour TTL)
- Multi-model inference (XGBoost, Random Forest, LSTM, Prophet)
- Ensemble prediction method
- Confidence interval calculation
- Price alert configuration

**ML Pipeline:**
1. Load historical data from PostgreSQL
2. Feature engineering (time-series, seasonality, economic indicators)
3. Parallel model inference across 4 models
4. Ensemble weighted averaging
5. Confidence interval computation
6. Result caching

---

### 4. Data Collection and Processing Activity Diagram
**Purpose:** Documents the automated data scraping and processing pipeline

**Key Flows:**
- Scheduled execution (daily at 2:00 AM)
- Web scraping from multiple suppliers
- PDF price list extraction using OCR
- Data validation and cleaning
- PostgreSQL transaction management
- Redis cache invalidation
- Feature engineering for ML
- Price change notifications

**Data Quality Measures:**
- Duplicate detection
- Outlier validation
- Material name verification
- Completeness checks
- Error logging and retry mechanisms

---

### 5. Mobile App Price Search Activity Diagram
**Purpose:** Shows mobile-specific user workflows and offline capabilities

**Key Flows:**
- Biometric authentication (fingerprint/Face ID)
- Multiple search methods:
  - Manual text entry
  - Barcode scanning (ML Kit)
  - Voice search (speech-to-text)
- Geolocation-based supplier filtering
- Offline mode with Hive local storage
- Push notification setup
- Favorites management

**Offline Capabilities:**
- Local data caching with Hive
- Offline browsing of cached prices
- Sync status indicators
- Graceful degradation

---

### 6. ML Model Training and Evaluation Activity Diagram
**Purpose:** Documents the complete machine learning development pipeline

**Key Flows:**
- Data preparation from PostgreSQL
- Feature engineering (time-series, seasonality, economic)
- Data splitting (70% train, 15% validation, 15% test)
- Parallel model training:
  - ARIMA (baseline)
  - XGBoost (gradient boosting)
  - Random Forest (ensemble)
  - Prophet (seasonality)
  - LSTM (deep learning, GPU-trained)
- Model evaluation (MAE, RMSE, MAPE, R²)
- Performance comparison
- Model deployment and versioning

**Success Criteria:**
- Target: >59% improvement over ARIMA baseline
- Metrics: MAE, RMSE, MAPE
- Deployment threshold validation

---

## 🎯 Diagram Coverage

### System Components Documented:
✅ User Authentication & Authorization  
✅ Price Comparison & Search  
✅ Price Forecasting (ML-driven)  
✅ Data Collection & Processing  
✅ Mobile Application Features  
✅ Machine Learning Pipeline  
✅ Caching Strategy (Redis)  
✅ Database Operations (PostgreSQL)  
✅ Offline Capabilities  
✅ Security Measures  

### Architectural Patterns Illustrated:
- **Caching Strategy**: Redis-based with TTL management
- **Authentication**: JWT + biometric (mobile)
- **ML Pipeline**: Training → Evaluation → Deployment
- **Data Flow**: Scraping → Validation → Storage → Processing
- **Mobile Offline-First**: Local storage with sync
- **Rate Limiting**: Redis-based API protection
- **Ensemble ML**: Multi-model prediction averaging

---

## 📁 File Organization

```
diagrams/
├── README.md                          # Comprehensive documentation
├── use_case_diagram.puml              # PlantUML use case diagram
├── use_case_diagram_mermaid.md        # Mermaid version (GitHub-friendly)
├── activity_authentication.puml       # Authentication flow
├── activity_price_comparison.puml     # Price comparison flow
├── activity_price_forecasting.puml    # ML forecasting flow
├── activity_data_collection.puml      # Data scraping pipeline
├── activity_mobile_search.puml        # Mobile search workflow
├── activity_ml_training.puml          # ML training pipeline
└── activity_diagrams_mermaid.md       # All activity diagrams in Mermaid
```

---

## 🛠️ How to Use These Diagrams

### For Developers:
- **Implementation Guide**: Use activity diagrams to understand detailed workflows
- **API Design**: Reference use cases for endpoint requirements
- **Testing**: Identify test scenarios from decision points

### For Project Managers:
- **Progress Tracking**: Map implementation against documented use cases
- **Feature Planning**: Use use case diagram for sprint planning
- **Communication**: Share diagrams with stakeholders

### For Documentation:
- **Technical Docs**: Embed diagrams in system documentation
- **Onboarding**: Use as training material for new team members
- **Requirements**: Reference for feature completeness

### For Quality Assurance:
- **Test Planning**: Each use case = test scenario
- **Edge Cases**: Decision points in activity diagrams = test cases
- **Integration Testing**: Follow activity diagram flows

---

## 🔗 Integration with Project Documentation

These diagrams complement:
- **[README.md](../README.md)** - High-level system overview
- **[ROADMAP.md](../ROADMAP.md)** - Implementation timeline
- **[TECHNOLOGY_CHOICES.md](../TECHNOLOGY_CHOICES.md)** - Technical stack details
- **[SECURITY.md](../SECURITY.md)** - Security architecture
- **[RESEARCH_FOUNDATION.md](../RESEARCH_FOUNDATION.md)** - ML research basis

---

## 📈 Metrics Captured in Diagrams

Performance Targets:
- **Cache Response Time**: < 50ms (Redis hit)
- **Cache Hit Rate**: > 70% target
- **ML Prediction Cache**: 6-hour TTL
- **Price Query Cache**: 1-hour TTL
- **API Rate Limiting**: Redis-based
- **ML Improvement**: > 59% over baseline (ARIMA)

---

## ✅ Completeness Checklist

- [x] Use case diagram with all actors
- [x] Authentication workflow
- [x] Price comparison workflow
- [x] Price forecasting (ML) workflow
- [x] Data collection pipeline
- [x] Mobile-specific workflows
- [x] ML training pipeline
- [x] PlantUML format (professional tools)
- [x] Mermaid format (GitHub rendering)
- [x] Comprehensive documentation
- [x] Integration with main README

---

## 📝 Notes for Academic Evaluation

These diagrams fulfill the requirements for:
1. **System Design Documentation**: Complete architectural visualization
2. **Software Engineering Practices**: Industry-standard UML notation
3. **Requirements Specification**: All functional requirements captured
4. **Process Documentation**: Development workflows illustrated
5. **Quality Assurance**: Test scenario identification

**Diagram Count:**
- 1 Use Case Diagram (30 use cases)
- 6 Activity Diagrams (covering all major workflows)
- 2 Format Versions (PlantUML + Mermaid)

---

Last Updated: February 16, 2026
Project: Building Materials Price Intelligence Platform
Team Size: 4 Members
Duration: 13 Weeks (March 9 - June 9, 2026)
