# 📱 Mobile Developer & Data Collection Lead — Detailed Roadmap

**Team Member:** Member 4  
**Primary Tech Stack:** Python (Scrapers), Flutter, Hive, ML Kit  
**Timeline:** March 9 – June 9, 2026 (13 Weeks)

---

## 🚀 Getting Started — March 8, 2026

Complete **both** environment setups before Week 1 begins.

### System Requirements
- **OS:** Windows 10/11, macOS 10.15+, or Ubuntu 20.04+
- **RAM:** Minimum 8 GB (16 GB recommended)
- **Storage:** At least 15 GB free (Flutter SDK + Android SDK)
- **Mobile:** Android device/emulator, or iOS device/simulator (macOS only)

---

## PART 1: Python Environment for Web Scrapers

### Step 1 – Install Python and Conda

```bash
# https://www.anaconda.com/download
python --version   # Must be 3.9+
conda --version
```

### Step 2 – Create Scraper Environment

```bash
conda create -n scrapers python=3.10 -y
conda activate scrapers
```

### Step 3 – Install Scraping Libraries

```bash
pip install beautifulsoup4 requests httpx selenium webdriver-manager
pip install lxml html5lib pandas numpy openpyxl
pip install pdfplumber tabula-py
pip install tqdm python-dotenv schedule

pip freeze > requirements-scrapers.txt
```

### Step 4 – Test Selenium

```bash
pip install webdriver-manager

python << 'EOF'
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument('--headless')   # No visible window needed for testing
service = Service(ChromeDriverManager().install())
driver = webdriver.Chrome(service=service, options=options)
driver.get('https://www.google.com')
print("✅ Selenium working! Title:", driver.title)
driver.quit()
EOF
```

### Step 5 – Create Project Structure

```bash
mkdir -p ~/projects/buildmat-scrapers
cd ~/projects/buildmat-scrapers
mkdir -p scrapers/suppliers data/{raw,processed,archive} logs config tests

touch scrapers/__init__.py scrapers/base_scraper.py
touch config/suppliers.json .env
```

### Scraper Setup Checklist ✅
- [ ] `scrapers` conda environment created and activated
- [ ] All packages installed; `pip list` shows beautifulsoup4, selenium, pdfplumber
- [ ] Selenium headless test passes
- [ ] Project structure created

---

## PART 2: Flutter Environment

### Step 6 – Install Flutter SDK

```bash
# Download from: https://flutter.dev/docs/get-started/install

# macOS/Linux — add to ~/.bashrc or ~/.zshrc:
export PATH="$PATH:$HOME/flutter/bin"
source ~/.bashrc

flutter --version
flutter doctor    # Fix any issues shown here before continuing
```

### Step 7 – Android Setup

```bash
# Install Android Studio: https://developer.android.com/studio
# During install: Android SDK + Android Virtual Device (AVD)

flutter doctor --android-licenses   # Type 'y' to accept all
```

### Step 8 – iOS Setup (macOS only)

```bash
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch
sudo gem install cocoapods
flutter doctor   # Should show no iOS issues
```

### Step 9 – Create Flutter Project

```bash
mkdir -p ~/projects/buildmat-mobile
cd ~/projects/buildmat-mobile
flutter create buildmat_app
cd buildmat_app
flutter run   # Verify default app launches on emulator/device
```

### Step 10 – Install Flutter Packages

Add all dependencies to `pubspec.yaml` (see Step 11), then:

```bash
flutter pub get
flutter pub add --dev build_runner hive_generator
```

### Step 11 – Complete pubspec.yaml

```yaml
name: buildmat_app
description: Building Materials Price Intelligence Mobile App
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter

  # UI & fonts
  cupertino_icons: ^1.0.6
  google_fonts: ^6.1.0

  # State management
  provider: ^6.1.2

  # Networking
  http: ^1.2.0

  # Local storage
  hive: ^2.2.3
  hive_flutter: ^1.1.0
  shared_preferences: ^2.2.3
  flutter_secure_storage: ^9.2.2

  # Charts — used in PriceHistoryChart and SupplierComparisonWidget
  fl_chart: ^0.68.0

  # Images
  cached_network_image: ^3.3.1

  # Utilities
  intl: ^0.19.0

  # Device features
  geolocator: ^11.0.0
  permission_handler: ^11.3.1
  local_auth: ^2.3.0

  # Barcode scanning
  mobile_scanner: ^5.1.1   # Replaces flutter_barcode_scanner (unmaintained)

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^4.0.0
  build_runner: ^2.4.9
  hive_generator: ^2.0.1

flutter:
  uses-material-design: true
  assets:
    - assets/images/
    - assets/icons/
```

> **Note:** `flutter_barcode_scanner` is no longer maintained — replaced with `mobile_scanner` which supports both Android and iOS and is actively developed.

### Step 12 – Create Project Structure

```bash
mkdir -p lib/{models,services,screens,widgets,utils,providers}
touch lib/utils/constants.dart
touch lib/services/api_service.dart
touch lib/services/hive_storage.dart
touch lib/models/price_record.dart
touch lib/screens/material_list_screen.dart
touch lib/widgets/price_card.dart
touch lib/widgets/price_history_chart.dart
touch lib/widgets/supplier_comparison_widget.dart
touch lib/providers/price_provider.dart
```

### Step 13 – App Constants

```dart
// lib/utils/constants.dart
class AppConstants {
  // Android emulator uses 10.0.2.2 to reach host machine's localhost
  // iOS simulator uses localhost directly
  static const String apiBaseUrl = String.fromEnvironment(
    'API_BASE_URL',
    defaultValue: 'http://10.0.2.2:3000/api',
  );

  static const String appName = 'BuildMat';
  static const String appVersion = '1.0.0';

  static const int cacheExpiryHours = 24;
  static const double borderRadius = 10.0;
  static const double spacing = 16.0;
}
```

### Step 14 – Android Permissions

Open `android/app/src/main/AndroidManifest.xml` and add inside `<manifest>` before `<application>`:

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.CAMERA"/>
<uses-permission android:name="android.permission.USE_BIOMETRIC"/>
<uses-permission android:name="android.permission.USE_FINGERPRINT"/>
```

### Flutter Setup Checklist ✅
- [ ] `flutter doctor` shows no critical issues
- [ ] Android Studio + licenses accepted
- [ ] Flutter project created and default app runs
- [ ] All packages installed (`flutter pub get` succeeds)
- [ ] `fl_chart`, `mobile_scanner`, `local_auth` all present in pubspec

### Common Troubleshooting

**`flutter` command not found:**
```bash
export PATH="$PATH:$HOME/flutter/bin"
source ~/.bashrc
```

**Gradle build fails:**
```bash
flutter clean && flutter pub get && flutter run
```

**iOS CocoaPods issues:**
```bash
cd ios && rm -rf Pods Podfile.lock && pod install && cd ..
flutter clean && flutter run
```

**Selenium browser doesn't open:**
```bash
pip install --upgrade webdriver-manager
# Use headless mode — add options.add_argument('--headless') as shown in Step 4
```

---

## 📊 Data Flow Architecture

> **Key decision:** Scrapers post directly to the backend API. Member 2 (ML) reads from the database — not from CSV files or the Excel workbook.

```
Scraper (Member 4)
    │
    │  POST /api/data/bulk-import
    │  JSON array of 16-column records
    ▼
Backend API (Member 1)
    │
    │  Writes to PostgreSQL/Supabase
    ▼
Database
    │
    ├── Member 2 (ML) reads via DB query or API
    └── Member 3/4 (Mobile) reads via GET /api/prices
```

The synthetic `SA_Building_Materials_Dataset.xlsx` is the **training seed** — it pre-populates the database. As scrapers collect real data, they POST to the API and the database grows. Member 2's models retrain on the live database, not the Excel file.

---

## 📋 Canonical Schema (16 Columns)

Every scraper record and every API payload must match exactly:

```
record_id, date, year, month, material_name, material_category,
supplier_name, region, province, price_zar, unit, price_per_kg_zar,
price_change_mom_pct, price_change_yoy_pct, stock_status, bulk_discount_available
```

Valid enum values:
- `stock_status`: `"In Stock"` | `"Low Stock"` | `"Out of Stock"`
- `bulk_discount_available`: `"Yes"` | `"No"`

---

## 🐍 Python — Scraper Infrastructure

### Base Scraper

```python
# scrapers/base_scraper.py
import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime
import hashlib
import logging
import time
import re
from typing import List, Dict, Optional


class BaseScraper:
    """
    Base class for all supplier scrapers.
    Enforces the canonical 16-column schema on every record.
    """

    SCHEMA_COLUMNS = [
        'record_id', 'date', 'year', 'month', 'material_name', 'material_category',
        'supplier_name', 'region', 'province', 'price_zar', 'unit', 'price_per_kg_zar',
        'price_change_mom_pct', 'price_change_yoy_pct', 'stock_status', 'bulk_discount_available'
    ]

    VALID_STOCK   = {'In Stock', 'Low Stock', 'Out of Stock'}
    VALID_BULK    = {'Yes', 'No'}

    CATEGORY_MAP = {
        'cement': 'Cement', 'plaster': 'Cement',
        'steel': 'Steel', 'rebar': 'Steel', 'iron': 'Steel',
        'timber': 'Timber', 'pine': 'Timber', 'wood': 'Timber',
        'copper': 'Electrical', 'cable': 'Electrical', 'conduit': 'Electrical',
        'pvc': 'Building', 'pipe': 'Building', 'brick': 'Building',
        'sand': 'Building', 'stone': 'Building', 'block': 'Building',
        'bitumen': 'Building',
    }

    def __init__(self, supplier_name: str, region: str, province: str):
        self.supplier_name = supplier_name
        self.region        = region
        self.province      = province

        self.session = requests.Session()
        self.session.headers.update({
            'User-Agent': ('Mozilla/5.0 (Windows NT 10.0; Win64; x64) '
                           'AppleWebKit/537.36 Chrome/120.0 Safari/537.36')
        })

        logging.basicConfig(
            filename=f'logs/{supplier_name}_{datetime.now():%Y%m%d}.log',
            level=logging.INFO,
            format='%(asctime)s %(levelname)s %(message)s'
        )
        self.logger = logging.getLogger(supplier_name)

    # ── HTTP ──────────────────────────────────────────────────────────────────

    def fetch_page(self, url: str, retries: int = 3) -> Optional[requests.Response]:
        for attempt in range(retries):
            try:
                resp = self.session.get(url, timeout=10)
                resp.raise_for_status()
                return resp
            except requests.RequestException as e:
                self.logger.warning(f"Attempt {attempt + 1} failed for {url}: {e}")
                time.sleep(2 ** attempt)
        self.logger.error(f"All {retries} attempts failed for {url}")
        return None

    def parse_html(self, content: bytes) -> BeautifulSoup:
        return BeautifulSoup(content, 'lxml')

    # ── Record helpers ────────────────────────────────────────────────────────

    def _make_record_id(self, material_name: str, date_str: str) -> str:
        raw = f"{material_name}_{self.supplier_name}_{date_str}".encode()
        suffix = hashlib.md5(raw).hexdigest()[:6].upper()
        ym = date_str[:7].replace('-', '_')
        return f"REC_{ym}_{suffix}"

    def _infer_category(self, material_name: str) -> str:
        name_lower = material_name.lower()
        for keyword, category in self.CATEGORY_MAP.items():
            if keyword in name_lower:
                return category
        return 'Building'

    def _price_per_kg(self, price: float, unit: str) -> float:
        m = re.search(r'(\d+)\s*kg', unit.lower())
        if m:
            return round(price / float(m.group(1)), 4)
        if 'ton' in unit.lower():
            return round(price / 1000, 4)
        return round(price, 4)

    def _infer_stock(self, text: Optional[str]) -> str:
        if not text:
            return 'In Stock'
        t = text.lower()
        if any(w in t for w in ['out of stock', 'unavailable', 'sold out']):
            return 'Out of Stock'
        if any(w in t for w in ['low stock', 'limited', 'few left']):
            return 'Low Stock'
        return 'In Stock'

    def _infer_bulk(self, text: Optional[str]) -> str:
        if not text:
            return 'No'
        return 'Yes' if any(p in text.lower()
                            for p in ['bulk', 'volume discount', 'wholesale']) else 'No'

    def build_record(
        self,
        material_name: str,
        price_zar: float,
        unit: str,
        stock_status: str = 'In Stock',
        bulk_discount_available: str = 'No',
        price_change_mom_pct: float = 0.0,
        price_change_yoy_pct: float = 0.0,
    ) -> Dict:
        """Build a schema-compliant record dict. Call this from every child scraper."""
        now      = datetime.now()
        date_str = now.strftime('%Y-%m-%d')

        if stock_status not in self.VALID_STOCK:
            stock_status = 'In Stock'
        if bulk_discount_available not in self.VALID_BULK:
            bulk_discount_available = 'No'

        return {
            'record_id':               self._make_record_id(material_name, date_str),
            'date':                    date_str,
            'year':                    now.year,
            'month':                   now.month,
            'material_name':           material_name,
            'material_category':       self._infer_category(material_name),
            'supplier_name':           self.supplier_name,
            'region':                  self.region,
            'province':                self.province,
            'price_zar':               round(price_zar, 2),
            'unit':                    unit,
            'price_per_kg_zar':        self._price_per_kg(price_zar, unit),
            'price_change_mom_pct':    round(price_change_mom_pct, 2),
            'price_change_yoy_pct':    round(price_change_yoy_pct, 2),
            'stock_status':            stock_status,
            'bulk_discount_available': bulk_discount_available,
        }

    def scrape(self) -> List[Dict]:
        """Override in every child scraper. Must return list of schema dicts."""
        raise NotImplementedError

    def save_csv(self, records: List[Dict], path: str):
        """Save to CSV with enforced column order (for archive/debugging only)."""
        pd.DataFrame(records, columns=self.SCHEMA_COLUMNS).to_csv(path, index=False)
        self.logger.info(f"Saved {len(records)} records to {path}")
        print(f"✅ Saved {len(records)} records → {path}")
```

### Builders Warehouse Scraper

```python
# scrapers/suppliers/builders_warehouse.py
from scrapers.base_scraper import BaseScraper
from typing import List, Dict


class BuildersWarehouseScraper(BaseScraper):
    """
    Scraper for Builders Warehouse (builders.co.za).
    Adjust CSS selectors if the site structure changes.
    """

    MATERIALS_TO_SCRAPE = [
        ('cement',  'https://www.builders.co.za/c/cement'),
        ('steel',   'https://www.builders.co.za/c/steel-reinforcing'),
        ('sand',    'https://www.builders.co.za/c/sand-gravel'),
        ('timber',  'https://www.builders.co.za/c/timber'),
    ]

    def __init__(self):
        super().__init__(
            supplier_name='Builders Warehouse',
            region='Gauteng',
            province='Johannesburg',
        )

    def _parse_category_page(self, url: str) -> List[Dict]:
        resp = self.fetch_page(url)
        if not resp:
            return []

        soup  = self.parse_html(resp.content)
        cards = soup.find_all('div', class_='product-card')  # adjust selector as needed
        records = []

        for card in cards:
            try:
                name_tag  = card.find('h3', class_='product-name')
                price_tag = card.find('span', class_='price')
                unit_tag  = card.find('span', class_='unit')
                avail_tag = card.find('span', class_='stock-status')
                desc_tag  = card.find('p',    class_='description')

                if not name_tag or not price_tag:
                    continue

                name       = name_tag.text.strip()
                price_text = price_tag.text.strip().replace('R', '').replace(',', '')
                price      = float(price_text)
                unit       = unit_tag.text.strip() if unit_tag else 'each'
                stock      = self._infer_stock(avail_tag.text if avail_tag else None)
                bulk       = self._infer_bulk(desc_tag.text  if desc_tag  else None)

                records.append(self.build_record(
                    material_name           = name,
                    price_zar               = price,
                    unit                    = unit,
                    stock_status            = stock,
                    bulk_discount_available = bulk,
                ))

            except (AttributeError, ValueError) as e:
                self.logger.warning(f"Error parsing product card: {e}")
                continue

        self.logger.info(f"Parsed {len(records)} products from {url}")
        return records

    def scrape(self) -> List[Dict]:
        all_records = []
        for label, url in self.MATERIALS_TO_SCRAPE:
            print(f"  Scraping {label}...")
            all_records.extend(self._parse_category_page(url))
        return all_records


if __name__ == '__main__':
    scraper  = BuildersWarehouseScraper()
    records  = scraper.scrape()
    scraper.save_csv(records, 'data/raw/builders_warehouse_test.csv')
    print(f"Total: {len(records)} records")
```

### Cashbuild Scraper

```python
# scrapers/suppliers/cashbuild.py
from scrapers.base_scraper import BaseScraper
from typing import List, Dict


class CashbuildScraper(BaseScraper):
    """
    Scraper for Cashbuild (cashbuild.co.za).
    Adjust selectors to match live site structure.
    """

    CATEGORIES = [
        ('cement', 'https://www.cashbuild.co.za/categories/cement'),
        ('bricks', 'https://www.cashbuild.co.za/categories/bricks'),
        ('sand',   'https://www.cashbuild.co.za/categories/sand-aggregates'),
        ('steel',  'https://www.cashbuild.co.za/categories/steel'),
    ]

    def __init__(self):
        super().__init__(
            supplier_name='Cashbuild',
            region='Gauteng',
            province='Johannesburg',
        )

    def scrape(self) -> List[Dict]:
        records = []
        for label, url in self.CATEGORIES:
            print(f"  Scraping Cashbuild {label}...")
            resp = self.fetch_page(url)
            if not resp:
                continue

            soup  = self.parse_html(resp.content)
            items = soup.find_all('div', class_='product-item')  # adjust selector

            for item in items:
                try:
                    name  = item.find('span', class_='product-title').text.strip()
                    price = float(item.find('span', class_='price').text
                                  .replace('R', '').replace(',', '').strip())
                    unit  = item.find('span', class_='pack-size').text.strip() if \
                            item.find('span', class_='pack-size') else 'each'

                    records.append(self.build_record(
                        material_name = name,
                        price_zar     = price,
                        unit          = unit,
                    ))
                except (AttributeError, ValueError) as e:
                    self.logger.warning(f"Cashbuild parse error: {e}")
                    continue

        return records


if __name__ == '__main__':
    scraper = CashbuildScraper()
    records = scraper.scrape()
    scraper.save_csv(records, 'data/raw/cashbuild_test.csv')
```

### PDF Price List Parser

```python
# scrapers/pdf_parser.py
import pdfplumber
import pandas as pd
import re
from typing import List, Dict
from scrapers.base_scraper import BaseScraper


class PDFPriceParser(BaseScraper):
    """
    Extracts price tables from supplier PDF price lists.
    Outputs schema-compliant records.
    """

    def __init__(self, supplier_name: str, region: str, province: str):
        super().__init__(supplier_name, region, province)

    def parse_pdf(self, pdf_path: str) -> List[Dict]:
        records = []

        with pdfplumber.open(pdf_path) as pdf:
            for page_num, page in enumerate(pdf.pages, 1):
                tables = page.extract_tables()
                for table in tables:
                    if not table or len(table) < 2:
                        continue
                    for row in table[1:]:    # skip header row
                        if not row or len(row) < 3:
                            continue
                        try:
                            material   = str(row[0]).strip()
                            unit       = str(row[1]).strip() if row[1] else 'each'
                            price_str  = str(row[2]).strip()

                            m = re.search(r'[\d,]+\.?\d*', price_str)
                            if not m:
                                continue

                            price = float(m.group().replace(',', ''))

                            if not (1 <= price <= 500_000):
                                continue    # sanity filter

                            records.append(self.build_record(
                                material_name = material,
                                price_zar     = price,
                                unit          = unit,
                            ))
                        except (ValueError, IndexError) as e:
                            self.logger.warning(f"PDF row parse error page {page_num}: {e}")
                            continue

        print(f"✅ Extracted {len(records)} records from {pdf_path}")
        return records

    def scrape(self) -> List[Dict]:
        raise NotImplementedError("Use parse_pdf(path) directly for PDF parsing.")


# Usage
if __name__ == '__main__':
    parser  = PDFPriceParser('PPC Cement', 'Gauteng', 'Johannesburg')
    records = parser.parse_pdf('data/raw/ppc_price_list_march2026.pdf')
    parser.save_csv(records, 'data/raw/ppc_pdf_extracted.csv')
```

### Data Validator

```python
# scrapers/data_validator.py
import pandas as pd
from typing import List, Tuple

REQUIRED_COLUMNS = [
    'record_id', 'date', 'year', 'month', 'material_name', 'material_category',
    'supplier_name', 'region', 'province', 'price_zar', 'unit', 'price_per_kg_zar',
    'price_change_mom_pct', 'price_change_yoy_pct', 'stock_status', 'bulk_discount_available'
]
NUMERIC_COLUMNS = ['year', 'month', 'price_zar', 'price_per_kg_zar',
                   'price_change_mom_pct', 'price_change_yoy_pct']
VALID_STOCK = {'In Stock', 'Low Stock', 'Out of Stock'}
VALID_BULK  = {'Yes', 'No'}


def validate(df: pd.DataFrame) -> Tuple[bool, List[str]]:
    errors = []

    missing = set(REQUIRED_COLUMNS) - set(df.columns)
    if missing:
        errors.append(f"Missing columns: {missing}")
        return False, errors   # can't continue without required columns

    for col in NUMERIC_COLUMNS:
        if not pd.api.types.is_numeric_dtype(df[col]):
            errors.append(f"'{col}' must be numeric, got {df[col].dtype}")

    bad_stock = set(df['stock_status'].dropna().unique()) - VALID_STOCK
    if bad_stock:
        errors.append(f"Invalid stock_status values: {bad_stock}")

    bad_bulk = set(df['bulk_discount_available'].dropna().unique()) - VALID_BULK
    if bad_bulk:
        errors.append(f"Invalid bulk_discount_available values: {bad_bulk}")

    try:
        pd.to_datetime(df['date'])
    except Exception as e:
        errors.append(f"Invalid date format: {e}")

    nulls = df[REQUIRED_COLUMNS].isnull().sum()
    if nulls.any():
        errors.append(f"Null values: {nulls[nulls > 0].to_dict()}")

    if (df['price_zar'] < 0).any():
        errors.append("Negative prices found")
    if (df['price_zar'] > 500_000).any():
        errors.append("Suspiciously high prices (>R500,000) — check data")

    return len(errors) == 0, errors


def validate_and_report(records_or_path) -> bool:
    """
    Pass either a list of dicts, a DataFrame, or a CSV filepath.
    Prints a validation report and returns True if valid.
    """
    if isinstance(records_or_path, str):
        df = pd.read_csv(records_or_path)
    elif isinstance(records_or_path, list):
        df = pd.DataFrame(records_or_path)
    else:
        df = records_or_path

    is_valid, errors = validate(df)

    print("=" * 50)
    print(f"VALIDATION: {'✅ PASSED' if is_valid else '❌ FAILED'}")
    print(f"Records: {len(df):,}")
    if not is_valid:
        for i, e in enumerate(errors, 1):
            print(f"  {i}. {e}")
    else:
        print(f"Materials: {df['material_name'].nunique()}")
        print(f"Suppliers: {df['supplier_name'].nunique()}")
        print(f"Dates:     {df['date'].min()} → {df['date'].max()}")
    print("=" * 50)
    return is_valid
```

### API Uploader — Posts Directly to Backend

```python
# scrapers/api_uploader.py
"""
Uploads validated scraper records directly to the backend API.
Member 1's POST /api/data/bulk-import endpoint writes to the database.
Member 2 (ML) then reads from the database — NOT from CSV files.
"""
import requests
import time
from typing import List, Dict
from scrapers.data_validator import validate_and_report


class APIUploader:
    def __init__(self, api_base_url: str = 'http://localhost:3000/api',
                 api_token: str = ''):
        self.bulk_endpoint = f"{api_base_url}/data/bulk-import"
        self.headers = {
            'Content-Type': 'application/json',
            **(({'Authorization': f'Bearer {api_token}'}) if api_token else {}),
        }

    def upload(self, records: List[Dict],
               batch_size: int = 100) -> Dict:
        """
        Validate then upload records in batches.
        Backend expects a JSON array of 16-column records.
        Returns summary of what was uploaded.
        """
        if not validate_and_report(records):
            print("❌ Upload aborted — validation failed. Fix errors above.")
            return {'uploaded': 0, 'total': len(records), 'success': False}

        total     = len(records)
        uploaded  = 0
        failed    = 0

        for i in range(0, total, batch_size):
            batch = records[i : i + batch_size]
            try:
                resp = requests.post(
                    self.bulk_endpoint,
                    json=batch,           # raw JSON array — no wrapper key
                    headers=self.headers,
                    timeout=30,
                )
                resp.raise_for_status()
                result    = resp.json()
                imported  = result.get('imported', len(batch))
                uploaded += imported
                print(f"  Batch {i // batch_size + 1}: {imported}/{len(batch)} imported "
                      f"({result.get('skipped', 0)} duplicates skipped)")
            except requests.RequestException as e:
                failed += len(batch)
                print(f"  Batch {i // batch_size + 1}: FAILED — {e}")

            time.sleep(0.3)   # polite rate limiting

        print(f"\n✅ Upload complete: {uploaded}/{total} records imported, "
              f"{failed} failed")
        return {'uploaded': uploaded, 'total': total,
                'failed': failed, 'success': failed == 0}


# Usage
if __name__ == '__main__':
    from scrapers.suppliers.builders_warehouse import BuildersWarehouseScraper

    scraper  = BuildersWarehouseScraper()
    records  = scraper.scrape()

    uploader = APIUploader(api_base_url='http://localhost:3000/api')
    uploader.upload(records)
```

### Scheduler — Daily Automated Scraping

```python
# scrapers/scheduler.py
import schedule
import time
import logging
from datetime import datetime
from scrapers.suppliers.builders_warehouse import BuildersWarehouseScraper
from scrapers.suppliers.cashbuild import CashbuildScraper
from scrapers.api_uploader import APIUploader

logging.basicConfig(
    filename=f'logs/scheduler_{datetime.now():%Y%m%d}.log',
    level=logging.INFO,
    format='%(asctime)s %(levelname)s %(message)s'
)


class ScraperScheduler:
    def __init__(self, api_base_url: str = 'http://localhost:3000/api',
                 api_token: str = ''):
        # Add new scrapers here as you build them
        self.scrapers = [
            BuildersWarehouseScraper(),
            CashbuildScraper(),
        ]
        self.uploader = APIUploader(api_base_url, api_token)

    def run_all(self):
        """Scrape all suppliers and upload to backend API."""
        logging.info("Daily scrape started")
        all_records = []

        for scraper in self.scrapers:
            try:
                records = scraper.scrape()
                all_records.extend(records)
                logging.info(f"{scraper.supplier_name}: {len(records)} records")
                print(f"✅ {scraper.supplier_name}: {len(records)} records")
            except Exception as e:
                logging.error(f"{scraper.supplier_name} failed: {e}")
                print(f"❌ {scraper.supplier_name} failed: {e}")

        if all_records:
            result = self.uploader.upload(all_records)
            logging.info(f"Upload result: {result}")
        else:
            logging.warning("No records collected — nothing uploaded")

    def start(self):
        print("Scheduler started. Running immediately then daily at 02:00.")
        self.run_all()   # run once immediately

        schedule.every().day.at("02:00").do(self.run_all)
        while True:
            schedule.run_pending()
            time.sleep(60)


if __name__ == '__main__':
    import os
    scheduler = ScraperScheduler(
        api_base_url=os.getenv('API_BASE_URL', 'http://localhost:3000/api'),
        api_token=os.getenv('API_TOKEN', ''),
    )
    scheduler.start()
```

---

## 📅 WEEK 1 — March 9–15, 2026
### Data Collection Infrastructure

#### Monday (March 9) — Environment + Supplier Research

```bash
conda activate scrapers
cd ~/projects/buildmat-scrapers
```

Afternoon — research SA suppliers:
1. Identify 10+ suppliers (Builders Warehouse, Cashbuild, Pennypinchers, Buco, BuildIt…)
2. Open each site, check `robots.txt` (e.g. `builders.co.za/robots.txt`)
3. Note HTML structure — use browser DevTools to find product card selectors
4. Check if site uses JavaScript rendering (need Selenium) or static HTML (requests ok)
5. Document findings in `config/suppliers.json`

**Deliverable:** Supplier list with scraping strategy notes per site.

#### Tuesday–Wednesday (March 10–11) — First Working Scraper

Build `BuildersWarehouseScraper` (code in infrastructure section above). Test it:

```bash
python -m scrapers.suppliers.builders_warehouse
# Should print: Total: N records
# Check data/raw/builders_warehouse_test.csv
```

Then run validation:

```python
from scrapers.data_validator import validate_and_report
validate_and_report('data/raw/builders_warehouse_test.csv')
```

**Deliverable:** Working scraper for 1 supplier, passing validation.

#### Thursday–Friday (March 12–13) — More Scrapers + PDF Parser

Build `CashbuildScraper` and `PDFPriceParser`. Test all three. Then do a first live upload:

```python
from scrapers.suppliers.builders_warehouse import BuildersWarehouseScraper
from scrapers.api_uploader import APIUploader

records  = BuildersWarehouseScraper().scrape()
uploader = APIUploader('http://localhost:3000/api')
uploader.upload(records)
```

**Deliverable:** 3 working scrapers + PDF parser + first live API upload.

---

## 📅 WEEK 2 — March 16–22, 2026
### Data Cleaning & Automation

#### Monday–Tuesday (March 16–17) — Data Cleaner

```python
# scrapers/data_cleaner.py
import pandas as pd
import re
from typing import Optional

# Standard material name mappings (add more as you find variants)
MATERIAL_ALIASES = {
    'opc cement':        'PPC Cement 50kg',
    'portland cement':   'PPC Cement 50kg',
    'cement 50kg':       'PPC Cement 50kg',
    'river sand':        'River Sand',
    'plaster sand':      'Plaster Sand',
    'crusher stone':     'Crusher Stone 19mm',
    'rebar 12':          'Steel Rebar 12mm',
    'y12':               'Steel Rebar 12mm',
    'y16':               'Steel Rebar 16mm',
}


def standardise_material_name(raw: str) -> str:
    """Map scraped material name variants to canonical names."""
    raw_lower = raw.lower().strip()
    for alias, canonical in MATERIAL_ALIASES.items():
        if alias in raw_lower:
            return canonical
    return raw.strip().title()   # fallback — title-case the raw name


def clean_price(raw) -> Optional[float]:
    """Extract and validate a price from any format."""
    if pd.isna(raw):
        return None
    cleaned = str(raw).replace('R', '').replace(',', '').strip()
    try:
        price = float(cleaned)
        return price if 1 <= price <= 500_000 else None
    except ValueError:
        return None


def clean_dataframe(df: pd.DataFrame) -> pd.DataFrame:
    """
    Standardise material names and validate prices.
    Uses price_zar (canonical column name) — NOT 'price'.
    """
    df = df.copy()
    df['material_name'] = df['material_name'].apply(standardise_material_name)
    df['price_zar']     = df['price_zar'].apply(clean_price)

    before = len(df)
    df = df[df['price_zar'].notna()]
    df = df.drop_duplicates(subset=['supplier_name', 'material_name', 'date'])
    print(f"Cleaned: {before} → {len(df)} records "
          f"({before - len(df)} dropped)")
    return df
```

#### Wednesday–Thursday (March 18–19) — Scheduler + Automation

Wire up `ScraperScheduler` (code in infrastructure section). Test it runs end-to-end:

```bash
python scrapers/scheduler.py
# Should: scrape → validate → upload → confirm imported count
```

#### Friday (March 20) — Verify Data in Backend

Confirm records appear in the database via the backend's admin endpoint or directly:

```bash
curl http://localhost:3000/api/prices?limit=10
# Should return JSON array of 16-column records
```

**Deliverable:** Fully automated scrape → validate → upload pipeline running daily.

---

## 📅 WEEK 3 — March 23–29, 2026
### Flutter Setup + D1

#### Monday–Tuesday (March 23–24) — Flutter Project Init

Run Steps 9–14 from the setup section. Verify the default Flutter counter app runs on your device. Then add all packages and create the folder structure.

#### D1 Demo — Wednesday (March 25) 🎯

- Scrapers have collected 500+ real records, uploaded to backend
- Show the scheduler running and records in the DB
- Show Flutter project boots up cleanly

---

## 📱 Flutter — Data Models & Services

### PriceRecord Model (16 Fields)

```dart
// lib/models/price_record.dart

/// Matches the canonical 16-column schema exactly.
/// record_id, date, year, month, material_name, material_category,
/// supplier_name, region, province, price_zar, unit, price_per_kg_zar,
/// price_change_mom_pct, price_change_yoy_pct, stock_status, bulk_discount_available
class PriceRecord {
  final String recordId;
  final String date;
  final int    year;
  final int    month;
  final String materialName;
  final String materialCategory;
  final String supplierName;
  final String region;
  final String province;
  final double priceZar;
  final String unit;
  final double pricePerKgZar;
  final double priceChangeMomPct;
  final double priceChangeYoyPct;
  final String stockStatus;             // "In Stock" | "Low Stock" | "Out of Stock"
  final String bulkDiscountAvailable;   // "Yes" | "No"

  const PriceRecord({
    required this.recordId,
    required this.date,
    required this.year,
    required this.month,
    required this.materialName,
    required this.materialCategory,
    required this.supplierName,
    required this.region,
    required this.province,
    required this.priceZar,
    required this.unit,
    required this.pricePerKgZar,
    required this.priceChangeMomPct,
    required this.priceChangeYoyPct,
    required this.stockStatus,
    required this.bulkDiscountAvailable,
  });

  factory PriceRecord.fromJson(Map<String, dynamic> j) => PriceRecord(
    recordId:              j['record_id']               as String,
    date:                  j['date']                    as String,
    year:                  j['year']                    as int,
    month:                 j['month']                   as int,
    materialName:          j['material_name']           as String,
    materialCategory:      j['material_category']       as String,
    supplierName:          j['supplier_name']           as String,
    region:                j['region']                  as String,
    province:              j['province']                as String,
    priceZar:              (j['price_zar']              as num).toDouble(),
    unit:                  j['unit']                    as String,
    pricePerKgZar:         (j['price_per_kg_zar']       as num).toDouble(),
    priceChangeMomPct:     (j['price_change_mom_pct']   as num).toDouble(),
    priceChangeYoyPct:     (j['price_change_yoy_pct']   as num).toDouble(),
    stockStatus:           j['stock_status']            as String,
    bulkDiscountAvailable: j['bulk_discount_available'] as String,
  );

  Map<String, dynamic> toJson() => {
    'record_id':               recordId,
    'date':                    date,
    'year':                    year,
    'month':                   month,
    'material_name':           materialName,
    'material_category':       materialCategory,
    'supplier_name':           supplierName,
    'region':                  region,
    'province':                province,
    'price_zar':               priceZar,
    'unit':                    unit,
    'price_per_kg_zar':        pricePerKgZar,
    'price_change_mom_pct':    priceChangeMomPct,
    'price_change_yoy_pct':    priceChangeYoyPct,
    'stock_status':            stockStatus,
    'bulk_discount_available': bulkDiscountAvailable,
  };
}
```

### Hive Offline Cache

```dart
// lib/services/hive_storage.dart
import 'package:hive_flutter/hive_flutter.dart';
import '../models/price_record.dart';

class HiveStorage {
  static const String _boxName = 'price_records';

  Future<void> init() async {
    await Hive.initFlutter();
    // Store raw JSON maps — avoids needing a generated Hive adapter
    if (!Hive.isBoxOpen(_boxName)) {
      await Hive.openBox<Map>(_boxName);
    }
  }

  Box<Map> get _box => Hive.box<Map>(_boxName);

  Future<void> cache(List<PriceRecord> records) async {
    await _box.clear();
    final entries = {
      for (final r in records) r.recordId: r.toJson()
    };
    await _box.putAll(entries);
  }

  List<PriceRecord> getCached() {
    return _box.values
        .map((m) => PriceRecord.fromJson(Map<String, dynamic>.from(m)))
        .toList();
  }

  bool get hasCachedData => _box.isNotEmpty;

  Future<void> clear() => _box.clear();
}
```

### API Service

```dart
// lib/services/api_service.dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import '../models/price_record.dart';
import '../services/hive_storage.dart';
import '../utils/constants.dart';

class ApiService {
  final HiveStorage _cache = HiveStorage();
  String? _token;

  void setToken(String token) => _token = token;

  Map<String, String> get _headers => {
    'Content-Type': 'application/json',
    if (_token != null) 'Authorization': 'Bearer $_token',
  };

  /// Fetch all price records. Falls back to Hive cache if API is unreachable.
  /// No materialId required — filterable on the screen level.
  Future<List<PriceRecord>> getPrices({
    String? materialName,
    String? region,
    int limit = 200,
  }) async {
    final uri = Uri.parse('${AppConstants.apiBaseUrl}/prices').replace(
      queryParameters: {
        if (materialName != null) 'material_name': materialName,
        if (region       != null) 'region':        region,
        'limit': '$limit',
      },
    );

    try {
      final resp = await http.get(uri, headers: _headers)
                             .timeout(const Duration(seconds: 10));

      if (resp.statusCode == 200) {
        final List<dynamic> data = json.decode(resp.body);
        final records = data.map((j) => PriceRecord.fromJson(j)).toList();
        await _cache.cache(records);   // keep cache fresh
        return records;
      } else {
        throw Exception('API returned ${resp.statusCode}');
      }
    } catch (e) {
      // Graceful fallback — user sees stale data with a banner, not a crash
      if (_cache.hasCachedData) {
        print('API unavailable, using cached data: $e');
        return _cache.getCached();
      }
      rethrow;
    }
  }

  Future<Map<String, dynamic>> getForecast(
      String materialName, int days) async {
    final resp = await http.get(
      Uri.parse('${AppConstants.apiBaseUrl}/forecast'
                '?material_name=${Uri.encodeComponent(materialName)}&days=$days'),
      headers: _headers,
    ).timeout(const Duration(seconds: 15));

    if (resp.statusCode == 200) {
      return json.decode(resp.body) as Map<String, dynamic>;
    }
    throw Exception('Forecast request failed: ${resp.statusCode}');
  }
}
```

---

## 📱 Flutter — UI Components

### PriceCard Widget

```dart
// lib/widgets/price_card.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import '../models/price_record.dart';

class PriceCard extends StatelessWidget {
  final PriceRecord record;
  const PriceCard({super.key, required this.record});

  Color get _stockColor => switch (record.stockStatus) {
    'In Stock'     => const Color(0xFF16a34a),
    'Low Stock'    => const Color(0xFFf59e0b),
    'Out of Stock' => const Color(0xFFdc2626),
    _              => const Color(0xFF6b7280),
  };

  Color get _stockBg => switch (record.stockStatus) {
    'In Stock'     => const Color(0xFFdcfce7),
    'Low Stock'    => const Color(0xFFfef3c7),
    'Out of Stock' => const Color(0xFFfee2e2),
    _              => const Color(0xFFf3f4f6),
  };

  @override
  Widget build(BuildContext context) {
    return Card(
      elevation: 2,
      shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(10)),
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // Material name + category
            Text(record.materialName,
                style: GoogleFonts.dmSans(
                    fontSize: 18, fontWeight: FontWeight.w700,
                    color: const Color(0xFF111827))),
            const SizedBox(height: 4),
            Text(record.materialCategory,
                style: GoogleFonts.dmSans(
                    fontSize: 12, color: const Color(0xFF6b7280))),
            const SizedBox(height: 12),

            // Price
            Row(
              crossAxisAlignment: CrossAxisAlignment.baseline,
              textBaseline: TextBaseline.alphabetic,
              children: [
                Text('R${record.priceZar.toStringAsFixed(2)}',
                    style: GoogleFonts.dmSans(
                        fontSize: 28, fontWeight: FontWeight.w700,
                        color: const Color(0xFF2563eb))),
                const SizedBox(width: 8),
                Text('/ ${record.unit}',
                    style: GoogleFonts.dmSans(
                        fontSize: 14, color: const Color(0xFF6b7280))),
              ],
            ),
            Text('R${record.pricePerKgZar.toStringAsFixed(2)} per kg',
                style: GoogleFonts.dmSans(
                    fontSize: 12, color: const Color(0xFF6b7280))),
            const SizedBox(height: 12),

            // MoM / YoY change chips
            Row(children: [
              _changeChip(
                  'MoM ${record.priceChangeMomPct >= 0 ? '+' : ''}'
                  '${record.priceChangeMomPct.toStringAsFixed(1)}%',
                  record.priceChangeMomPct >= 0),
              const SizedBox(width: 8),
              _changeChip(
                  'YoY ${record.priceChangeYoyPct >= 0 ? '+' : ''}'
                  '${record.priceChangeYoyPct.toStringAsFixed(1)}%',
                  record.priceChangeYoyPct >= 0),
            ]),
            const SizedBox(height: 12),

            // Stock badge + bulk discount badge
            Row(children: [
              _badge(record.stockStatus, _stockColor, _stockBg),
              if (record.bulkDiscountAvailable == 'Yes') ...[
                const SizedBox(width: 8),
                _badge('Bulk Discount',
                    const Color(0xFF2563eb), const Color(0xFFeff6ff)),
              ],
            ]),
            const SizedBox(height: 12),

            // Supplier + location
            Row(children: [
              const Icon(Icons.store, size: 14,
                  color: Color(0xFF6b7280)),
              const SizedBox(width: 4),
              Expanded(
                child: Text(
                    '${record.supplierName} • '
                    '${record.province}, ${record.region}',
                    style: GoogleFonts.dmSans(
                        fontSize: 12, color: const Color(0xFF6b7280)),
                    overflow: TextOverflow.ellipsis),
              ),
            ]),
          ],
        ),
      ),
    );
  }

  Widget _changeChip(String label, bool isIncrease) => Container(
    padding: const EdgeInsets.symmetric(horizontal: 8, vertical: 3),
    decoration: BoxDecoration(
      // Red = price went up (bad for buyer); green = price went down (good)
      color: isIncrease
          ? const Color(0xFFfee2e2) : const Color(0xFFdcfce7),
      borderRadius: BorderRadius.circular(8),
    ),
    child: Text(label,
        style: GoogleFonts.dmSans(
            fontSize: 10, fontWeight: FontWeight.w600,
            color: isIncrease
                ? const Color(0xFFdc2626) : const Color(0xFF16a34a))),
  );

  Widget _badge(String label, Color fg, Color bg) => Container(
    padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 4),
    decoration: BoxDecoration(
        color: bg, borderRadius: BorderRadius.circular(12)),
    child: Text(label,
        style: GoogleFonts.dmSans(
            fontSize: 11, fontWeight: FontWeight.w600, color: fg)),
  );
}
```

### PriceHistoryChart (fl_chart 0.68)

```dart
// lib/widgets/price_history_chart.dart
import 'package:flutter/material.dart';
import 'package:fl_chart/fl_chart.dart';
import 'package:google_fonts/google_fonts.dart';
import '../models/price_record.dart';

class PriceHistoryChart extends StatelessWidget {
  final List<PriceRecord> history;
  final String title;

  const PriceHistoryChart(
      {super.key, required this.history, this.title = 'Price History'});

  @override
  Widget build(BuildContext context) {
    if (history.isEmpty) {
      return const Center(child: Text('No price history data'));
    }

    final sorted = List<PriceRecord>.from(history)
      ..sort((a, b) => a.date.compareTo(b.date));

    final spots = sorted.asMap().entries
        .map((e) => FlSpot(e.key.toDouble(), e.value.priceZar))
        .toList();

    final prices = sorted.map((r) => r.priceZar);
    final minY   = prices.reduce((a, b) => a < b ? a : b) * 0.95;
    final maxY   = prices.reduce((a, b) => a > b ? a : b) * 1.05;

    return Card(
      elevation: 2,
      shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(10)),
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(title,
                style: GoogleFonts.dmSans(
                    fontSize: 18, fontWeight: FontWeight.w700,
                    color: const Color(0xFF111827))),
            const SizedBox(height: 16),
            SizedBox(
              height: 250,
              child: LineChart(LineChartData(
                minY: minY,
                maxY: maxY,
                gridData: FlGridData(
                  drawVerticalLine: false,
                  getDrawingHorizontalLine: (_) => const FlLine(
                      color: Color(0xFFe5e7eb), strokeWidth: 1),
                ),
                borderData: FlBorderData(show: false),
                titlesData: FlTitlesData(
                  rightTitles: const AxisTitles(
                      sideTitles: SideTitles(showTitles: false)),
                  topTitles: const AxisTitles(
                      sideTitles: SideTitles(showTitles: false)),
                  leftTitles: AxisTitles(
                    sideTitles: SideTitles(
                      showTitles: true,
                      reservedSize: 52,
                      getTitlesWidget: (v, _) => Text(
                          'R${v.toStringAsFixed(0)}',
                          style: GoogleFonts.dmSans(
                              fontSize: 10,
                              color: const Color(0xFF6b7280))),
                    ),
                  ),
                  bottomTitles: AxisTitles(
                    sideTitles: SideTitles(
                      showTitles: true,
                      reservedSize: 30,
                      getTitlesWidget: (v, _) {
                        final i = v.toInt();
                        if (i < 0 || i >= sorted.length) {
                          return const SizedBox.shrink();
                        }
                        final parts = sorted[i].date.split('-');
                        return Text(
                            parts.length == 3
                                ? '${parts[1]}/${parts[2]}' : '',
                            style: GoogleFonts.dmSans(
                                fontSize: 10,
                                color: const Color(0xFF6b7280)));
                      },
                    ),
                  ),
                ),
                lineBarsData: [
                  LineChartBarData(
                    spots: spots,
                    isCurved: true,
                    color: const Color(0xFF2563eb),
                    barWidth: 3,
                    dotData: FlDotData(
                      getDotPainter: (_, __, ___, ____) =>
                          FlDotCirclePainter(
                              radius: 4,
                              color: const Color(0xFF2563eb),
                              strokeWidth: 2,
                              strokeColor: Colors.white),
                    ),
                    belowBarData: BarAreaData(
                      show: true,
                      color: const Color(0xFF2563eb).withOpacity(0.1),
                    ),
                  ),
                ],
                lineTouchData: LineTouchData(
                  touchTooltipData: LineTouchTooltipData(
                    // fl_chart ≥ 0.66: use getTooltipColor, not tooltipBgColor
                    getTooltipColor: (_) => Colors.white,
                    getTooltipItems: (spots) => spots.map((s) {
                      final r = sorted[s.x.toInt()];
                      return LineTooltipItem(
                          'R${r.priceZar.toStringAsFixed(2)}\n${r.date}',
                          GoogleFonts.dmSans(
                              color: const Color(0xFF111827),
                              fontWeight: FontWeight.w600,
                              fontSize: 12));
                    }).toList(),
                  ),
                ),
              )),
            ),
          ],
        ),
      ),
    );
  }
}
```

### SupplierComparisonWidget (fl_chart 0.68)

```dart
// lib/widgets/supplier_comparison_widget.dart
import 'package:flutter/material.dart';
import 'package:fl_chart/fl_chart.dart';
import 'package:google_fonts/google_fonts.dart';
import '../models/price_record.dart';

class SupplierComparisonWidget extends StatelessWidget {
  final List<PriceRecord> supplierPrices;
  final String materialName;

  const SupplierComparisonWidget(
      {super.key, required this.supplierPrices, required this.materialName});

  @override
  Widget build(BuildContext context) {
    if (supplierPrices.isEmpty) {
      return const Center(child: Text('No supplier data'));
    }

    final sorted = List<PriceRecord>.from(supplierPrices)
      ..sort((a, b) => a.priceZar.compareTo(b.priceZar));

    final maxY = sorted.last.priceZar * 1.15;

    return Card(
      elevation: 2,
      shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(10)),
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('Supplier Comparison: $materialName',
                style: GoogleFonts.dmSans(
                    fontSize: 18, fontWeight: FontWeight.w700,
                    color: const Color(0xFF111827))),
            const SizedBox(height: 16),
            SizedBox(
              height: 300,
              child: BarChart(BarChartData(
                alignment: BarChartAlignment.spaceAround,
                maxY: maxY,
                gridData: FlGridData(
                  drawVerticalLine: false,
                  getDrawingHorizontalLine: (_) => const FlLine(
                      color: Color(0xFFe5e7eb), strokeWidth: 1),
                ),
                borderData: FlBorderData(show: false),
                titlesData: FlTitlesData(
                  topTitles: const AxisTitles(
                      sideTitles: SideTitles(showTitles: false)),
                  rightTitles: const AxisTitles(
                      sideTitles: SideTitles(showTitles: false)),
                  leftTitles: AxisTitles(
                    sideTitles: SideTitles(
                      showTitles: true,
                      reservedSize: 52,
                      getTitlesWidget: (v, _) => Text(
                          'R${v.toStringAsFixed(0)}',
                          style: GoogleFonts.dmSans(
                              fontSize: 10,
                              color: const Color(0xFF6b7280))),
                    ),
                  ),
                  bottomTitles: AxisTitles(
                    sideTitles: SideTitles(
                      showTitles: true,
                      reservedSize: 60,
                      getTitlesWidget: (v, _) {
                        final i = v.toInt();
                        if (i < 0 || i >= sorted.length) {
                          return const SizedBox.shrink();
                        }
                        return Padding(
                          padding: const EdgeInsets.only(top: 8),
                          child: RotatedBox(
                            quarterTurns: 1,
                            child: Text(sorted[i].supplierName,
                                style: GoogleFonts.dmSans(
                                    fontSize: 10,
                                    color: const Color(0xFF6b7280)),
                                overflow: TextOverflow.ellipsis),
                          ),
                        );
                      },
                    ),
                  ),
                ),
                barTouchData: BarTouchData(
                  touchTooltipData: BarTouchTooltipData(
                    // fl_chart ≥ 0.66: use getTooltipColor, not tooltipBgColor
                    getTooltipColor: (_) => Colors.white,
                    getTooltipItem: (group, _, rod, __) {
                      final r = sorted[group.x];
                      return BarTooltipItem(
                          '${r.supplierName}\nR${r.priceZar.toStringAsFixed(2)}',
                          GoogleFonts.dmSans(
                              color: const Color(0xFF111827),
                              fontWeight: FontWeight.w600,
                              fontSize: 12));
                    },
                  ),
                ),
                barGroups: sorted.asMap().entries.map((e) {
                  final hasBulk = e.value.bulkDiscountAvailable == 'Yes';
                  return BarChartGroupData(x: e.key, barRods: [
                    BarChartRodData(
                      toY: e.value.priceZar,
                      // Green = bulk discount available; blue = standard
                      color: hasBulk
                          ? const Color(0xFF16a34a)
                          : const Color(0xFF2563eb),
                      width: 20,
                      borderRadius: const BorderRadius.vertical(
                          top: Radius.circular(4)),
                    ),
                  ]);
                }).toList(),
              )),
            ),
            const SizedBox(height: 12),
            Row(mainAxisAlignment: MainAxisAlignment.center, children: [
              _legend('Standard', const Color(0xFF2563eb)),
              const SizedBox(width: 16),
              _legend('Bulk Discount', const Color(0xFF16a34a)),
            ]),
          ],
        ),
      ),
    );
  }

  Widget _legend(String label, Color color) => Row(children: [
    Container(
        width: 16, height: 16,
        decoration: BoxDecoration(
            color: color, borderRadius: BorderRadius.circular(3))),
    const SizedBox(width: 6),
    Text(label,
        style: GoogleFonts.dmSans(
            fontSize: 11, color: const Color(0xFF6b7280))),
  ]);
}
```

### MaterialListScreen

```dart
// lib/screens/material_list_screen.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import '../models/price_record.dart';
import '../services/api_service.dart';
import '../widgets/price_card.dart';

class MaterialListScreen extends StatefulWidget {
  const MaterialListScreen({super.key});

  @override
  State<MaterialListScreen> createState() => _MaterialListScreenState();
}

class _MaterialListScreenState extends State<MaterialListScreen> {
  final _api     = ApiService();
  List<PriceRecord> _records   = [];
  bool   _loading  = true;
  String? _error;
  bool   _offline  = false;    // true when showing cached data

  @override
  void initState() {
    super.initState();
    _load();
  }

  Future<void> _load() async {
    setState(() { _loading = true; _error = null; _offline = false; });
    try {
      final records = await _api.getPrices();
      setState(() { _records = records; _loading = false; });
    } on Exception catch (e) {
      // Check if we have cached data
      final cached = await _api.getPrices();   // falls back to cache internally
      if (cached.isNotEmpty) {
        setState(() {
          _records = cached;
          _loading = false;
          _offline = true;
        });
      } else {
        setState(() { _error = e.toString(); _loading = false; });
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: const Color(0xFFf9fafb),
      appBar: AppBar(
        title: Text('Building Materials',
            style: GoogleFonts.dmSans(
                fontWeight: FontWeight.w700, color: Colors.white)),
        backgroundColor: const Color(0xFF1e3a8a),
        elevation: 0,
      ),
      body: Column(children: [
        if (_offline)
          MaterialBanner(
            content: Text('Showing cached data — offline mode',
                style: GoogleFonts.dmSans(fontSize: 13)),
            actions: [
              TextButton(onPressed: _load, child: const Text('Retry'))
            ],
            backgroundColor: const Color(0xFFfef3c7),
          ),
        Expanded(child: _buildBody()),
      ]),
    );
  }

  Widget _buildBody() {
    if (_loading) return const Center(child: CircularProgressIndicator());
    if (_error != null) {
      return Center(child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Icon(Icons.error_outline, size: 64, color: Colors.red[300]),
          const SizedBox(height: 16),
          Text(_error!, style: const TextStyle(color: Colors.red)),
          const SizedBox(height: 16),
          ElevatedButton(onPressed: _load, child: const Text('Retry')),
        ],
      ));
    }
    return RefreshIndicator(
      onRefresh: _load,
      child: ListView.builder(
        padding: const EdgeInsets.all(16),
        itemCount: _records.length,
        itemBuilder: (_, i) => Padding(
          padding: const EdgeInsets.only(bottom: 16),
          child: PriceCard(record: _records[i]),
        ),
      ),
    );
  }
}
```

---

## 📅 WEEKS 4–5 — March 30–April 12, 2026
### Core Mobile Features + ST1/D2

#### MaterialDetailScreen (Week 4, Monday–Tuesday)

```dart
// lib/screens/material_detail_screen.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import '../models/price_record.dart';
import '../services/api_service.dart';
import '../widgets/price_history_chart.dart';
import '../widgets/supplier_comparison_widget.dart';
import '../widgets/price_card.dart';

class MaterialDetailScreen extends StatefulWidget {
  final String materialName;
  const MaterialDetailScreen({super.key, required this.materialName});

  @override
  State<MaterialDetailScreen> createState() => _MaterialDetailScreenState();
}

class _MaterialDetailScreenState extends State<MaterialDetailScreen>
    with SingleTickerProviderStateMixin {
  final _api = ApiService();
  late TabController _tabs;

  List<PriceRecord> _history   = [];
  List<PriceRecord> _suppliers = [];
  bool _loading = true;
  String? _error;

  @override
  void initState() {
    super.initState();
    _tabs = TabController(length: 2, vsync: this);
    _load();
  }

  @override
  void dispose() {
    _tabs.dispose();
    super.dispose();
  }

  Future<void> _load() async {
    setState(() { _loading = true; _error = null; });
    try {
      final results = await Future.wait([
        _api.getPrices(materialName: widget.materialName),
        _api.getPrices(materialName: widget.materialName, limit: 200),
      ]);
      setState(() {
        _history   = results[0];
        _suppliers = results[1];
        _loading   = false;
      });
    } catch (e) {
      setState(() { _error = e.toString(); _loading = false; });
    }
  }

  @override
  Widget build(BuildContext context) {
    final latest = _history.isNotEmpty ? _history.first : null;

    return Scaffold(
      backgroundColor: const Color(0xFFf9fafb),
      appBar: AppBar(
        title: Text(widget.materialName,
            style: GoogleFonts.dmSans(
                fontWeight: FontWeight.w700, color: Colors.white),
            overflow: TextOverflow.ellipsis),
        backgroundColor: const Color(0xFF1e3a8a),
        iconTheme: const IconThemeData(color: Colors.white),
        bottom: TabBar(
          controller: _tabs,
          indicatorColor: Colors.white,
          labelColor: Colors.white,
          unselectedLabelColor: Colors.white60,
          tabs: const [
            Tab(text: 'Price History'),
            Tab(text: 'Suppliers'),
          ],
        ),
      ),
      body: _loading
          ? const Center(child: CircularProgressIndicator())
          : _error != null
              ? Center(child: Column(
                  mainAxisAlignment: MainAxisAlignment.center,
                  children: [
                    Text(_error!, style: const TextStyle(color: Colors.red)),
                    const SizedBox(height: 16),
                    ElevatedButton(onPressed: _load, child: const Text('Retry')),
                  ],
                ))
              : Column(children: [
                  // Stat header
                  if (latest != null)
                    Container(
                      color: const Color(0xFF1e3a8a),
                      padding: const EdgeInsets.fromLTRB(16, 0, 16, 16),
                      child: Row(
                        mainAxisAlignment: MainAxisAlignment.spaceBetween,
                        children: [
                          Column(crossAxisAlignment: CrossAxisAlignment.start, children: [
                            Text('R${latest.priceZar.toStringAsFixed(2)}',
                                style: GoogleFonts.dmSans(
                                    fontSize: 32, fontWeight: FontWeight.w700,
                                    color: Colors.white)),
                            Text(latest.unit,
                                style: GoogleFonts.dmSans(
                                    fontSize: 13, color: Colors.white60)),
                          ]),
                          Column(crossAxisAlignment: CrossAxisAlignment.end, children: [
                            _changePill(
                                'MoM ${latest.priceChangeMomPct >= 0 ? '+' : ''}'
                                '${latest.priceChangeMomPct.toStringAsFixed(1)}%',
                                latest.priceChangeMomPct >= 0),
                            const SizedBox(height: 4),
                            _changePill(
                                'YoY ${latest.priceChangeYoyPct >= 0 ? '+' : ''}'
                                '${latest.priceChangeYoyPct.toStringAsFixed(1)}%',
                                latest.priceChangeYoyPct >= 0),
                          ]),
                        ],
                      ),
                    ),
                  // Tab views
                  Expanded(
                    child: TabBarView(
                      controller: _tabs,
                      children: [
                        // History tab
                        RefreshIndicator(
                          onRefresh: _load,
                          child: ListView(
                            padding: const EdgeInsets.all(16),
                            children: [
                              PriceHistoryChart(
                                history: _history,
                                title: 'Price History',
                              ),
                              const SizedBox(height: 16),
                              ..._history.map((r) => Padding(
                                padding: const EdgeInsets.only(bottom: 12),
                                child: PriceCard(record: r),
                              )),
                            ],
                          ),
                        ),
                        // Suppliers tab
                        RefreshIndicator(
                          onRefresh: _load,
                          child: ListView(
                            padding: const EdgeInsets.all(16),
                            children: [
                              SupplierComparisonWidget(
                                supplierPrices: _suppliers,
                                materialName: widget.materialName,
                              ),
                            ],
                          ),
                        ),
                      ],
                    ),
                  ),
                ]),
    );
  }

  Widget _changePill(String label, bool isUp) => Container(
    padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 4),
    decoration: BoxDecoration(
      color: isUp
          ? Colors.red.withOpacity(0.25)
          : Colors.green.withOpacity(0.25),
      borderRadius: BorderRadius.circular(12),
    ),
    child: Text(label,
        style: GoogleFonts.dmSans(
            fontSize: 12, fontWeight: FontWeight.w600,
            color: isUp ? Colors.red[200] : Colors.green[200])),
  );
}
```

#### Search + Filter Bar (Week 4, Wednesday)

```dart
// lib/widgets/search_filter_bar.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';

class SearchFilterBar extends StatefulWidget {
  final ValueChanged<String> onSearch;
  final ValueChanged<String?> onCategoryFilter;
  final ValueChanged<String?> onStockFilter;

  const SearchFilterBar({
    super.key,
    required this.onSearch,
    required this.onCategoryFilter,
    required this.onStockFilter,
  });

  @override
  State<SearchFilterBar> createState() => _SearchFilterBarState();
}

class _SearchFilterBarState extends State<SearchFilterBar> {
  final _ctrl       = TextEditingController();
  String? _category;
  String? _stock;

  static const _categories = ['Cement', 'Building', 'Steel', 'Timber', 'Electrical'];
  static const _stocks     = ['In Stock', 'Low Stock', 'Out of Stock'];

  @override
  void dispose() { _ctrl.dispose(); super.dispose(); }

  @override
  Widget build(BuildContext context) {
    return Column(children: [
      // Search bar
      Padding(
        padding: const EdgeInsets.fromLTRB(16, 12, 16, 8),
        child: TextField(
          controller: _ctrl,
          onChanged: widget.onSearch,
          style: GoogleFonts.dmSans(fontSize: 14),
          decoration: InputDecoration(
            hintText: 'Search materials…',
            hintStyle: GoogleFonts.dmSans(color: const Color(0xFF9ca3af)),
            prefixIcon: const Icon(Icons.search, color: Color(0xFF6b7280)),
            suffixIcon: _ctrl.text.isNotEmpty
                ? IconButton(
                    icon: const Icon(Icons.clear, size: 18),
                    onPressed: () {
                      _ctrl.clear();
                      widget.onSearch('');
                    })
                : null,
            filled: true,
            fillColor: Colors.white,
            contentPadding: const EdgeInsets.symmetric(vertical: 0, horizontal: 16),
            border: OutlineInputBorder(
                borderRadius: BorderRadius.circular(10),
                borderSide: const BorderSide(color: Color(0xFFe5e7eb))),
            enabledBorder: OutlineInputBorder(
                borderRadius: BorderRadius.circular(10),
                borderSide: const BorderSide(color: Color(0xFFe5e7eb))),
            focusedBorder: OutlineInputBorder(
                borderRadius: BorderRadius.circular(10),
                borderSide: const BorderSide(color: Color(0xFF2563eb), width: 2)),
          ),
        ),
      ),
      // Filter chips
      SizedBox(
        height: 44,
        child: ListView(
          scrollDirection: Axis.horizontal,
          padding: const EdgeInsets.symmetric(horizontal: 16),
          children: [
            // Category chips
            ..._categories.map((c) => Padding(
              padding: const EdgeInsets.only(right: 8),
              child: FilterChip(
                label: Text(c, style: GoogleFonts.dmSans(fontSize: 12)),
                selected: _category == c,
                onSelected: (_) {
                  setState(() => _category = _category == c ? null : c);
                  widget.onCategoryFilter(_category);
                },
                selectedColor: const Color(0xFFdbeafe),
                checkmarkColor: const Color(0xFF2563eb),
              ),
            )),
            const VerticalDivider(width: 16),
            // Stock chips
            ..._stocks.map((s) => Padding(
              padding: const EdgeInsets.only(right: 8),
              child: FilterChip(
                label: Text(s, style: GoogleFonts.dmSans(fontSize: 12)),
                selected: _stock == s,
                onSelected: (_) {
                  setState(() => _stock = _stock == s ? null : s);
                  widget.onStockFilter(_stock);
                },
                selectedColor: const Color(0xFFdcfce7),
                checkmarkColor: const Color(0xFF16a34a),
              ),
            )),
          ],
        ),
      ),
      const SizedBox(height: 8),
    ]);
  }
}
```

Wire `SearchFilterBar` into `MaterialListScreen` — replace the existing `AppBar` + `body` with:

```dart
// Updated MaterialListScreen body structure:
// body: Column(children: [
//   if (_offline) MaterialBanner(...)
//   SearchFilterBar(
//     onSearch:        (q) => setState(() => _search = q),
//     onCategoryFilter: (c) => setState(() => _category = c),
//     onStockFilter:   (s) => setState(() => _stock = s),
//   ),
//   Expanded(child: _buildBody()),
// ]),
//
// And in _buildBody(), filter _records before the ListView:
// final filtered = _records.where((r) {
//   final matchSearch   = _search.isEmpty ||
//       r.materialName.toLowerCase().contains(_search.toLowerCase()) ||
//       r.supplierName.toLowerCase().contains(_search.toLowerCase());
//   final matchCategory = _category == null || r.materialCategory == _category;
//   final matchStock    = _stock    == null || r.stockStatus       == _stock;
//   return matchSearch && matchCategory && matchStock;
// }).toList();
```

#### Favourites System (Week 5, Monday–Tuesday)

```dart
// lib/services/favourites_service.dart
import 'package:hive_flutter/hive_flutter.dart';

/// Stores a set of favourite record_ids locally in Hive.
/// No API call needed — purely local.
class FavouritesService {
  static const _boxName = 'favourites';

  Future<void> init() async {
    if (!Hive.isBoxOpen(_boxName)) {
      await Hive.openBox<String>(_boxName);
    }
  }

  Box<String> get _box => Hive.box<String>(_boxName);

  bool isFavourite(String recordId) => _box.containsKey(recordId);

  Future<void> toggle(String recordId) async {
    if (isFavourite(recordId)) {
      await _box.delete(recordId);
    } else {
      await _box.put(recordId, recordId);
    }
  }

  List<String> get all => _box.values.toList();
}
```

```dart
// lib/screens/favourites_screen.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import '../models/price_record.dart';
import '../services/api_service.dart';
import '../services/favourites_service.dart';
import '../widgets/price_card.dart';

class FavouritesScreen extends StatefulWidget {
  const FavouritesScreen({super.key});

  @override
  State<FavouritesScreen> createState() => _FavouritesScreenState();
}

class _FavouritesScreenState extends State<FavouritesScreen> {
  final _api  = ApiService();
  final _favs = FavouritesService();
  List<PriceRecord> _records = [];
  bool _loading = true;

  @override
  void initState() {
    super.initState();
    _load();
  }

  Future<void> _load() async {
    setState(() => _loading = true);
    // Fetch all records then filter to favourites by record_id
    try {
      final all     = await _api.getPrices();
      final favIds  = _favs.all.toSet();
      setState(() {
        _records = all.where((r) => favIds.contains(r.recordId)).toList();
        _loading = false;
      });
    } catch (_) {
      setState(() => _loading = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: const Color(0xFFf9fafb),
      appBar: AppBar(
        title: Text('Favourites',
            style: GoogleFonts.dmSans(fontWeight: FontWeight.w700, color: Colors.white)),
        backgroundColor: const Color(0xFF1e3a8a),
      ),
      body: _loading
          ? const Center(child: CircularProgressIndicator())
          : _records.isEmpty
              ? Center(
                  child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: [
                      const Icon(Icons.star_border, size: 64, color: Color(0xFF9ca3af)),
                      const SizedBox(height: 16),
                      Text('No favourites yet',
                          style: GoogleFonts.dmSans(
                              fontSize: 16, color: const Color(0xFF6b7280))),
                      const SizedBox(height: 8),
                      Text('Tap ⭐ on any price card to save it here',
                          style: GoogleFonts.dmSans(
                              fontSize: 13, color: const Color(0xFF9ca3af))),
                    ],
                  ),
                )
              : RefreshIndicator(
                  onRefresh: _load,
                  child: ListView.builder(
                    padding: const EdgeInsets.all(16),
                    itemCount: _records.length,
                    itemBuilder: (_, i) => Padding(
                      padding: const EdgeInsets.only(bottom: 16),
                      child: PriceCard(record: _records[i]),
                    ),
                  ),
                ),
    );
  }
}
```

#### Barcode Scanner Screen (Week 5, Wednesday)

```dart
// lib/screens/barcode_scanner_screen.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import 'package:mobile_scanner/mobile_scanner.dart';
import '../services/api_service.dart';
import '../models/price_record.dart';
import '../widgets/price_card.dart';

class BarcodeScannerScreen extends StatefulWidget {
  const BarcodeScannerScreen({super.key});

  @override
  State<BarcodeScannerScreen> createState() => _BarcodeScannerScreenState();
}

class _BarcodeScannerScreenState extends State<BarcodeScannerScreen> {
  final _controller = MobileScannerController();
  final _api        = ApiService();

  bool _scanning  = true;
  bool _loading   = false;
  String? _scanned;
  List<PriceRecord> _results = [];

  Future<void> _onDetect(BarcodeCapture capture) async {
    if (!_scanning) return;
    final code = capture.barcodes.firstOrNull?.rawValue;
    if (code == null) return;

    setState(() { _scanning = false; _scanned = code; _loading = true; });
    _controller.stop();

    try {
      // Try to match the barcode against material names in the DB
      final records = await _api.getPrices(materialName: code);
      setState(() { _results = records; _loading = false; });
    } catch (_) {
      setState(() { _loading = false; });
    }
  }

  void _reset() {
    setState(() { _scanning = true; _scanned = null; _results = []; });
    _controller.start();
  }

  @override
  void dispose() { _controller.dispose(); super.dispose(); }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Scan Barcode',
            style: GoogleFonts.dmSans(fontWeight: FontWeight.w700, color: Colors.white)),
        backgroundColor: const Color(0xFF1e3a8a),
        iconTheme: const IconThemeData(color: Colors.white),
        actions: [
          if (!_scanning)
            TextButton(
              onPressed: _reset,
              child: Text('Scan Again',
                  style: GoogleFonts.dmSans(color: Colors.white)),
            ),
        ],
      ),
      body: _scanning
          ? Stack(children: [
              MobileScanner(controller: _controller, onDetect: _onDetect),
              // Viewfinder overlay
              Center(
                child: Container(
                  width: 260, height: 260,
                  decoration: BoxDecoration(
                    border: Border.all(color: const Color(0xFF2563eb), width: 3),
                    borderRadius: BorderRadius.circular(12),
                  ),
                ),
              ),
              Positioned(
                bottom: 40, left: 0, right: 0,
                child: Text('Align barcode within the frame',
                    textAlign: TextAlign.center,
                    style: GoogleFonts.dmSans(
                        color: Colors.white, fontSize: 14,
                        shadows: [const Shadow(blurRadius: 4)])),
              ),
            ])
          : _loading
              ? const Center(child: CircularProgressIndicator())
              : ListView(
                  padding: const EdgeInsets.all(16),
                  children: [
                    Padding(
                      padding: const EdgeInsets.only(bottom: 12),
                      child: Text('Results for: $_scanned',
                          style: GoogleFonts.dmSans(
                              fontSize: 14, color: const Color(0xFF6b7280))),
                    ),
                    if (_results.isEmpty)
                      Center(
                        child: Text('No materials found for this barcode',
                            style: GoogleFonts.dmSans(color: const Color(0xFF6b7280))),
                      )
                    else
                      ..._results.map((r) => Padding(
                        padding: const EdgeInsets.only(bottom: 16),
                        child: PriceCard(record: r),
                      )),
                  ],
                ),
    );
  }
}
```

#### ST1 Demo — April 1 🎯
- Show scraper pipeline: raw HTML → validated record → API upload → DB
- Demo `MaterialListScreen` with search + category filter
- Navigate into `MaterialDetailScreen` — show price chart and supplier tab
- Demo offline fallback (airplane mode)

#### D2 Demo — April 8 🎯
- Full app navigation: list → detail → comparison chart
- Favourites: star a card, switch to Favourites screen
- Barcode scanner demo
- Offline mode demonstration

---

## 📅 WEEKS 6–8 — April 13–May 3, 2026
### Mobile Polish + MVP

#### Biometric Authentication (Week 6, Monday–Tuesday)

```dart
// lib/services/biometric_service.dart
import 'package:local_auth/local_auth.dart';
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

class BiometricService {
  final _auth    = LocalAuthentication();
  final _storage = const FlutterSecureStorage();

  static const _tokenKey = 'auth_token';

  Future<bool> get isAvailable async {
    final canCheck = await _auth.canCheckBiometrics;
    final enrolled = await _auth.isDeviceSupported();
    return canCheck && enrolled;
  }

  Future<bool> authenticate() async {
    try {
      return await _auth.authenticate(
        localizedReason: 'Confirm your identity to access BuildMat',
        options: const AuthenticationOptions(
          biometricOnly: false,   // allows PIN fallback
          stickyAuth: true,
        ),
      );
    } catch (_) {
      return false;
    }
  }

  Future<void> saveToken(String token) =>
      _storage.write(key: _tokenKey, value: token);

  Future<String?> getToken() => _storage.read(key: _tokenKey);

  Future<void> clearToken() => _storage.delete(key: _tokenKey);
}
```

```dart
// lib/screens/login_screen.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import '../services/biometric_service.dart';
import '../services/api_service.dart';
import 'material_list_screen.dart';

class LoginScreen extends StatefulWidget {
  const LoginScreen({super.key});
  @override
  State<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _bio    = BiometricService();
  final _api    = ApiService();
  final _email  = TextEditingController();
  final _pass   = TextEditingController();
  bool  _loading      = false;
  bool  _bioAvailable = false;

  @override
  void initState() {
    super.initState();
    _checkBio();
  }

  Future<void> _checkBio() async {
    final avail = await _bio.isAvailable;
    // Also check if a saved token exists
    final token = await _bio.getToken();
    if (avail && token != null) {
      setState(() => _bioAvailable = true);
    }
  }

  Future<void> _loginWithBiometrics() async {
    final ok = await _bio.authenticate();
    if (!ok) return;
    final token = await _bio.getToken();
    if (token != null) _navigate(token);
  }

  Future<void> _loginWithPassword() async {
    setState(() => _loading = true);
    try {
      final result = await _api.login(_email.text.trim(), _pass.text);
      final token  = result['token'] as String;
      await _bio.saveToken(token);   // save for future biometric login
      _api.setToken(token);
      _navigate(token);
    } catch (e) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Login failed: $e')));
    } finally {
      setState(() => _loading = false);
    }
  }

  void _navigate(String token) {
    _api.setToken(token);
    Navigator.of(context).pushReplacement(
      MaterialPageRoute(builder: (_) => const MaterialListScreen()));
  }

  @override
  void dispose() { _email.dispose(); _pass.dispose(); super.dispose(); }

  @override
  Widget build(BuildContext context) {
    final field = InputDecoration(
      filled: true, fillColor: Colors.white,
      border: OutlineInputBorder(borderRadius: BorderRadius.circular(10),
          borderSide: const BorderSide(color: Color(0xFFe5e7eb))),
      enabledBorder: OutlineInputBorder(borderRadius: BorderRadius.circular(10),
          borderSide: const BorderSide(color: Color(0xFFe5e7eb))),
    );

    return Scaffold(
      backgroundColor: const Color(0xFF1e3a8a),
      body: SafeArea(
        child: Center(
          child: SingleChildScrollView(
            padding: const EdgeInsets.all(32),
            child: Column(children: [
              Text('🏗️ BuildMat',
                  style: GoogleFonts.dmSans(
                      fontSize: 36, fontWeight: FontWeight.w800,
                      color: Colors.white)),
              const SizedBox(height: 8),
              Text('Price Intelligence',
                  style: GoogleFonts.dmSans(
                      fontSize: 16, color: Colors.white60)),
              const SizedBox(height: 40),

              // Login card
              Container(
                padding: const EdgeInsets.all(24),
                decoration: BoxDecoration(
                  color: Colors.white,
                  borderRadius: BorderRadius.circular(16),
                  boxShadow: [BoxShadow(
                      color: Colors.black.withOpacity(0.2),
                      blurRadius: 20, offset: const Offset(0, 8))],
                ),
                child: Column(children: [
                  TextField(
                    controller: _email,
                    keyboardType: TextInputType.emailAddress,
                    decoration: field.copyWith(labelText: 'Email'),
                    style: GoogleFonts.dmSans()),
                  const SizedBox(height: 16),
                  TextField(
                    controller: _pass,
                    obscureText: true,
                    decoration: field.copyWith(labelText: 'Password'),
                    style: GoogleFonts.dmSans()),
                  const SizedBox(height: 24),
                  SizedBox(
                    width: double.infinity,
                    child: ElevatedButton(
                      onPressed: _loading ? null : _loginWithPassword,
                      style: ElevatedButton.styleFrom(
                          backgroundColor: const Color(0xFF2563eb),
                          padding: const EdgeInsets.symmetric(vertical: 14),
                          shape: RoundedRectangleBorder(
                              borderRadius: BorderRadius.circular(10))),
                      child: _loading
                          ? const SizedBox(height: 20, width: 20,
                              child: CircularProgressIndicator(
                                  color: Colors.white, strokeWidth: 2))
                          : Text('Sign In',
                              style: GoogleFonts.dmSans(
                                  fontSize: 16, fontWeight: FontWeight.w700,
                                  color: Colors.white)),
                    ),
                  ),
                  if (_bioAvailable) ...[
                    const SizedBox(height: 16),
                    TextButton.icon(
                      onPressed: _loginWithBiometrics,
                      icon: const Icon(Icons.fingerprint, color: Color(0xFF2563eb)),
                      label: Text('Use Biometrics',
                          style: GoogleFonts.dmSans(
                              color: const Color(0xFF2563eb),
                              fontWeight: FontWeight.w600)),
                    ),
                  ],
                ]),
              ),
            ]),
          ),
        ),
      ),
    );
  }
}
```

#### Geolocation — Nearby Supplier Filter (Week 6, Wednesday–Thursday)

```dart
// lib/services/location_service.dart
import 'package:geolocator/geolocator.dart';

class LocationService {
  Future<Position?> getCurrentPosition() async {
    LocationPermission perm = await Geolocator.checkPermission();
    if (perm == LocationPermission.denied) {
      perm = await Geolocator.requestPermission();
    }
    if (perm == LocationPermission.deniedForever) return null;

    return Geolocator.getCurrentPosition(
      desiredAccuracy: LocationAccuracy.medium,
      timeLimit: const Duration(seconds: 10),
    );
  }

  /// Maps current GPS coords to an approximate SA province string.
  /// Good enough for filtering — no external geocoding API required.
  String? inferProvince(double lat, double lng) {
    if (lat < -33.5 && lng > 18.3 && lng < 19.5) return 'Western Cape';
    if (lat > -30 && lat < -25.5 && lng > 27 && lng < 32)  return 'Gauteng';
    if (lat < -28 && lat > -31.5 && lng > 29 && lng < 32)  return 'KwaZulu-Natal';
    if (lat < -31 && lng > 22 && lng < 30)                  return 'Eastern Cape';
    return null;   // unknown — don't filter
  }
}
```

```dart
// lib/widgets/nearby_filter_button.dart
// Add to MaterialListScreen AppBar actions
import 'package:flutter/material.dart';
import '../services/location_service.dart';

class NearbyFilterButton extends StatelessWidget {
  final ValueChanged<String?> onProvinceDetected;
  const NearbyFilterButton({super.key, required this.onProvinceDetected});

  @override
  Widget build(BuildContext context) {
    return IconButton(
      icon: const Icon(Icons.my_location, color: Colors.white),
      tooltip: 'Filter by my location',
      onPressed: () async {
        final svc = LocationService();
        final pos = await svc.getCurrentPosition();
        if (pos == null) {
          ScaffoldMessenger.of(context).showSnackBar(
            const SnackBar(content: Text('Location permission denied')));
          return;
        }
        final province = svc.inferProvince(pos.latitude, pos.longitude);
        onProvinceDetected(province);
        if (province != null) {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('Showing suppliers in $province')));
        }
      },
    );
  }
}
```

#### App Navigation (Bottom Nav — Week 7)

```dart
// lib/screens/main_nav_screen.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';
import 'material_list_screen.dart';
import 'favourites_screen.dart';
import 'barcode_scanner_screen.dart';

class MainNavScreen extends StatefulWidget {
  const MainNavScreen({super.key});
  @override
  State<MainNavScreen> createState() => _MainNavScreenState();
}

class _MainNavScreenState extends State<MainNavScreen> {
  int _index = 0;

  final _screens = const [
    MaterialListScreen(),
    FavouritesScreen(),
    BarcodeScannerScreen(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(index: _index, children: _screens),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _index,
        onTap: (i) => setState(() => _index = i),
        selectedItemColor:   const Color(0xFF2563eb),
        unselectedItemColor: const Color(0xFF9ca3af),
        selectedLabelStyle:  GoogleFonts.dmSans(fontWeight: FontWeight.w600),
        unselectedLabelStyle: GoogleFonts.dmSans(),
        items: const [
          BottomNavigationBarItem(icon: Icon(Icons.home_outlined),
              activeIcon: Icon(Icons.home), label: 'Prices'),
          BottomNavigationBarItem(icon: Icon(Icons.star_outline),
              activeIcon: Icon(Icons.star), label: 'Favourites'),
          BottomNavigationBarItem(icon: Icon(Icons.qr_code_scanner),
              activeIcon: Icon(Icons.qr_code_scanner), label: 'Scan'),
        ],
      ),
    );
  }
}
```

**D3 (May 1) — MVP Complete** 🎉

---

## 📅 WEEKS 9–13 — May 4–June 9, 2026
### Testing & Deployment

#### Flutter Widget Tests (Week 9)

```dart
// test/widget_test.dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:buildmat_app/widgets/price_card.dart';
import 'package:buildmat_app/models/price_record.dart';

final testRecord = PriceRecord(
  recordId: 'REC_001',
  date: '2026-03-09', year: 2026, month: 3,
  materialName: 'PPC Cement 50kg', materialCategory: 'Cement',
  supplierName: 'Builders Warehouse', region: 'Gauteng', province: 'Johannesburg',
  priceZar: 89.99, unit: '50kg bag', pricePerKgZar: 1.7998,
  priceChangeMomPct: 2.3, priceChangeYoyPct: -1.1,
  stockStatus: 'In Stock', bulkDiscountAvailable: 'Yes',
);

void main() {
  testWidgets('PriceCard displays material name and price', (tester) async {
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(body: PriceCard(record: testRecord))));

    expect(find.text('PPC Cement 50kg'),  findsOneWidget);
    expect(find.textContaining('R89.99'), findsOneWidget);
    expect(find.text('In Stock'),         findsOneWidget);
    expect(find.text('Bulk Discount'),    findsOneWidget);
  });

  testWidgets('PriceCard shows MoM change chip', (tester) async {
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(body: PriceCard(record: testRecord))));

    expect(find.textContaining('MoM'), findsOneWidget);
    expect(find.textContaining('+2.3%'), findsOneWidget);
  });
}
```

```bash
# Run all Flutter tests
flutter test

# Run tests with coverage
flutter test --coverage
genhtml coverage/lcov.info -o coverage/html
open coverage/html/index.html

# Profile performance on a real device
flutter run --profile

# Build release APK
flutter build apk --release

# Build release for iOS (macOS only)
flutter build ios --release
```

**ST2 (May 6)** — Testing report: widget test coverage, scraper reliability, upload success rate, offline fallback  
**D4 (May 13)** — Final polish  
**Final (June 10)** — Championship demo

---

## 🎯 Key Metrics

| Metric | Target |
|---|---|
| Scraped records uploaded | 2,000+ by D1 |
| Suppliers covered | 10+ |
| Scraper success rate | >90% per run |
| App size | < 50 MB |
| Offline functionality | 100% read access |
| App load time | < 2 s |

---

## ⚠️ Bugs Fixed in This Version

| Original bug | Fix applied |
|---|---|
| `DataCleaner` used `df['price']` and `df['supplier']` | Corrected to `df['price_zar']` and `df['supplier_name']` (canonical column names) |
| `ScraperScheduler` called `scraper.scrape_all_materials()` and `scraper.name` | Unified to `scraper.scrape()` and `scraper.supplier_name` on every class |
| Two uploaders with inconsistent payload formats (`{'prices': [...]}` vs raw array) | Single `APIUploader` class; sends raw JSON array to match Member 1's endpoint |
| `from suppliers.cashbuild import CashbuildScraper` with no cashbuild.py defined | `CashbuildScraper` now fully defined |
| `fl_chart` missing from pubspec | Added `fl_chart: ^0.68.0` |
| `flutter_barcode_scanner` (unmaintained) | Replaced with `mobile_scanner: ^5.1.1` |
| `HiveStorage` typed as `Box<Map>` but opened as `Hive.box<Map>()` — needs matching init | `init()` now correctly calls `Hive.openBox<Map>()` with matching type |
| `ApiService.getPrices()` had a required `materialId` arg; `MaterialListScreen` called it with none | `getPrices()` now takes optional named params; no required args |
| `tooltipBgColor` deprecated in fl_chart ≥ 0.66 | Updated both charts to use `getTooltipColor: (_) => Colors.white` |
| `http` import missing in Hive/ApiService combined block | Full import added at top of `api_service.dart` |

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active | **Next Milestone:** D1 — March 25, 2026
