# UML Diagrams - Table of Contents

Quick navigation for all UML diagrams in the Building Materials Price Intelligence Platform.

---

## 📖 Getting Started

**New to these diagrams?** Start here:
1. Read [QUICK_START.md](QUICK_START.md) for viewing options
2. Review [OVERVIEW.md](OVERVIEW.md) for diagram descriptions
3. Check [README.md](README.md) for detailed documentation

---

## 🗺️ Diagram Index

### Use Case Diagram
Shows all system actors and their interactions with the platform (30 use cases).

| Format | File | Best For |
|--------|------|----------|
| PNG | [use_case_diagram.png](use_case_diagram.png) | Quick viewing |
| PlantUML | [use_case_diagram.puml](use_case_diagram.puml) | Professional tools, exports |
| Mermaid | [use_case_diagram_mermaid.md](use_case_diagram_mermaid.md) | GitHub viewing |

**Actors:** Contractor/User, Admin, Data Scraper, ML Engine, External Suppliers

---

### Activity Diagrams

#### 1. User Authentication
Documents login, JWT token generation, and biometric authentication.

| Format | File |
|--------|------|
| PNG | [activity_authentication.png](activity_authentication.png) |
| PlantUML | [activity_authentication.puml](activity_authentication.puml) |
| Mermaid | [activity_diagrams_mermaid.md](activity_diagrams_mermaid.md#1-user-authentication-flow) |

**Key Features:** JWT tokens, bcrypt/Argon2, biometric auth, account locking

---

#### 2. Price Comparison
Shows the workflow for comparing material prices across suppliers.

| Format | File |
|--------|------|
| PNG | [activity_price_comparison.png](activity_price_comparison.png) |
| PlantUML | [activity_price_comparison.puml](activity_price_comparison.puml) |
| Mermaid | [activity_diagrams_mermaid.md](activity_diagrams_mermaid.md#2-price-comparison-workflow) |

**Key Features:** Redis caching (<50ms), regional filtering, geolocation, export

---

#### 3. Price Forecasting
ML-powered price prediction workflow with ensemble models.

| Format | File |
|--------|------|
| PNG | [activity_price_forecasting.png](activity_price_forecasting.png) |
| PlantUML | [activity_price_forecasting.puml](activity_price_forecasting.puml) |
| Mermaid | [activity_diagrams_mermaid.md](activity_diagrams_mermaid.md#3-price-forecasting-workflow) |

**Key Features:** 4 ML models, ensemble method, 6-hour cache, confidence intervals

---

#### 4. Data Collection and Processing
Automated web scraping, OCR extraction, and data validation pipeline.

| Format | File |
|--------|------|
| PNG | [activity_data_collection.png](activity_data_collection.png) |
| PlantUML | [activity_data_collection.puml](activity_data_collection.puml) |
| Mermaid | [activity_diagrams_mermaid.md](activity_diagrams_mermaid.md#4-data-collection-process) |

**Key Features:** Scheduled scraping, OCR, validation, notifications, error handling

---

#### 5. Mobile App Search
Mobile-specific workflows including offline mode and barcode scanning.

| Format | File |
|--------|------|
| PNG | [activity_mobile_search.png](activity_mobile_search.png) |
| PlantUML | [activity_mobile_search.puml](activity_mobile_search.puml) |
| Mermaid | [activity_diagrams_mermaid.md](activity_diagrams_mermaid.md#5-mobile-app-search-simplified) |

**Key Features:** Biometric auth, barcode/voice search, offline mode, Hive storage

---

#### 6. ML Model Training
Complete machine learning pipeline from data prep to deployment.

| Format | File |
|--------|------|
| PNG | [activity_ml_training.png](activity_ml_training.png) |
| PlantUML | [activity_ml_training.puml](activity_ml_training.puml) |
| Mermaid | [activity_diagrams_mermaid.md](activity_diagrams_mermaid.md#6-ml-model-training-process) |

**Key Features:** 5 models (ARIMA, XGBoost, RF, Prophet, LSTM), evaluation, deployment

---

## 📊 Quick Stats

- **Total Use Cases:** 30
- **Total Activity Diagrams:** 6
- **System Actors:** 5
- **Use Case Categories:** 7
- **File Formats:** 2 (PlantUML + Mermaid)
- **Total Files:** 14

---

## 🎯 Use Cases by Category

| Category | Count | Use Cases |
|----------|-------|-----------|
| Authentication & Authorization | 4 | Register, Login, Profile, Reset Password |
| Price Comparison & Search | 6 | Search, Compare, History, Filter, Favorites, Barcode |
| Price Forecasting & Analytics | 4 | Forecast, Alerts, Trends, Export |
| Mobile Features | 4 | Nearby Suppliers, Notifications, Offline, Biometric |
| Administration | 4 | Users, Monitoring, Config, Analytics |
| Data Processing | 4 | Scraping, Validation, Storage, Caching |
| Machine Learning | 4 | Training, Prediction, Anomalies, Evaluation |

---

## 🔍 Finding Specific Information

### Need to understand...
- **Authentication?** → See activity_authentication.puml
- **How prices are compared?** → See activity_price_comparison.puml
- **ML forecasting?** → See activity_price_forecasting.puml
- **Data collection?** → See activity_data_collection.puml
- **Mobile features?** → See activity_mobile_search.puml
- **ML pipeline?** → See activity_ml_training.puml
- **All use cases?** → See use_case_diagram.puml

### Need to...
- **View quickly on GitHub?** → Use Mermaid .md files
- **Generate professional images?** → Use PlantUML .puml files
- **Understand architecture?** → Start with OVERVIEW.md
- **Learn viewing options?** → See QUICK_START.md

---

## 🔗 Related Documentation

- [../README.md](../README.md) - Project overview
- [../ROADMAP.md](../ROADMAP.md) - Implementation timeline
- [../TECHNOLOGY_CHOICES.md](../TECHNOLOGY_CHOICES.md) - Tech stack
- [../SECURITY.md](../SECURITY.md) - Security architecture
- [../RESEARCH_FOUNDATION.md](../RESEARCH_FOUNDATION.md) - ML research

---

## 🎓 For Academic Review

These diagrams document:
- ✅ Complete system requirements (30 use cases)
- ✅ Detailed workflows (6 activity diagrams)
- ✅ Architectural patterns (caching, ML, offline-first)
- ✅ Security measures (JWT, biometric, rate limiting)
- ✅ Performance targets (<50ms cache, >70% hit rate)
- ✅ ML capabilities (59% improvement target)

**Standard:** UML 2.5 compliant  
**Tools:** PlantUML, Mermaid  
**Coverage:** All major system components

---

Last Updated: February 16, 2026  
Project: Building Materials Price Intelligence Platform  
Academic Institution: Academy of Computer Science & Software Engineering
