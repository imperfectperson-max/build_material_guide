# 🎯 Building Materials Price Intelligence Platform - Master Roadmap

**Project Start Date:** March 9, 2026  
**Project End Date:** June 9, 2026  
**Total Duration:** 13 Weeks  
**Team Size:** 4 Members

---

## 🚀 Getting Started - March 8, 2026

**CRITICAL: All team members must complete setup by end of day March 8!**

### Overview
March 8 is dedicated to environment setup so everyone can hit the ground running on March 9. Each role has specific requirements detailed in their respective roadmap files.

### Setup by Role

#### 📋 All Members - Universal Setup
```bash
# 1. Install Git
git --version  # Verify installation

# 2. Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 3. Clone repository (will be provided by team lead)
git clone <repository-url>
cd buildmat-platform

# 4. Install VS Code or preferred IDE
# Download from: https://code.visualstudio.com/
```

#### 💻 Backend Developer (Member 1)
**Setup Time: 2-3 hours**
```bash
# Install Node.js v18+ and npm
node --version && npm --version

# Create accounts:
# - Supabase: https://supabase.com (for PostgreSQL)
# - Redis Cloud: https://redis.io/cloud (free 30MB tier)
# - Fly.io: https://fly.io (for deployment)

# Initialize backend project
mkdir buildmat-backend && cd buildmat-backend
npm init -y
npm install express dotenv cors helmet pg ioredis jsonwebtoken

# Test server
npm run dev  # Should start on port 3000
```
**Detailed Guide:** [01_Backend_Developer.md - Getting Started](01_Backend_Developer.md#-getting-started---march-8-2026)

#### 🤖 ML Engineer (Member 2)
**Setup Time: 3-4 hours**
```bash
# Install Conda
conda --version

# Create ML environment
conda create -n buildmat python=3.10 -y
conda activate buildmat

# Install core packages
pip install pandas numpy scikit-learn matplotlib seaborn
pip install statsmodels pmdarima prophet xgboost tensorflow
pip install jupyter jupyterlab

# Set up Kaggle
# 1. Create account at https://www.kaggle.com
# 2. Get API token from Account > API > "Create New Token"
# 3. Place kaggle.json in ~/.kaggle/
pip install kaggle
kaggle datasets list  # Test Kaggle CLI

# Test TensorFlow
python -c "import tensorflow as tf; print('TF:', tf.__version__); print('GPU:', tf.config.list_physical_devices('GPU'))"
```
**Detailed Guide:** [02_ML_Engineer.md - Getting Started](02_ML_Engineer.md#-getting-started---march-8-2026)

#### 🎨 Frontend Developer (Member 3)
**Setup Time: 2-3 hours**
```bash
# Install Node.js v18+ and npm
node --version && npm --version

# Create React app
npx create-react-app buildmat-dashboard
cd buildmat-dashboard

# Install dependencies
npm install tailwindcss postcss autoprefixer
npm install recharts axios react-query react-router-dom
npm install @headlessui/react @heroicons/react

# Initialize Tailwind
npx tailwindcss init -p

# Start dev server
npm start  # Should open on http://localhost:3000
```
**Detailed Guide:** [03_Frontend_Developer.md - Getting Started](03_Frontend_Developer.md#-getting-started---march-8-2026)

#### 📱 Mobile/Data Lead (Member 4)
**Setup Time: 3-4 hours**

**Python for Scrapers:**
```bash
# Create scraper environment
conda create -n scrapers python=3.10 -y
conda activate scrapers

# Install scraping tools
pip install beautifulsoup4 requests selenium webdriver-manager
pip install pandas PyPDF2 pdfplumber

# Test Selenium
python -c "from selenium import webdriver; from webdriver_manager.chrome import ChromeDriverManager; print('✅ Selenium ready')"
```

**Flutter for Mobile:**
```bash
# Install Flutter SDK
# Download from: https://flutter.dev/docs/get-started/install

# Add to PATH and verify
flutter --version
flutter doctor  # Check for issues

# Install Android Studio (for Android) or Xcode (for iOS on macOS)
# Accept licenses
flutter doctor --android-licenses

# Create test app
flutter create test_app
cd test_app
flutter run  # Test on emulator
```
**Detailed Guide:** [04_Mobile_Data_Lead.md - Getting Started](04_Mobile_Data_Lead.md#-getting-started---march-8-2026)

### Setup Verification

#### End of Day Checklist (March 8)
All team members should be able to answer YES to these:

**Backend Developer:**
- [ ] Can run Express server on port 3000
- [ ] Supabase and Redis accounts created
- [ ] Can make HTTP request to /health endpoint
- [ ] Database schema design started

**ML Engineer:**
- [ ] Can run Jupyter notebooks
- [ ] TensorFlow imported successfully
- [ ] Kaggle CLI configured and working
- [ ] Created first Kaggle notebook with GPU enabled
- [ ] Downloaded sample price data

**Frontend Developer:**
- [ ] React app running on localhost:3000
- [ ] Tailwind CSS working (test with className)
- [ ] Can make API calls with axios
- [ ] Component folder structure created

**Mobile/Data Lead:**
- [ ] Selenium opens Chrome browser successfully
- [ ] Can scrape a test webpage
- [ ] Flutter app runs on emulator/device
- [ ] Both Python and Flutter environments working

### Troubleshooting Resources

**Common Issues:**

1. **Port already in use:**
   ```bash
   # macOS/Linux:
   lsof -ti:3000 | xargs kill -9
   
   # Windows:
   netstat -ano | findstr :3000
   taskkill /PID <process_id> /F
   ```

2. **npm permission errors:**
   ```bash
   # Don't use sudo! Fix npm permissions:
   mkdir ~/.npm-global
   npm config set prefix '~/.npm-global'
   export PATH=~/.npm-global/bin:$PATH
   ```

3. **Python package conflicts:**
   ```bash
   # Use isolated conda environment
   conda create -n buildmat python=3.10 -y
   conda activate buildmat
   # Install packages fresh
   ```

4. **Flutter doctor warnings:**
   ```bash
   # Most warnings are OK if you can run flutter run
   # Critical: Android licenses must be accepted
   flutter doctor --android-licenses
   ```

### Team Sync - End of March 8

**Quick Stand-up (15 min):**
- Each member shares: ✅ Setup complete or ⚠️ Blocked
- Help teammates with blockers
- Confirm everyone is ready for Week 1

**If you're blocked:**
1. Share your error message in team chat
2. Check the detailed setup guide for your role
3. Ask for help - don't stay stuck!

---

## 📊 Timeline Overview

| Week | Dates | Phase | Key Focus | Demo/Milestone |
|------|-------|-------|-----------|----------------|
| **1** | Mar 9-15 | Foundation | Infrastructure Setup | - |
| **2** | Mar 16-22 | Foundation | Data Collection | - |
| **3** | Mar 23-29 | Core Development | API & Caching | **D1 (Mar 25)** |
| **4** | Mar 30-Apr 5 | Core Development | ML Baseline | **ST1 (Apr 1)** |
| **5** | Apr 6-12 | ML Acceleration | Advanced Models | **D2 (Apr 8)** |
| **6** | Apr 13-19 | Frontend Dev | Web Dashboard | - |
| **7** | Apr 20-26 | Integration | System Connection | - |
| **8** | Apr 27-May 3 | Feature Complete | Mobile & Testing | **D3 (May 1) - MVP** |
| **9** | May 4-10 | Testing | Comprehensive QA | **ST2 (May 6)** |
| **10** | May 11-17 | Polish | Optimization | **D4 (May 13)** |
| **11** | May 18-24 | Final Polish | Production Ready | - |
| **12** | May 25-31 | Presentation Prep | Rehearsals | - |
| **13** | Jun 1-9 | Final Prep | Refinement | **Final (Jun 10)** |

---

## 🎯 Adjusted Milestone Schedule

### D1 - March 25, 2026 (Week 3)
**Theme:** "Foundation Complete"
- Database populated with real SA price data (500+ records)
- Basic web scraper operational
- Redis caching configured
- Simple API endpoints returning data

### ST1 - April 1, 2026 (Week 4)
**Theme:** "Architecture Presentation"
- Complete system architecture diagram
- Redis caching layer demonstration
- Database schema presentation
- Security architecture overview

### D2 - April 8, 2026 (Week 5)
**Theme:** "Intelligence Layer Active"
- ML models trained and producing forecasts
- API serving real predictions
- Web dashboard displaying data
- Cache performance metrics

### D3 - May 1, 2026 (Week 8)
**Theme:** "MVP Complete" 🎉
- Full system operational
- Web + Mobile apps functional
- ML predictions accurate
- Production deployment ready

### ST2 - May 6, 2026 (Week 9)
**Theme:** "Testing & Performance Report"
- Comprehensive testing results
- Performance benchmarks
- Cache hit rate analysis
- Security audit results

### D4 - May 13, 2026 (Week 10)
**Theme:** "Production Polish"
- All bugs resolved
- UI/UX refinements complete
- Demo flow perfected
- Backup videos recorded

### Final Presentation - June 10, 2026 (Week 13)
**Theme:** "Championship Performance"
- 15-minute polished presentation
- Live system demonstration
- Q&A mastery
- Professional delivery

---

## 👥 Team Role Assignments

### Member 1: Backend Developer & Security Lead
**Primary Responsibilities:**
- Database architecture (Supabase/PostgreSQL)
- Redis caching implementation and optimization
- RESTful API development
- JWT authentication and security
- Rate limiting and API protection
- Model inference API endpoints

**Detailed Weekly Breakdown:** See `01_Backend_Developer.md`

---

### Member 2: Machine Learning Engineer
**Primary Responsibilities:**
- ML model research and development
- Feature engineering pipelines
- Model training (ARIMA, XGBoost, Random Forest, Prophet, LSTM)
- Model evaluation and benchmarking
- Anomaly detection algorithms
- Performance optimization

**Detailed Weekly Breakdown:** See `02_ML_Engineer.md`

---

### Member 3: Frontend Web Developer
**Primary Responsibilities:**
- React web application development
- Tailwind CSS styling and responsive design
- Recharts data visualization
- API integration and state management
- Progressive Web App features
- UI/UX polish

**Detailed Weekly Breakdown:** See `03_Frontend_Developer.md`

---

### Member 4: Mobile Developer & Data Collection Lead
**Primary Responsibilities:**
- Web scraper development
- Data cleaning and validation
- Flutter mobile application
- Offline storage (Hive)
- Push notifications
- Biometric authentication

**Detailed Weekly Breakdown:** See `04_Mobile_Data_Lead.md`

---

## 🎯 Success Metrics

### Technical KPIs
- ✅ **Database Records:** 2,000+ validated price entries
- ✅ **Cache Hit Rate:** >70% (target: 75%)
- ✅ **API Response Time:** <50ms for cached queries
- ✅ **ML Model Performance:** 59% improvement over ARIMA
- ✅ **System Uptime:** >99% during demo periods
- ✅ **Mobile App Size:** <50MB

### Project Management KPIs
- ✅ **On-Time Delivery:** All 6 milestones met
- ✅ **Code Coverage:** >70% test coverage
- ✅ **Documentation:** Complete for all components
- ✅ **Team Sync:** Weekly meetings with clear agendas

---

## 🚨 Critical Dependencies

### Week 1-2 (Blockers for Future Work)
- ⚠️ Database schema finalized
- ⚠️ Redis Cloud account configured
- ⚠️ Supplier list identified (10+ sources)
- ⚠️ GitHub repository structure established

### Week 3-4 (Blockers for D1/ST1)
- ⚠️ Web scrapers collecting real data
- ⚠️ API endpoints functional
- ⚠️ ML baseline model trained

### Week 5-6 (Blockers for D2)
- ⚠️ Advanced ML models operational
- ⚠️ Web dashboard consuming API
- ⚠️ Cache layer optimized

### Week 7-8 (Blockers for MVP)
- ⚠️ Mobile app feature-complete
- ⚠️ System integration tested
- ⚠️ Security audit passed

---

## 📚 Documentation Structure

```
roadmap/
├── 00_MASTER_ROADMAP.md (this file)
├── 01_Backend_Developer.md
├── 02_ML_Engineer.md
├── 03_Frontend_Developer.md
├── 04_Mobile_Data_Lead.md
└── DAILY_TEAM_DEPENDENCY_MATRIX.md
```

---

## 🔄 Weekly Team Synchronization

**Meeting Schedule:** Every Monday at 10:00 AM (1 hour)

**Agenda Template:**
1. Previous week accomplishments (5 min per person)
2. Current week goals (5 min per person)
3. Blockers and dependencies (15 min)
4. Integration points discussion (10 min)
5. Next milestone preparation (10 min)

See `DAILY_TEAM_DEPENDENCY_MATRIX.md` for detailed team coordination and dependencies.

---

## ⚠️ Risk Management

### High-Priority Risks
1. **Data Collection Delays** - Scrapers fail or suppliers change formats
2. **ML Model Underperformance** - Models don't achieve 59% improvement
3. **Cache Complexity** - Redis implementation more complex than expected
4. **Integration Issues** - Components don't connect smoothly

### Mitigation Strategies
Mitigation strategies should be coordinated during weekly team synchronization meetings and documented in the team's project management system.

---

## 🎓 Academic Requirements Alignment

### Informatics 3 Criteria
✅ **Full-Stack Development:** Web + Mobile + Backend  
✅ **Database Design:** Normalized schema with relationships  
✅ **API Development:** RESTful architecture with authentication  
✅ **Security:** JWT, rate limiting, input validation  
✅ **Machine Learning:** Supervised learning with evaluation  
✅ **Data Engineering:** Web scraping, ETL pipelines  
✅ **UI/UX Design:** Responsive, accessible interfaces  
✅ **Testing:** Unit, integration, and user acceptance testing  
✅ **Documentation:** Technical and user documentation  
✅ **Professional Practices:** Git workflow, code reviews, CI/CD

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active Development  
**Next Review:** March 16, 2026
