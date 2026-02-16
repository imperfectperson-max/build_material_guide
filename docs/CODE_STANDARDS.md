# 📝 Code Documentation Standards

**Building Materials Price Intelligence Platform**  
**Code Documentation Guidelines and Templates**

---

## 📋 Overview

This document establishes the code documentation standards for the Building Materials Price Intelligence Platform. Consistent, high-quality documentation ensures maintainability, facilitates collaboration, and accelerates onboarding of new team members.

**Documentation Philosophy:** Document the "why," not just the "what."

---

## 🎯 Documentation Principles

### 1. Clarity
- Write for humans, not machines
- Use simple, direct language
- Avoid jargon where possible

### 2. Completeness
- Document all public APIs
- Include examples for complex functionality
- Explain edge cases and limitations

### 3. Consistency
- Follow language-specific conventions
- Use standard templates
- Maintain uniform formatting

### 4. Currency
- Update docs with code changes
- Remove outdated documentation
- Version significant changes

### 5. Relevance
- Focus on "why" and "how"
- Avoid obvious comments
- Add value beyond reading the code

---

## 📚 Language-Specific Standards

### JavaScript/TypeScript (JSDoc/TSDoc)

#### Function Documentation Template

```javascript
/**
 * Retrieves the latest price for a specific material from the database.
 * 
 * This function queries the prices table for the most recent price entry
 * for the given material, joining with supplier information. Results are
 * cached for 1 hour to improve performance.
 * 
 * @param {string} materialId - The unique identifier of the material (e.g., 'mat_001')
 * @param {Object} [options] - Optional query parameters
 * @param {string} [options.region] - Filter by specific region (e.g., 'Gauteng')
 * @param {string} [options.supplierId] - Filter by specific supplier
 * @returns {Promise<Object|null>} Price object with material and supplier details, or null if not found
 * @throws {DatabaseError} When database connection fails
 * @throws {ValidationError} When materialId format is invalid
 * 
 * @example
 * // Get latest price for cement
 * const price = await getLatestPrice('mat_001');
 * console.log(price.price); // 145.50
 * 
 * @example
 * // Get latest price for specific region
 * const price = await getLatestPrice('mat_001', { region: 'Gauteng' });
 * 
 * @since 1.0.0
 * @see {@link PriceService}
 */
async function getLatestPrice(materialId, options = {}) {
  // Validate input
  if (!isValidMaterialId(materialId)) {
    throw new ValidationError('Invalid material ID format');
  }

  // Check cache first
  const cacheKey = `price:latest:${materialId}:${options.region || 'all'}`;
  const cached = await cache.get(cacheKey);
  if (cached) return cached;

  // Query database
  const query = `
    SELECT p.*, m.name as material_name, s.name as supplier_name
    FROM prices p
    JOIN materials m ON p.material_id = m.id
    JOIN suppliers s ON p.supplier_id = s.id
    WHERE p.material_id = $1
      AND ($2::text IS NULL OR p.region = $2)
    ORDER BY p.date DESC
    LIMIT 1
  `;

  const result = await db.query(query, [materialId, options.region]);
  const price = result.rows[0] || null;

  // Cache result
  if (price) {
    await cache.set(cacheKey, price, 3600); // 1 hour
  }

  return price;
}
```

#### Class Documentation Template

```javascript
/**
 * Service for managing material price data and forecasts.
 * 
 * This service provides methods for retrieving, comparing, and forecasting
 * material prices. It integrates with the database, cache layer, and ML
 * inference services to provide efficient price intelligence.
 * 
 * @class PriceService
 * @implements {IPriceService}
 * 
 * @example
 * const priceService = new PriceService(db, cache, mlService);
 * const prices = await priceService.comparePrices('mat_001', { region: 'Gauteng' });
 */
class PriceService {
  /**
   * Creates a new PriceService instance.
   * 
   * @param {Database} db - Database connection instance
   * @param {Cache} cache - Redis cache instance
   * @param {MLService} mlService - Machine learning service instance
   * @throws {Error} If required dependencies are not provided
   */
  constructor(db, cache, mlService) {
    if (!db || !cache || !mlService) {
      throw new Error('All dependencies (db, cache, mlService) are required');
    }
    this.db = db;
    this.cache = cache;
    this.mlService = mlService;
  }

  /**
   * Compares prices for a material across multiple suppliers.
   * 
   * @param {string} materialId - Material identifier
   * @param {Object} options - Comparison options
   * @param {string} [options.region] - Region filter
   * @param {string} [options.sortBy='price'] - Sort field (price|distance|rating)
   * @returns {Promise<Array<Object>>} Array of price comparison results
   * 
   * @private Internal method not exposed via API
   */
  async comparePrices(materialId, options = {}) {
    // Implementation
  }
}
```

#### Module Documentation Template

```javascript
/**
 * @fileoverview Validation utilities for user input and API requests.
 * 
 * This module provides a comprehensive set of validation functions for
 * ensuring data integrity across the application. All validators follow
 * a consistent interface and provide detailed error messages.
 * 
 * @module utils/validators
 * @requires express-validator
 * @requires ../config/constants
 * 
 * @author Backend Team
 * @version 1.0.0
 * @since 2026-03-01
 */

const { body, param, query } = require('express-validator');
const { VALID_CATEGORIES, VALID_REGIONS } = require('../config/constants');

// Validator functions...
```

#### Inline Comments

```javascript
// Good: Explains WHY, not WHAT
// Use XGBoost for forecasting because it handles non-linear relationships
// better than ARIMA (59% improvement per research)
const model = await mlService.loadModel('xgboost');

// Bad: States the obvious
// Load the XGBoost model
const model = await mlService.loadModel('xgboost');

// Good: Explains complex logic
// Cache warming: Pre-load popular materials to improve initial response time
// Materials with >100 searches/day are warmed every 6 hours
await cacheWarmer.warmPopularMaterials();

// Good: Documents workaround
// HACK: Temporary fix for Supabase connection pool exhaustion
// TODO: Replace with proper connection pooling in v2.0
// See issue #123
const connection = await db.getConnection({ forceNew: true });
```

---

### Python (Google Style Docstrings)

#### Function Documentation Template

```python
def forecast_price(material_id: str, horizon: int = 7, model: str = 'lstm') -> dict:
    """
    Generate price forecast for a material using machine learning models.
    
    This function loads the specified trained model and generates price
    predictions for the given forecast horizon. Results include confidence
    intervals and purchase recommendations.
    
    Args:
        material_id: Unique identifier for the material (e.g., 'mat_001').
        horizon: Number of days to forecast (7 or 30). Defaults to 7.
        model: Model name to use for forecasting. One of: 'lstm', 'prophet',
            'xgboost', 'ensemble'. Defaults to 'lstm'.
    
    Returns:
        Dictionary containing forecast results:
            {
                'forecast': List[dict],  # Daily predictions
                'confidence': float,     # Overall confidence (0-1)
                'recommendation': dict,  # Purchase recommendation
                'model_metadata': dict   # Model info and metrics
            }
    
    Raises:
        ValueError: If material_id is not found or horizon is invalid.
        ModelNotFoundError: If specified model is not available.
        InferenceError: If model inference fails.
    
    Examples:
        >>> forecast = forecast_price('mat_001', horizon=30, model='ensemble')
        >>> print(forecast['forecast'][0])
        {'date': '2026-03-16', 'price': 146.20, 'confidence': 0.85}
        
        >>> # Use LSTM for single material
        >>> forecast = forecast_price('mat_002', model='lstm')
    
    Note:
        This function uses cached predictions when available (6-hour TTL).
        For real-time predictions, use the force_refresh parameter.
    
    See Also:
        train_model: Train a new forecasting model
        evaluate_model: Evaluate model performance
    
    References:
        [1] Lyu et al. (2025), "LSTM for Construction Material Price Forecasting"
    
    Version:
        Added in v1.0.0
        Modified in v1.2.0 to add ensemble model support
    """
    # Validate inputs
    if horizon not in [7, 30]:
        raise ValueError(f'Invalid horizon: {horizon}. Must be 7 or 30.')
    
    # Load model
    try:
        ml_model = load_model(model, material_id)
    except FileNotFoundError:
        raise ModelNotFoundError(f'Model {model} not found for {material_id}')
    
    # Generate forecast
    predictions = ml_model.predict(horizon=horizon)
    
    # Calculate confidence intervals
    confidence = calculate_confidence(predictions)
    
    # Generate recommendation
    recommendation = generate_recommendation(predictions)
    
    return {
        'forecast': predictions,
        'confidence': confidence,
        'recommendation': recommendation,
        'model_metadata': ml_model.metadata
    }
```

#### Class Documentation Template

```python
class PriceForecastModel:
    """
    LSTM-based model for building material price forecasting.
    
    This class implements a Long Short-Term Memory (LSTM) neural network
    for time-series price forecasting. The model is trained on historical
    price data and can generate multi-day ahead predictions.
    
    Attributes:
        model_name: Name of the model (e.g., 'lstm_cement_v1').
        input_window: Number of historical days used for prediction (default: 60).
        hidden_units: Number of LSTM hidden units (default: 100).
        dropout_rate: Dropout rate for regularization (default: 0.2).
        trained: Whether the model has been trained.
        metrics: Dictionary of evaluation metrics (MAE, RMSE, MAPE).
    
    Example:
        >>> model = PriceForecastModel(model_name='lstm_cement_v1')
        >>> model.train(train_data, epochs=50, batch_size=32)
        >>> forecast = model.predict(horizon=7)
        >>> print(f'MAE: {model.metrics["mae"]:.2f}')
    
    Note:
        Model training requires GPU for reasonable performance. Use Kaggle
        notebooks for training in development.
    
    See Also:
        ProphetModel: Alternative forecasting model using Facebook Prophet
        XGBoostModel: Gradient boosting model for price forecasting
    """
    
    def __init__(self, model_name: str, input_window: int = 60, 
                 hidden_units: int = 100, dropout_rate: float = 0.2):
        """
        Initialize LSTM forecasting model.
        
        Args:
            model_name: Unique identifier for this model instance.
            input_window: Number of historical days to use as input.
            hidden_units: Number of LSTM layer hidden units.
            dropout_rate: Dropout rate for preventing overfitting.
        """
        self.model_name = model_name
        self.input_window = input_window
        self.hidden_units = hidden_units
        self.dropout_rate = dropout_rate
        self.trained = False
        self.metrics = {}
```

#### Module Documentation Template

```python
"""
Machine Learning module for price forecasting.

This module provides forecasting models (LSTM, Prophet, XGBoost, Random Forest)
for predicting building material prices. Models are trained on historical price
data and can generate 7-day or 30-day ahead forecasts with confidence intervals.

The module includes:
    - Model training and evaluation utilities
    - Feature engineering functions
    - Prediction generation
    - Model versioning and registry

Typical usage example:

    from ml.forecasting import PriceForecastModel
    
    model = PriceForecastModel('lstm_cement_v1')
    model.train(historical_data)
    forecast = model.predict(horizon=7)

Author: ML Team
Version: 1.0.0
Date: 2026-03-01
"""

import numpy as np
import pandas as pd
from tensorflow import keras
```

---

### Dart (Flutter Documentation Comments)

#### Function Documentation Template

```dart
/// Fetches price data for a specific material from the API.
///
/// This function makes an authenticated HTTP GET request to retrieve
/// the latest price information for the given material. Results are
/// cached locally using Hive for offline access.
///
/// **Parameters:**
/// - [materialId]: Unique identifier of the material (e.g., 'mat_001')
/// - [region]: Optional region filter for regional pricing
///
/// **Returns:**
/// A [Future] that completes with a [Material] object containing
/// price data, or throws an exception if the request fails.
///
/// **Throws:**
/// - [ApiException] if the API request fails
/// - [AuthenticationException] if the user is not authenticated
/// - [NetworkException] if there's no internet connection
///
/// **Example:**
/// ```dart
/// try {
///   final material = await fetchMaterial('mat_001', region: 'Gauteng');
///   print('Price: R${material.price}');
/// } catch (e) {
///   print('Error: $e');
/// }
/// ```
///
/// **See also:**
/// - [Material] for the returned data structure
/// - [ApiService] for the underlying HTTP client
Future<Material> fetchMaterial(String materialId, {String? region}) async {
  // Validate input
  if (materialId.isEmpty) {
    throw ArgumentError('materialId cannot be empty');
  }

  // Check cache first (offline support)
  final cached = await _hiveBox.get(materialId);
  if (cached != null && !_isStale(cached)) {
    return Material.fromJson(cached);
  }

  // Fetch from API
  try {
    final response = await _apiClient.get(
      '/materials/$materialId',
      queryParameters: {'region': region},
    );

    final material = Material.fromJson(response.data);

    // Cache for offline access
    await _hiveBox.put(materialId, material.toJson());

    return material;
  } on DioError catch (e) {
    if (e.response?.statusCode == 404) {
      throw ApiException('Material not found');
    }
    throw NetworkException('Failed to fetch material: ${e.message}');
  }
}
```

#### Class Documentation Template

```dart
/// Service for managing material price data in the mobile app.
///
/// This service provides methods for fetching, caching, and synchronizing
/// material price data between the API and local storage. It handles
/// offline scenarios gracefully and provides background sync capabilities.
///
/// **Features:**
/// - Automatic caching with Hive database
/// - Offline-first data access
/// - Background synchronization
/// - Conflict resolution (server wins)
///
/// **Usage:**
/// ```dart
/// final priceService = PriceService(apiClient: dio, hiveBox: box);
/// 
/// // Fetch with automatic caching
/// final material = await priceService.fetchMaterial('mat_001');
/// 
/// // Force refresh from server
/// final updated = await priceService.fetchMaterial('mat_001', refresh: true);
/// ```
///
/// **See also:**
/// - [ApiService] for HTTP communication
/// - [SyncService] for background synchronization
class PriceService {
  /// Creates a new PriceService instance.
  ///
  /// **Parameters:**
  /// - [apiClient]: Dio client for HTTP requests
  /// - [hiveBox]: Hive box for local storage
  ///
  /// **Throws:**
  /// [ArgumentError] if required parameters are null
  PriceService({
    required this.apiClient,
    required this.hiveBox,
  }) {
    if (apiClient == null || hiveBox == null) {
      throw ArgumentError('apiClient and hiveBox are required');
    }
  }

  /// Dio HTTP client instance
  final Dio apiClient;

  /// Hive box for local data storage
  final Box hiveBox;

  /// Cache validity duration (1 hour)
  static const Duration _cacheValidity = Duration(hours: 1);

  // Methods...
}
```

---

## 📖 README Standards

### Repository README Template

```markdown
# Project Name

Brief one-sentence description of what the project does.

## Overview

2-3 paragraph overview explaining:
- What problem does this solve?
- Who is the target audience?
- What are the key features?

## Features

- 🎯 Feature 1 with emoji
- 🚀 Feature 2 with emoji
- 💡 Feature 3 with emoji

## Quick Start

### Prerequisites

- Requirement 1
- Requirement 2
- Requirement 3

### Installation

\`\`\`bash
# Step 1
command-here

# Step 2
another-command
\`\`\`

### Usage

\`\`\`bash
# Basic usage example
npm start
\`\`\`

## Documentation

- [API Documentation](./docs/api/README.md)
- [Architecture](./docs/architecture/SYSTEM_ARCHITECTURE.md)
- [Deployment Guide](./DEPLOYMENT.md)

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md)

## License

See [LICENSE](./LICENSE)
```

### Module README Template

```markdown
# Module Name

## Purpose

What does this module do and why?

## Structure

\`\`\`
module/
├── file1.js          # Description
├── file2.js          # Description
└── subfolder/
    └── file3.js      # Description
\`\`\`

## Usage

### Import

\`\`\`javascript
const module = require('./module');
\`\`\`

### Example

\`\`\`javascript
// Example code here
\`\`\`

## API Reference

### `functionName(param1, param2)`

Description of what function does.

**Parameters:**
- `param1` (Type): Description
- `param2` (Type): Description

**Returns:** Description

## Testing

\`\`\`bash
npm test
\`\`\`

## Dependencies

- dependency1: Why it's needed
- dependency2: Why it's needed
```

---

## 🚫 Anti-Patterns

### What NOT to Do

```javascript
// ❌ BAD: Obvious comment
// Increment i by 1
i++;

// ❌ BAD: Commented-out code
// const oldFunction = () => {
//   // old implementation
// };

// ❌ BAD: Vague documentation
/**
 * Does something with data
 */
function processData(data) { }

// ❌ BAD: No examples
/**
 * @param {Object} options - Configuration options
 */
function configure(options) { }

// ❌ BAD: Outdated documentation
/**
 * Returns a string (NOTE: This is outdated, now returns an object)
 */
function getData() {
  return { data: 'value' };
}
```

### What TO Do

```javascript
// ✅ GOOD: Explains non-obvious logic
// Use binary search because materials array is sorted by name
// O(log n) instead of O(n) for 1000+ materials
const index = binarySearch(materials, targetName);

// ✅ GOOD: Comprehensive documentation
/**
 * Configures the API client with authentication and rate limiting.
 * 
 * @param {Object} options - Configuration options
 * @param {string} options.apiKey - API authentication key
 * @param {number} [options.timeout=5000] - Request timeout in ms
 * @param {number} [options.maxRetries=3] - Max retry attempts
 * 
 * @example
 * configure({ 
 *   apiKey: 'abc123',
 *   timeout: 10000,
 *   maxRetries: 5
 * });
 */
function configure(options) { }

// ✅ GOOD: Explains business logic
// Apply 10% bulk discount for orders >50 units
// per pricing policy document (see PRICING.md)
if (quantity > 50) {
  price *= 0.9;
}
```

---

## 🔍 Documentation Review Checklist

### For Code Authors

- [ ] All public functions/methods documented
- [ ] Parameters and return values described
- [ ] Exceptions/errors documented
- [ ] Examples provided for complex functionality
- [ ] Edge cases and limitations noted
- [ ] Related functions cross-referenced
- [ ] Updated existing docs if behavior changed
- [ ] No typos or grammatical errors

### For Code Reviewers

- [ ] Documentation is accurate
- [ ] Examples work correctly
- [ ] Explanations are clear
- [ ] Follows language conventions
- [ ] No outdated information
- [ ] Links and references valid

---

## 📚 Additional Resources

- [JSDoc Documentation](https://jsdoc.app/)
- [TSDoc Specification](https://tsdoc.org/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- [Effective Dart: Documentation](https://dart.dev/guides/language/effective-dart/documentation)
- [Write the Docs](https://www.writethedocs.org/)

---

**Last Updated:** March 9, 2026  
**Version:** 1.0  
**Next Review:** Post-MVP (June 2026)
