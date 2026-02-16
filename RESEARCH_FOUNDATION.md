# 📚 Research Foundation

**Building Materials Price Intelligence Platform**  
**Academic Context & Literature Review**

---

## 📋 Overview

This document provides the academic and research foundation for the Building Materials Price Intelligence Platform. The project is grounded in peer-reviewed literature, market analysis, and empirical studies demonstrating the effectiveness of machine learning approaches for construction material price forecasting.

---

## 🌍 Market Context

### South African Construction Industry

#### Price Volatility Statistics

**Recent Trends:**
- **2024**: Construction material price index increased by **6.5% year-on-year** (Statistics South Africa, 2024)
- **2023**: Annual growth of **6.6%** in construction material prices
- **Civil Engineering Sector**: 7% year-on-year increase in early 2024, following 4.4% growth in 2023

#### Economic Impact

The persistent price inflation places **substantial pressure** on:
- **Contractors**: Budget overruns and reduced profit margins
- **Developers**: Project delays and cost uncertainty
- **Infrastructure Projects**: Government budget constraints
- **Housing Sector**: Affordability challenges for buyers

**Problem Scale:**
> "These price fluctuations pose significant challenges for construction project management, often resulting in cost overruns and project delays." — Project Proposal Analysis

---

### Volatile Materials in South African Market

The Construction Industry Development Board (CIDB, 2007) identified the following materials as **particularly volatile**:

| Material | Volatility Factors |
|----------|-------------------|
| **Steel** | Import parity pricing, exchange rate sensitivity, global demand |
| **Cement** | Energy costs, production capacity, regional supply |
| **Sand** | Environmental regulations, extraction permits, transport costs |
| **Copper** | Global commodity markets, electrical demand |
| **Timber** | Seasonal availability, forestry regulations, import dependency |
| **PVC** | Oil price correlation, polymer market fluctuations |
| **Bitumen** | Crude oil prices, refinery production schedules |
| **Masonry Blocks** | Cement prices, local production capacity, labor costs |

---

### Current Industry Limitations

**Challenges in Current Procurement Practices:**

1. **Manual Price Comparison**
   - Contractors call multiple suppliers for quotes
   - Time-consuming process (hours per material)
   - No systematic tracking of price changes

2. **No Centralized Data**
   - No structured historical pricing database exists
   - Price history scattered across individual purchase orders
   - Difficult to identify trends or patterns

3. **Reactive Decision-Making**
   - Purchasing decisions made without predictive insight
   - No advance warning of price increases
   - Missed opportunities for strategic buying

4. **Limited Analytical Tools**
   - No anomaly detection for suspicious pricing
   - No comparison against market baselines
   - No forecasting capabilities

**Industry Adoption Gap:**
> "45% of construction companies currently use basic analytics, while another 45% are transitioning to more advanced digital tools, highlighting growing recognition of data's role in cost management." — Datamam (2025)

This statistic reveals both the **market opportunity** and the **industry readiness** for predictive pricing intelligence tools.

---

## 📖 Literature Review

### Machine Learning in Construction Price Forecasting

#### Research Landscape Evolution

**Systematic Literature Review Findings** (Nguyen et al., 2023):
- Analyzed publications from **2012-2022**
- **Significant increase** in research activity since 2016
- Most frequently employed models:
  - **Vector Error Correction Model (VECM)**
  - **Artificial Neural Networks (ANN)**
  - **Autoregressive Integrated Moving Average (ARIMA)**

**Key Insight:**
> "Traditional econometric methods have been challenged by their inability to capture frequent fluctuations in construction material prices, creating an opportunity for more sophisticated predictive approaches." — Wang et al. (2024)

---

#### Forecasting Model Classifications

**Two Primary Approaches:**

1. **Causal Modeling**
   - Analyzes historical data alongside **independent variables**
   - Variables: Consumer Price Index (CPI), Producer Price Index (PPI), GDP
   - Captures macroeconomic influences on construction prices
   - Example: Regression models with economic indicators

2. **Time-Series Analysis**
   - Relies primarily on **historical price patterns**
   - Identifies trends, seasonality, and cyclic behavior
   - No external variables required (data availability advantage)
   - Example: ARIMA, LSTM, Prophet

**Project Focus:** Primarily time-series analysis (due to data availability constraints), with potential for causal modeling in future phases.

---

### Comparative Model Performance

#### Breakthrough: LSTM Superiority

**Key Research Finding** (Lyu et al., 2025):
> "LSTM models achieved RMSE values as low as **1.390** with MAPE values of **0.957**, representing improvements of **up to 59% over traditional ARIMA models** for construction material price forecasting."

**Significance:**
- Establishes **benchmark target** for project: 59% improvement over ARIMA
- Demonstrates **practical applicability** of deep learning in construction economics
- Validates investment in advanced ML models (XGBoost, Random Forest, Prophet, LSTM)

---

#### Model-Specific Advantages

**XGBoost (Extreme Gradient Boosting):**

Research: Frifra et al. (2024)
> "Gradient boosting algorithms like XGBoost can effectively handle the non-linearity of time series data with efficient self-learning abilities, performing exceptionally well in forecasting tasks."

**Strengths:**
- ✅ Captures complex, non-linear relationships
- ✅ Regularization prevents overfitting
- ✅ Handles missing data effectively
- ✅ Fast training and inference

**Application:** Suitable for construction materials with complex interdependencies (e.g., steel prices affecting concrete demand).

---

**Random Forest:**

Research: Comparative ML Studies
> "Random Forest with differentiated inputs proved to be the best performing method across scenarios with varying complexity, including situations with jumps, random walks, or combinations of both."

**Strengths:**
- ✅ Robust across varied datasets
- ✅ Resistant to outliers
- ✅ Provides confidence intervals
- ✅ Minimal hyperparameter tuning required

**Application:** Ideal for South African market with regional pricing variations and diverse material characteristics.

---

**Prophet (Facebook Time-Series Model):**

**Strengths:**
- ✅ Excellent seasonality modeling (construction industry has strong seasonal patterns)
- ✅ Trend detection with changepoint analysis
- ✅ Handles missing data gracefully
- ✅ Intuitive hyperparameters for domain experts

**Application:** Captures quarterly fiscal cycles, holiday effects, and seasonal construction activity patterns.

---

**LSTM (Long Short-Term Memory Networks):**

Research: Zhang et al. (2025)
> "Harnessing the power of improved deep learning for precise building material price predictions."

Research: Hochreiter & Schmidhuber (1997) - Original LSTM paper

**Strengths:**
- ✅ Captures long-term dependencies in time-series data
- ✅ Remembers historical price shocks and their durations
- ✅ Gating mechanism preserves relevant information
- ✅ Superior to linear models in volatile markets

**Application:** Models complex patterns spanning weeks/months, essential for materials with memory effects (e.g., post-supply-shock price recovery).

---

### Baseline Models for Comparison

#### Why Baselines Matter

**Scientific Rigor:**
- Demonstrates **actual improvement** over simple methods
- Prevents "impressive-looking but unhelpful" models
- Establishes **minimum acceptable performance**

**Project Baselines:**

1. **Naïve Forecast**
   - Prediction: Tomorrow's price = Today's price
   - Simplest possible model
   - Any ML model **must** outperform this

2. **Moving Average**
   - Prediction: Average of last 7 or 30 days
   - Smooths short-term volatility
   - Common in construction industry practice

3. **ARIMA (AutoRegressive Integrated Moving Average)**
   - Traditional statistical method
   - **Primary benchmark** for project (target: 59% improvement)
   - Established in construction economics literature

---

### Evaluation Metrics

**Standard Forecasting Metrics:**

1. **MAE (Mean Absolute Error)**
   - Formula: `(1/n) * Σ|actual - predicted|`
   - Interpretation: Average price prediction error in Rands
   - **Most interpretable** for contractors (e.g., "predictions are off by R50 on average")

2. **RMSE (Root Mean Squared Error)**
   - Formula: `√((1/n) * Σ(actual - predicted)²)`
   - **Penalizes large errors** more heavily than MAE
   - Standard in academic literature (enables comparison with research papers)

3. **MAPE (Mean Absolute Percentage Error)**
   - Formula: `(1/n) * Σ|(actual - predicted) / actual| * 100`
   - **Scale-independent**: Allows comparison across different materials
   - Industry standard (e.g., "7% average error")

**Project Target:** Achieve **59% improvement** in RMSE/MAPE over ARIMA baseline, matching LSTM research findings.

---

## 🌐 Data Acquisition via Web Scraping

### Research Foundation

**Web Scraping for Construction Intelligence:**

Research: Datamam (2025)
> "Web scraping enables construction companies to transform into agile entities by monitoring suppliers' websites and tracking material availability and pricing fluctuations."

Research: InstantAPI (2025)
> "Web scraping in the construction industry enables data-driven decision-making for better planning."

---

### Industry Adoption Statistics

**Digital Transformation in Construction:**
- **45%** of construction companies use **basic analytics**
- **45%** are transitioning to **advanced digital tools**
- **10%** are early adopters of AI/ML solutions

**Implication:** Market is ready for predictive pricing intelligence platforms.

---

### Web Scraping Advantages

1. **Real-Time Market Intelligence**
   - Monitor supplier pricing changes as they occur
   - Identify regional price disparities
   - Track material availability constraints

2. **Structured Dataset Creation**
   - No centralized pricing database exists in South Africa
   - Web scraping creates proprietary dataset
   - Enables historical trend analysis

3. **Cost Efficiency**
   - Modern scraping tools: **$0.005 per page scrape**
   - Far cheaper than manual data entry
   - Scalable to hundreds of suppliers

4. **Competitive Advantage**
   - **Proactive procurement** strategies
   - Strategic buying based on predicted price movements
   - Avoid peak pricing periods

---

### Ethical & Legal Considerations

**Responsible Web Scraping:**
- ✅ Respect `robots.txt` files
- ✅ Rate limiting to avoid server overload
- ✅ Only scrape publicly available information
- ✅ Comply with Terms of Service
- ✅ No personal data collection (only pricing data)

**South African Context:**
- Construction material prices are **public market information**
- Suppliers publish prices for competitive comparison
- Academic research use falls under fair use

---

## 🔬 Research Question

### Primary Research Question

**Can supervised machine learning models outperform traditional statistical forecasting methods in predicting short-term (7-30 day) building material price fluctuations in the South African construction market?**

---

### Hypothesis

**Null Hypothesis (H₀):**
Machine learning models (XGBoost, Random Forest, Prophet, LSTM) show **no significant improvement** over ARIMA baseline for 7-30 day price forecasts.

**Alternative Hypothesis (H₁):**
Machine learning models achieve **statistically significant improvement** over ARIMA baseline, with target of **59% reduction in RMSE/MAPE** (based on Lyu et al., 2025 findings).

---

### Research Approach

**Methodology:**

1. **Data Collection**
   - Web scraping of 10+ South African suppliers
   - Target: 2000+ price records across 8 volatile materials
   - Historical backfill via Wayback Machine (where available)

2. **Model Development**
   - Implement baseline models (Naïve, Moving Average, ARIMA)
   - Train advanced models (XGBoost, Random Forest, Prophet, LSTM)
   - Ensemble approach combining multiple models

3. **Evaluation**
   - Time-series cross-validation (no future data leakage)
   - Compare MAE, RMSE, MAPE across all models
   - Statistical significance testing (paired t-test)
   - Material-specific performance analysis

4. **Deployment**
   - Integrate best-performing models into production system
   - Real-time inference via backend API
   - Caching layer for performance optimization

---

## 🎯 Expected Contributions

### 1. Structured Dataset

**Contribution:** South African building material price dataset

**Significance:**
- **First of its kind**: No centralized SA construction materials pricing database exists
- **Public Good**: Dataset can benefit broader construction industry research
- **Reproducibility**: Enables future research validation and extension

**Dataset Specifications:**
- **Materials**: Steel, cement, sand, copper, timber, PVC, bitumen, masonry blocks
- **Attributes**: Material name, supplier, region, price, date, bulk pricing
- **Target Size**: 2000+ validated price records
- **Time Span**: Historical data + continuous collection (Feb-June 2026)

---

### 2. Comparative ML Model Evaluation

**Contribution:** Empirical comparison of ML models for short-term price forecasting

**Research Value:**
- **Reproduces International Findings**: Validates 59% improvement claim in SA context
- **Model Suitability**: Identifies which models work best for specific materials
- **Practical Insights**: Real-world performance vs. controlled research environments

**Evaluation Dimensions:**
- Forecast horizon: 7-day vs. 30-day
- Material type: High-volatility vs. stable materials
- Seasonal effects: Summer vs. winter performance
- Data availability: Performance with limited historical data

---

### 3. Secure Full-Stack Implementation

**Contribution:** Production-ready system integrating multiple technologies

**Educational Value:**
- ✅ **Web Application**: React frontend with responsive design
- ✅ **Mobile Application**: Flutter cross-platform development
- ✅ **Backend Architecture**: RESTful API with JWT authentication
- ✅ **Machine Learning**: End-to-end ML pipeline (training to inference)
- ✅ **Security**: Industry-standard authentication, authorization, API security

**Academic Alignment:** Fulfills Informatics 3 project requirements for comprehensive full-stack development.

---

### 4. Deployable System

**Contribution:** Functional, publicly accessible platform

**Impact:**
- **Practical Demonstration**: AI application solving real-world problem
- **Construction Economics**: Bridges gap between ML research and industry practice
- **Scalability Blueprint**: Architecture designed for production scaling

**Post-Academic Potential:**
- Commercialization opportunity (SaaS model)
- Open-source release for community benefit
- Expansion to other African markets

---

## 📊 Research Timeline

### 13-Week Research & Development Schedule

**Phase 1: Foundation (Weeks 1-4)**
- Literature review completion
- Data collection infrastructure
- Initial EDA and baseline models

**Phase 2: Model Development (Weeks 5-8)**
- Advanced ML model training
- Hyperparameter optimization
- Model evaluation and comparison

**Phase 3: Integration (Weeks 9-12)**
- Full system integration
- Production deployment
- Performance monitoring

**Phase 4: Analysis (Weeks 11-13)**
- Statistical analysis of results
- Documentation of findings
- Presentation preparation

---

## 📚 References

### Key Research Papers

**Machine Learning in Construction:**

1. **Lyu, B., et al. (2025).** A granular framework for construction material price forecasting. *arXiv preprint arXiv:2512.09360.*
   - **Key Finding:** LSTM models achieved up to **59% improvement** over ARIMA

2. **Zhang, L., et al. (2025).** Harnessing the power of improved deep learning for precise building material price predictions. *Buildings, 15(6), 873.*
   - **Contribution:** Deep learning approaches for construction material forecasting

3. **Wang, Y., et al. (2024).** A survey of data-driven construction materials price forecasting. *Buildings, 14(10), 3156.*
   - **Contribution:** Comprehensive survey of forecasting methods

4. **Nguyen, T. H., Kutluer, S., & Günaydın, H. M. (2023).** A systematic literature review on price forecasting models in construction industry. *Engineering, Construction and Architectural Management.*
   - **Contribution:** Literature review covering 2012-2022 research

---

**Model-Specific Research:**

5. **Chen, T., & Guestrin, C. (2016).** XGBoost: A scalable tree boosting system. *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining, 785–794.*
   - **Contribution:** Original XGBoost algorithm paper

6. **Hochreiter, S., & Schmidhuber, J. (1997).** Long short-term memory. *Neural Computation, 9(8), 1735–1780.*
   - **Contribution:** Original LSTM architecture paper

7. **Frifra, M., Naouar, M., & Boudhar, A. (2024).** Harnessing LSTM and XGBoost algorithms for storm prediction. *Scientific Reports, 14, Article 11424.*
   - **Contribution:** Comparative analysis of LSTM and XGBoost

---

**Construction-Specific Applications:**

8. **Mir, M., Kabir, H. D., Nasirzadeh, F., & Khosravi, A. (2021).** Neural network-based interval forecasting of construction material prices. *Journal of Building Engineering, 39, 102288.*
   - **Contribution:** Neural networks for construction price forecasting

9. **Hwang, S., Park, M., Lee, H. S., & Kim, H. (2012).** Automated time-series cost forecasting system for construction materials. *Journal of Construction Engineering and Management, 138(11), 1259–1269.*
   - **Contribution:** Early automated forecasting system

10. **Marzouk, M., & Amin, A. (2013).** Predicting construction materials prices using fuzzy logic and neural networks. *Journal of Construction Engineering and Management, 139(9), 1190–1198.*
    - **Contribution:** Fuzzy logic approaches to price prediction

---

**Market Context & Data:**

11. **Statistics South Africa. (2024).** Construction material price indices. *Pretoria: Stats SA.*
    - **Contribution:** Official South African construction price data

12. **Construction Industry Development Board (CIDB). (2007).** Construction materials price volatility in South Africa.
    - **Contribution:** Identification of volatile materials in SA market

13. **GlobalData. (2025).** South Africa construction market analysis.
    - **Contribution:** Market sizing and trend analysis

---

**Web Scraping & Data Collection:**

14. **Datamam. (2025).** Web scraping in the construction industry: Real-time analysis. Retrieved from https://datamam.com/web-scraping-in-the-construction-industry/
    - **Contribution:** Industry adoption statistics (45% analytics usage)

15. **InstantAPI. (2025).** Web scraping in the construction industry: Data for better planning. Retrieved from https://web.instantapi.ai
    - **Contribution:** Web scraping best practices for construction

---

## 🎓 Academic Integrity

### Research Ethics

**Principles Followed:**
- ✅ Proper citation of all sources
- ✅ Original implementation and analysis
- ✅ Transparent methodology documentation
- ✅ Reproducible research design
- ✅ Responsible data collection practices

---

### Limitations & Scope

**Acknowledged Constraints:**

1. **Data Availability**
   - Limited historical depth initially (15-week project timeline)
   - Dependence on publicly available supplier data
   - Regional coverage limited to major South African urban areas

2. **Model Generalizability**
   - Results specific to South African market conditions
   - Market shocks may reduce predictability
   - Short-term focus (7-30 days) - not long-term forecasting

3. **Infrastructure Constraints**
   - Free-tier cloud services (performance limitations)
   - MVP scope (not all advanced features implemented)
   - Academic timeline (13 weeks)

4. **External Validity**
   - Findings may not generalize to other countries
   - Different market structures may require model adaptation
   - Macroeconomic factors unique to South Africa

---

## 🚀 Future Research Directions

**Post-MVP Research Opportunities:**

1. **Causal Modeling Integration**
   - Incorporate macroeconomic indicators (CPI, PPI, GDP, exchange rates)
   - Test if causal models outperform pure time-series approaches
   - Analyze which economic factors have strongest predictive power

2. **Multi-Step Ahead Forecasting**
   - Extend to 60-90 day forecasts
   - Compare recursive vs. direct forecasting strategies
   - Quantify accuracy degradation with longer horizons

3. **Explainable AI**
   - SHAP values for model interpretability
   - Feature importance analysis for each material
   - Actionable insights for procurement managers

4. **Reinforcement Learning**
   - Optimal purchase timing recommendations
   - Multi-agent systems modeling supplier competition
   - Dynamic pricing strategy optimization

5. **Geographic Expansion**
   - Extend to other African markets (Kenya, Nigeria, Ghana)
   - Compare model performance across different economic contexts
   - Cross-country transfer learning approaches

6. **Supply Chain Disruption Prediction**
   - Early warning system for supply shocks
   - Anomaly detection for unusual price movements
   - Integration with logistics and inventory systems

---

**Last Updated:** March 9, 2026  
**Academic Program:** Informatics 3, Academy of Computer Science & Software Engineering  
**Project Duration:** 13 Weeks (March 9 - June 9, 2026)
