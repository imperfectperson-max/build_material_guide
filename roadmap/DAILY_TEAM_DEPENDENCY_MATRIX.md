# 📊 DAILY TEAM DEPENDENCY MATRIX

## WEEK 1 (March 9-15) - Foundation Phase

| Day | Member 1 (Backend) Needs | Member 2 (ML) Needs | Member 3 (Web) Needs | Member 4 (Mobile/Data) Needs |
|-----|-------------------------|-------------------|-------------------|--------------------------|
| **Mon** | • DB schema approved by all | • Dataset from Member 4 | • API spec from Member 1 | • Supplier list approved by team |
| **Tue** | • Sample data from Member 4 | • Historical data sources | • Design feedback from all | • Scraping targets from Member 2 |
| **Wed** | • Data format specs from M4 | • Feature list from M2 | • Color palette approved | • First 100 records to Member 1 |
| **Thu** | • Test data for API testing | • EDA results to team | • Component library review | • PDF samples to all |
| **Fri** | • API endpoints for testing | • Feature engineering plan | • Layout feedback from all | • 500+ records to Member 1 |
| **Sat** | • Database connection string | • Baseline approach doc | • Prototype link to team | • Data quality report |
| **Sun** | • Redis connection ready | • Research summary | • Wireframes complete | • Scraper code in GitHub |

### CRITICAL PATH WEEK 1:

```
Member 4 ──(data)──> Member 1 ──(API)──> Member 3
    │                      │
    └────(features)────> Member 2
```

---

## WEEK 2 (March 16-22) - Core Development

| Day | Member 1 Gives | Member 2 Gives | Member 3 Gives | Member 4 Gives |
|-----|---------------|---------------|---------------|---------------|
| **Mon** | • API docs to M3/M4 | • Data requirements to M4 | • UI components preview | • Daily scraped data |
| **Tue** | • Auth endpoints to test | • Baseline metrics | • Material list UI | • PDF parser results |
| **Wed** | • Redis cache stats | • Feature importance | • Chart components | • New supplier data |
| **Thu** | • Rate limiting demo | • Model comparison | • Search UI | • Data validation rules |
| **Fri** | • JWT blacklist test | • ARIMA results | • Detail page | • 800+ records total |
| **Sat** | • Performance metrics | • Feature list final | • Responsive design | • Data cleaning script |
| **Sun** | • API stability report | • Week 2 summary | • UI component docs | • Scraper monitoring |

### CRITICAL PATH WEEK 2:

```
Member 1 ──(API)──> Member 3 & Member 4
    │
    └──(cache)──> All demos faster
```

---

## WEEK 3 (March 23-29) - D1 Week!

| Day | Member 1 | Member 2 | Member 3 | Member 4 |
|-----|----------|----------|----------|----------|
| **Mon** | **NEEDS:** Test data from M4<br>**GIVES:** API endpoint | **NEEDS:** Clean data from M4<br>**GIVES:** Baseline results | **NEEDS:** API from M1<br>**GIVES:** Dashboard preview | **NEEDS:** Feedback on data<br>**GIVES:** 1000+ records |
| **Tue** | **NEEDS:** Forecast structure<br>**GIVES:** Cache demo | **NEEDS:** Model feedback<br>**GIVES:** XGBoost results | **NEEDS:** Charts feedback<br>**GIVES:** Forecast UI | **NEEDS:** API test results<br>**GIVES:** Data quality report |
| **Wed (D1)** | ✅ **DEMO:** API + Redis | ✅ **DEMO:** Baseline models | ✅ **DEMO:** Dashboard UI | ✅ **DEMO:** 1000+ records |
| **Thu** | **NEEDS:** ML endpoint specs<br>**GIVES:** Performance tuning | **NEEDS:** GPU time<br>**GIVES:** LSTM plan | **NEEDS:** Real data<br>**GIVES:** Chart refinements | **NEEDS:** New targets<br>**GIVES:** More suppliers |
| **Fri** | **NEEDS:** Model format<br>**GIVES:** Integration plan | **NEEDS:** Feedback<br>**GIVES:** Prophet results | **NEEDS:** API updates<br>**GIVES:** Mobile designs | **NEEDS:** API docs<br>**GIVES:** Mobile mock data |
| **Sat** | **NEEDS:** Test results<br>**GIVES:** Week 3 summary | **NEEDS:** Data for tuning<br>**GIVES:** Progress report | **NEEDS:** Review time<br>**GIVES:** UI polish | **NEEDS:** Feedback<br>**GIVES:** 1500+ records |
| **Sun** | REST/PLAN for Week 4 | REST/PLAN for Week 4 | REST/PLAN for Week 4 | REST/PLAN for Week 4 |

---

## WEEK 4 (March 30-April 5) - ST1 Week + ML Sprint

| Day | Member 1 (Backend) | Member 2 (ML) | Member 3 (Web) | Member 4 (Mobile/Data) |
|-----|-------------------|--------------|---------------|----------------------|
| **Mon** | 🔴 **NEEDS FROM M2:** Model prediction format<br>🔴 **NEEDS FROM M4:** 1500+ records<br>🟢 **GIVES TO M3:** ML endpoints | 🔴 **NEEDS FROM M4:** Historical data<br>🟢 **GIVES TO M1:** Model specs | 🔴 **NEEDS FROM M1:** ML API<br>🟢 **GIVES TO M4:** UI designs | 🔴 **NEEDS FROM M3:** Mobile specs<br>🟢 **GIVES:** Fresh data |
| **Tue** | 🔴 **NEEDS:** Model files<br>🟢 **GIVES:** Cache for ML | 🔴 **NEEDS:** GPU access<br>🟢 **GIVES:** LSTM results | 🔴 **NEEDS:** Forecast format<br>🟢 **GIVES:** Charts | 🔴 **NEEDS:** API docs<br>🟢 **GIVES:** 10+ suppliers |
| **Wed** | 🔴 **NEEDS:** Test results<br>🟢 **GIVES:** API stability | 🔴 **NEEDS:** Feature feedback<br>🟢 **GIVES:** Ensemble plan | 🔴 **NEEDS:** Real predictions<br>🟢 **GIVES:** Forecast UI | 🔴 **NEEDS:** Mobile API<br>🟢 **GIVES:** Data pipeline |
| **Thu** | 🔴 **NEEDS:** Performance targets<br>🟢 **GIVES:** Load tests | 🔴 **NEEDS:** Validation data<br>🟢 **GIVES:** 59% improvement | 🔴 **NEEDS:** Comparison data<br>🟢 **GIVES:** Dashboard | 🔴 **NEEDS:** Geolocation<br>🟢 **GIVES:** Supplier locations |
| **Fri** | 🔴 **NEEDS:** Final specs<br>🟢 **GIVES:** Production API | 🔴 **NEEDS:** Model storage<br>🟢 **GIVES:** Saved models | 🔴 **NEEDS:** Final UI feedback<br>🟢 **GIVES:** PWA setup | 🔴 **NEEDS:** Offline format<br>🟢 **GIVES:** Hive schema |
| **Sat** | PREP for ST1 | PREP for ST1 | PREP for ST1 | PREP for ST1 |
| **Sun** | 🎯 **ST1 PRESENTATION** | 🎯 **ST1 PRESENTATION** | 🎯 **ST1 PRESENTATION** | 🎯 **ST1 PRESENTATION** |

### ST1 CRITICAL PATH:

```
Member 4 ──(data)──> Member 2 ──(model)──> Member 1 ──(API)──> Member 3
    ↑______________________|                    |                      |
    └──────────────────────┴────────────────────┴──────────────────────┘
                             All for ONE demo!
```

---

## WEEK 5 (April 6-12) - D2 Week! The 59% Moment

| Day | Member 1 | Member 2 | Member 3 | Member 4 |
|-----|----------|----------|----------|----------|
| **Mon** | 🔴 **NEEDS:** Final models<br>🟢 **GIVES:** Inference API | 🔴 **NEEDS:** Production data<br>🟢 **GIVES:** LSTM + XGBoost | 🔴 **NEEDS:** ML API<br>🟢 **GIVES:** Forecast dashboard | 🔴 **NEEDS:** API keys<br>🟢 **GIVES:** Live data feed |
| **Tue** | 🔴 **NEEDS:** Model validation<br>🟢 **GIVES:** Cache warming | 🔴 **NEEDS:** Performance review<br>🟢 **GIVES:** 59% METRICS | 🔴 **NEEDS:** Chart feedback<br>🟢 **GIVES:** Comparison view | 🔴 **NEEDS:** Mobile API<br>🟢 **GIVES:** Flutter screens |
| **Wed (D2)** | ✅ **DEMO:** ML API | ✅ **DEMO:** 59% Improvement! | ✅ **DEMO:** Live Forecasts | ✅ **DEMO:** Mobile + Data |
| **Thu** | POST-DEMO fixes | POST-DEMO tuning | POST-DEMO polish | POST-DEMO data refresh |
| **Fri** | 🔴 **NEEDS:** Mobile endpoints<br>🟢 **GIVES:** Optimized API | 🔴 **NEEDS:** Feedback<br>🟢 **GIVES:** Model cards | 🔴 **NEEDS:** Mobile designs<br>🟢 **GIVES:** Web components | 🔴 **NEEDS:** API docs<br>🟢 **GIVES:** Mobile v0.5 |
| **Sat** | Team sync - plan Weeks 6-7 | Team sync - plan Weeks 6-7 | Team sync - plan Weeks 6-7 | Team sync - plan Weeks 6-7 |
| **Sun** | REST/Recover | REST/Recover | REST/Recover | REST/Recover |

---

## WEEK 6 (April 13-19) - Mobile Sprint

| Day | Member 1 (Backend) | Member 2 (ML) | Member 3 (Web) | Member 4 (Mobile/Data) |
|-----|-------------------|--------------|---------------|----------------------|
| **Mon** | 🔴 **NEEDS:** Mobile requirements from M4<br>🟢 **GIVES:** Mobile-optimized endpoints | 🔴 **NEEDS:** Lightweight model specs<br>🟢 **GIVES:** Quantized models | 🔴 **NEEDS:** Mobile API docs<br>🟢 **GIVES:** Component library | 🔴 **NEEDS:** Mobile API from M1<br>🟢 **GIVES:** Flutter structure |
| **Tue** | 🔴 **NEEDS:** Test devices<br>🟢 **GIVES:** Auth for mobile | 🔴 **NEEDS:** Model testing<br>🟢 **GIVES:** On-device inference | 🔴 **NEEDS:** UI feedback<br>🟢 **GIVES:** Design system | 🔴 **NEEDS:** API working<br>🟢 **GIVES:** Material list screen |
| **Wed** | 🔴 **NEEDS:** Push notification setup<br>🟢 **GIVES:** Webhook endpoints | 🔴 **NEEDS:** Edge cases<br>🟢 **GIVES:** Anomaly detection | 🔴 **NEEDS:** Mobile preview<br>🟢 **GIVES:** Web components | 🔴 **NEEDS:** Geolocation API<br>🟢 **GIVES:** Map integration |
| **Thu** | 🔴 **NEEDS:** Offline requirements<br>🟢 **GIVES:** Sync endpoints | 🔴 **NEEDS:** Model updates<br>🟢 **GIVES:** Retraining pipeline | 🔴 **NEEDS:** Icon set<br>🟢 **GIVES:** Asset export | 🔴 **NEEDS:** Hive schema approval<br>🟢 **GIVES:** Offline storage |
| **Fri** | 🔴 **NEEDS:** Camera features<br>🟢 **GIVES:** Image upload API | 🔴 **NEEDS:** Real-time data<br>🟢 **GIVES:** ML Kit integration | 🔴 **NEEDS:** Mobile website<br>🟢 **GIVES:** PWA for testing | 🔴 **NEEDS:** Camera permission<br>🟢 **GIVES:** Barcode scanner |
| **Sat** | Code review with M4 | Model review | UI review | Integration testing |
| **Sun** | Mobile API stable | ML mobile-ready | Web v0.9 | Mobile v0.8 |

---

## WEEK 7 (April 20-26) - Integration Hell Week (But Prepared!)

| Day | Team Integration Tasks | Who Leads | Who Supports |
|-----|----------------------|-----------|--------------|
| **Mon** | **INTEGRATION POINT 1:** Web + API<br>• Connect web to real ML endpoints<br>• Test all chart visualizations<br>• Verify caching headers | Member 3 | Member 1 |
| **Tue** | **INTEGRATION POINT 2:** Mobile + API<br>• Connect Flutter to backend<br>• Test offline sync<br>• Push notification test | Member 4 | Member 1 |
| **Wed** | **INTEGRATION POINT 3:** ML + API<br>• Model inference speed test<br>• Cache hit rate verification<br>• 59% improvement validation | Member 2 | Member 1 |
| **Thu** | **INTEGRATION POINT 4:** Data + All<br>• Live data pipeline test<br>• Update frequency check<br>• Data quality validation | Member 4 | All |
| **Fri** | **FULL SYSTEM TEST #1**<br>• End-to-end user journey<br>• Error handling verification<br>• Performance benchmarks | Member 1 (lead) | All |
| **Sat** | Bug fixes from test #1 | Each their own | Team support |
| **Sun** | **FULL SYSTEM TEST #2**<br>• Regression testing<br>• Load testing (simulated users) | Member 1 | All |

---

## WEEK 8 (April 27-May 3) - MVP COMPLETE! D3 Ready

| Day | Morning | Afternoon | Evening |
|-----|---------|-----------|---------|
| **Mon** | Final features | Performance tuning | Documentation |
| **Tue** | Bug bash #1 | Bug bash #2 | Bug fixes |
| **Wed** | D3 DRESS REHEARSAL #1 | Fixes from rehearsal | Individual prep |
| **Thu** | D3 DRESS REHEARSAL #2 | Final fixes | REST |
| **Fri** | D3 FINAL POLISH | Deploy to production | Celebrate 🎉 |
| **Sat** | REST | REST | REST |
| **Sun** | 🎯 **D3 - MVP COMPLETE!** | 🎯 **D3 - MVP COMPLETE!** | 🎯 **D3 - MVP COMPLETE!** |

---

## 🚨 WHAT HAPPENS IF DEPENDENCIES MISS

| If This is Late | Impact | Backup Plan |
|----------------|--------|-------------|
| Member 4 data (Week 1) | ML can't train, API has no data | Use synthetic data (Member 2 generates) |
| Member 1 API (Week 2) | Web can't build UI | Member 3 uses mock data |
| Member 2 models (Week 3) | D1 lacks ML | Show baseline only |
| Member 3 UI (Week 4) | ST1 looks unfinished | Show wireframes + API demo |
| Member 4 mobile (Week 5) | D2 no mobile | Web-only demo |
| Member 1 cache (Week 6) | Slow API | Optimize queries |
| Any integration (Week 7) | MVP at risk | Daily standups, escalate |
