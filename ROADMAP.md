# 📅 Project Roadmap

**Building Materials Price Intelligence Platform**  
**Timeline:** 13 Weeks (March 9 - June 9, 2026)  
**Team:** 4 Members with defined specializations

---

## 🚀 Getting Started - March 8, 2026

**All team members must complete their setup before Week 1 begins on March 9!**

### Quick Setup Links by Role

Each team member should follow their specific setup guide:

- **Member 1 (Backend Developer):** See [01_Backend_Developer.md](roadmap/01_Backend_Developer.md#-getting-started---march-8-2026)
- **Member 2 (ML Engineer):** See [02_ML_Engineer.md](roadmap/02_ML_Engineer.md#-getting-started---march-8-2026)
- **Member 3 (Frontend Developer):** See [03_Frontend_Developer.md](roadmap/03_Frontend_Developer.md#-getting-started---march-8-2026)
- **Member 4 (Mobile/Data Lead):** See [04_Mobile_Data_Lead.md](roadmap/04_Mobile_Data_Lead.md#-getting-started---march-8-2026)

### Universal Setup (All Members)

#### 1. Install Git
```bash
# Windows: Download from https://git-scm.com/
# macOS:
brew install git

# Linux (Ubuntu/Debian):
sudo apt update && sudo apt install git

# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

#### 2. Clone Project Repository
```bash
# Clone the repository
git clone https://github.com/your-org/buildmat-platform.git
cd buildmat-platform

# Create your branch
git checkout -b feature/your-name-initial-setup

# View repository structure
ls -la
```

#### 3. Install VS Code (Recommended IDE)
```bash
# Download from: https://code.visualstudio.com/

# Essential extensions for all members:
# - GitLens
# - Prettier - Code formatter
# - Better Comments
# - TODO Highlight
```

#### 4. Set Up Communication
- **Slack/Discord:** Join team workspace
- **GitHub:** Set up notifications for repository
- **Calendar:** Add weekly sync meeting (Mondays 10 AM)
- **Trello/Notion:** Access project board

#### 5. Review Project Documentation
```bash
# Read these files in order:
# 1. README.md - Project overview
# 2. CONTRIBUTING.md - Development workflow
# 3. CODE_OF_CONDUCT.md - Team expectations
# 4. Your specific role roadmap in roadmap/ directory
```

### Team Checklist for March 8

**Backend Developer:**
- [ ] Node.js (v18+) and npm installed
- [ ] Supabase account created
- [ ] Redis Cloud account created
- [ ] Basic Express server running
- [ ] Postman or REST Client installed

**ML Engineer:**
- [ ] Python 3.9+ and Conda installed
- [ ] Jupyter Lab working
- [ ] TensorFlow installed
- [ ] Kaggle account created with API configured
- [ ] Test dataset loaded successfully

**Frontend Developer:**
- [ ] Node.js (v18+) and npm installed
- [ ] React app created and running
- [ ] Tailwind CSS configured
- [ ] All dependencies installed
- [ ] Dev server runs on localhost:3000

**Mobile/Data Lead:**
- [ ] Python environment for scrapers
- [ ] Selenium and ChromeDriver working
- [ ] Flutter SDK installed
- [ ] Android Studio/Xcode set up
- [ ] Flutter app runs on emulator

### First Team Meeting - Monday, March 9, 10 AM

**Agenda:**
1. Confirm everyone's setup is complete (15 min)
2. Review Week 1 objectives (15 min)
3. Assign initial tasks (15 min)
4. Set up daily standups schedule (5 min)
5. Q&A and blockers (10 min)

**Come prepared with:**
- Screenshots of your dev environment working
- Any setup issues you encountered
- Questions about Week 1 tasks

---

## 🎯 Overview

This roadmap outlines the comprehensive 13-week development timeline for the Building Materials Price Intelligence Platform. The project is structured into distinct phases, each with clear deliverables and technical milestones, culminating in a production-ready MVP with advanced machine learning capabilities.

---

## 📊 Project Phases Summary

| Phase | Weeks | Focus Area | Key Deliverables |
|-------|-------|------------|------------------|
| **Foundation** | 1-2 | Infrastructure & Data Collection | Database, Redis, scrapers, 500+ records |
| **Core Development** | 3-4 | API & ML Baseline | REST endpoints, cache hierarchy, ARIMA baseline |
| **ML & Frontend Acceleration** | 5-6 | Model Training & Web Development | LSTM, Prophet, dashboard homepage |
| **Integration Sprint** | 7-8 | Component Integration | Model caching, end-to-end connectivity |
| **Feature Completion** | 9-10 | Mobile & Testing | Complete mobile app, security audit |
| **MVP Polish** | 11-12 | Optimization & Deployment | Redis tuning, production deployment |
| **Presentation Prep** | 13 | Rehearsals & Refinement | Demo scripts, presentation materials |

---

## 🏗️ WEEKS 1-2 (Mar 9-22): Foundation

**Focus:** Data collection, infrastructure setup, Redis configuration

### Week 1 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | Supabase setup, database schema design, initial table creation |
| **Member 2 (ML)** | Research ML models, download Statistics South Africa data, initial EDA |
| **Member 3 (Web)** | React project initialization, wireframe design, component planning |
| **Member 4 (Mobile/Data)** | Identify 10 SA suppliers, develop basic web scraper prototype |

### Week 2 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | ⭐ **REDIS SETUP**: Configure Redis Cloud instance, implement basic cache service |
| **Member 2 (ML)** | Exploratory Data Analysis (EDA) on historical price data |
| **Member 3 (Web)** | Tailwind CSS setup, build component library, design system |
| **Member 4 (Mobile/Data)** | PDF parser implementation, Wayback Machine backfill for historical data |

### End of Week 2 Deliverables

✅ Supabase database with complete table structure  
✅ Redis Cloud instance configured (30MB free tier)  
✅ 3 functional web scrapers operational  
✅ **500+ price records** collected and validated  
✅ Initial data cleaning pipeline established

---

## ⚙️ WEEKS 3-4 (Mar 23-Apr 5): Core Development

**Focus:** API development, caching strategies, machine learning baseline models

### Week 3 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | REST endpoints (`/prices`, `/materials`), Redis-based rate limiting |
| **Member 2 (ML)** | Kaggle environment setup, ARIMA baseline model implementation |
| **Member 3 (Web)** | API service layer, React Query integration for state management |
| **Member 4 (Mobile/Data)** | Flutter project setup, basic navigation architecture |

### Week 4 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | ⭐ **CACHE HIERARCHY**: L1 (price cache), L2 (forecast cache), JWT blacklist |
| **Member 2 (ML)** | Feature engineering pipeline, XGBoost model initialization |
| **Member 3 (Web)** | Price charts implementation (Recharts), search UI development |
| **Member 4 (Mobile/Data)** | Material list view, HTTP client configuration |

### Milestone: 🎯 D1 (March 25) - Demo Ready

**Demonstration Requirements:**
- Database populated with **real South African price data**
- API returning cached responses (5ms vs 150ms comparison)
- Web dashboard wireframes functional
- Basic data visualization

**Judge Takeaway:** *"Solid foundation, real data"*

### Milestone: 🎯 ST1 (April 1) - Architecture Presentation

**Presentation Requirements:**
- Complete architecture diagram with Redis caching layer
- Cache hit rate metrics and performance improvements
- Demo basic price query functionality
- Security architecture overview

**Judge Takeaway:** *"Professional design, production-ready thinking"*

---

## 🚀 WEEKS 5-6 (Apr 6-19): ML & Frontend Acceleration

**Focus:** Advanced model training, web application development

### Week 5 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | ML inference endpoints (`/forecast`), model result caching |
| **Member 2 (ML)** | LSTM training on Kaggle GPU, Prophet seasonality modeling |
| **Member 3 (Web)** | Dashboard homepage, supplier comparison interface |
| **Member 4 (Mobile/Data)** | Offline storage with Hive, price card UI components |

### Week 6 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | Background retraining tasks, admin endpoints for model management |
| **Member 2 (ML)** | Random Forest implementation, comprehensive model comparison |
| **Member 3 (Web)** | Forecast visualization pages, material detail views |
| **Member 4 (Mobile/Data)** | Geolocation integration, favorites feature implementation |

---

## 🔗 WEEKS 7-8 (Apr 20-May 3): Integration Sprint

**Focus:** Connecting all system components, mobile development acceleration

### Week 7 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | ⭐ **MODEL CACHING**: Implement 6-hour TTL for ML predictions |
| **Member 2 (ML)** | Ensemble model creation, evaluation metrics (MAE/RMSE/MAPE) |
| **Member 3 (Web)** | Connect web application to real ML endpoints |
| **Member 4 (Mobile/Data)** | Push notification system setup |

### Week 8 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | Cache warming strategies, performance tuning |
| **Member 2 (ML)** | Model cards documentation, feature importance analysis |
| **Member 3 (Web)** | Anomaly alert system, data export functionality (CSV/PDF) |
| **Member 4 (Mobile/Data)** | Camera barcode scanning (ML Kit), biometric authentication |

### Milestone: 🎯 D2 (April 8) - Working Backend Demo

**Demonstration Requirements:**
- Complete API with Redis caching operational
- ML predictions functional (7-day and 30-day forecasts)
- Web application consuming real API data
- Mobile app displaying live price information

**Judge Takeaway:** *"End-to-end working system, impressive progress"*

---

## ✨ WEEKS 9-10 (May 4-17): Feature Completion

**Focus:** Mobile application completion, comprehensive testing

### Week 9 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | Mobile-optimized endpoints, webhook integration |
| **Member 2 (ML)** | Anomaly detection service implementation |
| **Member 3 (Web)** | UI polish, Progressive Web App (PWA) support |
| **Member 4 (Mobile/Data)** | Complete all remaining mobile features |

### Week 10 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | **Security audit**, load testing, vulnerability assessment |
| **Member 2 (ML)** | Model versioning system, comprehensive documentation |
| **Member 3 (Web)** | Cross-browser testing (Chrome, Firefox, Safari, Edge) |
| **Member 4 (Mobile/Data)** | iOS/Android testing, crash reporting integration |

---

## 🎨 WEEKS 11-12 (May 18-31): MVP Polish

**Focus:** Final optimization, production readiness

### Week 11 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | ⭐ **REDIS TUNING**: Optimize TTLs, achieve **>70% cache hit rate** |
| **Member 2 (ML)** | Final model metrics validation (target: **59% improvement over ARIMA**) |
| **Member 3 (Web)** | Performance optimization (code splitting, lazy loading) |
| **Member 4 (Mobile/Data)** | Offline synchronization finalization |

### Week 12 Activities

| Team Member | Responsibilities |
|-------------|------------------|
| **Member 1 (Backend)** | Deploy to **Fly.io**, production configuration |
| **Member 2 (ML)** | Create comprehensive benchmark comparison vs ARIMA |
| **Member 3 (Web)** | Deploy to **Vercel**, CDN optimization |
| **Member 4 (Mobile/Data)** | Demo video recording, app store preparation |

### Milestone: 🎯 D3 (May 1) - MVP COMPLETE!

**Deliverables:**
- ✅ Full working system with Redis caching layer
- ✅ ML predictions demonstrating **59% improvement** claim
- ✅ Web dashboard + Mobile app fully functional
- ✅ Real SA price data (**2000+ records**)
- ✅ **5-minute demo** polished and rehearsed

**Judge Takeaway:** *"Complete, polished, production-ready MVP"*

---

## 🎤 WEEK 13 (Jun 1-9): Presentation Preparation

**Focus:** Rehearsals, refinement, final preparations

### Milestone: 🎯 ST2 (May 6) - Testing Results Presentation

**Presentation Requirements:**
- Test reports and coverage metrics
- Cache hit rates: **75%+**
- API response time: **<50ms**
- User acceptance testing results

### Milestone: 🎯 D4 (May 13) - Final Polish Complete

**Deliverables:**
- Perfect demo flow established
- Backup videos recorded for all features
- Q&A preparation completed
- All known bugs resolved

**Judge Takeaway:** *"They're ready to win"*

### Week 13 Schedule

| Dates | Activity |
|-------|----------|
| **June 1-5** | Individual rehearsals, script refinement, final technical tweaks |
| **June 6-9** | Full team rehearsals, equipment check, contingency planning |
| **June 10** | 🏆 **FINAL PRESENTATION** |

---

## 🔑 Key Technical Milestones

### Redis Integration Targets
- **Week 2**: Basic Redis Cloud setup
- **Week 4**: Cache hierarchy implementation (L1/L2)
- **Week 7**: Model prediction caching (6-hour TTL)
- **Week 11**: Optimization target achieved (>70% hit rate, <50ms response)

### Machine Learning Benchmarks
- **Week 4**: ARIMA baseline established
- **Week 6**: All advanced models trained (XGBoost, Random Forest, Prophet, LSTM)
- **Week 7**: Ensemble model creation
- **Week 11**: Final metrics validation (**59% improvement** over ARIMA)

### Cache Performance Targets
- **L1 Cache (Prices)**: >80% hit rate, <10ms response time
- **L2 Cache (Forecasts)**: >70% hit rate, <50ms response time
- **JWT Blacklist**: Sub-millisecond lookup
- **Overall API Response**: <50ms for cached queries

---

## 🚀 Future Enhancements (Post-MVP)

### Phase 2 Features
- **API Integrations**: Direct connections with wholesale supplier APIs
- **Advanced ML**: Reinforcement learning for optimal purchase timing recommendations
- **Communication**: SMS and WhatsApp alerts for price changes
- **Geographic Expansion**: Extend to additional African markets (Kenya, Nigeria, Ghana)
- **Enterprise Features**: Project management system integration
- **Advanced Analytics**: Enhanced anomaly detection with explainable AI

### Research Extensions
- Multi-step ahead forecasting (60-90 days)
- Causal modeling with macroeconomic indicators (CPI, PPI, GDP)
- Supply chain disruption prediction
- Automated supplier reliability scoring

### Infrastructure Scaling
- Migration from free-tier to production-grade infrastructure
- Multi-region deployment for latency optimization
- Advanced caching strategies (CDN integration)
- Real-time WebSocket connections for live price updates

---

## 📈 Success Metrics

### Technical Metrics
- **Cache Hit Rate**: >70% (target: 75%)
- **API Response Time**: <50ms (cached queries)
- **ML Model Accuracy**: 59% improvement over ARIMA baseline
- **System Uptime**: >99% during demo periods
- **Data Collection**: 2000+ validated price records

### Project Management Metrics
- **On-Time Deliveries**: All 6 milestone demos
- **Code Quality**: Comprehensive test coverage
- **Documentation**: Complete technical and user documentation
- **Team Collaboration**: Weekly sync meetings, clear task ownership

---

## 🎓 Academic Alignment

This roadmap aligns with Informatics 3 project requirements:

- ✅ **Full-Stack Development**: Web + Mobile + Backend integration
- ✅ **Security Architecture**: Authentication, authorization, API security
- ✅ **Applied Machine Learning**: Research-backed predictive models
- ✅ **Data Engineering**: Web scraping, ETL pipelines, data validation
- ✅ **Professional Practices**: Agile methodology, version control, CI/CD

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active Development  
**Next Review:** March 16, 2026
