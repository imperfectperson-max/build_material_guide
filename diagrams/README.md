# UML Diagrams for Building Materials Price Intelligence Platform

This directory contains comprehensive UML diagrams documenting the system architecture, user interactions, and key workflows of the Building Materials Price Intelligence Platform.

## Diagram Files

### Use Case Diagram
**File:** `use_case_diagram.puml`

This diagram illustrates all the use cases and actors in the system, showing:
- **Actors:**
  - Contractor/User (primary user)
  - Admin (system administrator)
  - Data Scraper (automated system)
  - ML Engine (machine learning system)
  - External Supplier Websites

- **Use Case Categories:**
  - Authentication & Authorization
  - Price Comparison & Search
  - Price Forecasting & Analytics
  - Mobile Features
  - Administration
  - Data Processing (Backend)
  - Machine Learning

### Activity Diagrams

#### 1. User Authentication Activity Diagram
**File:** `activity_authentication.puml`

Describes the complete authentication workflow including:
- Credential validation
- Password verification using bcrypt/Argon2
- JWT token generation and storage
- Biometric authentication setup (mobile)
- Failed login handling and account locking
- Redis integration for token management

#### 2. Price Comparison Workflow Activity Diagram
**File:** `activity_price_comparison.puml`

Details the price comparison process:
- Material selection
- Redis cache checking (<50ms response for cache hits)
- Database queries for price data
- Supplier comparison and sorting
- Regional filtering
- Mobile geolocation features
- Export functionality (PDF/CSV)

#### 3. Price Forecasting Workflow Activity Diagram
**File:** `activity_price_forecasting.puml`

Explains the ML-driven price forecasting workflow:
- Forecast period selection (7-day or 30-day)
- Rate limiting checks
- Redis cache management (6-hour TTL)
- Multi-model inference (XGBoost, Random Forest, LSTM, Prophet)
- Ensemble prediction method
- Confidence interval calculation
- Price alert configuration

#### 4. Data Collection and Processing Activity Diagram
**File:** `activity_data_collection.puml`

Illustrates the automated data collection pipeline:
- Scheduled scraping jobs (daily at 2:00 AM)
- Web scraping and PDF extraction with OCR
- Data validation and cleaning
- Database transaction management
- Cache invalidation
- Feature engineering for ML models
- Error handling and notifications

#### 5. Mobile App Price Search Activity Diagram
**File:** `activity_mobile_search.puml`

Shows the mobile-specific user workflow:
- Biometric authentication
- Multiple search methods (manual, barcode scan, voice)
- Geolocation-based supplier filtering
- Offline mode with Hive storage
- Push notification setup
- Favorites management

#### 6. ML Model Training and Evaluation Activity Diagram
**File:** `activity_ml_training.puml`

Documents the complete ML pipeline:
- Data preparation and feature engineering
- Model training (ARIMA, XGBoost, Random Forest, Prophet, LSTM)
- Hyperparameter tuning
- Performance evaluation (MAE, RMSE, MAPE, R²)
- Model deployment and versioning
- Performance monitoring

## Viewing the Diagrams

### Viewing PNG Exports

All diagrams are available as PNG images in this directory:
- `use_case_diagram.png`
- `activity_authentication.png`
- `activity_price_comparison.png`
- `activity_price_forecasting.png`
- `activity_data_collection.png`
- `activity_mobile_search.png`
- `activity_ml_training.png`

Simply click on any PNG file to view it directly in GitHub or your file browser.

### Online Viewers
These PlantUML diagrams can be viewed using:

1. **PlantUML Online Server**: http://www.plantuml.com/plantuml/uml/
   - Copy and paste the content of any `.puml` file

2. **VS Code Extension**: 
   - Install "PlantUML" extension by jebbs
   - Open any `.puml` file and use Alt+D to preview

3. **IntelliJ IDEA Plugin**:
   - Install "PlantUML integration" plugin
   - Right-click on `.puml` file → "Show PlantUML Preview"

### Generating Images

To generate PNG/SVG images from these diagrams:

```bash
# Install PlantUML
# On Ubuntu/Debian:
sudo apt-get install plantuml

# On macOS:
brew install plantuml

# Generate PNG images
plantuml diagrams/*.puml

# Generate SVG images
plantuml -tsvg diagrams/*.puml
```

### Integration with Documentation

These diagrams can be integrated into markdown documentation using:

```markdown
![Use Case Diagram](./diagrams/use_case_diagram.png)
![Authentication Flow](./diagrams/activity_authentication.png)
```

## Diagram Conventions

### PlantUML Syntax Elements Used:
- **Actors**: Represented using `actor` keyword
- **Use Cases**: Represented using `usecase` keyword
- **Swimlanes**: Used in activity diagrams to show different system components
- **Decision Points**: Diamond shapes showing conditional logic
- **Fork/Join**: Parallel processing representation
- **Notes**: Additional context and explanations

### System Components Represented:
- **Frontend**: Web App, Mobile App
- **Backend**: API, Database (PostgreSQL), Cache (Redis)
- **ML Pipeline**: Training, Inference, Feature Engineering
- **External Systems**: Supplier websites, notification services

## Key Architectural Patterns Illustrated

1. **Caching Strategy**: Redis-based caching with TTL management
2. **Authentication**: JWT-based with biometric support on mobile
3. **ML Pipeline**: Feature engineering → Training → Evaluation → Deployment
4. **Data Flow**: Scraping → Validation → Storage → Processing
5. **Mobile Offline-First**: Hive local storage with sync capability
6. **Rate Limiting**: Redis-based API protection

## System Requirements Captured

- **Security**: JWT authentication, bcrypt/Argon2 hashing, rate limiting
- **Performance**: Redis caching (<50ms response time target)
- **Scalability**: Scheduled jobs, cache warming, parallel processing
- **Mobile Features**: Offline support, biometric auth, push notifications
- **ML Capabilities**: Multiple models, ensemble methods, 59% improvement target
- **User Experience**: Real-time search, geolocation, export functionality

## Related Documentation

- [README.md](../README.md) - Project overview
- [ROADMAP.md](../ROADMAP.md) - Project timeline and milestones
- [TECHNOLOGY_CHOICES.md](../TECHNOLOGY_CHOICES.md) - Technology stack details
- [SECURITY.md](../SECURITY.md) - Security architecture
- [RESEARCH_FOUNDATION.md](../RESEARCH_FOUNDATION.md) - ML research basis

## Notes for Development Team

These diagrams serve as:
- **Requirements Documentation**: Capturing all system use cases
- **Design Reference**: Guiding implementation decisions
- **Communication Tool**: Facilitating team understanding
- **Testing Guide**: Identifying test scenarios and edge cases
- **Maintenance Reference**: Understanding system workflows for debugging

Last Updated: February 16, 2026
