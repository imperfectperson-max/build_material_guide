# 🤖 Machine Learning Engineer - Detailed Roadmap

**Team Member:** Member 2  
**Primary Tech Stack:** Python, Scikit-learn, XGBoost, Prophet, TensorFlow/Keras, Pandas  
**Timeline:** March 9 - June 9, 2026 (13 weeks)

---

## 🚀 Getting Started - March 8, 2026

**Complete this comprehensive setup before Week 1 begins on March 9!**

### System Requirements
- **Operating System:** Windows 10/11, macOS 10.15+, or Ubuntu 20.04+
- **RAM:** Minimum 8GB (16GB recommended for deep learning)
- **Storage:** At least 20GB free space for datasets and models
- **GPU:** Optional but recommended for LSTM training (or use Kaggle)

### Step 1: Install Python and Conda
```bash
# Download and install Anaconda or Miniconda
# Visit: https://www.anaconda.com/download or https://docs.conda.io/en/latest/miniconda.html

# Verify installation
python --version  # Should show Python 3.9+ or 3.10+
conda --version   # Should show conda 4.x or higher
```

### Step 2: Create Virtual Environment
```bash
# Create a new conda environment
conda create -n buildmat python=3.10 -y
conda activate buildmat

# Verify environment
which python  # Should point to your conda environment
```

### Step 3: Install Core ML Libraries
```bash
# Data manipulation and analysis
pip install pandas numpy scipy

# Visualization
pip install matplotlib seaborn plotly

# Scientific computing
pip install scikit-learn statsmodels

# Jupyter for notebooks
pip install jupyter jupyterlab ipykernel

# Add kernel to Jupyter
python -m ipykernel install --user --name=buildmat --display-name="BuildMat ML"
```

### Step 4: Install Machine Learning Frameworks
```bash
# Time series forecasting
pip install pmdarima prophet

# Gradient boosting
pip install xgboost lightgbm catboost

# Deep learning (TensorFlow)
pip install tensorflow

# OR install PyTorch (alternative to TensorFlow)
# pip install torch torchvision torchaudio

# Verify TensorFlow installation
python -c "import tensorflow as tf; print('TensorFlow version:', tf.__version__); print('GPU Available:', tf.config.list_physical_devices('GPU'))"
```

### Step 5: Install Additional Tools
```bash
# Model evaluation and utilities
pip install optuna  # Hyperparameter tuning
pip install mlflow  # Experiment tracking
pip install shap    # Model explainability

# File handling
pip install openpyxl xlrd

# Progress bars
pip install tqdm

# Save requirements
pip freeze > requirements.txt
```

### Step 6: Set Up Kaggle Account and API

**Why Kaggle?** You'll use Kaggle's free GPU resources for training LSTM and deep learning models.

#### 6.1: Create Kaggle Account
```bash
# 1. Go to https://www.kaggle.com/
# 2. Click "Register" and create your account
# 3. Verify your email address
# 4. Complete your profile (required for API access)
```

#### 6.2: Get Kaggle API Credentials
```bash
# 1. Go to https://www.kaggle.com/settings/account
# 2. Scroll to "API" section
# 3. Click "Create New Token"
# 4. This downloads kaggle.json to your computer
```

#### 6.3: Install and Configure Kaggle CLI
```bash
# Install Kaggle API
pip install kaggle

# Place API credentials in correct location
# For macOS/Linux:
mkdir -p ~/.kaggle
mv ~/Downloads/kaggle.json ~/.kaggle/
chmod 600 ~/.kaggle/kaggle.json

# For Windows (PowerShell):
# mkdir $env:USERPROFILE\.kaggle
# Move-Item $env:USERPROFILE\Downloads\kaggle.json $env:USERPROFILE\.kaggle\kaggle.json

# Verify Kaggle CLI works
kaggle --version
kaggle datasets list
```

#### 6.4: What You'll Do on Kaggle

**Throughout the project, you'll use Kaggle for:**

1. **GPU Training (Weeks 5-6):** Train LSTM models using free GPU resources
   ```python
   # In Kaggle notebook, check GPU availability:
   import tensorflow as tf
   print("Num GPUs Available: ", len(tf.config.list_physical_devices('GPU')))
   ```

2. **Dataset Hosting:** Upload and version your price datasets
   ```bash
   # Create a dataset on Kaggle
   kaggle datasets init -p /path/to/data
   # Edit dataset-metadata.json
   kaggle datasets create -p /path/to/data
   
   # Update existing dataset
   kaggle datasets version -p /path/to/data -m "Updated with new price data"
   ```

3. **Notebook Development:** Create Kaggle notebooks for model training
   ```bash
   # Download your notebook
   kaggle kernels pull your-username/notebook-name
   
   # Push updated notebook
   kaggle kernels push -p /path/to/notebook
   ```

4. **Model Sharing:** Save and download trained models
   ```python
   # In Kaggle notebook - save model
   model.save('/kaggle/working/lstm_model.h5')
   
   # Download from Kaggle Output:
   # Go to your notebook > Output tab > Download
   ```

#### 6.5: Create Your First Kaggle Notebook
```bash
# 1. Go to https://www.kaggle.com/code
# 2. Click "New Notebook"
# 3. Choose "Notebook" (not Script)
# 4. Select "GPU" under Settings > Accelerator
# 5. Title it: "BuildMat Price Forecasting - LSTM"
# 6. Save and run a test cell:
```

```python
# Test cell for your Kaggle notebook
import numpy as np
import pandas as pd
import tensorflow as tf

print("✅ NumPy version:", np.__version__)
print("✅ Pandas version:", pd.__version__)
print("✅ TensorFlow version:", tf.__version__)
print("✅ GPU available:", len(tf.config.list_physical_devices('GPU')) > 0)

# Test GPU with simple computation
if tf.config.list_physical_devices('GPU'):
    with tf.device('/GPU:0'):
        a = tf.constant([[1.0, 2.0, 3.0], [4.0, 5.0, 6.0]])
        b = tf.constant([[1.0, 2.0], [3.0, 4.0], [5.0, 6.0]])
        c = tf.matmul(a, b)
        print("✅ GPU computation successful!")
        print(c)
else:
    print("⚠️ No GPU found - make sure GPU accelerator is enabled in Settings")
```

### Step 7: Set Up Project Structure
```bash
# Create project directory
mkdir -p ~/projects/buildmat-ml
cd ~/projects/buildmat-ml

# Create comprehensive folder structure
mkdir -p data/{raw,processed,external,features}
mkdir -p notebooks/{exploratory,modeling,evaluation}
mkdir -p src/{data,features,models,evaluation,visualization}
mkdir -p models/{baseline,advanced,ensemble,production}
mkdir -p reports/{figures,metrics,presentations}
mkdir -p tests
mkdir -p configs
mkdir -p scripts

# Create README files
touch data/README.md
touch notebooks/README.md
touch models/README.md
```

### Step 8: Create Requirements File
```bash
# Save all dependencies
cat > requirements.txt << 'EOF'
# Core Data Science
pandas==2.0.3
numpy==1.24.3
scipy==1.11.1

# Visualization
matplotlib==3.7.2
seaborn==0.12.2
plotly==5.15.0

# Machine Learning
scikit-learn==1.3.0
statsmodels==0.14.0
pmdarima==2.0.3
prophet==1.1.4

# Gradient Boosting
xgboost==1.7.6
lightgbm==4.0.0
catboost==1.2

# Deep Learning
tensorflow==2.13.0

# Utilities
jupyter==1.0.0
jupyterlab==4.0.3
ipykernel==6.25.0
tqdm==4.65.0
openpyxl==3.1.2

# MLOps
mlflow==2.5.0
optuna==3.2.0
shap==0.42.1

# Kaggle
kaggle==1.5.16
EOF

# Install from requirements
pip install -r requirements.txt
```

### Step 9: Set Up Git and Version Control
```bash
# Initialize git
git init
git config user.name "Your Name"
git config user.email "your.email@example.com"

# Create .gitignore
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Jupyter Notebook
.ipynb_checkpoints
*/.ipynb_checkpoints/*

# Environment
.env
.venv
buildmat/

# Kaggle
kaggle.json

# Data (large files)
data/raw/*
!data/raw/.gitkeep
data/processed/*
!data/processed/.gitkeep

# Models (large files)
models/*.h5
models/*.pkl
models/*.joblib
*.h5
*.pkl

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# MLflow
mlruns/
mlartifacts/
EOF

# Create .gitkeep files
touch data/raw/.gitkeep
touch data/processed/.gitkeep
touch models/.gitkeep
```

### Step 10: Create Initial Notebook Template
```bash
# Create starter notebook
cat > notebooks/00_environment_test.ipynb << 'EOF'
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Environment Setup Test\n",
    "Run this notebook to verify all dependencies are installed correctly."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "source": [
    "import sys\n",
    "print(f\"Python version: {sys.version}\")\n",
    "\n",
    "# Test imports\n",
    "import pandas as pd\n",
    "import numpy as np\n",
    "import matplotlib.pyplot as plt\n",
    "import seaborn as sns\n",
    "from sklearn import __version__ as sklearn_version\n",
    "import statsmodels.api as sm\n",
    "import xgboost as xgb\n",
    "from prophet import Prophet\n",
    "import tensorflow as tf\n",
    "\n",
    "print(\"✅ All imports successful!\")\n",
    "print(f\"Pandas: {pd.__version__}\")\n",
    "print(f\"NumPy: {np.__version__}\")\n",
    "print(f\"Scikit-learn: {sklearn_version}\")\n",
    "print(f\"XGBoost: {xgb.__version__}\")\n",
    "print(f\"TensorFlow: {tf.__version__}\")\n",
    "print(f\"GPU Available: {len(tf.config.list_physical_devices('GPU')) > 0}\")"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "BuildMat ML",
   "language": "python",
   "name": "buildmat"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
EOF
```

### Step 11: Test Your Setup
```bash
# Activate environment
conda activate buildmat

# Start Jupyter Lab
jupyter lab

# Open and run notebooks/00_environment_test.ipynb
# All cells should run without errors

# Test Kaggle CLI
kaggle datasets list --sort-by votes

# Expected: List of popular Kaggle datasets
```

### Step 12: Download Sample Data for Testing
```bash
# Create a simple test dataset
python << 'PYTHON_SCRIPT'
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

# Generate synthetic cement prices for testing
np.random.seed(42)
dates = pd.date_range(start='2024-01-01', end='2026-03-01', freq='D')
base_price = 85.50
trend = np.linspace(0, 15, len(dates))
seasonality = 5 * np.sin(2 * np.pi * np.arange(len(dates)) / 365)
noise = np.random.normal(0, 3, len(dates))
prices = base_price + trend + seasonality + noise

df = pd.DataFrame({
    'date': dates,
    'material': 'Cement 50kg',
    'price': prices.clip(min=50),
    'supplier': 'Test Supplier',
    'region': 'Gauteng'
})

df.to_csv('data/raw/test_cement_prices.csv', index=False)
print(f"✅ Created test dataset: {len(df)} records")
print(f"   Date range: {df['date'].min()} to {df['date'].max()}")
print(f"   Price range: R{df['price'].min():.2f} to R{df['price'].max():.2f}")
PYTHON_SCRIPT
```

### Checklist - Confirm Everything Works ✅
- [ ] Python 3.9+ and Conda installed
- [ ] Virtual environment created and activated
- [ ] All Python packages installed successfully
- [ ] Jupyter Lab launches and runs notebooks
- [ ] TensorFlow recognizes GPU (or Kaggle GPU tested)
- [ ] Kaggle account created and verified
- [ ] Kaggle API credentials configured
- [ ] Kaggle CLI works (can list datasets)
- [ ] First Kaggle notebook created with GPU enabled
- [ ] Project directory structure created
- [ ] Git initialized with proper .gitignore
- [ ] Test dataset created successfully
- [ ] requirements.txt saved

### Troubleshooting Common Issues

**Issue: Conda command not found**
```bash
# Add conda to PATH
# For macOS/Linux, add to ~/.bashrc or ~/.zshrc:
export PATH="/opt/anaconda3/bin:$PATH"
source ~/.bashrc

# For Windows, run Anaconda Prompt as administrator
```

**Issue: TensorFlow GPU not detected**
```bash
# Install CUDA and cuDNN (if you have NVIDIA GPU)
# OR use Kaggle's free GPU instead!

# Verify TensorFlow sees GPU
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
```

**Issue: Kaggle API authentication error**
```bash
# Verify kaggle.json is in correct location
# macOS/Linux: ~/.kaggle/kaggle.json
# Windows: C:\Users\<Windows-username>\.kaggle\kaggle.json

# Check file permissions
chmod 600 ~/.kaggle/kaggle.json
```

**Issue: Prophet installation fails**
```bash
# Prophet requires additional dependencies
# For macOS:
conda install -c conda-forge prophet

# For Linux:
sudo apt-get install python3-dev
pip install prophet

# For Windows:
conda install -c conda-forge prophet
```

**Issue: Memory errors with large datasets**
```bash
# Use chunking for large files
df = pd.read_csv('large_file.csv', chunksize=10000)
for chunk in df:
    process(chunk)

# OR use Dask for out-of-core computation
pip install dask
import dask.dataframe as dd
df = dd.read_csv('large_file.csv')
```

### Kaggle-Specific Tips 💡

1. **GPU Hours Limit:** Kaggle provides 30 hours/week of free GPU. Monitor usage in Settings.

2. **Data Upload Methods:**
   ```bash
   # Method 1: Direct upload via web interface
   # Go to Datasets > New Dataset > Upload files
   
   # Method 2: Via Kaggle API
   kaggle datasets create -p /path/to/data
   ```

3. **Saving Work from Kaggle:**
   ```python
   # In notebook, save to /kaggle/working/
   model.save('/kaggle/working/model.h5')
   df.to_csv('/kaggle/working/results.csv')
   
   # Then download from Output tab
   ```

4. **Using External Data in Kaggle:**
   ```python
   # Install additional packages in notebook
   !pip install custom-package
   
   # Import data from other Kaggle datasets
   import os
   for dirname, _, filenames in os.walk('/kaggle/input'):
       for filename in filenames:
           print(os.path.join(dirname, filename))
   ```

5. **Notebook Best Practices:**
   - Save versions frequently (File > Save Version)
   - Use clear section headers with markdown
   - Comment your code thoroughly
   - Keep notebooks under 2 hours runtime

### Ready for Week 1! 🎉
You're now fully set up with both local development and Kaggle cloud resources. Week 1 begins Monday, March 9, 2026.

---

## 🎯 Role Overview

As the Machine Learning Engineer, you are responsible for developing and evaluating predictive models that form the intelligence core of the platform. Your work must demonstrate a **59% improvement over traditional ARIMA forecasting**.

**Core Responsibilities:**
- Data collection and exploratory data analysis
- Feature engineering for time series prediction
- Model development (ARIMA, XGBoost, Random Forest, Prophet, LSTM)
- Model evaluation and comparison
- Anomaly detection algorithms
- Model deployment and monitoring

---

## 📅 WEEK 1: March 9-15, 2026
### Theme: Data Collection & Research

#### Monday (March 9)
**Morning (2-3 hours):**
1. Set up development environment:
   ```bash
   conda create -n buildmat python=3.9
   conda activate buildmat
   pip install pandas numpy scikit-learn matplotlib seaborn jupyter
   pip install statsmodels pmdarima xgboost prophet tensorflow
   ```

2. Create project structure:
   ```
   ml-models/
   ├── data/
   │   ├── raw/
   │   ├── processed/
   │   └── external/
   ├── notebooks/
   │   ├── 01_eda.ipynb
   │   ├── 02_feature_engineering.ipynb
   │   └── 03_modeling.ipynb
   ├── src/
   │   ├── data/
   │   ├── features/
   │   ├── models/
   │   └── evaluation/
   ├── models/
   └── requirements.txt
   ```

**Afternoon (2-3 hours):**
1. Research South African construction price data sources:
   - Statistics South Africa (Stats SA) website
   - Construction Industry Development Board (CIDB)
   - Building cost indices
   
2. Download historical data:
   ```python
   # Example: Stats SA construction material prices
   import pandas as pd
   
   # Download from Stats SA API or manual download
   # Target: Get at least 2 years of monthly data
   ```

3. Document data sources in `data/README.md`

**Deliverable:** Environment setup + initial data sources identified

#### Tuesday (March 10)
**Morning (2-3 hours):**
1. Create data collection notebook (`01_data_collection.ipynb`):
   ```python
   import pandas as pd
   import numpy as np
   from datetime import datetime, timedelta
   
   # Load Stats SA data
   stats_sa = pd.read_csv('data/external/stats_sa_construction.csv')
   
   # Explore structure
   print(stats_sa.info())
   print(stats_sa.head())
   print(stats_sa.describe())
   ```

2. Clean and standardize external data:
   ```python
   # Standardize column names
   stats_sa.columns = stats_sa.columns.str.lower().str.replace(' ', '_')
   
   # Handle missing values
   print("Missing values:")
   print(stats_sa.isnull().sum())
   
   # Convert dates
   stats_sa['date'] = pd.to_datetime(stats_sa['date'])
   
   # Sort by date
   stats_sa = stats_sa.sort_values('date')
   ```

**Afternoon (2-3 hours):**
1. Create synthetic/augmented data for materials not in Stats SA:
   ```python
   # Generate realistic price trends based on known materials
   def generate_synthetic_prices(base_price, start_date, periods, volatility=0.05):
       dates = pd.date_range(start=start_date, periods=periods, freq='D')
       
       # Random walk with trend
       trend = np.linspace(0, 0.1 * base_price, periods)
       noise = np.random.normal(0, volatility * base_price, periods)
       prices = base_price + trend + noise
       
       return pd.DataFrame({
           'date': dates,
           'price': prices.clip(min=base_price * 0.5)  # No negative prices
       })
   
   # Example: Generate cement prices
   cement_prices = generate_synthetic_prices(
       base_price=85.50,  # R85.50 per 50kg bag
       start_date='2024-01-01',
       periods=365,
       volatility=0.08
   )
   ```

2. Save processed data:
   ```python
   # Save to processed folder
   stats_sa.to_csv('data/processed/stats_sa_clean.csv', index=False)
   cement_prices.to_csv('data/processed/cement_synthetic.csv', index=False)
   ```

**Deliverable:** Clean historical dataset

#### Wednesday (March 11)
**Full Day (4-5 hours):**
1. Begin Exploratory Data Analysis (EDA):
   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   
   # Set plotting style
   sns.set_style("whitegrid")
   plt.rcParams['figure.figsize'] = (12, 6)
   
   # Load all processed data
   materials = {
       'cement': pd.read_csv('data/processed/cement_synthetic.csv'),
       'steel': pd.read_csv('data/processed/steel_synthetic.csv'),
       'timber': pd.read_csv('data/processed/timber_synthetic.csv')
   }
   
   # Time series plots for each material
   for material, df in materials.items():
       df['date'] = pd.to_datetime(df['date'])
       
       plt.figure()
       plt.plot(df['date'], df['price'])
       plt.title(f'{material.capitalize()} Price Trends')
       plt.xlabel('Date')
       plt.ylabel('Price (R)')
       plt.xticks(rotation=45)
       plt.tight_layout()
       plt.savefig(f'notebooks/figures/eda_{material}_trend.png')
       plt.close()
   ```

2. Statistical analysis:
   ```python
   # Calculate statistics
   for material, df in materials.items():
       print(f"\n{material.upper()} Statistics:")
       print(f"Mean: R{df['price'].mean():.2f}")
       print(f"Std Dev: R{df['price'].std():.2f}")
       print(f"Min: R{df['price'].min():.2f}")
       print(f"Max: R{df['price'].max():.2f}")
       print(f"CV: {(df['price'].std() / df['price'].mean() * 100):.2f}%")
       
       # Check for stationarity (Augmented Dickey-Fuller test)
       from statsmodels.tsa.stattools import adfuller
       
       result = adfuller(df['price'])
       print(f"ADF Statistic: {result[0]:.4f}")
       print(f"p-value: {result[1]:.4f}")
       print(f"Stationary: {'Yes' if result[1] < 0.05 else 'No'}")
   ```

**Deliverable:** EDA notebook with insights

#### Thursday (March 12)
**Morning (2-3 hours):**
1. Analyze seasonality and trends:
   ```python
   from statsmodels.tsa.seasonal import seasonal_decompose
   
   for material, df in materials.items():
       df = df.set_index('date')
       df = df.asfreq('D')  # Set daily frequency
       df = df.interpolate(method='linear')  # Fill gaps
       
       # Decompose time series
       decomposition = seasonal_decompose(
           df['price'], 
           model='additive', 
           period=30  # Monthly seasonality
       )
       
       # Plot components
       fig, axes = plt.subplots(4, 1, figsize=(12, 10))
       decomposition.observed.plot(ax=axes[0], title='Observed')
       decomposition.trend.plot(ax=axes[1], title='Trend')
       decomposition.seasonal.plot(ax=axes[2], title='Seasonal')
       decomposition.resid.plot(ax=axes[3], title='Residual')
       plt.tight_layout()
       plt.savefig(f'notebooks/figures/decomposition_{material}.png')
       plt.close()
   ```

**Afternoon (2-3 hours):**
1. Correlation analysis between materials:
   ```python
   # Merge all material prices by date
   merged_df = materials['cement'][['date', 'price']].rename(columns={'price': 'cement'})
   
   for material, df in materials.items():
       if material != 'cement':
           merged_df = merged_df.merge(
               df[['date', 'price']].rename(columns={'price': material}),
               on='date',
               how='outer'
           )
   
   # Calculate correlation matrix
   corr_matrix = merged_df.drop('date', axis=1).corr()
   
   # Heatmap
   plt.figure(figsize=(10, 8))
   sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', center=0)
   plt.title('Material Price Correlations')
   plt.tight_layout()
   plt.savefig('notebooks/figures/correlation_matrix.png')
   ```

2. Document findings in notebook

**Deliverable:** Seasonality and correlation analysis

#### Friday (March 13)
**Morning (2-3 hours):**
1. Create feature engineering functions:
   ```python
   # src/features/time_features.py
   import pandas as pd
   import numpy as np
   
   def create_time_features(df):
       """Create time-based features"""
       df = df.copy()
       df['date'] = pd.to_datetime(df['date'])
       
       # Extract date components
       df['year'] = df['date'].dt.year
       df['month'] = df['date'].dt.month
       df['day'] = df['date'].dt.day
       df['dayofweek'] = df['date'].dt.dayofweek
       df['quarter'] = df['date'].dt.quarter
       df['weekofyear'] = df['date'].dt.isocalendar().week
       
       # Cyclical encoding
       df['month_sin'] = np.sin(2 * np.pi * df['month'] / 12)
       df['month_cos'] = np.cos(2 * np.pi * df['month'] / 12)
       df['dayofweek_sin'] = np.sin(2 * np.pi * df['dayofweek'] / 7)
       df['dayofweek_cos'] = np.cos(2 * np.pi * df['dayofweek'] / 7)
       
       return df
   
   def create_lag_features(df, target_col='price', lags=[1, 7, 14, 30]):
       """Create lagged features"""
       df = df.copy()
       
       for lag in lags:
           df[f'{target_col}_lag_{lag}'] = df[target_col].shift(lag)
       
       return df
   
   def create_rolling_features(df, target_col='price', windows=[7, 14, 30]):
       """Create rolling statistics"""
       df = df.copy()
       
       for window in windows:
           df[f'{target_col}_rolling_mean_{window}'] = df[target_col].rolling(window).mean()
           df[f'{target_col}_rolling_std_{window}'] = df[target_col].rolling(window).std()
           df[f'{target_col}_rolling_min_{window}'] = df[target_col].rolling(window).min()
           df[f'{target_col}_rolling_max_{window}'] = df[target_col].rolling(window).max()
       
       return df
   ```

**Afternoon (2-3 hours):**
1. Apply feature engineering to one material:
   ```python
   # Test feature engineering
   cement_df = pd.read_csv('data/processed/cement_synthetic.csv')
   
   # Apply all feature engineering
   cement_features = create_time_features(cement_df)
   cement_features = create_lag_features(cement_features)
   cement_features = create_rolling_features(cement_features)
   
   # Drop NaN rows created by lag/rolling
   cement_features = cement_features.dropna()
   
   print(f"Original shape: {cement_df.shape}")
   print(f"Features shape: {cement_features.shape}")
   print(f"\nNew features:")
   print(cement_features.columns.tolist())
   
   # Save
   cement_features.to_csv('data/processed/cement_features.csv', index=False)
   ```

2. Visualize feature importance (correlation with target):
   ```python
   # Calculate correlation with price
   correlations = cement_features.corr()['price'].sort_values(ascending=False)
   
   plt.figure(figsize=(10, 12))
   correlations[1:21].plot(kind='barh')  # Top 20 features
   plt.title('Top 20 Features Correlated with Price')
   plt.xlabel('Correlation Coefficient')
   plt.tight_layout()
   plt.savefig('notebooks/figures/feature_correlations.png')
   ```

**Deliverable:** Feature engineering pipeline

#### Weekend (Optional - 2-3 hours)
1. Read research papers on price forecasting
2. Review ARIMA, Prophet, LSTM architectures
3. Prepare for baseline model development (Week 2)

---

## 📅 WEEK 2: March 16-22, 2026
### Theme: Baseline Models (Naïve, Moving Average, ARIMA)

#### Monday (March 16)
**Morning (2-3 hours):**
1. Set up model evaluation framework:
   ```python
   # src/evaluation/metrics.py
   from sklearn.metrics import mean_absolute_error, mean_squared_error
   import numpy as np
   
   def calculate_metrics(y_true, y_pred):
       """Calculate MAE, RMSE, MAPE"""
       mae = mean_absolute_error(y_true, y_pred)
       rmse = np.sqrt(mean_squared_error(y_true, y_pred))
       
       # MAPE (avoid division by zero)
       mask = y_true != 0
       mape = np.mean(np.abs((y_true[mask] - y_pred[mask]) / y_true[mask])) * 100
       
       return {
           'MAE': mae,
           'RMSE': rmse,
           'MAPE': mape
       }
   
   def print_metrics(metrics, model_name):
       """Pretty print metrics"""
       print(f"\n{model_name} Performance:")
       print(f"  MAE:  {metrics['MAE']:.4f}")
       print(f"  RMSE: {metrics['RMSE']:.4f}")
       print(f"  MAPE: {metrics['MAPE']:.2f}%")
   ```

2. Create train-test split function:
   ```python
   # src/data/splitting.py
   def time_series_split(df, test_days=30):
       """Split time series data maintaining temporal order"""
       df = df.sort_values('date')
       
       split_date = df['date'].max() - pd.Timedelta(days=test_days)
       
       train = df[df['date'] <= split_date].copy()
       test = df[df['date'] > split_date].copy()
       
       print(f"Train: {train['date'].min()} to {train['date'].max()} ({len(train)} samples)")
       print(f"Test:  {test['date'].min()} to {test['date'].max()} ({len(test)} samples)")
       
       return train, test
   ```

**Afternoon (2-3 hours):**
1. Implement Naïve Forecast baseline:
   ```python
   # src/models/naive.py
   class NaiveForecast:
       """Persistence model: tomorrow's price = today's price"""
       
       def __init__(self):
           self.last_value = None
       
       def fit(self, y_train):
           self.last_value = y_train.iloc[-1]
           return self
       
       def predict(self, steps):
           return np.array([self.last_value] * steps)
   
   # Test on cement data
   cement_df = pd.read_csv('data/processed/cement_synthetic.csv')
   train, test = time_series_split(cement_df, test_days=30)
   
   # Fit and predict
   naive_model = NaiveForecast()
   naive_model.fit(train['price'])
   naive_pred = naive_model.predict(len(test))
   
   # Evaluate
   naive_metrics = calculate_metrics(test['price'].values, naive_pred)
   print_metrics(naive_metrics, "Naïve Forecast")
   ```

**Deliverable:** Evaluation framework + Naïve baseline

#### Tuesday (March 17)
**Morning (2-3 hours):**
1. Implement Moving Average model:
   ```python
   # src/models/moving_average.py
   class MovingAverageModel:
       """Simple Moving Average forecaster"""
       
       def __init__(self, window=7):
           self.window = window
           self.history = None
       
       def fit(self, y_train):
           self.history = list(y_train.values)
           return self
       
       def predict(self, steps):
           predictions = []
           
           for _ in range(steps):
               # Predict as average of last 'window' values
               pred = np.mean(self.history[-self.window:])
               predictions.append(pred)
               self.history.append(pred)  # Use prediction for next step
           
           return np.array(predictions)
   
   # Test different window sizes
   for window in [3, 7, 14, 30]:
       ma_model = MovingAverageModel(window=window)
       ma_model.fit(train['price'])
       ma_pred = ma_model.predict(len(test))
       
       ma_metrics = calculate_metrics(test['price'].values, ma_pred)
       print_metrics(ma_metrics, f"Moving Average (window={window})")
   ```

**Afternoon (2-3 hours):**
1. Research ARIMA parameters:
   ```python
   from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
   
   # ACF and PACF plots to determine p, q parameters
   fig, axes = plt.subplots(1, 2, figsize=(14, 4))
   
   plot_acf(train['price'], lags=30, ax=axes[0])
   axes[0].set_title('Autocorrelation Function (ACF)')
   
   plot_pacf(train['price'], lags=30, ax=axes[1])
   axes[1].set_title('Partial Autocorrelation Function (PACF)')
   
   plt.tight_layout()
   plt.savefig('notebooks/figures/acf_pacf.png')
   ```

2. Document ARIMA parameter selection process

**Deliverable:** Moving Average baseline

#### Wednesday (March 18)
**Full Day (4-5 hours):**
1. Implement ARIMA model:
   ```python
   # src/models/arima_model.py
   from statsmodels.tsa.arima.model import ARIMA
   import warnings
   warnings.filterwarnings('ignore')
   
   class ARIMAForecaster:
       """ARIMA forecasting model"""
       
       def __init__(self, order=(1, 1, 1)):
           self.order = order
           self.model = None
           self.fitted_model = None
       
       def fit(self, y_train):
           self.model = ARIMA(y_train, order=self.order)
           self.fitted_model = self.model.fit()
           return self
       
       def predict(self, steps):
           forecast = self.fitted_model.forecast(steps=steps)
           return forecast.values
       
       def get_params(self):
           return {
               'order': self.order,
               'aic': self.fitted_model.aic,
               'bic': self.fitted_model.bic
           }
   
   # Test ARIMA
   arima = ARIMAForecaster(order=(1, 1, 1))
   arima.fit(train['price'])
   arima_pred = arima.predict(len(test))
   
   arima_metrics = calculate_metrics(test['price'].values, arima_pred)
   print_metrics(arima_metrics, "ARIMA(1,1,1)")
   print(f"AIC: {arima.get_params()['aic']:.2f}")
   print(f"BIC: {arima.get_params()['bic']:.2f}")
   ```

2. Auto-tune ARIMA parameters:
   ```python
   # Grid search for best ARIMA parameters
   from itertools import product
   
   p_values = range(0, 3)
   d_values = range(0, 2)
   q_values = range(0, 3)
   
   best_aic = np.inf
   best_order = None
   best_model = None
   
   for p, d, q in product(p_values, d_values, q_values):
       try:
           model = ARIMA(train['price'], order=(p, d, q))
           fitted = model.fit()
           
           if fitted.aic < best_aic:
               best_aic = fitted.aic
               best_order = (p, d, q)
               best_model = fitted
               
               print(f"New best: ARIMA{best_order} - AIC: {best_aic:.2f}")
       except:
           continue
   
   print(f"\nBest ARIMA order: {best_order}")
   print(f"Best AIC: {best_aic:.2f}")
   
   # Evaluate best model
   best_pred = best_model.forecast(steps=len(test))
   best_metrics = calculate_metrics(test['price'].values, best_pred.values)
   print_metrics(best_metrics, f"ARIMA{best_order}")
   ```

**Deliverable:** Tuned ARIMA baseline model

#### Thursday (March 19)
**Morning (2-3 hours):**
1. Create model comparison framework:
   ```python
   # src/evaluation/comparison.py
   import pandas as pd
   
   class ModelComparison:
       """Track and compare model performance"""
       
       def __init__(self):
           self.results = []
       
       def add_result(self, model_name, metrics, params=None):
           result = {
               'model': model_name,
               **metrics,
               'params': params
           }
           self.results.append(result)
       
       def get_comparison_df(self):
           return pd.DataFrame(self.results).sort_values('MAPE')
       
       def plot_comparison(self, metric='MAPE'):
           df = self.get_comparison_df()
           
           plt.figure(figsize=(10, 6))
           plt.barh(df['model'], df[metric])
           plt.xlabel(metric)
           plt.title(f'Model Comparison by {metric}')
           plt.tight_layout()
           plt.savefig(f'notebooks/figures/model_comparison_{metric}.png')
   
   # Compare all baseline models
   comparison = ModelComparison()
   
   comparison.add_result('Naïve', naive_metrics)
   comparison.add_result('Moving Average (7)', ma_metrics)
   comparison.add_result(f'ARIMA{best_order}', best_metrics, best_order)
   
   print(comparison.get_comparison_df())
   comparison.plot_comparison('MAPE')
   comparison.plot_comparison('RMSE')
   ```

**Afternoon (2-3 hours):**
1. Visualize predictions vs actual:
   ```python
   # Plot all models
   plt.figure(figsize=(14, 6))
   
   plt.plot(test['date'], test['price'], label='Actual', linewidth=2, color='black')
   plt.plot(test['date'], naive_pred, label='Naïve', linestyle='--', alpha=0.7)
   plt.plot(test['date'], ma_pred, label='Moving Average', linestyle='--', alpha=0.7)
   plt.plot(test['date'], arima_pred, label=f'ARIMA{best_order}', linestyle='--', alpha=0.7)
   
   plt.xlabel('Date')
   plt.ylabel('Price (R)')
   plt.title('Model Predictions Comparison')
   plt.legend()
   plt.xticks(rotation=45)
   plt.tight_layout()
   plt.savefig('notebooks/figures/predictions_comparison.png')
   ```

**Deliverable:** Baseline model comparison

#### Friday (March 20)
**Full Day (4-5 hours):**
1. Apply baseline models to all materials
2. Create results summary report
3. Document baseline performance (this is the benchmark to beat!)
4. Save baseline results for later comparison:
   ```python
   # Save baseline results
   baseline_results = {
       'cement': {
           'naive': naive_metrics,
           'ma': ma_metrics,
           'arima': best_metrics
       }
   }
   
   import json
   with open('models/baseline_results.json', 'w') as f:
       json.dump(baseline_results, f, indent=2)
   ```

**Deliverable:** Complete baseline benchmark

#### Weekend (Optional)
1. Research XGBoost for time series
2. Prepare Kaggle account for GPU access
3. Review LSTM architectures

---

## 📅 WEEK 3: March 23-29, 2026
### Theme: Advanced Models - XGBoost & Random Forest

#### Monday (March 23) - **Focus on D1 Prep**
**Morning (2-3 hours):**
1. Prepare baseline results presentation
2. Create visualization slides
3. Document methodology

**Afternoon (2-3 hours):**
1. Begin XGBoost implementation:
   ```python
   # src/models/xgboost_model.py
   import xgboost as xgb
   from sklearn.preprocessing import StandardScaler
   
   class XGBoostForecaster:
       """XGBoost model for time series forecasting"""
       
       def __init__(self, n_estimators=100, max_depth=5, learning_rate=0.1):
           self.model = xgb.XGBRegressor(
               n_estimators=n_estimators,
               max_depth=max_depth,
               learning_rate=learning_rate,
               objective='reg:squarederror',
               random_state=42
           )
           self.scaler = StandardScaler()
           self.feature_columns = None
       
       def prepare_features(self, df):
           """Prepare features from engineered dataset"""
           feature_cols = [col for col in df.columns 
                          if col not in ['date', 'price']]
           self.feature_columns = feature_cols
           return df[feature_cols]
       
       def fit(self, X_train, y_train):
           X_scaled = self.scaler.fit_transform(X_train)
           self.model.fit(X_scaled, y_train)
           return self
       
       def predict(self, X_test):
           X_scaled = self.scaler.transform(X_test)
           return self.model.predict(X_scaled)
       
       def get_feature_importance(self):
           importance = pd.DataFrame({
               'feature': self.feature_columns,
               'importance': self.model.feature_importances_
           }).sort_values('importance', ascending=False)
           return importance
   ```

**Deliverable:** XGBoost starter code

#### Tuesday (March 24)
**Full Day (4-5 hours):**
1. Prepare features for XGBoost:
   ```python
   # Load engineered features
   cement_features = pd.read_csv('data/processed/cement_features.csv')
   
   # Split
   train_df, test_df = time_series_split(cement_features, test_days=30)
   
   # Prepare X and y
   feature_cols = [col for col in train_df.columns 
                   if col not in ['date', 'price']]
   
   X_train = train_df[feature_cols]
   y_train = train_df['price']
   X_test = test_df[feature_cols]
   y_test = test_df['price']
   
   print(f"Training features: {X_train.shape}")
   print(f"Test features: {X_test.shape}")
   ```

2. Train XGBoost model:
   ```python
   # Initialize and train
   xgb_model = XGBoostForecaster(
       n_estimators=200,
       max_depth=5,
       learning_rate=0.05
   )
   
   xgb_model.fit(X_train, y_train)
   
   # Predict
   xgb_pred = xgb_model.predict(X_test)
   
   # Evaluate
   xgb_metrics = calculate_metrics(y_test.values, xgb_pred)
   print_metrics(xgb_metrics, "XGBoost")
   
   # Feature importance
   importance = xgb_model.get_feature_importance()
   print("\nTop 10 Important Features:")
   print(importance.head(10))
   
   # Plot feature importance
   plt.figure(figsize=(10, 8))
   importance.head(15).plot(x='feature', y='importance', kind='barh')
   plt.title('XGBoost Feature Importance')
   plt.tight_layout()
   plt.savefig('notebooks/figures/xgboost_feature_importance.png')
   ```

**Deliverable:** Trained XGBoost model

#### Wednesday (March 25) - **D1 DEMO DAY** 🎯
**Morning (2-3 hours):**
1. Finalize D1 presentation
2. Showcase baseline results
3. Preview XGBoost preliminary results

**Afternoon:**
1. D1 Demo
2. Gather feedback

**Deliverable:** Successful D1 presentation

#### Thursday (March 26)
**Morning (2-3 hours):**
1. Hyperparameter tuning for XGBoost:
   ```python
   from sklearn.model_selection import GridSearchCV
   
   param_grid = {
       'n_estimators': [100, 200, 300],
       'max_depth': [3, 5, 7],
       'learning_rate': [0.01, 0.05, 0.1],
       'subsample': [0.8, 1.0],
       'colsample_bytree': [0.8, 1.0]
   }
   
   # Time series cross-validation
   from sklearn.model_selection import TimeSeriesSplit
   tscv = TimeSeriesSplit(n_splits=3)
   
   grid_search = GridSearchCV(
       xgb.XGBRegressor(objective='reg:squarederror', random_state=42),
       param_grid,
       cv=tscv,
       scoring='neg_mean_absolute_error',
       n_jobs=-1,
       verbose=1
   )
   
   grid_search.fit(X_train, y_train)
   
   print("Best parameters:", grid_search.best_params_)
   print("Best MAE:", -grid_search.best_score_)
   
   # Retrain with best params
   best_xgb = XGBoostForecaster(**grid_search.best_params_)
   best_xgb.fit(X_train, y_train)
   best_xgb_pred = best_xgb.predict(X_test)
   
   best_xgb_metrics = calculate_metrics(y_test.values, best_xgb_pred)
   print_metrics(best_xgb_metrics, "XGBoost (Tuned)")
   ```

**Afternoon (2-3 hours):**
1. Implement Random Forest:
   ```python
   # src/models/random_forest.py
   from sklearn.ensemble import RandomForestRegressor
   
   class RandomForestForecaster:
       """Random Forest model for time series"""
       
       def __init__(self, n_estimators=100, max_depth=None, min_samples_split=2):
           self.model = RandomForestRegressor(
               n_estimators=n_estimators,
               max_depth=max_depth,
               min_samples_split=min_samples_split,
               random_state=42,
               n_jobs=-1
           )
           self.scaler = StandardScaler()
           self.feature_columns = None
       
       def fit(self, X_train, y_train):
           X_scaled = self.scaler.fit_transform(X_train)
           self.model.fit(X_scaled, y_train)
           return self
       
       def predict(self, X_test):
           X_scaled = self.scaler.transform(X_test)
           return self.model.predict(X_scaled)
       
       def get_feature_importance(self):
           importance = pd.DataFrame({
               'feature': X_train.columns,
               'importance': self.model.feature_importances_
           }).sort_values('importance', ascending=False)
           return importance
   
   # Train Random Forest
   rf_model = RandomForestForecaster(n_estimators=200, max_depth=10)
   rf_model.fit(X_train, y_train)
   rf_pred = rf_model.predict(X_test)
   
   rf_metrics = calculate_metrics(y_test.values, rf_pred)
   print_metrics(rf_metrics, "Random Forest")
   ```

**Deliverable:** Random Forest model

#### Friday (March 27)
**Full Day (4-5 hours):**
1. Compare XGBoost vs Random Forest:
   ```python
   # Update comparison
   comparison.add_result('XGBoost (Tuned)', best_xgb_metrics, grid_search.best_params_)
   comparison.add_result('Random Forest', rf_metrics)
   
   print(comparison.get_comparison_df())
   
   # Calculate improvement over ARIMA
   arima_mape = best_metrics['MAPE']
   xgb_mape = best_xgb_metrics['MAPE']
   improvement = ((arima_mape - xgb_mape) / arima_mape) * 100
   
   print(f"\nXGBoost improvement over ARIMA: {improvement:.2f}%")
   ```

2. Document results and save models:
   ```python
   import joblib
   
   # Save models
   joblib.dump(best_xgb.model, 'models/xgboost_cement.pkl')
   joblib.dump(rf_model.model, 'models/random_forest_cement.pkl')
   
   # Save scalers
   joblib.dump(best_xgb.scaler, 'models/scaler_cement.pkl')
   ```

**Deliverable:** Complete tree-based models

---

## 📅 WEEK 4: March 30-April 5, 2026
### Theme: Prophet & LSTM Models + ST1 Preparation

#### Monday (March 30)
**Morning (2-3 hours):**
1. Implement Prophet model:
   ```python
   # src/models/prophet_model.py
   from prophet import Prophet
   
   class ProphetForecaster:
       """Facebook Prophet model for time series"""
       
       def __init__(self, yearly_seasonality=True, weekly_seasonality=True):
           self.model = Prophet(
               yearly_seasonality=yearly_seasonality,
               weekly_seasonality=weekly_seasonality,
               daily_seasonality=False,
               changepoint_prior_scale=0.05
           )
       
       def prepare_data(self, df):
           """Convert to Prophet format (ds, y)"""
           prophet_df = pd.DataFrame({
               'ds': pd.to_datetime(df['date']),
               'y': df['price']
           })
           return prophet_df
       
       def fit(self, train_df):
           prophet_data = self.prepare_data(train_df)
           self.model.fit(prophet_data)
           return self
       
       def predict(self, steps):
           future = self.model.make_future_dataframe(periods=steps)
           forecast = self.model.predict(future)
           return forecast.tail(steps)
   
   # Train Prophet
   prophet_model = ProphetForecaster()
   prophet_model.fit(train_df[['date', 'price']])
   prophet_forecast = prophet_model.predict(len(test_df))
   
   prophet_pred = prophet_forecast['yhat'].values
   prophet_metrics = calculate_metrics(y_test.values, prophet_pred)
   print_metrics(prophet_metrics, "Prophet")
   ```

**Afternoon (2-3 hours):**
1. Visualize Prophet components:
   ```python
   # Plot forecast
   fig1 = prophet_model.model.plot(prophet_forecast)
   plt.title('Prophet Forecast')
   plt.savefig('notebooks/figures/prophet_forecast.png')
   
   # Plot components (trend, seasonality)
   fig2 = prophet_model.model.plot_components(prophet_forecast)
   plt.savefig('notebooks/figures/prophet_components.png')
   ```

**Deliverable:** Prophet model

#### Tuesday (March 31) - **ST1 DEMO DAY** 🎯
**Morning (2-3 hours):**
1. Prepare ST1 presentation:
   - Model architecture slides
   - Performance comparison table
   - Feature importance visualizations
   - Next steps: LSTM

**Afternoon:**
1. ST1 Presentation
2. Showcase modeling progress

**Deliverable:** Successful ST1 presentation

#### Wednesday (April 2)
**Full Day (4-5 hours):**

### 🚀 Kaggle Setup for GPU-Accelerated LSTM Training

**Why Use Kaggle?** Train deep learning models 10-100x faster with free GPU access!

1. **Create Your Kaggle Notebook:**
   ```bash
   # Step 1: Go to https://www.kaggle.com/code
   # Step 2: Click "New Notebook"
   # Step 3: Title: "BuildMat Price Forecasting - LSTM Training"
   # Step 4: Settings > Accelerator > Select "GPU T4 x2" (free!)
   # Step 5: Settings > Internet > Turn ON (to install packages)
   ```

2. **Upload Your Dataset to Kaggle:**
   ```bash
   # Method 1: Via Web Interface
   # Go to https://www.kaggle.com/datasets
   # Click "New Dataset"
   # Upload your CSV files from data/processed/
   # Title: "SA Building Materials Price Data"
   # Make it Private initially
   
   # Method 2: Via Kaggle API (from your local machine)
   cd data/processed/
   
   # Create dataset-metadata.json
   cat > dataset-metadata.json << 'EOF'
   {
     "title": "SA Building Materials Price Data",
     "id": "your-username/sa-building-materials-prices",
     "licenses": [{"name": "CC0-1.0"}]
   }
   EOF
   
   # Upload dataset
   kaggle datasets create -p .
   
   # Update dataset later
   kaggle datasets version -p . -m "Updated with Week 3 data"
   ```

3. **In Your Kaggle Notebook - Initial Setup Cell:**
   ```python
   # Cell 1: Environment Setup and Verification
   import numpy as np
   import pandas as pd
   import tensorflow as tf
   from tensorflow import keras
   from tensorflow.keras.models import Sequential
   from tensorflow.keras.layers import LSTM, Dense, Dropout, Bidirectional
   from tensorflow.keras.callbacks import EarlyStopping, ModelCheckpoint, ReduceLROnPlateau
   from sklearn.preprocessing import MinMaxScaler
   from sklearn.metrics import mean_absolute_error, mean_squared_error
   import matplotlib.pyplot as plt
   import seaborn as sns
   
   # Verify GPU
   print("TensorFlow version:", tf.__version__)
   print("GPU Available:", len(tf.config.list_physical_devices('GPU')) > 0)
   print("GPU Devices:", tf.config.list_physical_devices('GPU'))
   
   # Set memory growth (prevents OOM errors)
   gpus = tf.config.list_physical_devices('GPU')
   if gpus:
       try:
           for gpu in gpus:
               tf.config.experimental.set_memory_growth(gpu, True)
           print("✅ GPU memory growth enabled")
       except RuntimeError as e:
           print(e)
   
   # Set seeds for reproducibility
   np.random.seed(42)
   tf.random.set_seed(42)
   print("✅ Seeds set for reproducibility")
   ```

4. **Load Your Data in Kaggle:**
   ```python
   # Cell 2: Load Data
   # After uploading dataset, it will be in /kaggle/input/
   
   # List available datasets
   import os
   for dirname, _, filenames in os.walk('/kaggle/input'):
       for filename in filenames:
           print(os.path.join(dirname, filename))
   
   # Load your price data
   df = pd.read_csv('/kaggle/input/sa-building-materials-prices/cement_features.csv')
   
   print(f"✅ Loaded {len(df)} records")
   print(f"Date range: {df['date'].min()} to {df['date'].max()}")
   print(f"\nDataset info:")
   print(df.info())
   print(f"\nFirst few rows:")
   print(df.head())
   ```

5. **Data Preprocessing on Kaggle:**
   ```python
   # Cell 3: Prepare Data for LSTM
   def create_sequences(data, seq_length=30):
       """Create sequences for LSTM input"""
       X, y = [], []
       
       for i in range(len(data) - seq_length):
           X.append(data[i:i+seq_length])
           y.append(data[i+seq_length])
       
       return np.array(X), np.array(y)
   
   # Extract price column
   prices = df['price'].values.reshape(-1, 1)
   
   # Normalize data
   scaler = MinMaxScaler(feature_range=(0, 1))
   prices_scaled = scaler.fit_transform(prices)
   
   print(f"Original price range: {prices.min():.2f} to {prices.max():.2f}")
   print(f"Scaled price range: {prices_scaled.min():.4f} to {prices_scaled.max():.4f}")
   
   # Create sequences
   SEQ_LENGTH = 30  # Use last 30 days to predict next day
   X, y = create_sequences(prices_scaled, SEQ_LENGTH)
   
   print(f"\n✅ Created sequences:")
   print(f"X shape: {X.shape}")  # (samples, timesteps, features)
   print(f"y shape: {y.shape}")  # (samples,)
   
   # Train-test split (80-20)
   train_size = int(0.8 * len(X))
   X_train, X_test = X[:train_size], X[train_size:]
   y_train, y_test = y[:train_size], y[train_size:]
   
   # Reshape for LSTM [samples, timesteps, features]
   X_train = X_train.reshape(X_train.shape[0], X_train.shape[1], 1)
   X_test = X_test.reshape(X_test.shape[0], X_test.shape[1], 1)
   
   print(f"\n✅ Data split:")
   print(f"Train: {X_train.shape}, {y_train.shape}")
   print(f"Test:  {X_test.shape}, {y_test.shape}")
   
   # Save scaler parameters for later use
   scaler_params = {
       'min': float(scaler.data_min_[0]),
       'max': float(scaler.data_max_[0]),
       'scale': float(scaler.scale_[0]),
   }
   print(f"\n✅ Scaler params saved: {scaler_params}")
   ```

6. **Build LSTM Model on Kaggle:**
   ```python
   # Cell 4: Build LSTM Architecture
   def build_lstm_model(seq_length, n_features=1):
       """Build LSTM model for time series forecasting"""
       model = Sequential([
           # First LSTM layer with return sequences
           LSTM(64, return_sequences=True, input_shape=(seq_length, n_features)),
           Dropout(0.2),
           
           # Second LSTM layer
           LSTM(64, return_sequences=True),
           Dropout(0.2),
           
           # Third LSTM layer
           LSTM(32, return_sequences=False),
           Dropout(0.2),
           
           # Dense layers
           Dense(32, activation='relu'),
           Dropout(0.2),
           Dense(1)
       ])
       
       # Compile with Adam optimizer
       model.compile(
           optimizer=keras.optimizers.Adam(learning_rate=0.001),
           loss='mse',
           metrics=['mae', 'mse']
       )
       
       return model
   
   # Build model
   lstm_model = build_lstm_model(SEQ_LENGTH)
   
   print("✅ LSTM Model Architecture:")
   print(lstm_model.summary())
   
   # Count parameters
   trainable_params = np.sum([np.prod(v.shape) for v in lstm_model.trainable_weights])
   print(f"\nTotal trainable parameters: {trainable_params:,}")
   ```

7. **Train Model on Kaggle GPU:**
   ```python
   # Cell 5: Train LSTM Model
   # Define callbacks
   early_stop = EarlyStopping(
       monitor='val_loss',
       patience=15,
       restore_best_weights=True,
       verbose=1
   )
   
   checkpoint = ModelCheckpoint(
       '/kaggle/working/lstm_cement_best.h5',
       monitor='val_loss',
       save_best_only=True,
       verbose=1
   )
   
   reduce_lr = ReduceLROnPlateau(
       monitor='val_loss',
       factor=0.5,
       patience=5,
       min_lr=0.00001,
       verbose=1
   )
   
   # Train model (GPU will accelerate this!)
   print("🚀 Starting training on GPU...")
   history = lstm_model.fit(
       X_train, y_train,
       validation_split=0.2,
       epochs=100,
       batch_size=32,
       callbacks=[early_stop, checkpoint, reduce_lr],
       verbose=1
   )
   
   print(f"\n✅ Training complete!")
   print(f"Best epoch: {early_stop.best_epoch + 1}")
   print(f"Best val_loss: {min(history.history['val_loss']):.6f}")
   ```

8. **Visualize Training Results:**
   ```python
   # Cell 6: Plot Training History
   fig, axes = plt.subplots(1, 2, figsize=(15, 5))
   
   # Plot loss
   axes[0].plot(history.history['loss'], label='Train Loss', linewidth=2)
   axes[0].plot(history.history['val_loss'], label='Val Loss', linewidth=2)
   axes[0].set_title('Model Loss During Training', fontsize=14, fontweight='bold')
   axes[0].set_xlabel('Epoch')
   axes[0].set_ylabel('Loss (MSE)')
   axes[0].legend()
   axes[0].grid(True, alpha=0.3)
   
   # Plot MAE
   axes[1].plot(history.history['mae'], label='Train MAE', linewidth=2)
   axes[1].plot(history.history['val_mae'], label='Val MAE', linewidth=2)
   axes[1].set_title('Model MAE During Training', fontsize=14, fontweight='bold')
   axes[1].set_xlabel('Epoch')
   axes[1].set_ylabel('MAE')
   axes[1].legend()
   axes[1].grid(True, alpha=0.3)
   
   plt.tight_layout()
   plt.savefig('/kaggle/working/training_history.png', dpi=300, bbox_inches='tight')
   plt.show()
   
   print("✅ Training history plotted and saved")
   ```

9. **Evaluate Model Performance:**
   ```python
   # Cell 7: Evaluate LSTM Model
   # Predict on test set
   lstm_pred_scaled = lstm_model.predict(X_test, verbose=0)
   
   # Inverse transform to original scale
   lstm_pred = scaler.inverse_transform(lstm_pred_scaled)
   y_test_original = scaler.inverse_transform(y_test)
   
   # Calculate metrics
   mae = mean_absolute_error(y_test_original, lstm_pred)
   rmse = np.sqrt(mean_squared_error(y_test_original, lstm_pred))
   mape = np.mean(np.abs((y_test_original - lstm_pred) / y_test_original)) * 100
   
   print("📊 LSTM Model Performance on Test Set:")
   print(f"  MAE:  R{mae:.2f}")
   print(f"  RMSE: R{rmse:.2f}")
   print(f"  MAPE: {mape:.2f}%")
   
   # Compare with baseline ARIMA (if you have those metrics)
   # arima_mae = 15.20  # Example from your earlier results
   # improvement = ((arima_mae - mae) / arima_mae) * 100
   # print(f"\n✅ Improvement over ARIMA: {improvement:.1f}%")
   ```

10. **Download Trained Model:**
    ```python
    # Cell 8: Save Model and Artifacts
    # Model is already saved at /kaggle/working/lstm_cement_best.h5
    
    # Save predictions for analysis
    results_df = pd.DataFrame({
        'actual': y_test_original.flatten(),
        'predicted': lstm_pred.flatten(),
        'error': (y_test_original - lstm_pred).flatten()
    })
    results_df.to_csv('/kaggle/working/lstm_predictions.csv', index=False)
    
    # Save model metrics
    metrics = {
        'MAE': float(mae),
        'RMSE': float(rmse),
        'MAPE': float(mape),
        'train_samples': int(len(X_train)),
        'test_samples': int(len(X_test)),
        'epochs_trained': len(history.history['loss']),
        'best_epoch': int(early_stop.best_epoch) + 1,
    }
    
    import json
    with open('/kaggle/working/lstm_metrics.json', 'w') as f:
        json.dump(metrics, f, indent=2)
    
    print("✅ All artifacts saved to /kaggle/working/")
    print("   - lstm_cement_best.h5 (trained model)")
    print("   - lstm_predictions.csv (test predictions)")
    print("   - lstm_metrics.json (performance metrics)")
    print("   - training_history.png (visualization)")
    print("\n📥 Download from the 'Output' tab on the right →")
    ```

11. **After Training - Download Your Results:**
    ```bash
    # In Kaggle notebook interface:
    # 1. Click "Save Version" button (top right)
    # 2. Choose "Save & Run All" (Quick Save)
    # 3. Wait for notebook to complete
    # 4. Go to "Output" tab on the right sidebar
    # 5. Download:
    #    - lstm_cement_best.h5
    #    - lstm_predictions.csv
    #    - lstm_metrics.json
    #    - training_history.png
    
    # Then on your local machine:
    # Move downloads to your project
    mv ~/Downloads/lstm_cement_best.h5 ~/projects/buildmat-ml/models/
    mv ~/Downloads/lstm_predictions.csv ~/projects/buildmat-ml/reports/
    mv ~/Downloads/lstm_metrics.json ~/projects/buildmat-ml/reports/
    mv ~/Downloads/training_history.png ~/projects/buildmat-ml/reports/figures/
    ```

**What You Must Do on Kaggle (Summary):**
- ✅ Create GPU-enabled notebook
- ✅ Upload your processed price dataset
- ✅ Build and train LSTM model using free GPU (10-100x faster!)
- ✅ Monitor training with callbacks and visualizations
- ✅ Evaluate model performance
- ✅ Save and download trained model + predictions
- ✅ Use the downloaded model in your backend API

**Kaggle Tips for Success:**
1. **Save versions frequently** - Don't lose your work!
2. **Monitor GPU usage** - You get 30 hours/week free
3. **Use callbacks** - EarlyStopping prevents overfitting
4. **Check output size** - Keep under 20GB to download easily
5. **Private notebooks** - Keep your work private until project completion

2. Prepare LSTM data (locally for comparison):
   ```python
   # src/data/lstm_preprocessing.py
   import numpy as np
   
   def create_sequences(data, seq_length=30):
       """Create sequences for LSTM input"""
       X, y = [], []
       
       for i in range(len(data) - seq_length):
           X.append(data[i:i+seq_length])
           y.append(data[i+seq_length])
       
       return np.array(X), np.array(y)
   
   # Normalize data
   from sklearn.preprocessing import MinMaxScaler
   
   scaler = MinMaxScaler()
   prices_scaled = scaler.fit_transform(cement_df[['price']])
   
   # Create sequences
   SEQ_LENGTH = 30  # Use last 30 days to predict next day
   X, y = create_sequences(prices_scaled, SEQ_LENGTH)
   
   # Split
   train_size = int(0.8 * len(X))
   X_train_lstm, X_test_lstm = X[:train_size], X[train_size:]
   y_train_lstm, y_test_lstm = y[:train_size], y[train_size:]
   
   # Reshape for LSTM [samples, timesteps, features]
   X_train_lstm = X_train_lstm.reshape(X_train_lstm.shape[0], X_train_lstm.shape[1], 1)
   X_test_lstm = X_test_lstm.reshape(X_test_lstm.shape[0], X_test_lstm.shape[1], 1)
   
   print(f"LSTM Training shape: {X_train_lstm.shape}")
   print(f"LSTM Test shape: {X_test_lstm.shape}")
   ```

**Deliverable:** LSTM data preparation

#### Thursday (April 3)
**Full Day (4-5 hours):**
1. Build LSTM architecture:
   ```python
   # src/models/lstm_model.py
   from tensorflow.keras.models import Sequential
   from tensorflow.keras.layers import LSTM, Dense, Dropout
   from tensorflow.keras.callbacks import EarlyStopping, ModelCheckpoint
   
   def build_lstm_model(seq_length, n_features=1):
       """Build LSTM architecture"""
       model = Sequential([
           LSTM(50, return_sequences=True, input_shape=(seq_length, n_features)),
           Dropout(0.2),
           LSTM(50, return_sequences=False),
           Dropout(0.2),
           Dense(25),
           Dense(1)
       ])
       
       model.compile(optimizer='adam', loss='mse', metrics=['mae'])
       return model
   
   # Build model
   lstm_model = build_lstm_model(SEQ_LENGTH)
   print(lstm_model.summary())
   
   # Callbacks
   early_stop = EarlyStopping(monitor='val_loss', patience=10, restore_best_weights=True)
   checkpoint = ModelCheckpoint('models/lstm_cement_best.h5', save_best_only=True)
   
   # Train
   history = lstm_model.fit(
       X_train_lstm, y_train_lstm,
       validation_split=0.2,
       epochs=100,
       batch_size=32,
       callbacks=[early_stop, checkpoint],
       verbose=1
   )
   
   # Plot training history
   plt.figure(figsize=(12, 4))
   
   plt.subplot(1, 2, 1)
   plt.plot(history.history['loss'], label='Train Loss')
   plt.plot(history.history['val_loss'], label='Val Loss')
   plt.title('Model Loss')
   plt.xlabel('Epoch')
   plt.ylabel('Loss')
   plt.legend()
   
   plt.subplot(1, 2, 2)
   plt.plot(history.history['mae'], label='Train MAE')
   plt.plot(history.history['val_mae'], label='Val MAE')
   plt.title('Model MAE')
   plt.xlabel('Epoch')
   plt.ylabel('MAE')
   plt.legend()
   
   plt.tight_layout()
   plt.savefig('notebooks/figures/lstm_training_history.png')
   ```

**Deliverable:** Trained LSTM model

#### Friday (April 4)
**Full Day (4-5 hours):**
1. Evaluate LSTM:
   ```python
   # Predict
   lstm_pred_scaled = lstm_model.predict(X_test_lstm)
   
   # Inverse transform to original scale
   lstm_pred = scaler.inverse_transform(lstm_pred_scaled)
   y_test_original = scaler.inverse_transform(y_test_lstm)
   
   # Calculate metrics
   lstm_metrics = calculate_metrics(y_test_original.flatten(), lstm_pred.flatten())
   print_metrics(lstm_metrics, "LSTM")
   
   # Add to comparison
   comparison.add_result('LSTM', lstm_metrics)
   ```

2. Multi-step LSTM forecasting:
   ```python
   def lstm_multi_step_forecast(model, last_sequence, steps, scaler):
       """Forecast multiple steps ahead"""
       current_sequence = last_sequence.copy()
       predictions = []
       
       for _ in range(steps):
           # Predict next value
           next_pred_scaled = model.predict(current_sequence.reshape(1, SEQ_LENGTH, 1), verbose=0)
           
           # Store prediction
           predictions.append(next_pred_scaled[0, 0])
           
           # Update sequence (remove first, add prediction)
           current_sequence = np.append(current_sequence[1:], next_pred_scaled[0, 0])
       
       # Inverse transform
       predictions = np.array(predictions).reshape(-1, 1)
       predictions_original = scaler.inverse_transform(predictions)
       
       return predictions_original.flatten()
   
   # Forecast next 30 days
   last_seq = prices_scaled[-SEQ_LENGTH:]
   lstm_forecast = lstm_multi_step_forecast(lstm_model, last_seq, 30, scaler)
   
   print(f"30-day LSTM forecast:")
   print(lstm_forecast)
   ```

**Deliverable:** Complete LSTM implementation

---

## 📅 WEEK 5: April 6-12, 2026
### Theme: Model Ensemble & D2 Preparation

#### Monday (April 6)
**Full Day (4-5 hours):**
1. Create ensemble model:
   ```python
   # src/models/ensemble.py
   class EnsembleForecaster:
       """Weighted ensemble of multiple models"""
       
       def __init__(self, models, weights=None):
           self.models = models
           self.weights = weights if weights else [1/len(models)] * len(models)
       
       def predict(self, X_test=None, steps=None):
           """Get weighted predictions from all models"""
           predictions = []
           
           for model, weight in zip(self.models, self.weights):
               if hasattr(model, 'predict'):
                   if X_test is not None:
                       pred = model.predict(X_test)
                   else:
                       pred = model.predict(steps)
               predictions.append(pred * weight)
           
           # Weighted average
           ensemble_pred = np.sum(predictions, axis=0)
           return ensemble_pred
   
   # Create ensemble
   ensemble_models = [best_xgb, rf_model, prophet_model]
   
   # Optimize weights (simple grid search)
   from itertools import product
   
   weights_range = np.arange(0, 1.1, 0.1)
   best_weights = None
   best_ensemble_mae = np.inf
   
   for w1 in weights_range:
       for w2 in weights_range:
           w3 = 1 - w1 - w2
           if w3 < 0 or w3 > 1:
               continue
           
           weights = [w1, w2, w3]
           
           # Get predictions
           xgb_p = best_xgb.predict(X_test)
           rf_p = rf_model.predict(X_test)
           prophet_p = prophet_pred
           
           ensemble_p = xgb_p * w1 + rf_p * w2 + prophet_p * w3
           
           mae = mean_absolute_error(y_test, ensemble_p)
           
           if mae < best_ensemble_mae:
               best_ensemble_mae = mae
               best_weights = weights
   
   print(f"Best weights: {best_weights}")
   print(f"Ensemble MAE: {best_ensemble_mae}")
   
   # Final ensemble predictions
   final_ensemble_pred = (
       best_xgb.predict(X_test) * best_weights[0] +
       rf_model.predict(X_test) * best_weights[1] +
       prophet_pred * best_weights[2]
   )
   
   ensemble_metrics = calculate_metrics(y_test.values, final_ensemble_pred)
   print_metrics(ensemble_metrics, "Ensemble")
   
   comparison.add_result('Ensemble', ensemble_metrics, {'weights': best_weights})
   ```

**Deliverable:** Ensemble model

#### Tuesday (April 7)
**Full Day (4-5 hours):**
1. Create comprehensive model comparison:
   ```python
   # Final comparison
   results_df = comparison.get_comparison_df()
   print("\n=== FINAL MODEL COMPARISON ===")
   print(results_df.to_string())
   
   # Calculate improvement over ARIMA baseline
   arima_mape = results_df[results_df['model'].str.contains('ARIMA')]['MAPE'].values[0]
   
   for idx, row in results_df.iterrows():
       if 'ARIMA' not in row['model']:
           improvement = ((arima_mape - row['MAPE']) / arima_mape) * 100
           print(f"{row['model']}: {improvement:.2f}% improvement over ARIMA")
   
   # Visualize all models
   plt.figure(figsize=(16, 8))
   
   plt.subplot(1, 2, 1)
   results_df.plot(x='model', y='MAPE', kind='bar', ax=plt.gca())
   plt.title('Model MAPE Comparison')
   plt.ylabel('MAPE (%)')
   plt.xticks(rotation=45, ha='right')
   
   plt.subplot(1, 2, 2)
   results_df.plot(x='model', y='RMSE', kind='bar', ax=plt.gca())
   plt.title('Model RMSE Comparison')
   plt.ylabel('RMSE')
   plt.xticks(rotation=45, ha='right')
   
   plt.tight_layout()
   plt.savefig('notebooks/figures/final_model_comparison.png', dpi=300)
   ```

**Deliverable:** Model performance report

#### Wednesday (April 8) - **D2 DEMO DAY** 🎯
**Morning (2-3 hours):**
1. Prepare D2 demo:
   - Show all models
   - Demonstrate 59% improvement claim
   - Live prediction demo
   - Explain ensemble approach

**Afternoon:**
1. D2 Presentation
2. Showcase working ML system

**Deliverable:** Successful D2 demo

#### Thursday-Friday (April 9-11)
**Full Days:**
1. Integrate models with backend API (Member 1)
2. Create model inference endpoint
3. Test prediction API
4. Optimize inference speed

---

## 📅 WEEKS 6-8: April 13-May 3, 2026
### Theme: Production Integration & MVP

**Key Activities:**
1. Deploy models to production
2. Create anomaly detection system
3. Model monitoring and logging
4. API performance optimization
5. Documentation

**D3 (May 1) - MVP COMPLETE** 🎉

---

## 📅 WEEKS 9-13: May 4-June 9, 2026
### Theme: Testing, Optimization, & Presentation

**ST2 (May 6)** - Testing Results  
**D4 (May 13)** - Final Polish  
**Final (June 10)** - Championship Presentation

**Focus:**
1. Model retraining pipeline
2. Performance benchmarking
3. Documentation finalization
4. Presentation preparation

---

## 🎯 Key Performance Targets

| Model | MAPE Target | RMSE Target | Status |
|-------|-------------|-------------|--------|
| Naïve | ~15% | Baseline | ✅ |
| Moving Average | ~12% | Baseline | ✅ |
| ARIMA | ~10% | Baseline | ✅ |
| XGBoost | <6% | 59% better | 🎯 |
| Random Forest | <7% | 50% better | 🎯 |
| Prophet | <8% | 40% better | 🎯 |
| LSTM | <6% | 59% better | 🎯 |
| Ensemble | <5% | >59% better | 🏆 |

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active | **Next Milestone:** D1 (March 25, 2026)
