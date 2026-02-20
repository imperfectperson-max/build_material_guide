# 📊 DAILY TEAM DEPENDENCY MATRIX

**Building Materials Price Intelligence Platform • March 9 – June 9, 2026**

*Revised & corrected — February 20, 2026*

## ✅ Corrections Applied to This Version

| Issue | Fix |
|-------|-----|
| Schema display: `price_per_kg` (truncated) | Corrected to `price_per_kg_zar` everywhere — matches the canonical 16-column contract |
| All milestone checkboxes pre-ticked `[x]` | Reset all to `[ ]` — project has not started yet (first day is March 9, 2026) |
| ST1 date: Week 4 table showed Sunday April 5 | Corrected to Wednesday April 1 — matches all four individual roadmaps |
| D3 date: Week 8 table showed Sunday May 4 | Corrected to Friday May 1 — matches all four individual roadmaps |
| Week 1 Mon M4: "Scraping targets from M2" | ML Engineer does not determine scraping targets. Corrected to "Supplier research brief from team" |
| Bulk import endpoint: matrix said Week 2 Thu | Backend roadmap delivers /api/data/bulk-import in Week 1 Fri. Matrix corrected accordingly |
| Week 4 ML col: "59% improvement" claimed | 59% is the Week 5 ensemble result. Week 4 delivers Prophet + LSTM only. Matrix corrected |
| Week 6 phantom features: image upload API, webhook endpoints, quantized on-device ML, ML Kit | None of these exist in any roadmap. Rows replaced with features that are actually being built |

---



| Day | Member 1 (Backend) Needs | Member 2 (ML) Needs | Member 3 (Web) Needs | Member 4 (Mobile/Data) Needs |
|-----|-------------------------|-------------------|-------------------|--------------------------|
| **Mon** | • DB schema approved by all | • Dataset from Member 4 | • API spec from Member 1 | • Supplier research brief from team |
| **Tue** | • Sample data from Member 4 | • Historical data sources | • Design feedback from all | • Scraping targets confirmed from team |
| **Wed** | • **🔴 SCHEMA CONTRACT:** 16-column format locked<br>• Data format specs from M4 | • Feature list from M2 | • Color palette approved | • **🟢 DELIVERS:** Canonical schema confirmed<br>• First 100 records to Member 1 |
| **Thu** | • Test data for API testing | • **🔴 NEEDS:** CMPI data (Sheet 2)<br>• EDA results to team | • Component library review | • PDF samples to all |
| **Fri** | • API endpoints live: /api/prices, /api/data/bulk-import | • **🟢 DELIVERS:** CMPI + World Bank data loaded<br>• Feature engineering plan | • Layout feedback from all | • 500+ records to Member 1 (schema-validated) |
| **Sat** | • Database connection string | • Baseline approach doc | • Prototype link to team | • Data quality report |
| **Sun** | • Redis connection ready | • Research summary | • Wireframes complete | • Scraper code in GitHub |

### CRITICAL PATH WEEK 1:

```
Member 4 ──(16-col schema)──> Member 1 ──(API)──> Member 3
    │                             │
    └────(schema + CMPI data)─> Member 2
    
🔑 KEY MILESTONE: Schema contract locked by Wed EOD
```

---

## WEEK 2 (March 16-22) - Core Development

| Day | Member 1 Gives | Member 2 Gives | Member 3 Gives | Member 4 Gives |
|-----|---------------|---------------|---------------|---------------|
| **Mon** | • API docs to M3/M4 | • Data requirements to M4 | • UI components preview | • Daily scraped data (schema format) |
| **Tue** | • Auth endpoints to test | • Baseline metrics | • Material list UI | • PDF parser results |
| **Wed** | • Redis cache stats | • Feature importance | • Chart components | • New supplier data |
| **Thu** | • Rate limiting demo<br>• JWT blacklist done | • Model comparison | • Search UI | • **🔴 NEEDS:** Bulk import confirmed working<br>• 800+ records via bulk import |
| **Fri** | • Performance metrics report<br>• API stability confirmed | • ARIMA baseline results to team | • Detail page draft<br>• Responsive layout done | • Data cleaning script shared |
| **Sun** | • API stability report | • Week 2 summary | • UI component docs | • Scraper monitoring |

### CRITICAL PATH WEEK 2:

```
Member 4 ──(bulk upload)──> Member 1 ──(API with schema)──> Member 3 & Member 4
                               │
                               └──(cache)──> All demos faster
                               
🔑 KEY MILESTONE: Bulk import endpoint live by Thu EOD
```

---

## WEEK 3 (March 23-29) - D1 Week!

| Day | Member 1 | Member 2 | Member 3 | Member 4 |
|-----|----------|----------|----------|----------|
| **Mon** | **NEEDS:** Test data from M4<br>**GIVES:** **🟢 API with all 16 schema fields** | **NEEDS:** Clean data from M4<br>**GIVES:** Baseline results | **NEEDS:** **🔴 Schema-validated API** from M1<br>**GIVES:** Dashboard preview | **NEEDS:** Feedback on data<br>**GIVES:** 1000+ records (all schema fields) |
| **Tue** | **NEEDS:** Forecast structure<br>**GIVES:** Cache demo | **NEEDS:** Model feedback<br>**GIVES:** XGBoost results | **NEEDS:** Charts feedback<br>**GIVES:** Forecast UI | **NEEDS:** API test results<br>**GIVES:** Data quality report |
| **Wed (D1)** | ✅ **DEMO:** API + Redis | ✅ **DEMO:** Baseline models | ✅ **DEMO:** Dashboard UI | ✅ **DEMO:** 1000+ records |
| **Thu** | **NEEDS:** ML endpoint specs<br>**GIVES:** Performance tuning | **NEEDS:** GPU time<br>**GIVES:** LSTM plan | **NEEDS:** Real data<br>**GIVES:** Chart refinements | **NEEDS:** New targets<br>**GIVES:** More suppliers |
| **Fri** | **NEEDS:** Model format<br>**GIVES:** Integration plan | **NEEDS:** Feedback<br>**GIVES:** Prophet results | **NEEDS:** API updates<br>**GIVES:** Mobile designs | **NEEDS:** API docs<br>**GIVES:** Mobile mock data |
| **Sat** | **NEEDS:** Test results<br>**GIVES:** Week 3 summary | **NEEDS:** Data for tuning<br>**GIVES:** Progress report | **NEEDS:** Review time<br>**GIVES:** UI polish | **NEEDS:** Feedback<br>**GIVES:** 1500+ records |
| **Sun** | REST/PLAN for Week 4 | REST/PLAN for Week 4 | REST/PLAN for Week 4 | REST/PLAN for Week 4 |

🔑 **KEY MILESTONE:** Member 1 must deliver GET /api/prices with all 16 schema fields by Monday EOD for Member 3's charts

---

## WEEK 4 (March 30-April 5) - ST1 Week + ML Sprint

| Day | Member 1 (Backend) | Member 2 (ML) | Member 3 (Web) | Member 4 (Mobile/Data) |
|-----|-------------------|--------------|---------------|----------------------|
| **Mon** | 🔴 **NEEDS FROM M2:** Model prediction format<br>🔴 **NEEDS FROM M4:** 1500+ records<br>🟢 **GIVES TO M3:** POST /api/forecast endpoint | 🔴 **NEEDS FROM M4:** Historical data<br>🟢 **GIVES TO M1:** Prophet results | 🔴 **NEEDS FROM M1:** ML API<br>🟢 **GIVES TO M4:** Forecast UI components | 🔴 **NEEDS FROM M3:** Mobile specs<br>🟢 **GIVES:** Fresh scraped data |
| **Tue** | 🔴 **NEEDS:** Model files<br>🟢 **GIVES:** Cache for ML | 🔴 **NEEDS:** GPU access<br>🟢 **GIVES:** LSTM results | 🔴 **NEEDS:** Forecast format<br>🟢 **GIVES:** Charts | 🔴 **NEEDS:** API docs<br>🟢 **GIVES:** 10+ suppliers |
| **Wed (🎯 ST1 — Apr 1)** | 🟢 **GIVES:** API stable for ST1, all endpoints documented | 🟢 **GIVES:** Prophet + LSTM results ready (Note: ensemble is Week 5) | 🟢 **GIVES:** Dashboard complete for ST1, anomaly indicators | 🟢 **GIVES:** Data pipeline demo ready, Mobile v0.3 |
| **Thu** | 🔴 **NEEDS:** Performance targets<br>🟢 **GIVES:** Load tests | 🔴 **NEEDS:** Validation data<br>🟢 **GIVES:** Prophet MAPE < 8% | 🔴 **NEEDS:** Comparison data<br>🟢 **GIVES:** Dashboard v2 | 🔴 **NEEDS:** Geolocation<br>🟢 **GIVES:** Supplier locations |
| **Fri** | 🔴 **NEEDS:** Final specs<br>🟢 **GIVES:** Production API | 🔴 **NEEDS:** Model storage<br>🟢 **GIVES:** Saved models | 🔴 **NEEDS:** Final UI feedback<br>🟢 **GIVES:** PWA setup | 🔴 **NEEDS:** Offline format<br>🟢 **GIVES:** Hive schema |
| **Sat** | PREP for ST1 | PREP for ST1 | PREP for ST1 | PREP for ST1 |
| **Sun** | Post-ST1 review / Week 5 plan | Post-ST1 review / Ensemble planning | Post-ST1 review / Week 5 plan | Post-ST1 review / Flutter integration plan |

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

*⚠ Note: Image upload API, webhook endpoints, on-device ML inference, and ML Kit were removed — none of these exist in any team member's roadmap. Rows replaced with features that are actually being built.*

| Day | Member 1 (Backend) | Member 2 (ML) | Member 3 (Web) | Member 4 (Mobile/Data) |
|-----|-------------------|--------------|---------------|----------------------|
| **Mon** | 🔴 **NEEDS:** Mobile requirements from M4<br>🟢 **GIVES:** Mobile-optimized API responses | 🔴 **NEEDS:** Retraining pipeline requirements<br>🟢 **GIVES:** Anomaly detection model tuned | 🔴 **NEEDS:** Mobile API docs from M1<br>🟢 **GIVES:** Component library exported | 🔴 **NEEDS:** Mobile API from M1<br>🟢 **GIVES:** Flutter project structure done |
| **Tue** | 🔴 **NEEDS:** Test device results<br>🟢 **GIVES:** Auth JWT for mobile confirmed | 🔴 **NEEDS:** Model testing feedback<br>🟢 **GIVES:** Anomaly detection results | 🔴 **NEEDS:** UI feedback from M4<br>🟢 **GIVES:** Design system docs | 🔴 **NEEDS:** API endpoints confirmed<br>🟢 **GIVES:** MaterialListScreen working |
| **Wed** | 🔴 **NEEDS:** Offline requirements from M4<br>🟢 **GIVES:** Sync endpoint /api/prices for offline cache | 🔴 **NEEDS:** Edge case data from M4<br>🟢 **GIVES:** Anomaly detection endpoint docs | 🔴 **NEEDS:** Mobile preview from M4<br>🟢 **GIVES:** PWA tested on mobile browser | 🔴 **NEEDS:** Location API clarification<br>🟢 **GIVES:** Geolocation filter integrated |
| **Thu** | 🔴 **NEEDS:** Offline sync test results<br>🟢 **GIVES:** Hive schema approved | 🔴 **NEEDS:** Model update frequency confirmed<br>🟢 **GIVES:** Retraining pipeline running | 🔴 **NEEDS:** Asset format confirmation<br>🟢 **GIVES:** Icon set exported to M4 | 🔴 **NEEDS:** Hive schema approval from M1<br>🟢 **GIVES:** Offline Hive cache working |
| **Fri** | 🔴 **NEEDS:** Pipeline status from M4<br>🟢 **GIVES:** Admin scraper-status endpoint | 🔴 **NEEDS:** Model card format<br>🟢 **GIVES:** Model version tracking in DB | 🟢 **GIVES:** PWA offline banner done<br>🟢 **GIVES:** Skeleton screens done | 🟢 **GIVES:** Barcode scanner screen done<br>🟢 **GIVES:** Favourites system done |
| **Sat** | Code review with M4 | Model review | UI review | Integration testing |
| **Sun** | Mobile API stable | ML mobile-ready | Web v0.9 | Mobile v0.8 |

---

## WEEK 7 (April 20-26) - Integration Hell Week (But Prepared!)

| Day | Team Integration Tasks | Who Leads | Who Supports |
|-----|----------------------|-----------|--------------|
| **Mon** | **INTEGRATION POINT 1:** Web + API<br>• Connect web to real ML endpoints<br>• Test all chart visualizations<br>• Verify caching headers | Member 3 | Member 1 |
| **Tue** | **INTEGRATION POINT 2:** Mobile + API<br>• Connect Flutter to backend<br>• Test offline Hive sync<br>• Biometric auth end-to-end | Member 4 | Member 1 |
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
| **Fri (🎉 D3 — May 1)** | D3 FINAL POLISH | Deploy to production | Celebrate 🎉 |
| **Sat–Sun** | REST — You earned it. D3 MVP is done. 🎉 | REST | REST |

---

## 🚨 WHAT HAPPENS IF DEPENDENCIES MISS

| If This is Late | Impact | Backup Plan |
|----------------|--------|-------------|
| Member 4 data (Week 1) | ML can't train, API has no data | Use synthetic data (Member 2 generates) |
| **Schema not locked (Week 1 Wed)** | **All code breaks, API/UI/Mobile incompatible** | **Emergency meeting, lock schema immediately with mock 16 fields** |
| Member 1 API (Week 2) | Web can't build UI | Member 3 uses mock data |
| **Bulk import endpoint missing (Week 1 Fri)** | **Member 4 can't upload scraped data** | **Member 4 temporarily uses psql direct INSERT; Member 1 unblocks as highest priority** |
| Member 2 models (Week 3) | D1 lacks ML | Show baseline only |
| **Schema fields missing from API (Week 3 Mon)** | **Member 3 charts break, Member 4 mobile models fail** | **Use mock JSON with all 16 fields, Member 1 fixes API urgently** |
| Member 3 UI (Week 4) | ST1 looks unfinished | Show wireframes + API demo |
| Member 4 mobile (Week 5) | D2 no mobile | Web-only demo |
| Member 1 cache (Week 6) | Slow API | Optimize queries |
| Any integration (Week 7) | MVP at risk | Daily standups, escalate |

---

## 🎯 SCHEMA-AWARE CRITICAL PATH

**The canonical 16-column schema is the shared contract between all 4 team members:**

```
┌─────────────────────────────────────────────────────────────────────┐
│  16-COLUMN CANONICAL SCHEMA (The Contract)                          │
│  record_id, date, year, month, material_name, material_category,   │
│  supplier_name, region, province, price_zar, unit, price_per_kg_zar,   │
│  price_change_mom_pct, price_change_yoy_pct, stock_status,         │
│  bulk_discount_available                                            │
└─────────────────────────────────────────────────────────────────────┘
                                    │
        ┌───────────────────────────┼───────────────────────────┐
        ▼                           ▼                           ▼
  Member 4 (Data)           Member 1 (Backend)         Member 2 (ML)
  • Python scraper          • PostgreSQL table          • Feature eng
    outputs schema            (16 columns)               using all fields
  • DataValidator           • POST /bulk-import         • CMPI features
  • Flutter PriceRecord       validates schema          • XGBoost training
    (16 fields)             • GET /prices returns         with schema
                              all 16 fields
        │                           │                           │
        └───────────────────────────┼───────────────────────────┘
                                    ▼
                          Member 3 (Frontend)
                          • TypeScript PriceRecord (16 fields)
                          • React charts use schema
                          • API calls expect 16 fields

🔴 CRITICAL: If schema changes after Week 1 Wed, ALL code breaks!
```

---

## 📊 SCHEMA MILESTONES CHECKLIST

### Week 1
- [ ] **Wed EOD:** Schema contract locked (16 columns defined)
- [ ] **Fri EOD:** Member 4 confirms scraper outputs schema
- [ ] **Fri EOD:** Member 2 loads CMPI data (Sheet 2) and World Bank data (Sheets 3/4/6)

### Week 2
- [ ] **Thu EOD:** Member 1 delivers POST /api/data/bulk-import (validates 16 fields + enums)
- [ ] **Fri EOD:** Member 4 successfully uploads first batch via bulk import

### Week 3
- [ ] **Mon EOD:** Member 1 confirms GET /api/prices returns all 16 schema fields
- [ ] **Tue EOD:** Member 3 tests charts with real schema data
- [ ] **Tue EOD:** Member 4 tests Flutter models with API data

### Week 4
- [ ] **Tue EOD:** Member 2 confirms LSTM Kaggle model trained and downloaded
- [ ] **Tue EOD:** Member 2 confirms Prophet MAPE < 8%
- [ ] **Wed:** ST1 presentation — Wednesday April 1

### Week 5
- [ ] **Tue EOD:** Member 2 confirms ensemble MAPE < 5% (59% improvement over ARIMA)
- [ ] **Wed:** D2 demo — Wednesday April 8

### Critical Success Factors
1. **Schema is immutable after Week 1 Wed** — no column renames, reordering, or deletions
2. **All team members validate their code against the schema** — use validators
3. **Any schema questions escalated immediately** — don't guess, ask team
4. **Bulk import is the only path from scraper to DB** — no CSV workarounds after Week 1
5. **Demo dates are fixed: D1=Mar 25, ST1=Apr 1, D2=Apr 8, D3=May 1, Final=Jun 10**

*Last updated: February 20, 2026 • Revised from original dependency matrix • All four individual roadmaps are authoritative for detailed weekly tasks*
