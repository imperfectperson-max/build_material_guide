# 📱 Mobile Developer & Data Collection Lead - Detailed Roadmap

**Team Member:** Member 4  
**Primary Tech Stack:** Python (Scrapers), Flutter, Hive, ML Kit  
**Timeline:** March 9 - June 9, 2026 (13 weeks)

---

## 🚀 Getting Started - March 8, 2026

**Complete this dual setup for both Python scrapers and Flutter mobile development!**

### System Requirements
- **Operating System:** Windows 10/11, macOS 10.15+, or Ubuntu 20.04+
- **RAM:** Minimum 8GB (16GB recommended)
- **Storage:** At least 15GB free space (Flutter SDK + Android SDK)
- **Mobile:** Android device or emulator, iOS device (macOS only) or simulator

---

## PART 1: Python Environment for Web Scrapers

### Step 1: Install Python and Conda
```bash
# Download and install Anaconda or Miniconda
# Visit: https://www.anaconda.com/download

# Verify installation
python --version  # Should show Python 3.9+ or 3.10+
conda --version
```

### Step 2: Create Python Environment for Scrapers
```bash
# Create dedicated environment for web scraping
conda create -n scrapers python=3.10 -y
conda activate scrapers

# Verify environment
which python  # Should point to scrapers environment
```

### Step 3: Install Web Scraping Libraries
```bash
# Core scraping tools
pip install beautifulsoup4 requests

# Advanced scraping (for JavaScript-heavy sites)
pip install selenium webdriver-manager

# HTML parsing
pip install lxml html5lib

# Data manipulation
pip install pandas numpy

# PDF parsing (for supplier price lists)
pip install PyPDF2 pdfplumber tabula-py

# Excel file handling
pip install openpyxl xlrd

# HTTP client with better performance
pip install httpx

# Utilities
pip install tqdm python-dotenv

# Save requirements
pip freeze > requirements-scrapers.txt
```

### Step 4: Install Browser Drivers for Selenium
```bash
# Chrome WebDriver (automatic installation)
pip install webdriver-manager

# Test Selenium setup
python << 'EOF'
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service

# This will auto-download ChromeDriver
service = Service(ChromeDriverManager().install())
driver = webdriver.Chrome(service=service)
driver.get('https://www.google.com')
print("✅ Selenium working! Browser opened successfully.")
driver.quit()
EOF
```

### Step 5: Create Scraper Project Structure
```bash
# Create project directory
mkdir -p ~/projects/buildmat-scrapers
cd ~/projects/buildmat-scrapers

# Create comprehensive structure
mkdir -p {scrapers/suppliers,data/{raw,processed,archive},logs,config,tests}

# Create necessary files
touch scrapers/__init__.py
touch scrapers/base_scraper.py
touch config/suppliers.json
touch .env
```

### Step 6: Create Base Scraper Template
```bash
cat > scrapers/base_scraper.py << 'EOF'
import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime
import logging
import time

class BaseScraper:
    """Base class for all supplier scrapers"""
    
    def __init__(self, supplier_name):
        self.supplier_name = supplier_name
        self.session = requests.Session()
        self.session.headers.update({
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
        })
        self.setup_logging()
    
    def setup_logging(self):
        """Configure logging"""
        logging.basicConfig(
            filename=f'logs/{self.supplier_name}_{datetime.now():%Y%m%d}.log',
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s'
        )
        self.logger = logging.getLogger(self.supplier_name)
    
    def fetch_page(self, url, retry=3):
        """Fetch webpage with retry logic"""
        for attempt in range(retry):
            try:
                response = self.session.get(url, timeout=10)
                response.raise_for_status()
                self.logger.info(f"Successfully fetched: {url}")
                return response
            except requests.RequestException as e:
                self.logger.error(f"Attempt {attempt + 1} failed: {e}")
                time.sleep(2 ** attempt)  # Exponential backoff
        return None
    
    def parse_html(self, html_content):
        """Parse HTML content"""
        return BeautifulSoup(html_content, 'lxml')
    
    def save_data(self, data, filename):
        """Save scraped data to CSV"""
        df = pd.DataFrame(data)
        filepath = f'data/raw/{filename}_{datetime.now():%Y%m%d}.csv'
        df.to_csv(filepath, index=False)
        self.logger.info(f"Data saved to: {filepath}")
        return filepath
    
    def scrape(self):
        """Override this method in child classes"""
        raise NotImplementedError("Subclasses must implement scrape()")

if __name__ == "__main__":
    print("✅ Base scraper template created successfully!")
EOF
```

### Step 7: Create Example Scraper
```bash
cat > scrapers/suppliers/builders_warehouse.py << 'EOF'
import sys
sys.path.append('../..')
from scrapers.base_scraper import BaseScraper
from datetime import datetime

class BuildersWarehouseScraper(BaseScraper):
    """Scraper for Builders Warehouse"""
    
    def __init__(self):
        super().__init__('BuildersWarehouse')
        self.base_url = 'https://www.builders.co.za'
    
    def scrape_material(self, material_name):
        """Scrape prices for specific material"""
        search_url = f"{self.base_url}/search?q={material_name}"
        
        response = self.fetch_page(search_url)
        if not response:
            return []
        
        soup = self.parse_html(response.content)
        
        # Adjust selectors based on actual website structure
        products = soup.find_all('div', class_='product-card')
        
        results = []
        for product in products:
            try:
                name = product.find('h3', class_='product-name').text.strip()
                price_text = product.find('span', class_='price').text.strip()
                price = float(price_text.replace('R', '').replace(',', '').strip())
                
                results.append({
                    'supplier': self.supplier_name,
                    'material_name': name,
                    'price': price,
                    'date': datetime.now().strftime('%Y-%m-%d'),
                    'region': 'National',
                    'url': self.base_url + product.find('a')['href']
                })
            except (AttributeError, ValueError) as e:
                self.logger.warning(f"Error parsing product: {e}")
                continue
        
        return results
    
    def scrape(self, materials=['cement', 'bricks', 'sand']):
        """Scrape multiple materials"""
        all_data = []
        
        for material in materials:
            print(f"Scraping {material}...")
            data = self.scrape_material(material)
            all_data.extend(data)
            self.logger.info(f"Found {len(data)} products for {material}")
        
        if all_data:
            self.save_data(all_data, f'{self.supplier_name}_prices')
        
        return all_data

if __name__ == "__main__":
    scraper = BuildersWarehouseScraper()
    results = scraper.scrape()
    print(f"✅ Scraped {len(results)} products")
EOF
```

---

## PART 2: Flutter Environment for Mobile Development

### Step 8: Install Flutter SDK
```bash
# Download Flutter SDK from: https://flutter.dev/docs/get-started/install

# macOS/Linux: Extract and add to PATH
# Add to ~/.bashrc or ~/.zshrc:
export PATH="$PATH:$HOME/flutter/bin"
source ~/.bashrc

# Windows: Follow Flutter installation guide and add to PATH

# Verify installation
flutter --version
flutter doctor

# This will check for missing dependencies
```

### Step 9: Install Platform-Specific Tools

#### For Android Development:
```bash
# Download Android Studio from: https://developer.android.com/studio

# During installation, make sure to install:
# - Android SDK
# - Android SDK Platform
# - Android Virtual Device (AVD)

# Accept Android licenses
flutter doctor --android-licenses

# Type 'y' to accept all licenses
```

#### For iOS Development (macOS only):
```bash
# Install Xcode from Mac App Store
# Install Xcode command line tools
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch

# Install CocoaPods
sudo gem install cocoapods
pod setup

# Verify iOS setup
flutter doctor
```

### Step 10: Create Flutter Project
```bash
# Create project directory
mkdir -p ~/projects/buildmat-mobile
cd ~/projects/buildmat-mobile

# Create Flutter app
flutter create buildmat_app

# Navigate to project
cd buildmat_app

# Test the app
flutter run
# This will start the app on connected device/emulator
```

### Step 11: Install Flutter Packages
```bash
# Navigate to project root
cd ~/projects/buildmat-mobile/buildmat_app

# Add dependencies to pubspec.yaml, then run:
flutter pub add http
flutter pub add provider
flutter pub add shared_preferences
flutter pub add hive hive_flutter
flutter pub add flutter_secure_storage
flutter pub add cached_network_image
flutter pub add intl
flutter pub add google_fonts
flutter pub add flutter_barcode_scanner
flutter pub add geolocator
flutter pub add permission_handler
flutter pub add local_auth

# Development dependencies
flutter pub add --dev build_runner
flutter pub add --dev hive_generator

# Get all packages
flutter pub get
```

### Step 12: Configure pubspec.yaml
```yaml
# Update pubspec.yaml with all dependencies
cat > pubspec.yaml << 'EOF'
name: buildmat_app
description: Building Materials Price Intelligence Mobile App
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter
  
  # UI
  cupertino_icons: ^1.0.2
  google_fonts: ^5.1.0
  
  # State Management
  provider: ^6.0.5
  
  # HTTP & API
  http: ^1.1.0
  
  # Local Storage
  hive: ^2.2.3
  hive_flutter: ^1.1.0
  shared_preferences: ^2.2.0
  flutter_secure_storage: ^9.0.0
  
  # Images
  cached_network_image: ^3.2.3
  
  # Utilities
  intl: ^0.18.1
  
  # Features
  flutter_barcode_scanner: ^2.0.0
  geolocator: ^10.0.1
  permission_handler: ^11.0.0
  local_auth: ^2.1.7

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^2.0.0
  build_runner: ^2.4.6
  hive_generator: ^2.0.1

flutter:
  uses-material-design: true
  
  assets:
    - assets/images/
    - assets/icons/
EOF
```

### Step 13: Create Flutter Project Structure
```bash
# Create organized folder structure
mkdir -p lib/{models,services,screens,widgets,utils,providers}

# Create placeholder files
touch lib/models/.gitkeep
touch lib/services/api_service.dart
touch lib/services/storage_service.dart
touch lib/screens/home_screen.dart
touch lib/widgets/price_card.dart
touch lib/utils/constants.dart
touch lib/providers/price_provider.dart
```

### Step 14: Create API Service Template
```bash
cat > lib/services/api_service.dart << 'EOF'
import 'dart:convert';
import 'package:http/http.dart' as http;

class ApiService {
  static const String baseUrl = 'http://localhost:3000/api';
  
  // Get all materials
  Future<List<dynamic>> getMaterials() async {
    try {
      final response = await http.get(
        Uri.parse('$baseUrl/materials'),
        headers: {'Content-Type': 'application/json'},
      );
      
      if (response.statusCode == 200) {
        return json.decode(response.body);
      } else {
        throw Exception('Failed to load materials');
      }
    } catch (e) {
      print('Error fetching materials: $e');
      rethrow;
    }
  }
  
  // Get prices for a material
  Future<List<dynamic>> getPrices(String materialId) async {
    try {
      final response = await http.get(
        Uri.parse('$baseUrl/prices?material_id=$materialId'),
        headers: {'Content-Type': 'application/json'},
      );
      
      if (response.statusCode == 200) {
        return json.decode(response.body);
      } else {
        throw Exception('Failed to load prices');
      }
    } catch (e) {
      print('Error fetching prices: $e');
      rethrow;
    }
  }
  
  // Get forecast
  Future<Map<String, dynamic>> getForecast(String materialId, int days) async {
    try {
      final response = await http.get(
        Uri.parse('$baseUrl/forecast/$materialId?days=$days'),
        headers: {'Content-Type': 'application/json'},
      );
      
      if (response.statusCode == 200) {
        return json.decode(response.body);
      } else {
        throw Exception('Failed to load forecast');
      }
    } catch (e) {
      print('Error fetching forecast: $e');
      rethrow;
    }
  }
}
EOF
```

### Step 15: Set Up Android Permissions
```bash
# Update android/app/src/main/AndroidManifest.xml
# Add these permissions before <application>:
cat >> android/app/src/main/AndroidManifest.xml << 'EOF'
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-permission android:name="android.permission.USE_BIOMETRIC"/>
EOF
```

### Step 16: Create Environment Configuration
```bash
# Create config file
cat > lib/utils/constants.dart << 'EOF'
class AppConstants {
  // API Configuration
  static const String apiBaseUrl = 'http://10.0.2.2:3000/api';  // For Android emulator
  static const String apiBaseUrlIOS = 'http://localhost:3000/api';  // For iOS simulator
  
  // App Configuration
  static const String appName = 'BuildMat';
  static const String appVersion = '1.0.0';
  
  // Cache Configuration
  static const int cacheExpiryHours = 24;
  
  // UI Configuration
  static const double borderRadius = 12.0;
  static const double spacing = 16.0;
}
EOF
```

### Step 17: Test Flutter Setup
```bash
# Run Flutter doctor to check setup
flutter doctor -v

# Create and run on Android emulator
flutter emulators --launch <emulator_id>
flutter run

# OR run on connected physical device
flutter devices
flutter run -d <device_id>

# Run tests
flutter test

# Build APK (for testing)
flutter build apk --debug
```

### Checklist - Confirm Everything Works ✅

**Python/Scrapers:**
- [ ] Python and Conda installed
- [ ] Scrapers environment created and activated
- [ ] All scraping libraries installed
- [ ] Selenium and ChromeDriver working
- [ ] Base scraper template created
- [ ] Example scraper runs successfully
- [ ] Project structure organized

**Flutter/Mobile:**
- [ ] Flutter SDK installed
- [ ] Flutter doctor shows no critical issues
- [ ] Android Studio installed (for Android)
- [ ] Xcode installed (for iOS on macOS)
- [ ] Android licenses accepted
- [ ] Flutter project created
- [ ] All packages installed successfully
- [ ] Android emulator or device connected
- [ ] App runs on emulator/device
- [ ] API service template created
- [ ] Project structure organized

### Troubleshooting Common Issues

**Python Issue: Selenium browser doesn't open**
```bash
# Update Chrome browser to latest version
# Update webdriver-manager
pip install --upgrade webdriver-manager

# Try with headless mode
from selenium.webdriver.chrome.options import Options
options = Options()
options.add_argument('--headless')
driver = webdriver.Chrome(service=service, options=options)
```

**Flutter Issue: flutter command not found**
```bash
# Add Flutter to PATH permanently
# For macOS/Linux, add to ~/.bashrc or ~/.zshrc:
export PATH="$PATH:$HOME/flutter/bin"
export PATH="$PATH:$HOME/flutter/bin/cache/dart-sdk/bin"
source ~/.bashrc

# Verify
echo $PATH
flutter --version
```

**Flutter Issue: Android licenses not accepted**
```bash
# Run this command and accept all
flutter doctor --android-licenses

# Type 'y' for each license agreement
```

**Flutter Issue: Gradle build fails**
```bash
# Update Gradle wrapper
cd android
./gradlew wrapper --gradle-version=7.5

# Clean and rebuild
flutter clean
flutter pub get
flutter run
```

**Flutter Issue: iOS build fails (macOS)**
```bash
# Update CocoaPods
sudo gem install cocoapods
pod repo update

# Clean iOS build
cd ios
rm -rf Pods Podfile.lock
pod install
cd ..
flutter clean
flutter run
```

**Issue: Port conflicts when running both scraper and app**
```bash
# Use different ports
# Backend on 3000
# Frontend on 3001
# Make sure mobile app points to correct port in constants.dart
```

### Quick Reference Commands 📝

**Python/Scrapers:**
```bash
# Activate environment
conda activate scrapers

# Run a scraper
python scrapers/suppliers/builders_warehouse.py

# Install new package
pip install package-name

# Update all packages
pip install --upgrade -r requirements-scrapers.txt
```

**Flutter/Mobile:**
```bash
# Run app
flutter run

# Hot reload (in running app)
# Press 'r' in terminal

# Hot restart (in running app)
# Press 'R' in terminal

# Build APK
flutter build apk

# Build iOS app (macOS only)
flutter build ios

# Run tests
flutter test

# Check for updates
flutter upgrade

# Clear cache and rebuild
flutter clean && flutter pub get && flutter run
```

### Ready for Week 1! 🎉
You now have both Python scraping tools and Flutter mobile development environment ready. Let's build something amazing starting March 9, 2026!

---

## 🎯 Role Overview

You have a dual role: building data collection infrastructure AND developing the mobile application. This requires strong Python skills for scraping and Flutter expertise for mobile development.

**Core Responsibilities:**
- Web scraper development (Python)
- Data cleaning and validation
- Flutter mobile app development
- Offline storage (Hive)
- Push notifications
- Biometric authentication

---

## 📅 WEEK 1: March 9-15, 2026
### Theme: Data Collection Infrastructure

#### Monday (March 9)
**Morning (2-3 hours):**
```bash
# Set up Python environment
conda create -n scrapers python=3.9
conda activate scrapers
pip install beautifulsoup4 requests pandas selenium lxml openpyxl

# Create project structure
mkdir -p scrapers/
cd scrapers/
mkdir suppliers/ data/ logs/
```

**Afternoon (2-3 hours):**
Research South African suppliers:
1. Identify 10+ suppliers (Builders Warehouse, Cashbuild, etc.)
2. Document website structures
3. Check robots.txt and scraping policies
4. Note data formats (HTML tables, JSON, PDF)

**Deliverable:** Supplier list with scraping notes

#### Tuesday-Wednesday (March 10-11)
**Focus: First Web Scraper**

```python
# scrapers/suppliers/builder_warehouse.py
import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime

class BuilderWarehouseScraper:
    def __init__(self):
        self.base_url = "https://www.builders.co.za"
        self.session = requests.Session()
        self.session.headers.update({
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
        })
    
    def scrape_material(self, material_name):
        """Scrape prices for a specific material"""
        search_url = f"{self.base_url}/search?q={material_name}"
        
        try:
            response = self.session.get(search_url)
            response.raise_for_status()
            
            soup = BeautifulSoup(response.content, 'html.parser')
            
            # Find product cards (adjust selectors based on actual website)
            products = soup.find_all('div', class_='product-card')
            
            results = []
            for product in products:
                name = product.find('h3', class_='product-name').text.strip()
                price_text = product.find('span', class_='price').text.strip()
                price = float(price_text.replace('R', '').replace(',', ''))
                
                results.append({
                    'supplier': 'Builder Warehouse',
                    'material_name': name,
                    'price': price,
                    'date': datetime.now().strftime('%Y-%m-%d'),
                    'url': product.find('a')['href']
                })
            
            return results
            
        except Exception as e:
            print(f"Error scraping: {e}")
            return []
    
    def scrape_category(self, category_url):
        """Scrape all materials in a category"""
        # Implementation for category scraping
        pass

# Test scraper
if __name__ == '__main__':
    scraper = BuilderWarehouseScraper()
    cement_prices = scraper.scrape_material('cement')
    
    df = pd.DataFrame(cement_prices)
    print(df)
    df.to_csv('data/builder_warehouse_cement.csv', index=False)
```

**Deliverable:** Working scraper for 1 supplier

#### Thursday-Friday (March 12-13)
**Focus: Multiple Scrapers + PDF Parser**

Create scrapers for 2-3 more suppliers.

PDF price list parser:
```python
# scrapers/pdf_parser.py
import pdfplumber
import pandas as pd
import re

class PDFPriceParser:
    def extract_prices(self, pdf_path):
        """Extract price tables from PDF"""
        prices = []
        
        with pdfplumber.open(pdf_path) as pdf:
            for page in pdf.pages:
                # Extract tables
                tables = page.extract_tables()
                
                for table in tables:
                    # Process table rows
                    for row in table[1:]:  # Skip header
                        if len(row) >= 3:
                            material = row[0]
                            unit = row[1]
                            price_str = row[2]
                            
                            # Clean price
                            price_match = re.search(r'[\d,]+\.?\d*', price_str)
                            if price_match:
                                price = float(price_match.group().replace(',', ''))
                                
                                prices.append({
                                    'material': material,
                                    'unit': unit,
                                    'price': price
                                })
        
        return pd.DataFrame(prices)

# Test PDF parser
parser = PDFPriceParser()
df = parser.extract_prices('supplier_pricelist_feb2026.pdf')
print(df)
```

**Deliverable:** 3 working scrapers + PDF parser

---

## 📅 WEEK 2: March 16-22, 2026
### Theme: Data Cleaning & Automation

#### Monday-Tuesday (March 16-17)
**Focus: Data Cleaning Pipeline**

```python
# scrapers/data_cleaner.py
import pandas as pd
import re

class DataCleaner:
    def __init__(self):
        self.material_mapping = {
            'cement 50kg': 'Cement',
            'opc cement': 'Cement',
            'portland cement': 'Cement',
            # Add more mappings
        }
    
    def clean_material_name(self, name):
        """Standardize material names"""
        name = name.lower().strip()
        
        # Check mappings
        for key, value in self.material_mapping.items():
            if key in name:
                return value
        
        return name.title()
    
    def clean_price(self, price):
        """Validate and clean price"""
        if pd.isna(price):
            return None
        
        # Remove currency symbols and commas
        price_str = str(price).replace('R', '').replace(',', '').strip()
        
        try:
            price_float = float(price_str)
            
            # Sanity check (prices between R1 and R100,000)
            if 1 <= price_float <= 100000:
                return price_float
            else:
                return None
        except ValueError:
            return None
    
    def clean_dataset(self, df):
        """Clean entire dataset"""
        df = df.copy()
        
        # Clean material names
        df['material_clean'] = df['material_name'].apply(self.clean_material_name)
        
        # Clean prices
        df['price_clean'] = df['price'].apply(self.clean_price)
        
        # Remove rows with invalid prices
        df = df[df['price_clean'].notna()]
        
        # Remove duplicates
        df = df.drop_duplicates(subset=['supplier', 'material_clean', 'date'])
        
        return df

# Usage
cleaner = DataCleaner()
raw_df = pd.read_csv('data/raw_scraped_data.csv')
clean_df = cleaner.clean_dataset(raw_df)
clean_df.to_csv('data/clean_data.csv', index=False)
```

**Deliverable:** Data cleaning pipeline

#### Wednesday-Thursday (March 18-19)
**Focus: Scheduled Scraping**

```python
# scrapers/scheduler.py
import schedule
import time
from datetime import datetime
from suppliers.builder_warehouse import BuilderWarehouseScraper
from suppliers.cashbuild import CashbuildScraper
from data_cleaner import DataCleaner
import logging

logging.basicConfig(
    filename='logs/scraper.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

class ScraperScheduler:
    def __init__(self):
        self.scrapers = [
            BuilderWarehouseScraper(),
            CashbuildScraper(),
            # Add more scrapers
        ]
        self.cleaner = DataCleaner()
    
    def run_daily_scrape(self):
        """Run all scrapers and clean data"""
        logging.info("Starting daily scrape")
        
        all_data = []
        
        for scraper in self.scrapers:
            try:
                data = scraper.scrape_all_materials()
                all_data.extend(data)
                logging.info(f"Scraped {len(data)} items from {scraper.name}")
            except Exception as e:
                logging.error(f"Error with {scraper.name}: {e}")
        
        # Clean and save
        df = pd.DataFrame(all_data)
        clean_df = self.cleaner.clean_dataset(df)
        
        timestamp = datetime.now().strftime('%Y%m%d')
        clean_df.to_csv(f'data/scraped_{timestamp}.csv', index=False)
        
        logging.info(f"Saved {len(clean_df)} clean records")
    
    def start(self):
        """Start scheduled scraping"""
        # Run daily at 2 AM
        schedule.every().day.at("02:00").do(self.run_daily_scrape)
        
        # Also run immediately for testing
        self.run_daily_scrape()
        
        while True:
            schedule.run_pending()
            time.sleep(60)

if __name__ == '__main__':
    scheduler = ScraperScheduler()
    scheduler.start()
```

**Deliverable:** Automated scraping system

#### Friday (March 20)
**Focus: API Integration with Backend**

```python
# scrapers/api_uploader.py
import requests
import pandas as pd

class DataUploader:
    def __init__(self, api_url, api_token):
        self.api_url = api_url
        self.headers = {
            'Authorization': f'Bearer {api_token}',
            'Content-Type': 'application/json'
        }
    
    def upload_prices(self, df):
        """Upload cleaned data to backend"""
        # Convert DataFrame to API format
        prices = df.to_dict('records')
        
        response = requests.post(
            f'{self.api_url}/data/bulk-import',
            json={'prices': prices},
            headers=self.headers
        )
        
        if response.status_code == 200:
            result = response.json()
            print(f"Uploaded: {result['imported']} records")
            print(f"Skipped: {result['skipped']} duplicates")
        else:
            print(f"Error: {response.status_code} - {response.text}")

# Usage
uploader = DataUploader(
    api_url='http://localhost:3000/api',
    api_token='your_token_here'
)

df = pd.read_csv('data/scraped_20260320.csv')
uploader.upload_prices(df)
```

**Deliverable:** API integration for data upload

---

## 📅 WEEK 3: March 23-29, 2026
### Theme: Mobile App Setup + D1

#### Monday-Tuesday (March 23-24)
**Focus: Flutter Project Setup**

```bash
# Install Flutter
flutter create buildmat_mobile
cd buildmat_mobile

# Add dependencies to pubspec.yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.1.0
  hive: ^2.2.3
  hive_flutter: ^1.1.0
  provider: ^6.0.5
  flutter_secure_storage: ^9.0.0
  local_auth: ^2.1.7
  
dev_dependencies:
  hive_generator: ^2.0.1
  build_runner: ^2.4.6
```

Project structure:
```
lib/
├── models/
├── services/
├── providers/
├── screens/
├── widgets/
└── main.dart
```

**Deliverable:** Flutter project initialized

#### Wednesday (March 25) - **D1 DEMO DAY** 🎯
**Morning:** Support D1 demo
- Ensure scrapers have collected 500+ records
- Verify data uploaded to backend
- Prepare data collection presentation

**Afternoon:** D1 Demo

#### Thursday-Friday (March 26-27)
**Focus: API Service Layer**

```dart
// lib/services/api_service.dart
import 'package:http/http.dart' as http;
import 'dart:convert';

class ApiService {
  final String baseUrl = 'http://localhost:3000/api';
  String? _token;
  
  void setToken(String token) {
    _token = token;
  }
  
  Map<String, String> get _headers => {
    'Content-Type': 'application/json',
    if (_token != null) 'Authorization': 'Bearer $_token',
  };
  
  Future<List<Material>> getMaterials() async {
    final response = await http.get(
      Uri.parse('$baseUrl/materials'),
      headers: _headers,
    );
    
    if (response.statusCode == 200) {
      final List<dynamic> data = json.decode(response.body);
      return data.map((json) => Material.fromJson(json)).toList();
    } else {
      throw Exception('Failed to load materials');
    }
  }
  
  Future<List<Price>> getPriceHistory(int materialId, int days) async {
    final response = await http.get(
      Uri.parse('$baseUrl/prices/history?material_id=$materialId&days=$days'),
      headers: _headers,
    );
    
    if (response.statusCode == 200) {
      final List<dynamic> data = json.decode(response.body);
      return data.map((json) => Price.fromJson(json)).toList();
    } else {
      throw Exception('Failed to load price history');
    }
  }
}
```

**Deliverable:** Mobile API layer

---

## 📅 WEEKS 4-5: March 30-April 12
### Theme: Core Mobile Features + ST1/D2

#### Week 4: Basic UI
- Material list screen
- Material detail screen
- Price chart widgets
- Search functionality
- Loading states

#### ST1 (April 1)
- Showcase data collection progress
- Demo mobile app prototype

#### Week 5: Advanced Features
- Offline storage with Hive
- Favorites system
- Push notifications setup
- Camera barcode scanning (ML Kit)

#### D2 (April 8)
- Functional mobile app demo
- Offline capabilities
- Real-time data sync

---

## 📅 WEEKS 6-8: April 13-May 3
### Theme: Mobile Polish + MVP

**Key Activities:**
1. Biometric authentication
2. Geolocation for nearby suppliers
3. Price alerts
4. UI/UX refinement
5. iOS testing
6. App performance optimization

**D3 (May 1) - MVP COMPLETE** 🎉

---

## 📅 WEEKS 9-13: May 4-June 9
### Theme: Testing & Deployment

**Focus:**
1. Beta testing
2. Bug fixes
3. App store preparation
4. Documentation
5. Presentation prep

**ST2 (May 6)** - Testing Report  
**D4 (May 13)** - Final Polish  
**Final (June 10)** - Championship Demo

---

## 🎯 Key Metrics

| Metric | Target |
|--------|--------|
| Scraped Records | 2,000+ |
| Suppliers Covered | 10+ |
| App Size | <50MB |
| Offline Functionality | 100% |
| Load Time | <2s |

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active | **Next Milestone:** D1 (March 25, 2026)
