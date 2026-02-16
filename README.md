# 🏗️ Building Materials Price Intelligence Platform

[![Project Status](https://img.shields.io/badge/status-in%20development-yellow.svg)](https://github.com/imperfectperson-max/build_material)
[![License](https://img.shields.io/badge/license-TBD-blue.svg)](LICENSE)
[![Tech Stack](https://img.shields.io/badge/stack-Full--Stack%20%7C%20ML-orange.svg)](#technology-stack)

> A Machine Learning-Driven Web and Mobile System for Price Forecasting and Supplier Comparison

**Informatics 3 Project** | Academy of Computer Science & Software Engineering  
**Duration:** 13 Weeks (March 9 - June 9, 2026)  
**Team Size:** 4 Members

---

## 📋 Overview

The **Building Materials Price Intelligence Platform** is a comprehensive full-stack system that addresses the critical challenge of volatile building material prices in South Africa's construction industry. By combining data engineering, machine learning, and modern web/mobile development, this platform provides contractors and developers with actionable pricing intelligence and predictive analytics.

## 🎯 Problem Statement

The construction industry in South Africa faces significant challenges due to volatile building material prices:

- **Market Volatility**: According to Statistics South Africa, construction material prices rose by **6.5% year-on-year in 2024**, following 6.6% growth in 2023
- **Manual Processes**: Contractors rely on manual price comparison across suppliers
- **No Structured Data**: Absence of centralized, historical pricing datasets
- **Reactive Decisions**: Purchasing decisions are made without predictive intelligence
- **Limited Tools**: No anomaly detection capabilities for pricing irregularities

**Key Volatile Materials**: Steel, cement, sand, copper, timber, PVC, bitumen, and masonry blocks

The platform aims to transform this reactive approach into a data-driven, predictive procurement strategy.

## ✨ Key Features

### 1. 📊 Data Acquisition Layer
- Automated web scraping of South African supplier catalogues
- PDF price list extraction using OCR
- Scheduled data collection for real-time market intelligence
- Data validation and cleaning pipelines
- Comprehensive tracking: material name, supplier, region, price, date, and bulk pricing

### 2. ⚙️ Backend Architecture
- RESTful API endpoints with JWT-based authentication
- PostgreSQL database for structured price history storage
- **Redis caching layer** for performance optimization (target: 70%+ hit rate, <50ms response time)
- Feature engineering pipelines for ML model preparation
- Model inference services with 6-hour TTL caching
- Rate limiting and security controls

### 3. 🤖 Machine Learning Engine
**Baseline Models:**
- Naïve forecast (price persistence)
- Moving Average
- ARIMA (traditional statistical method)

**Advanced Models:**
- **XGBoost**: Complex relationship detection with regularization
- **Random Forest**: Robust predictions across varied datasets
- **Prophet**: Seasonality modeling for construction trends
- **LSTM**: Time-series forecasting with long-term dependencies

**Evaluation Metrics:** MAE, RMSE, MAPE  
**Target Performance:** 59% improvement over traditional ARIMA models

### 4. 🌐 Web Dashboard
- Supplier price comparison interface
- Historical price trend visualization with **Recharts**
- 7-day and 30-day forecasted price projections
- Anomaly detection alerts for unusual price movements
- Regional filtering for location-specific insights
- Responsive design with **Tailwind CSS**
- PWA support for offline access

### 5. 📱 Mobile Application
- Cross-platform development with **Flutter**
- Quick material search and barcode scanning (ML Kit)
- Geolocation-based nearby supplier comparison
- **Offline storage** using Hive for field access
- Push notifications for price alerts
- Biometric authentication for secure access
- Favorites and saved searches

## 🛠️ Technology Stack

### Backend Infrastructure
- **Database**: PostgreSQL (Supabase)
- **Caching**: Redis (Redis Cloud - 30MB free tier)
- **API**: RESTful architecture with JWT authentication
- **Deployment**: Fly.io (backend hosting)

### Machine Learning
- **Libraries**: XGBoost, Scikit-learn, Prophet, TensorFlow/Keras (LSTM)
- **Training**: Kaggle GPU for compute-intensive models
- **Monitoring**: Model versioning and performance tracking

### Web Application
- **Framework**: React with React Query for state management
- **Styling**: Tailwind CSS for responsive design
- **Charts**: Recharts for interactive data visualization
- **Deployment**: Vercel (static hosting with CDN)

### Mobile Application
- **Framework**: Flutter (iOS & Android)
- **Offline Storage**: Hive (lightweight NoSQL)
- **Features**: ML Kit (barcode scanning), biometric authentication
- **Notifications**: Push notification service integration

### Infrastructure & DevOps
- **Version Control**: Git/GitHub
- **CI/CD**: Automated deployment pipeline
- **Free-Tier Architecture**: No institutional funding dependency
- **Monitoring**: Logging and performance analytics

## 👥 Team Structure

| Role | Responsibilities |
|------|------------------|
| **Member 1: Backend Developer & Security Lead** | Supabase setup, Redis caching architecture, RESTful API development, JWT authentication, rate limiting, security audit |
| **Member 2: Machine Learning Engineer** | Model research and development, feature engineering, training (ARIMA, XGBoost, Random Forest, Prophet, LSTM), model evaluation and benchmarking |
| **Member 3: Frontend Web Developer** | React application development, Tailwind CSS styling, Recharts integration, API service layer, responsive design, Vercel deployment |
| **Member 4: Mobile Developer & Data Collection Lead** | Web scraper development, data cleaning pipelines, Flutter mobile app, offline storage, push notifications, biometric authentication |

## 📅 Project Timeline

**Total Duration:** 13 Weeks (March 9 - June 9, 2026)

### Key Milestones
- **D1 (March 25)**: Demo with real SA price data
- **ST1 (April 1)**: Architecture presentation with Redis layer
- **D2 (April 8)**: Working backend demo with ML predictions
- **D3 (May 1)**: MVP Complete - Full system deployment
- **ST2 (May 6)**: Testing results presentation
- **D4 (May 13)**: Final polish complete
- **June 10**: Final presentation and evaluation

**Phases:**
1. **Weeks 1-2**: Foundation (Data collection, infrastructure, Redis setup)
2. **Weeks 3-4**: Core Development (API, caching strategies, ML baseline)
3. **Weeks 5-6**: ML & Frontend Acceleration
4. **Weeks 7-8**: Integration Sprint
5. **Weeks 9-10**: Feature Completion
6. **Weeks 11-12**: MVP Polish
7. **Week 13**: Presentation Preparation

See [ROADMAP.md](ROADMAP.md) for detailed weekly breakdown.

## 🚀 Getting Started

> **Note**: This section will be updated as development progresses.

### Prerequisites
- Node.js (v18+)
- Python (v3.9+)
- Flutter SDK
- PostgreSQL client
- Redis client

### Installation
```bash
# Clone the repository
git clone https://github.com/imperfectperson-max/build_material.git
cd build_material

# Backend setup (coming soon)
# cd backend && npm install

# Web frontend setup (coming soon)
# cd web && npm install

# Mobile app setup (coming soon)
# cd mobile && flutter pub get
```

### Environment Configuration
```bash
# Create .env file with required credentials
# DATABASE_URL=your_supabase_url
# REDIS_URL=your_redis_cloud_url
# JWT_SECRET=your_secret_key
```

### Running the Application
```bash
# Start backend server (coming soon)
# npm run start:backend

# Start web development server (coming soon)
# npm run start:web

# Run mobile app (coming soon)
# flutter run
```

## 📚 Documentation

- **[ROADMAP.md](ROADMAP.md)** - Detailed 15-week project timeline and milestones
- **[TECHNOLOGY_CHOICES.md](TECHNOLOGY_CHOICES.md)** - Technology stack justifications and rationale
- **[SECURITY.md](SECURITY.md)** - Comprehensive security architecture and best practices
- **[RESEARCH_FOUNDATION.md](RESEARCH_FOUNDATION.md)** - Academic context and literature review
- **[diagrams/](diagrams/)** - UML diagrams (Use Case and Activity diagrams) documenting system workflows

## 🔒 Security

Security is a core requirement of this project. Key security features include:

- JWT-based authentication with token blacklisting
- Role-based access control (RBAC)
- bcrypt/Argon2 password hashing
- HTTPS encryption for all API communication
- Redis-based rate limiting
- Protection against SQL injection, XSS, and CSRF attacks

See [SECURITY.md](SECURITY.md) for complete security documentation.

## 🎓 Academic Context

This project is developed as part of the **Informatics 3** curriculum at the **Academy of Computer Science & Software Engineering**. It demonstrates the integration of:

- Full-stack web application development
- Cross-platform mobile application development
- Backend system architecture and security
- Applied machine learning research
- Real-world data engineering and analytics

**Research Question:** Can supervised machine learning models outperform traditional statistical forecasting methods in predicting short-term (7-30 day) building material price fluctuations in the South African construction market?

## 🤝 Contributing

This is an academic project. Contributions are currently limited to the project team members.

## 📄 License

License to be determined. This project is currently developed for academic purposes.

## 🙏 Acknowledgments

- **Statistics South Africa** for publicly available construction price indices
- **Construction Industry Development Board (CIDB)** for market research
- Research publications on ML-based price forecasting
- South African building material suppliers for data sources

## 📧 Contact

For inquiries about this project, please contact the project team through the Academy of Computer Science & Software Engineering.

---

**Project Status**: 🟡 In Development | **Target Completion**: June 9, 2026
