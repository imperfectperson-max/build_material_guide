# Activity Diagrams (Mermaid Format)

## 1. User Authentication Flow

```mermaid
flowchart TD
    Start([User Starts Authentication]) --> Input[Enter Credentials]
    Input --> Validate{Valid Input?}
    Validate -->|No| Error1[Display Validation Error]
    Error1 --> End1([End])
    
    Validate -->|Yes| CheckUser{User Exists?}
    CheckUser -->|No| Error2[Display User Not Found]
    Error2 --> Register[Option to Register]
    Register --> End2([End])
    
    CheckUser -->|Yes| CheckPass{Password Matches?}
    CheckPass -->|No| LogFail[Log Failed Attempt]
    LogFail --> CheckAttempts{Max Attempts Exceeded?}
    CheckAttempts -->|Yes| LockAccount[Lock Account]
    LockAccount --> Notify[Send Security Notification]
    Notify --> Error3[Display Error]
    Error3 --> End3([End])
    
    CheckAttempts -->|No| Error4[Display Invalid Credentials]
    Error4 --> ResetOption[Option to Reset Password]
    ResetOption --> End4([End])
    
    CheckPass -->|Yes| GenToken[Generate JWT Token]
    GenToken --> StoreRedis[Store in Redis]
    StoreRedis --> CheckMobile{Mobile App?}
    
    CheckMobile -->|Yes| PromptBio[Prompt Biometric Setup]
    PromptBio --> AcceptBio{User Accepts?}
    AcceptBio -->|Yes| RegBio[Register Biometric]
    RegBio --> Dashboard[Navigate to Dashboard]
    AcceptBio -->|No| Dashboard
    
    CheckMobile -->|No| Dashboard
    Dashboard --> End5([End])
```

## 2. Price Comparison Workflow

```mermaid
flowchart TD
    Start([User Accesses Price Comparison]) --> Select[Select Material Type]
    Select --> ValidateToken[Validate JWT Token]
    ValidateToken --> CheckCache{Cache Hit?}
    
    CheckCache -->|Yes| CachedResults[Retrieve Cached Results <50ms]
    CheckCache -->|No| QueryDB[Query PostgreSQL Database]
    QueryDB --> FetchPrices[Fetch Current Prices]
    FetchPrices --> CalcStats[Calculate Statistics]
    CalcStats --> StoreCache[Store in Redis Cache]
    
    CachedResults --> Sort[Sort Suppliers by Price]
    StoreCache --> Sort
    Sort --> ApplyPrefs[Apply User Preferences]
    ApplyPrefs --> Display[Display Price Comparison]
    
    Display --> Filter{Apply Filters?}
    Filter -->|Yes| Requery[Re-query with Filters]
    Requery --> UpdateResults[Update Results]
    UpdateResults --> Display2[View Filtered Results]
    
    Filter -->|No| SaveFav{Save to Favorites?}
    Display2 --> SaveFav
    
    SaveFav -->|Yes| SaveDB[Store in User Profile]
    SaveDB --> Confirm[Confirm Action]
    
    SaveFav -->|No| MobileCheck{Mobile View?}
    Confirm --> MobileCheck
    
    MobileCheck -->|Yes| GeoLocation[Calculate Distances]
    GeoLocation --> ShowMap[Display Supplier Map]
    ShowMap --> Export{Export Report?}
    
    MobileCheck -->|No| Export
    Export -->|Yes| GenerateReport[Generate PDF/CSV]
    GenerateReport --> Download[Download Report]
    Download --> End([End])
    
    Export -->|No| End
```

## 3. Price Forecasting Workflow

```mermaid
flowchart TD
    Start([User Requests Forecast]) --> SelectMaterial[Select Material]
    SelectMaterial --> ChoosePeriod[Choose Forecast Period: 7 or 30 days]
    ChoosePeriod --> SendRequest[Send API Request]
    SendRequest --> ValidateToken[Validate JWT Token]
    
    ValidateToken --> RateLimit{Rate Limit OK?}
    RateLimit -->|No| Error429[Return 429 Error]
    Error429 --> DisplayError[Display Too Many Requests]
    DisplayError --> End1([End])
    
    RateLimit -->|Yes| CheckCache{Cache Hit?}
    CheckCache -->|Yes| GetCached[Retrieve Cached Forecast]
    
    CheckCache -->|No| LoadData[Load Historical Data]
    LoadData --> PrepFeatures[Prepare Feature Matrix]
    PrepFeatures --> LoadModels[Load ML Models]
    
    LoadModels --> XGBoost[XGBoost Prediction]
    LoadModels --> RF[Random Forest Prediction]
    LoadModels --> LSTM[LSTM Prediction]
    LoadModels --> Prophet[Prophet Prediction]
    
    XGBoost --> Ensemble[Apply Ensemble Method]
    RF --> Ensemble
    LSTM --> Ensemble
    Prophet --> Ensemble
    
    Ensemble --> CalcConfidence[Calculate Confidence Intervals]
    CalcConfidence --> CacheResult[Store in Redis 6h TTL]
    CacheResult --> ReturnData[Return Forecast Data]
    
    GetCached --> ReturnData
    ReturnData --> Render[Render Visualization]
    Render --> DisplayForecast[Display Forecast Chart]
    
    DisplayForecast --> SetAlert{Set Price Alert?}
    SetAlert -->|Yes| ConfigAlert[Configure Alert Threshold]
    ConfigAlert --> EnableNotif[Enable Notifications]
    EnableNotif --> SaveAlert[Store Alert Preferences]
    SaveAlert --> End2([End])
    
    SetAlert -->|No| End2
```

## 4. Data Collection Process

```mermaid
flowchart TD
    Start([Scheduled Job Triggers]) --> LoadConfig[Load Supplier Configuration]
    LoadConfig --> GetList[Retrieve Target Suppliers]
    
    GetList --> NextSupplier[Select Next Supplier]
    NextSupplier --> Scrape[Web Scraping / PDF Extraction]
    Scrape --> Extract[Extract Price Data]
    Extract --> ValidateData{Data Valid?}
    
    ValidateData -->|No| LogError[Log Extraction Error]
    LogError --> Retry[Add to Retry Queue]
    
    ValidateData -->|Yes| Normalize[Normalize Data Format]
    Normalize --> AddMeta[Add Metadata]
    AddMeta --> Buffer[Store in Buffer]
    
    Retry --> MoreSuppliers{More Suppliers?}
    Buffer --> MoreSuppliers
    
    MoreSuppliers -->|Yes| NextSupplier
    MoreSuppliers -->|No| Aggregate[Aggregate Collected Data]
    
    Aggregate --> Validation[Apply Validation Rules]
    Validation --> CheckDuplicates[Check Duplicates]
    Validation --> ValidateRanges[Validate Price Ranges]
    Validation --> VerifyNames[Verify Material Names]
    
    CheckDuplicates --> ValidationResult{Validation Passed?}
    ValidateRanges --> ValidationResult
    VerifyNames --> ValidationResult
    
    ValidationResult -->|No| ErrorHandler[Log Validation Errors]
    ErrorHandler --> Critical{Critical Error?}
    Critical -->|Yes| Alert[Trigger Alert & Pause]
    Critical -->|No| ContinueValid[Continue with Valid Data]
    
    ValidationResult -->|Yes| Clean[Clean & Transform Data]
    Clean --> Transaction[Begin Database Transaction]
    Transaction --> Insert[Insert Price Records]
    Insert --> Commit[Commit Transaction]
    
    Commit --> InvalidateCache[Invalidate Redis Caches]
    InvalidateCache --> FeaturePipeline[Trigger Feature Engineering]
    FeaturePipeline --> CheckThreshold{Price Changes > Threshold?}
    
    CheckThreshold -->|Yes| SendNotifications[Send Price Alerts]
    SendNotifications --> LogSuccess[Log Success]
    
    CheckThreshold -->|No| LogSuccess
    LogSuccess --> UpdateMetrics[Update Metrics]
    UpdateMetrics --> End([End])
    
    Alert --> End
    ContinueValid --> End
```

## 5. Mobile App Search (Simplified)

```mermaid
flowchart TD
    Start([User Opens Mobile App]) --> CheckConnection{Online?}
    
    CheckConnection -->|Yes| Biometric[Biometric Authentication]
    Biometric --> AuthSuccess{Auth Success?}
    
    AuthSuccess -->|No| ErrorAuth[Display Error]
    ErrorAuth --> PasswordOption[Offer Password Login]
    PasswordOption --> End1([End])
    
    AuthSuccess -->|Yes| SearchMethod{Search Method}
    SearchMethod -->|Manual| TypeSearch[Enter Material Name]
    SearchMethod -->|Barcode| ScanBarcode[Scan Barcode with Camera]
    SearchMethod -->|Voice| VoiceSearch[Voice Input]
    
    TypeSearch --> APIRequest[Send API Request]
    ScanBarcode --> MLKit[ML Kit Decodes Barcode]
    MLKit --> APIRequest
    VoiceSearch --> SpeechToText[Convert Speech to Text]
    SpeechToText --> APIRequest
    
    APIRequest --> ProcessQuery[Process Search Query]
    ProcessQuery --> FetchPrices[Fetch Prices]
    FetchPrices --> GeoFilter[Apply Geolocation Filter]
    GeoFilter --> DisplayResults[Display Results with Map]
    
    DisplayResults --> SaveFav{Save to Favorites?}
    SaveFav -->|Yes| SaveLocal[Save to Hive Storage]
    SaveLocal --> SyncAPI[Sync with Backend]
    SyncAPI --> End2([End])
    
    SaveFav -->|No| End2
    
    CheckConnection -->|No| CheckCache{Cached Data Available?}
    CheckCache -->|Yes| LoadOffline[Load from Hive Storage]
    LoadOffline --> DisplayOffline[Display Offline Data]
    DisplayOffline --> OfflineIndicator[Show Offline Mode Banner]
    OfflineIndicator --> End3([End])
    
    CheckCache -->|No| NoData[Display No Offline Data]
    NoData --> PromptConnect[Prompt to Connect]
    PromptConnect --> End4([End])
```

## 6. ML Model Training Process

```mermaid
flowchart TD
    Start([Initiate Training Pipeline]) --> LoadData[Load Historical Data]
    LoadData --> FeatureEngineering[Feature Engineering]
    
    FeatureEngineering --> TimeSeries[Time-Series Features]
    FeatureEngineering --> Seasonality[Seasonality Features]
    FeatureEngineering --> Economic[Economic Features]
    
    TimeSeries --> Merge[Merge Feature Datasets]
    Seasonality --> Merge
    Economic --> Merge
    
    Merge --> Normalize[Normalize/Scale]
    Normalize --> Split[Train/Val/Test Split]
    
    Split --> TrainModels[Train Models in Parallel]
    TrainModels --> ARIMA[ARIMA Baseline]
    TrainModels --> XGBoost[XGBoost]
    TrainModels --> RF[Random Forest]
    TrainModels --> Prophet[Prophet]
    TrainModels --> LSTM[LSTM on GPU]
    
    ARIMA --> SaveModels[Save Models]
    XGBoost --> SaveModels
    RF --> SaveModels
    Prophet --> SaveModels
    LSTM --> SaveModels
    
    SaveModels --> Evaluate[Model Evaluation]
    Evaluate --> MAE[Calculate MAE]
    Evaluate --> RMSE[Calculate RMSE]
    Evaluate --> MAPE[Calculate MAPE]
    
    MAE --> Compare[Compare Performance]
    RMSE --> Compare
    MAPE --> Compare
    
    Compare --> CheckImprovement{> 59% Improvement?}
    
    CheckImprovement -->|Yes| Document[Document Results]
    Document --> Deploy[Deploy to Inference Service]
    Deploy --> UpdateAPI[Update API Endpoints]
    UpdateAPI --> Monitoring[Set Up Monitoring]
    Monitoring --> End1([End])
    
    CheckImprovement -->|No| Review[Review Performance]
    Review --> MoreData{Need More Data?}
    
    MoreData -->|Yes| Collect[Collect Additional Data]
    Collect --> LoadData
    
    MoreData -->|No| NewFeatures{Try New Features?}
    NewFeatures -->|Yes| Engineer[Engineer New Features]
    Engineer --> FeatureEngineering
    
    NewFeatures -->|No| Hyperparams{Adjust Hyperparameters?}
    Hyperparams -->|Yes| GridSearch[Perform Grid Search]
    GridSearch --> TrainModels
    
    Hyperparams -->|No| DeployBest[Deploy Best Available]
    DeployBest --> End2([End])
```

---

## Viewing These Diagrams

These Mermaid diagrams can be viewed directly on GitHub in markdown files, or using:
- [Mermaid Live Editor](https://mermaid.live/)
- VS Code with Mermaid extension
- Any markdown viewer that supports Mermaid

## Notes

- These simplified versions focus on the main flow
- For detailed swimlane versions with error handling, see the PlantUML files
- Each diagram represents a critical system workflow
- Diagrams are designed to be self-documenting for the development team
