# 🎨 Frontend Web Developer — Detailed Roadmap

**Team Member:** Member 3  
**Primary Tech Stack:** React, TypeScript, Tailwind CSS, Recharts, TanStack Query v5  
**Timeline:** March 9 – June 9, 2026 (13 Weeks)

---

## 🚀 Getting Started — March 8, 2026

Complete this setup before Week 1 begins on March 9.

### System Requirements
- **OS:** Windows 10/11, macOS 10.15+, or Ubuntu 20.04+
- **RAM:** Minimum 8 GB (16 GB recommended)
- **Storage:** At least 5 GB free (node_modules)
- **Browser:** Chrome 90+, Firefox 88+, Edge 90+, or Safari 14+

### Step 1 – Install Node.js

```bash
# Download LTS from: https://nodejs.org/ (v20.x recommended)
node --version   # Must be v18.x or v20.x
npm --version    # Must be 9.x+
```

### Step 2 – Install Git

```bash
# macOS
brew install git

# Ubuntu/Debian
sudo apt update && sudo apt install git

git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Step 3 – Create React Project (TypeScript)

```bash
mkdir -p ~/projects/buildmat-web
cd ~/projects/buildmat-web

npx create-react-app buildmat-dashboard --template typescript
cd buildmat-dashboard
```

### Step 4 – Install All Dependencies

```bash
# Styling
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Routing
npm install react-router-dom

# API state — IMPORTANT: pin to v5; hooks API changed from v4
npm install @tanstack/react-query@5

# HTTP
npm install axios

# Charts
npm install recharts

# UI utilities
npm install @headlessui/react @heroicons/react

# Date formatting
npm install date-fns

# Forms
npm install react-hook-form

# Dev tooling
npm install --save-dev @testing-library/react @testing-library/jest-dom prettier eslint-plugin-react
```

### Step 5 – Configure Tailwind

```javascript
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: {
          50:  '#eff6ff',
          100: '#dbeafe',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
        },
      },
      fontFamily: {
        sans: ['DM Sans', 'Inter', 'sans-serif'],
      },
    },
  },
  plugins: [],
};
```

```css
/* src/index.css */
@import url('https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&display=swap');

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  body {
    @apply bg-gray-50 text-gray-900;
    font-family: 'DM Sans', sans-serif;
  }
}

@layer components {
  .btn-primary   { @apply px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors font-medium; }
  .btn-secondary { @apply px-4 py-2 bg-gray-200 text-gray-800 rounded-lg hover:bg-gray-300 transition-colors font-medium; }
  .card          { @apply bg-white rounded-[10px] p-6; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
}
```

### Step 6 – Project Structure

```bash
mkdir -p src/{components/{common,charts,layout,features},pages,services,hooks,utils,types,contexts,assets}
touch src/types/PriceRecord.ts
touch src/services/apiService.ts
touch src/hooks/usePrices.ts
touch src/hooks/useForecasts.ts
touch src/config/queryClient.ts
```

### Step 7 – Environment Variables

```bash
cat > .env << 'EOF'
REACT_APP_API_URL=http://localhost:3000/api
REACT_APP_ENV=development
EOF

cp .env .env.example
echo ".env" >> .gitignore
```

### Step 8 – React Query Client (TanStack Query v5)

```typescript
// src/config/queryClient.ts
import { QueryClient } from '@tanstack/react-query';

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      refetchOnWindowFocus: false,
      retry: 1,
      staleTime: 5 * 60 * 1000,  // 5 minutes
      gcTime:    10 * 60 * 1000, // 10 minutes — v5 renamed cacheTime → gcTime
    },
  },
});
```

```tsx
// src/index.tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClientProvider } from '@tanstack/react-query';
import { BrowserRouter } from 'react-router-dom';
import { queryClient } from './config/queryClient';
import App from './App';
import './index.css';

const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement);
root.render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </QueryClientProvider>
  </React.StrictMode>
);
```

### Step 9 – Test Setup

```bash
npm start
# Should open http://localhost:3000 — default React app
npm run build
# Should succeed with no errors
```

### Setup Checklist ✅
- [ ] Node.js v18+ installed
- [ ] React project created (TypeScript template)
- [ ] All packages installed — `npm list --depth=0` shows recharts, @tanstack/react-query@5, axios
- [ ] Tailwind configured — `DM Sans` font loading in browser
- [ ] `.env` created
- [ ] `npm start` and `npm run build` both succeed

---

## 📋 Canonical Schema & API Contract

### TypeScript Types (16 Fields)

```typescript
// src/types/PriceRecord.ts

/**
 * PriceRecord matches the canonical 16-column schema exactly.
 * This is the shared contract: Member 4's scraper → Member 1's DB → Member 3's UI.
 *
 * All API endpoints use material_name (string), not material_id.
 */
export interface PriceRecord {
  record_id:               string;
  date:                    string;   // "YYYY-MM-DD"
  year:                    number;
  month:                   number;
  material_name:           string;
  material_category:       string;   // "Cement" | "Building" | "Steel" | "Timber" | "Electrical"
  supplier_name:           string;
  region:                  string;
  province:                string;
  price_zar:               number;
  unit:                    string;
  price_per_kg_zar:        number;
  price_change_mom_pct:    number;
  price_change_yoy_pct:    number;
  stock_status:            'In Stock' | 'Low Stock' | 'Out of Stock';
  bulk_discount_available: 'Yes' | 'No';
}

export interface PricesResponse {
  success:    boolean;
  data:       PriceRecord[];
  pagination: {
    page:        number;
    per_page:    number;
    total:       number;
    total_pages: number;
  };
}

export interface CompareResponse {
  success:       boolean;
  material_name: string;
  region:        string;
  suppliers:     PriceRecord[];  // one record per supplier, sorted cheapest first
}

export interface HistoryResponse {
  success:       boolean;
  material_name: string;
  history: Array<{
    date:         string;
    avg_price:    number;
    min_price:    number;
    max_price:    number;
    sample_count: number;
  }>;
}

/**
 * NOTE FOR MEMBER 1: Frontend uses confidence_interval_lower / confidence_interval_upper
 * (more readable). Please rename the DB columns from confidence_lower / confidence_upper
 * to match, or add aliased column names in the SELECT query.
 */
export interface ForecastPoint {
  forecast_date:              string;
  predicted_price:            number;
  confidence_interval_lower:  number;
  confidence_interval_upper:  number;
  confidence_level:           number;
  model_name:                 string;
  model_version:              string;
  region:                     string | null;
  generated_at:               string;
}

export interface ForecastResponse {
  success:       boolean;
  material_name: string;
  forecasts:     ForecastPoint[];
}

export type StockStatus = PriceRecord['stock_status'];
export type Category    = 'Cement' | 'Building' | 'Steel' | 'Timber' | 'Electrical';
```

---

## 🔌 API Service Layer

```typescript
// src/services/apiService.ts
import axios, { AxiosInstance } from 'axios';
import {
  PriceRecord, PricesResponse, CompareResponse,
  HistoryResponse, ForecastResponse,
} from '../types/PriceRecord';

const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:3000/api';

class ApiService {
  private client: AxiosInstance;

  constructor() {
    this.client = axios.create({
      baseURL: API_BASE_URL,
      headers: { 'Content-Type': 'application/json' },
      timeout: 10_000,
    });

    // Attach JWT on every request
    this.client.interceptors.request.use((config) => {
      const token = localStorage.getItem('authToken');
      if (token) config.headers.Authorization = `Bearer ${token}`;
      return config;
    });

    // Handle 401 globally
    this.client.interceptors.response.use(
      (res) => res,
      (err) => {
        if (err.response?.status === 401) {
          localStorage.removeItem('authToken');
          window.location.href = '/login';
        }
        return Promise.reject(err);
      }
    );
  }

  /**
   * GET /api/prices
   * All filters use material_name (string), not material_id.
   */
  async getPrices(filters?: {
    material_name?:           string;
    material_category?:       string;
    supplier_name?:           string;
    region?:                  string;
    province?:                string;
    stock_status?:            PriceRecord['stock_status'];
    bulk_discount_available?: 'Yes' | 'No';
    start_date?:              string;
    end_date?:                string;
    page?:                    number;
    per_page?:                number;
    limit?:                   number;
  }): Promise<PricesResponse> {
    const { data } = await this.client.get<PricesResponse>('/prices', { params: filters });
    return data;
  }

  /**
   * GET /api/prices/history?material_name=...&days=...&supplier_name=...
   * Uses `days` (not `months`) — matches backend parameter name.
   */
  async getPriceHistory(
    materialName: string,
    days: number = 90,
    supplierName?: string
  ): Promise<HistoryResponse> {
    const { data } = await this.client.get<HistoryResponse>('/prices/history', {
      params: { material_name: materialName, days, supplier_name: supplierName },
    });
    return data;
  }

  /**
   * GET /api/prices/compare?material_name=...&region=...
   * Returns one PriceRecord per supplier, sorted cheapest first.
   */
  async compareSuppliers(materialName: string, region?: string): Promise<CompareResponse> {
    const { data } = await this.client.get<CompareResponse>('/prices/compare', {
      params: { material_name: materialName, region },
    });
    return data;
  }

  /**
   * GET /api/forecast?material_name=...&days=...
   * Endpoint is /forecast (no trailing s) — matches backend route.
   */
  async getForecast(materialName: string, days: number = 30): Promise<ForecastResponse> {
    const { data } = await this.client.get<ForecastResponse>('/forecast', {
      params: { material_name: materialName, days },
    });
    return data;
  }

  // Auth
  async login(email: string, password: string) {
    const { data } = await this.client.post('/auth/login', { email, password });
    return data;
  }
  async register(email: string, password: string, name?: string) {
    const { data } = await this.client.post('/auth/register', { email, password, name });
    return data;
  }
  async logout() {
    await this.client.post('/auth/logout');
    localStorage.removeItem('authToken');
  }
}

export const apiService = new ApiService();
```

---

## 🪝 React Query Hooks (TanStack Query v5)

> **v5 breaking change:** `useQuery` no longer accepts positional arguments. All hooks use the options-object form: `useQuery({ queryKey, queryFn, ...options })`. `cacheTime` was renamed `gcTime`.

```typescript
// src/hooks/usePrices.ts
import { useQuery } from '@tanstack/react-query';
import { apiService } from '../services/apiService';
import { PriceRecord } from '../types/PriceRecord';

interface PriceFilters {
  material_name?:           string;
  material_category?:       string;
  supplier_name?:           string;
  region?:                  string;
  province?:                string;
  stock_status?:            PriceRecord['stock_status'];
  bulk_discount_available?: 'Yes' | 'No';
  page?:                    number;
  per_page?:                number;
}

/** Fetch paginated/filtered price records. */
export const usePrices = (filters: PriceFilters = {}) => {
  return useQuery({
    queryKey:  ['prices', filters],
    queryFn:   () => apiService.getPrices(filters),
    staleTime: 5 * 60 * 1000,
    gcTime:    10 * 60 * 1000,
  });
};

/**
 * Fetch daily aggregate price history for a material.
 * Uses material_name (string) — not material_id.
 * Uses `days` param — not `months` (backend accepts days only).
 */
export const usePriceHistory = (materialName: string, days: number = 90, supplierName?: string) => {
  return useQuery({
    queryKey:  ['priceHistory', materialName, days, supplierName],
    queryFn:   () => apiService.getPriceHistory(materialName, days, supplierName),
    enabled:   !!materialName,
    staleTime: 60 * 1000,
    gcTime:    5 * 60 * 1000,
  });
};

/**
 * Compare latest prices across suppliers for a material.
 * Returns one PriceRecord per supplier.
 */
export const usePriceComparison = (materialName: string, region?: string) => {
  return useQuery({
    queryKey:  ['priceComparison', materialName, region],
    queryFn:   () => apiService.compareSuppliers(materialName, region),
    enabled:   !!materialName,
    staleTime: 5 * 60 * 1000,
    gcTime:    10 * 60 * 1000,  // v5: gcTime (not cacheTime)
  });
};
```

```typescript
// src/hooks/useForecasts.ts
import { useQuery } from '@tanstack/react-query';
import { apiService } from '../services/apiService';

/**
 * Fetch ML price forecasts.
 * Uses material_name (string) — not material_id.
 * 6-hour staleTime matches the backend's forecast cache TTL.
 */
export const useForecasts = (materialName: string, days: number = 30) => {
  return useQuery({
    queryKey:  ['forecasts', materialName, days],
    queryFn:   () => apiService.getForecast(materialName, days),
    enabled:   !!materialName,
    staleTime: 6 * 60 * 60 * 1000,   // 6 hours — matches backend cache
    gcTime:    7 * 60 * 60 * 1000,
  });
};
```

---

## 🧩 Common Components

### Button

```tsx
// src/components/common/Button.tsx
import React from 'react';

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'outline' | 'danger';
  size?:    'sm' | 'md' | 'lg';
}

export const Button: React.FC<ButtonProps> = ({
  children, variant = 'primary', size = 'md', className = '', ...props
}) => {
  const variants = {
    primary:   'bg-primary-600 text-white hover:bg-primary-700 disabled:bg-gray-300',
    secondary: 'bg-gray-200 text-gray-800 hover:bg-gray-300',
    outline:   'border-2 border-primary-600 text-primary-600 hover:bg-primary-50',
    danger:    'bg-red-600 text-white hover:bg-red-700',
  };
  const sizes = { sm: 'px-3 py-1.5 text-sm', md: 'px-4 py-2', lg: 'px-6 py-3 text-lg' };

  return (
    <button
      className={`font-medium transition-colors rounded-lg ${variants[variant]} ${sizes[size]} ${className}`}
      {...props}
    >
      {children}
    </button>
  );
};
```

### Card

```tsx
// src/components/common/Card.tsx
import React from 'react';

interface CardProps {
  children:     React.ReactNode;
  title?:       string;
  subtitle?:    string;
  headerAction?: React.ReactNode;
  className?:   string;
}

export const Card: React.FC<CardProps> = ({ children, title, subtitle, headerAction, className = '' }) => (
  <div
    className={`bg-white rounded-[10px] ${className}`}
    style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}
  >
    {(title || headerAction) && (
      <div className="border-b border-gray-200 px-6 py-4 flex justify-between items-center">
        <div>
          {title    && <h3 className="text-lg font-semibold text-gray-900">{title}</h3>}
          {subtitle && <p className="text-sm text-gray-500 mt-1">{subtitle}</p>}
        </div>
        {headerAction && <div>{headerAction}</div>}
      </div>
    )}
    <div className="p-6">{children}</div>
  </div>
);
```

### LoadingSpinner

```tsx
// src/components/common/LoadingSpinner.tsx
import React from 'react';

export const LoadingSpinner: React.FC<{ size?: 'sm' | 'md' | 'lg'; className?: string }> = ({
  size = 'md', className = ''
}) => {
  const sizes = { sm: 'h-4 w-4', md: 'h-8 w-8', lg: 'h-12 w-12' };
  return (
    <div className={`flex justify-center items-center p-8 ${className}`}>
      <div className={`${sizes[size]} border-4 border-primary-200 border-t-primary-600 rounded-full animate-spin`} />
    </div>
  );
};
```

### ErrorMessage

```tsx
// src/components/common/ErrorMessage.tsx
import React from 'react';

export const ErrorMessage: React.FC<{ message: string; onRetry?: () => void }> = ({ message, onRetry }) => (
  <div className="flex flex-col items-center justify-center p-12 text-center">
    <div className="text-red-400 text-5xl mb-4">⚠️</div>
    <p className="text-red-600 mb-4">{message}</p>
    {onRetry && (
      <button onClick={onRetry} className="btn-primary">
        Try Again
      </button>
    )}
  </div>
);
```

---

## 🏷️ Badge Components

```tsx
// src/components/common/StockBadge.tsx
import React from 'react';
import { StockStatus } from '../../types/PriceRecord';

const STYLES: Record<StockStatus, { bg: string; color: string }> = {
  'In Stock':     { bg: '#dcfce7', color: '#16a34a' },
  'Low Stock':    { bg: '#fef3c7', color: '#f59e0b' },
  'Out of Stock': { bg: '#fee2e2', color: '#dc2626' },
};

export const StockBadge: React.FC<{ status: StockStatus }> = ({ status }) => {
  const s = STYLES[status] ?? { bg: '#f3f4f6', color: '#6b7280' };
  return (
    <span
      className="px-3 py-1 rounded-full text-xs font-semibold"
      style={{ backgroundColor: s.bg, color: s.color }}
    >
      {status}
    </span>
  );
};
```

```tsx
// src/components/common/BulkDiscountTag.tsx
import React from 'react';

export const BulkDiscountTag: React.FC = () => (
  <span
    className="px-3 py-1 rounded-full text-xs font-semibold"
    style={{ backgroundColor: '#eff6ff', color: '#2563eb' }}
  >
    Bulk Discount
  </span>
);
```

---

## 💳 PriceCard Component

```tsx
// src/components/features/PriceCard.tsx
import React from 'react';
import { PriceRecord } from '../../types/PriceRecord';
import { StockBadge } from '../common/StockBadge';
import { BulkDiscountTag } from '../common/BulkDiscountTag';

interface PriceCardProps {
  record:   PriceRecord;
  onClick?: () => void;
}

export const PriceCard: React.FC<PriceCardProps> = ({ record, onClick }) => {
  const momUp = record.price_change_mom_pct >= 0;
  const yoyUp = record.price_change_yoy_pct >= 0;

  const changeChip = (label: string, isUp: boolean) => (
    <span
      className="px-2 py-1 rounded-lg text-xs font-semibold"
      style={{
        backgroundColor: isUp ? '#fee2e2' : '#dcfce7',
        color:           isUp ? '#dc2626' : '#16a34a',
      }}
    >
      {label}
    </span>
  );

  return (
    <div
      className="bg-white rounded-[10px] p-6 cursor-pointer hover:shadow-xl transition-shadow"
      style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}
      onClick={onClick}
    >
      <h3 className="text-xl font-bold text-gray-900 mb-1">{record.material_name}</h3>
      <p className="text-sm text-gray-500 mb-3">{record.material_category}</p>

      <div className="flex items-baseline mb-1">
        <span className="text-3xl font-bold" style={{ color: '#2563eb' }}>
          R{record.price_zar.toFixed(2)}
        </span>
        <span className="ml-2 text-sm text-gray-500">/ {record.unit}</span>
      </div>
      <p className="text-xs text-gray-400 mb-3">R{record.price_per_kg_zar.toFixed(2)} per kg</p>

      <div className="flex gap-2 mb-3">
        {changeChip(`MoM: ${momUp ? '+' : ''}${record.price_change_mom_pct.toFixed(1)}%`, momUp)}
        {changeChip(`YoY: ${yoyUp ? '+' : ''}${record.price_change_yoy_pct.toFixed(1)}%`, yoyUp)}
      </div>

      <div className="flex gap-2 mb-3">
        <StockBadge status={record.stock_status} />
        {record.bulk_discount_available === 'Yes' && <BulkDiscountTag />}
      </div>

      <p className="text-xs text-gray-400">
        🏪 {record.supplier_name} · {record.province}, {record.region}
      </p>
    </div>
  );
};
```

---

## 📊 Chart Components

### PriceHistoryChart

```tsx
// src/components/charts/PriceHistoryChart.tsx
import React from 'react';
import {
  LineChart, Line, XAxis, YAxis, CartesianGrid,
  Tooltip, Legend, ResponsiveContainer,
} from 'recharts';
import { format, parseISO } from 'date-fns';

interface HistoryPoint {
  date:         string;
  avg_price:    number;
  min_price:    number;
  max_price:    number;
  sample_count: number;
}

interface PriceHistoryChartProps {
  data:         HistoryPoint[];
  materialName: string;
  showRange?:   boolean;   // show min/max bands
}

const CustomTooltip = ({ active, payload }: any) => {
  if (!active || !payload?.length) return null;
  const d = payload[0].payload as HistoryPoint;
  return (
    <div className="bg-white p-3 rounded-lg border border-gray-200 text-sm"
         style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}>
      <p className="font-semibold mb-1">{format(parseISO(d.date), 'MMM d, yyyy')}</p>
      <p style={{ color: '#2563eb' }}>Avg: R{d.avg_price.toFixed(2)}</p>
      <p className="text-gray-400">Min: R{d.min_price.toFixed(2)}</p>
      <p className="text-gray-400">Max: R{d.max_price.toFixed(2)}</p>
      <p className="text-gray-400">Samples: {d.sample_count}</p>
    </div>
  );
};

export const PriceHistoryChart: React.FC<PriceHistoryChartProps> = ({
  data, materialName, showRange = false,
}) => (
  <div className="bg-white rounded-[10px] p-6" style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}>
    <h3 className="text-lg font-bold text-gray-900 mb-4">Price History: {materialName}</h3>
    <ResponsiveContainer width="100%" height={350}>
      <LineChart data={data} margin={{ top: 5, right: 30, left: 20, bottom: 5 }}>
        <CartesianGrid strokeDasharray="3 3" stroke="#e5e7eb" />
        <XAxis
          dataKey="date"
          tickFormatter={(d) => format(parseISO(d), 'MMM d')}
          tick={{ fill: '#6b7280', fontSize: 12 }}
        />
        <YAxis
          tickFormatter={(v) => `R${v}`}
          tick={{ fill: '#6b7280', fontSize: 12 }}
        />
        <Tooltip content={<CustomTooltip />} />
        <Legend />
        <Line
          type="monotone"
          dataKey="avg_price"
          stroke="#2563eb"
          strokeWidth={2}
          dot={{ r: 3, fill: '#2563eb' }}
          activeDot={{ r: 6 }}
          name="Avg Price (ZAR)"
        />
        {showRange && (
          <>
            <Line type="monotone" dataKey="min_price" stroke="#16a34a" strokeWidth={1}
                  strokeDasharray="4 4" dot={false} name="Min Price" />
            <Line type="monotone" dataKey="max_price" stroke="#dc2626" strokeWidth={1}
                  strokeDasharray="4 4" dot={false} name="Max Price" />
          </>
        )}
      </LineChart>
    </ResponsiveContainer>
  </div>
);
```

### SupplierComparisonChart

```tsx
// src/components/charts/SupplierComparisonChart.tsx
import React from 'react';
import {
  BarChart, Bar, XAxis, YAxis, CartesianGrid,
  Tooltip, ResponsiveContainer, Cell,
} from 'recharts';
import { PriceRecord } from '../../types/PriceRecord';

interface SupplierComparisonChartProps {
  data:         PriceRecord[];   // one record per supplier from /prices/compare
  materialName: string;
}

export const SupplierComparisonChart: React.FC<SupplierComparisonChartProps> = ({
  data, materialName,
}) => {
  // data already sorted cheapest-first by the backend; show as-is
  const chartData = data.map((r) => ({
    supplier:    r.supplier_name,
    price:       r.price_zar,        // ← uses price_zar, not price
    hasBulk:     r.bulk_discount_available === 'Yes',
    stock:       r.stock_status,
  }));

  return (
    <div className="bg-white rounded-[10px] p-6" style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}>
      <h3 className="text-lg font-bold text-gray-900 mb-4">
        Supplier Comparison: {materialName}
      </h3>
      <ResponsiveContainer width="100%" height={350}>
        <BarChart data={chartData} margin={{ top: 10, right: 20, left: 10, bottom: 60 }}>
          <CartesianGrid strokeDasharray="3 3" stroke="#e5e7eb" />
          <XAxis
            dataKey="supplier"
            angle={-35}
            textAnchor="end"
            height={70}
            tick={{ fill: '#6b7280', fontSize: 11 }}
          />
          <YAxis
            tickFormatter={(v) => `R${v}`}
            tick={{ fill: '#6b7280', fontSize: 12 }}
          />
          <Tooltip
            contentStyle={{ backgroundColor: '#fff', border: '1px solid #e5e7eb', borderRadius: 8 }}
            formatter={(v: number) => [`R${v.toFixed(2)}`, 'Price']}
          />
          <Bar dataKey="price" radius={[6, 6, 0, 0]} name="Price (ZAR)">
            {chartData.map((entry, i) => (
              <Cell
                key={`cell-${i}`}
                fill={entry.hasBulk ? '#16a34a' : '#2563eb'}
                opacity={entry.stock === 'Out of Stock' ? 0.4 : 1}
              />
            ))}
          </Bar>
        </BarChart>
      </ResponsiveContainer>
      <div className="flex justify-center gap-6 mt-3 text-xs text-gray-500">
        <span>🟦 Standard price</span>
        <span>🟩 Bulk discount available</span>
        <span style={{ opacity: 0.5 }}>Faded = Out of Stock</span>
      </div>
    </div>
  );
};
```

### ForecastChart

```tsx
// src/components/charts/ForecastChart.tsx
import React from 'react';
import {
  ComposedChart, Line, Area, XAxis, YAxis,
  CartesianGrid, Tooltip, Legend, ResponsiveContainer,
} from 'recharts';
import { format, parseISO } from 'date-fns';
import { PriceRecord, ForecastPoint } from '../../types/PriceRecord';

interface ForecastChartProps {
  historical:   Pick<PriceRecord, 'date' | 'price_zar'>[];
  forecasts:    ForecastPoint[];
  materialName: string;
}

export const ForecastChart: React.FC<ForecastChartProps> = ({
  historical, forecasts, materialName,
}) => {
  // Merge historical and forecast into one series for a seamless line
  const histData = historical.map((r) => ({
    date:        r.date,
    actual:      r.price_zar,
    predicted:   undefined as number | undefined,
    ci_upper:    undefined as number | undefined,
    ci_lower:    undefined as number | undefined,
    isForecast:  false,
  }));

  const foreData = forecasts.map((f) => ({
    date:       f.forecast_date,
    actual:     undefined as number | undefined,
    predicted:  f.predicted_price,
    // FIX: confidence band rendered correctly with separate upper + lower Areas.
    // Both areas stacked from 0 — lower clips the bottom, upper shows the band.
    // We render ci_upper first (full band) then ci_lower as a white fill to create
    // the visual gap. Using one color for both is wrong — the lower would erase the upper.
    ci_upper:   f.confidence_interval_upper,
    ci_lower:   f.confidence_interval_lower,
    isForecast: true,
  }));

  const combined = [...histData, ...foreData].sort((a, b) => a.date.localeCompare(b.date));

  return (
    <div className="bg-white rounded-[10px] p-6" style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}>
      <h3 className="text-lg font-bold text-gray-900 mb-4">Price Forecast: {materialName}</h3>
      <ResponsiveContainer width="100%" height={380}>
        <ComposedChart data={combined} margin={{ top: 10, right: 30, left: 20, bottom: 5 }}>
          <CartesianGrid strokeDasharray="3 3" stroke="#e5e7eb" />
          <XAxis
            dataKey="date"
            tickFormatter={(d) => format(parseISO(d), 'MMM d')}
            tick={{ fill: '#6b7280', fontSize: 12 }}
          />
          <YAxis tickFormatter={(v) => `R${v}`} tick={{ fill: '#6b7280', fontSize: 12 }} />
          <Tooltip
            contentStyle={{ backgroundColor: '#fff', border: '1px solid #e5e7eb', borderRadius: 8 }}
            formatter={(v: number, name: string) => {
              const labels: Record<string, string> = {
                actual: 'Actual', predicted: 'Forecast',
                ci_upper: 'CI Upper', ci_lower: 'CI Lower',
              };
              return [`R${v?.toFixed(2)}`, labels[name] ?? name];
            }}
            labelFormatter={(d) => format(parseISO(d as string), 'MMM d, yyyy')}
          />
          <Legend />

          {/* Confidence band: upper area filled purple, lower area white — creates the gap */}
          <Area
            type="monotone"
            dataKey="ci_upper"
            stroke="none"
            fill="#7c3aed"
            fillOpacity={0.15}
            name="ci_upper"
            legendType="none"
          />
          <Area
            type="monotone"
            dataKey="ci_lower"
            stroke="none"
            fill="white"      // ← white fill erases the bottom of the purple band correctly
            fillOpacity={1}
            name="ci_lower"
            legendType="none"
          />

          {/* Actual historical prices */}
          <Line
            type="monotone"
            dataKey="actual"
            stroke="#2563eb"
            strokeWidth={2}
            dot={{ r: 3, fill: '#2563eb' }}
            activeDot={{ r: 6 }}
            name="Actual Price"
            connectNulls={false}
          />

          {/* ML forecast */}
          <Line
            type="monotone"
            dataKey="predicted"
            stroke="#7c3aed"
            strokeWidth={2}
            strokeDasharray="5 5"
            dot={{ r: 3, fill: '#7c3aed' }}
            name="Forecast"
            connectNulls={false}
          />
        </ComposedChart>
      </ResponsiveContainer>
    </div>
  );
};
```

---

## 📋 MaterialsTable Component

```tsx
// src/components/features/MaterialsTable.tsx
import React, { useState, useMemo } from 'react';
import { PriceRecord } from '../../types/PriceRecord';
import { StockBadge } from '../common/StockBadge';

type SortKey = keyof Pick<PriceRecord,
  'material_name' | 'material_category' | 'supplier_name' | 'region' |
  'province' | 'price_zar' | 'price_per_kg_zar' |
  'price_change_mom_pct' | 'price_change_yoy_pct' | 'date'>;

export const MaterialsTable: React.FC<{ records: PriceRecord[] }> = ({ records }) => {
  const [sortKey,   setSortKey]   = useState<SortKey>('date');
  const [sortOrder, setSortOrder] = useState<'asc' | 'desc'>('desc');

  const sorted = useMemo(() => {
    return [...records].sort((a, b) => {
      const av = a[sortKey], bv = b[sortKey];
      if (typeof av === 'string' && typeof bv === 'string') {
        return sortOrder === 'asc' ? av.localeCompare(bv) : bv.localeCompare(av);
      }
      if (typeof av === 'number' && typeof bv === 'number') {
        return sortOrder === 'asc' ? av - bv : bv - av;
      }
      return 0;
    });
  }, [records, sortKey, sortOrder]);

  const toggleSort = (key: SortKey) => {
    if (sortKey === key) setSortOrder(o => o === 'asc' ? 'desc' : 'asc');
    else { setSortKey(key); setSortOrder('asc'); }
  };

  const Th: React.FC<{ col: SortKey; label: string; right?: boolean }> = ({ col, label, right }) => (
    <th
      className={`px-4 py-3 text-xs font-semibold cursor-pointer select-none ${right ? 'text-right' : 'text-left'}`}
      onClick={() => toggleSort(col)}
    >
      {label} {sortKey === col ? (sortOrder === 'asc' ? '↑' : '↓') : <span className="text-blue-300">⇅</span>}
    </th>
  );

  const changeCss = (v: number) => ({ color: v >= 0 ? '#dc2626' : '#16a34a' });

  return (
    <div className="overflow-x-auto rounded-[10px]" style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}>
      <table className="min-w-full bg-white text-sm">
        <thead style={{ backgroundColor: '#1e3a8a', color: 'white' }}>
          <tr>
            <Th col="material_name"        label="Material" />
            <Th col="material_category"    label="Category" />
            <Th col="supplier_name"        label="Supplier" />
            <Th col="region"               label="Region" />
            <Th col="province"             label="Province" />
            <Th col="price_zar"            label="Price (ZAR)"   right />
            <th className="px-4 py-3 text-xs font-semibold text-left">Unit</th>
            <Th col="price_per_kg_zar"     label="Per Kg"        right />
            <Th col="price_change_mom_pct" label="MoM %"         right />
            <Th col="price_change_yoy_pct" label="YoY %"         right />
            <th className="px-4 py-3 text-xs font-semibold text-left">Stock</th>
            <th className="px-4 py-3 text-xs font-semibold text-left">Bulk</th>
            <Th col="date"                 label="Date" />
          </tr>
        </thead>
        <tbody>
          {sorted.map((r, i) => (
            <tr
              key={r.record_id}   // ← uses record_id (not id which doesn't exist)
              className={i % 2 === 0 ? 'bg-gray-50' : 'bg-white'}
              style={{ borderBottom: '1px solid #e5e7eb' }}
            >
              <td className="px-4 py-3 font-medium text-gray-900">{r.material_name}</td>
              <td className="px-4 py-3 text-gray-600">{r.material_category}</td>
              <td className="px-4 py-3 text-gray-600">{r.supplier_name}</td>
              <td className="px-4 py-3 text-gray-600">{r.region}</td>
              <td className="px-4 py-3 text-gray-600">{r.province}</td>
              <td className="px-4 py-3 text-right font-semibold" style={{ color: '#2563eb' }}>
                R{r.price_zar.toFixed(2)}
              </td>
              <td className="px-4 py-3 text-gray-600">{r.unit}</td>
              <td className="px-4 py-3 text-right text-gray-600">R{r.price_per_kg_zar.toFixed(2)}</td>
              <td className="px-4 py-3 text-right font-medium" style={changeCss(r.price_change_mom_pct)}>
                {r.price_change_mom_pct >= 0 ? '+' : ''}{r.price_change_mom_pct.toFixed(1)}%
              </td>
              <td className="px-4 py-3 text-right font-medium" style={changeCss(r.price_change_yoy_pct)}>
                {r.price_change_yoy_pct >= 0 ? '+' : ''}{r.price_change_yoy_pct.toFixed(1)}%
              </td>
              <td className="px-4 py-3"><StockBadge status={r.stock_status} /></td>
              <td className="px-4 py-3 text-gray-600">{r.bulk_discount_available}</td>
              <td className="px-4 py-3 text-gray-500">{r.date}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};
```

---

## 🧭 Layout & Navigation

```tsx
// src/components/layout/Header.tsx
import React from 'react';
import { NavLink } from 'react-router-dom';

export const Header: React.FC = () => (
  <header className="bg-primary-900 text-white shadow-lg">
    <nav className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex justify-between items-center h-16">
      <span className="text-xl font-bold tracking-tight">🏗️ BuildMat Intelligence</span>
      <div className="flex gap-6 text-sm font-medium">
        {[
          { to: '/dashboard',  label: 'Dashboard'  },
          { to: '/materials',  label: 'Materials'  },
          { to: '/compare',    label: 'Compare'    },
          { to: '/forecasts',  label: 'Forecasts'  },
        ].map(({ to, label }) => (
          <NavLink
            key={to}
            to={to}
            className={({ isActive }) =>
              isActive
                ? 'text-white border-b-2 border-white pb-1'
                : 'text-blue-200 hover:text-white transition-colors'
            }
          >
            {label}
          </NavLink>
        ))}
      </div>
    </nav>
  </header>
);

// src/components/layout/Layout.tsx
export const Layout: React.FC<{ children: React.ReactNode }> = ({ children }) => (
  <div className="min-h-screen bg-gray-50">
    <Header />
    <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      {children}
    </main>
  </div>
);
```

---

## 📄 Pages

### MaterialsPage

```tsx
// src/pages/MaterialsPage.tsx
import React, { useState } from 'react';
import { usePrices } from '../hooks/usePrices';
import { PriceCard } from '../components/features/PriceCard';
import { LoadingSpinner } from '../components/common/LoadingSpinner';
import { ErrorMessage } from '../components/common/ErrorMessage';

const CATEGORIES = ['All', 'Cement', 'Building', 'Steel', 'Timber', 'Electrical'];
const REGIONS    = ['All', 'Gauteng', 'Western Cape', 'KwaZulu-Natal', 'Eastern Cape'];

export const MaterialsPage: React.FC = () => {
  const [category, setCategory] = useState('');
  const [region,   setRegion]   = useState('');
  const [search,   setSearch]   = useState('');

  const { data, isLoading, error, refetch } = usePrices({
    material_category: category || undefined,
    region:            region   || undefined,
    per_page: 48,
  });

  const records = (data?.data ?? []).filter((r) =>
    !search || r.material_name.toLowerCase().includes(search.toLowerCase())
  );

  if (isLoading) return <LoadingSpinner size="lg" />;
  if (error)     return <ErrorMessage message="Failed to load materials" onRetry={refetch} />;

  return (
    <div className="space-y-6">
      {/* Filters */}
      <div className="flex flex-wrap gap-4 items-center">
        <input
          type="text"
          placeholder="Search materials..."
          value={search}
          onChange={(e) => setSearch(e.target.value)}
          className="border border-gray-300 rounded-lg px-4 py-2 text-sm flex-1 min-w-48 focus:outline-none focus:ring-2 focus:ring-primary-500"
        />
        <select
          value={category}
          onChange={(e) => setCategory(e.target.value)}
          className="border border-gray-300 rounded-lg px-4 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary-500"
        >
          {CATEGORIES.map((c) => <option key={c} value={c === 'All' ? '' : c}>{c}</option>)}
        </select>
        <select
          value={region}
          onChange={(e) => setRegion(e.target.value)}
          className="border border-gray-300 rounded-lg px-4 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary-500"
        >
          {REGIONS.map((r) => <option key={r} value={r === 'All' ? '' : r}>{r}</option>)}
        </select>
      </div>

      {/* Count */}
      <p className="text-sm text-gray-500">{records.length} records</p>

      {/* Grid */}
      {records.length === 0
        ? <p className="text-center text-gray-400 py-16">No materials found.</p>
        : (
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            {records.map((r) => (
              <PriceCard key={r.record_id} record={r} />
            ))}
          </div>
        )
      }
    </div>
  );
};
```

### MaterialDetailPage

```tsx
// src/pages/MaterialDetailPage.tsx
import React, { useState } from 'react';
import { useParams } from 'react-router-dom';
import { usePriceHistory } from '../hooks/usePrices';
import { useForecasts } from '../hooks/useForecasts';
import { Card } from '../components/common/Card';
import { LoadingSpinner } from '../components/common/LoadingSpinner';
import { ErrorMessage } from '../components/common/ErrorMessage';
import { PriceHistoryChart } from '../components/charts/PriceHistoryChart';
import { ForecastChart } from '../components/charts/ForecastChart';

export const MaterialDetailPage: React.FC = () => {
  // Route: /materials/:materialName (URL-encoded)
  const { materialName: encodedName } = useParams<{ materialName: string }>();
  const materialName = decodeURIComponent(encodedName ?? '');

  const [historyDays, setHistoryDays] = useState(90);

  const { data: histData, isLoading: histLoading, error: histError } =
    usePriceHistory(materialName, historyDays);

  const { data: fcastData, isLoading: fcastLoading } =
    useForecasts(materialName, 30);

  if (histLoading) return <LoadingSpinner size="lg" />;
  if (histError)   return <ErrorMessage message={`Failed to load data for ${materialName}`} />;

  const history  = histData?.history ?? [];

  // Stats derived from the history aggregates returned by the API
  const prices   = history.map((h) => h.avg_price);
  const latest   = prices[prices.length - 1] ?? 0;
  const previous = prices[prices.length - 2] ?? latest;
  const change   = latest - previous;
  const changePct = previous ? ((change / previous) * 100).toFixed(1) : '0';
  const avg      = prices.length ? prices.reduce((a, b) => a + b, 0) / prices.length : 0;
  const minPrice = prices.length ? Math.min(...prices) : 0;
  const maxPrice = prices.length ? Math.max(...prices) : 0;

  return (
    <div className="space-y-6">
      {/* Header */}
      <Card>
        <div className="flex justify-between items-start">
          <div>
            <h1 className="text-3xl font-bold text-gray-900">{materialName}</h1>
            <p className="text-gray-500 mt-1">Last {historyDays} days of price history</p>
          </div>
          <div className="text-right">
            <p className="text-3xl font-bold" style={{ color: '#2563eb' }}>R{latest.toFixed(2)}</p>
            <p className={`text-sm font-medium ${change >= 0 ? 'text-red-600' : 'text-green-600'}`}>
              {change >= 0 ? '▲' : '▼'} {Math.abs(change).toFixed(2)} ({changePct}%) vs prev period
            </p>
          </div>
        </div>
      </Card>

      {/* Stat cards */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <Card title="Average Price">
          <p className="text-2xl font-bold text-gray-900">R{avg.toFixed(2)}</p>
        </Card>
        <Card title="Lowest Price">
          <p className="text-2xl font-bold text-green-600">R{minPrice.toFixed(2)}</p>
        </Card>
        <Card title="Highest Price">
          <p className="text-2xl font-bold text-red-600">R{maxPrice.toFixed(2)}</p>
        </Card>
      </div>

      {/* History chart */}
      <div>
        <div className="flex gap-2 mb-4">
          {[30, 60, 90, 180].map((d) => (
            <button
              key={d}
              onClick={() => setHistoryDays(d)}
              className={`px-3 py-1 rounded-lg text-sm font-medium transition-colors ${
                historyDays === d
                  ? 'bg-primary-600 text-white'
                  : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
              }`}
            >
              {d}d
            </button>
          ))}
        </div>
        <PriceHistoryChart data={history} materialName={materialName} showRange />
      </div>

      {/* Forecast chart */}
      {!fcastLoading && fcastData?.forecasts?.length ? (
        <ForecastChart
          historical={history.map((h) => ({ date: h.date, price_zar: h.avg_price }))}
          forecasts={fcastData.forecasts}
          materialName={materialName}
        />
      ) : fcastLoading ? (
        <Card title="Price Forecast"><LoadingSpinner /></Card>
      ) : null}
    </div>
  );
};
```

### ComparisonPage

```tsx
// src/pages/ComparisonPage.tsx
import React, { useState } from 'react';
import { usePriceComparison } from '../hooks/usePrices';
import { SupplierComparisonChart } from '../components/charts/SupplierComparisonChart';
import { MaterialsTable } from '../components/features/MaterialsTable';
import { LoadingSpinner } from '../components/common/LoadingSpinner';
import { Card } from '../components/common/Card';

const MATERIALS = [
  'PPC Cement 50kg', 'River Sand', 'Crusher Stone 19mm',
  'Steel Rebar 12mm', 'Timber Pine 76x38', 'PVC Conduit 20mm',
];
const REGIONS = ['Gauteng', 'Western Cape', 'KwaZulu-Natal', 'Eastern Cape'];

export const ComparisonPage: React.FC = () => {
  const [material, setMaterial] = useState(MATERIALS[0]);
  const [region,   setRegion]   = useState('Gauteng');

  const { data, isLoading, error } = usePriceComparison(material, region);

  return (
    <div className="space-y-6">
      <Card title="Supplier Price Comparison">
        <div className="flex flex-wrap gap-4 mb-6">
          <select
            value={material}
            onChange={(e) => setMaterial(e.target.value)}
            className="border border-gray-300 rounded-lg px-4 py-2 text-sm flex-1 focus:outline-none focus:ring-2 focus:ring-primary-500"
          >
            {MATERIALS.map((m) => <option key={m}>{m}</option>)}
          </select>
          <select
            value={region}
            onChange={(e) => setRegion(e.target.value)}
            className="border border-gray-300 rounded-lg px-4 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary-500"
          >
            {REGIONS.map((r) => <option key={r}>{r}</option>)}
          </select>
        </div>

        {isLoading && <LoadingSpinner />}
        {error     && <p className="text-red-500">Failed to load comparison data.</p>}

        {data?.suppliers && (
          <>
            <SupplierComparisonChart data={data.suppliers} materialName={material} />
            <div className="mt-6">
              <h4 className="text-sm font-semibold text-gray-700 mb-3">All Supplier Records</h4>
              <MaterialsTable records={data.suppliers} />
            </div>
          </>
        )}
      </Card>
    </div>
  );
};
```

---

## 📅 WEEK 1 — March 9–15, 2026
### Project Setup & Design System

**Monday (March 9):** Complete all setup steps above. Verify `npm start` and `npm run build` both pass.

**Tuesday–Wednesday (March 10–11):** Build the design system — `Button`, `Card`, `LoadingSpinner`, `ErrorMessage`, `StockBadge`, `BulkDiscountTag`. Write a simple `src/pages/DesignSystemPage.tsx` that renders all components side-by-side so you can visually verify them.

**Thursday–Friday (March 12–13):** Build `Header`, `Layout`, and wire up `react-router-dom` routes. Create stub pages for each route so navigation works end-to-end.

```tsx
// src/App.tsx
import React from 'react';
import { Routes, Route, Navigate } from 'react-router-dom';
import { Layout } from './components/layout/Layout';
import { MaterialsPage }   from './pages/MaterialsPage';
import { MaterialDetailPage } from './pages/MaterialDetailPage';
import { ComparisonPage }  from './pages/ComparisonPage';

const App: React.FC = () => (
  <Layout>
    <Routes>
      <Route path="/"              element={<Navigate to="/materials" replace />} />
      <Route path="/materials"     element={<MaterialsPage />} />
      <Route path="/materials/:materialName" element={<MaterialDetailPage />} />
      <Route path="/compare"       element={<ComparisonPage />} />
      <Route path="/forecasts"     element={<div>Forecasts coming Week 3</div>} />
    </Routes>
  </Layout>
);

export default App;
```

**Deliverable:** React project running with navigation, design system components visible.

---

## 📅 WEEK 2 — March 16–22, 2026
### API Integration & State Management

**Monday–Tuesday (March 16–17):** Wire up `apiService.ts` and both hook files. Point `REACT_APP_API_URL` at Member 1's running backend. Verify `usePrices()` returns real data in browser DevTools.

**Wednesday–Friday (March 18–20):** Build `MaterialsPage` with working filters. Each card should show real `price_zar`, `stock_status`, and `bulk_discount_available` from the API.

**Deliverable:** Materials listing page populated with live API data.

---

## 📅 WEEK 3 — March 23–29, 2026
### Charts + D1

**Monday–Tuesday (March 23–24):** Build `PriceHistoryChart` and `SupplierComparisonChart`. Wire up `MaterialDetailPage` and `ComparisonPage` with real data.

#### D1 Demo — Wednesday (March 25) 🎯

Demo flow:
1. Open Materials page — show cards with real prices, stock status, bulk discount tags
2. Click a material — navigate to Detail page showing the history chart
3. Switch to Compare page — show bar chart with cheapest supplier highlighted
4. Toggle history range (30/60/90d) — chart updates live

**Thursday–Friday (March 26–27):** Build `ForecastChart`. Wire `useForecasts` to `MaterialDetailPage`.

**Deliverable:** Dashboard with live charts and working navigation.

---

## 📅 WEEKS 4–5 — March 30–April 12, 2026
### Advanced Features + ST1/D2

#### Dashboard Page (Week 4, Monday–Tuesday)

```tsx
// src/pages/DashboardPage.tsx
import React from 'react';
import { usePrices }          from '../hooks/usePrices';
import { Card }               from '../components/common/Card';
import { LoadingSpinner }     from '../components/common/LoadingSpinner';
import { PriceCard }          from '../components/features/PriceCard';
import { useNavigate }        from 'react-router-dom';

const HIGHLIGHT_MATERIALS = [
  'PPC Cement 50kg', 'River Sand', 'Steel Rebar 12mm', 'Timber Pine 76x38',
];

export const DashboardPage: React.FC = () => {
  const navigate = useNavigate();
  const { data, isLoading } = usePrices({ per_page: 12 });
  const records = data?.data ?? [];

  const inStock    = records.filter((r) => r.stock_status === 'In Stock').length;
  const lowStock   = records.filter((r) => r.stock_status === 'Low Stock').length;
  const withBulk   = records.filter((r) => r.bulk_discount_available === 'Yes').length;
  const avgPrice   = records.length
    ? records.reduce((s, r) => s + r.price_zar, 0) / records.length
    : 0;

  return (
    <div className="space-y-8">
      {/* KPI row */}
      <div className="grid grid-cols-2 lg:grid-cols-4 gap-4">
        {[
          { label: 'In Stock',       value: inStock,             color: '#16a34a' },
          { label: 'Low Stock',      value: lowStock,            color: '#f59e0b' },
          { label: 'Bulk Deals',     value: withBulk,            color: '#2563eb' },
          { label: 'Avg Price (ZAR)',value: `R${avgPrice.toFixed(0)}`, color: '#7c3aed' },
        ].map(({ label, value, color }) => (
          <Card key={label} className="text-center">
            <p className="text-sm text-gray-500 mb-1">{label}</p>
            <p className="text-3xl font-bold" style={{ color }}>{value}</p>
          </Card>
        ))}
      </div>

      {/* Quick-access material cards */}
      <div>
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-xl font-bold text-gray-900">Latest Prices</h2>
          <button
            onClick={() => navigate('/materials')}
            className="text-sm text-primary-600 hover:underline font-medium"
          >
            View all →
          </button>
        </div>
        {isLoading
          ? <LoadingSpinner />
          : (
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              {records.slice(0, 6).map((r) => (
                <PriceCard
                  key={r.record_id}
                  record={r}
                  onClick={() => navigate(`/materials/${encodeURIComponent(r.material_name)}`)}
                />
              ))}
            </div>
          )
        }
      </div>

      {/* Quick links */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
        {[
          { label: '🔍 Compare Suppliers', to: '/compare',   desc: 'Find cheapest supplier per material' },
          { label: '📈 Price Forecasts',   to: '/forecasts', desc: 'ML predictions for next 30 days'    },
          { label: '🚨 Price Alerts',      to: '/alerts',    desc: 'Set threshold notifications'         },
        ].map(({ label, to, desc }) => (
          <Card
            key={to}
            className="cursor-pointer hover:shadow-lg transition-shadow"
            // @ts-ignore — onClick works on Card's outer div
            onClick={() => navigate(to)}
          >
            <p className="text-lg font-semibold text-gray-900">{label}</p>
            <p className="text-sm text-gray-500 mt-1">{desc}</p>
          </Card>
        ))}
      </div>
    </div>
  );
};
```

#### Pagination Component (Week 4, Wednesday)

```tsx
// src/components/common/Pagination.tsx
import React from 'react';

interface PaginationProps {
  page:        number;
  totalPages:  number;
  onPageChange: (p: number) => void;
}

export const Pagination: React.FC<PaginationProps> = ({ page, totalPages, onPageChange }) => {
  if (totalPages <= 1) return null;

  // Show up to 5 page numbers centred around current
  const pages: number[] = [];
  const start = Math.max(1, page - 2);
  const end   = Math.min(totalPages, page + 2);
  for (let i = start; i <= end; i++) pages.push(i);

  const btn = (label: React.ReactNode, target: number, disabled = false) => (
    <button
      key={String(label)}
      onClick={() => !disabled && onPageChange(target)}
      disabled={disabled}
      className={`px-3 py-1.5 rounded-lg text-sm font-medium transition-colors ${
        target === page && !disabled
          ? 'bg-primary-600 text-white'
          : 'bg-white text-gray-600 hover:bg-gray-100 border border-gray-300'
      } disabled:opacity-40 disabled:cursor-not-allowed`}
    >
      {label}
    </button>
  );

  return (
    <div className="flex items-center justify-center gap-1 mt-6">
      {btn('‹ Prev', page - 1, page === 1)}
      {start > 1 && <>{btn(1, 1)}{start > 2 && <span className="px-1 text-gray-400">…</span>}</>}
      {pages.map((p) => btn(p, p))}
      {end < totalPages && <>{end < totalPages - 1 && <span className="px-1 text-gray-400">…</span>}{btn(totalPages, totalPages)}</>}
      {btn('Next ›', page + 1, page === totalPages)}
    </div>
  );
};
```

Wire pagination into `MaterialsPage`:

```tsx
// Add to MaterialsPage.tsx — replace the existing page state and grid
const [page, setPage] = useState(1);
const PER_PAGE = 18;

const { data, isLoading, error, refetch } = usePrices({
  material_category: category || undefined,
  region:            region   || undefined,
  page,
  per_page: PER_PAGE,
});

// After the grid closing tag, add:
// <Pagination
//   page={page}
//   totalPages={data?.pagination.total_pages ?? 1}
//   onPageChange={setPage}
// />
// Reset to page 1 when filters change:
// useEffect(() => setPage(1), [category, region, search]);
```

#### Price Alert Form (Week 4, Thursday–Friday)

```tsx
// src/components/features/PriceAlertForm.tsx
import React from 'react';
import { useForm } from 'react-hook-form';
import { apiService } from '../../services/apiService';

interface AlertFormData {
  material_name:   string;
  threshold_price: number;
  threshold_type:  'above' | 'below';
  region:          string;
}

const MATERIALS = [
  'PPC Cement 50kg', 'River Sand', 'Crusher Stone 19mm',
  'Steel Rebar 12mm', 'Timber Pine 76x38', 'PVC Conduit 20mm',
];
const REGIONS = ['Gauteng', 'Western Cape', 'KwaZulu-Natal', 'Eastern Cape'];

export const PriceAlertForm: React.FC<{ onSuccess?: () => void }> = ({ onSuccess }) => {
  const { register, handleSubmit, reset, formState: { errors, isSubmitting } } =
    useForm<AlertFormData>({ defaultValues: { threshold_type: 'below' } });

  const onSubmit = async (data: AlertFormData) => {
    try {
      await apiService['client' as any].post('/auth/alerts', data);
      reset();
      onSuccess?.();
    } catch (e) {
      console.error('Alert creation failed:', e);
    }
  };

  const fieldCls = 'w-full border border-gray-300 rounded-lg px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary-500';
  const errCls   = 'text-xs text-red-500 mt-1';

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <label className="block text-sm font-medium text-gray-700 mb-1">Material</label>
        <select className={fieldCls} {...register('material_name', { required: true })}>
          <option value="">Select material…</option>
          {MATERIALS.map((m) => <option key={m}>{m}</option>)}
        </select>
        {errors.material_name && <p className={errCls}>Material is required</p>}
      </div>

      <div className="grid grid-cols-2 gap-4">
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Alert when price is</label>
          <select className={fieldCls} {...register('threshold_type')}>
            <option value="below">Below</option>
            <option value="above">Above</option>
          </select>
        </div>
        <div>
          <label className="block text-sm font-medium text-gray-700 mb-1">Threshold (ZAR)</label>
          <input
            type="number"
            step="0.01"
            min="0"
            className={fieldCls}
            placeholder="e.g. 85.00"
            {...register('threshold_price', { required: true, min: 0 })}
          />
          {errors.threshold_price && <p className={errCls}>Valid price required</p>}
        </div>
      </div>

      <div>
        <label className="block text-sm font-medium text-gray-700 mb-1">Region (optional)</label>
        <select className={fieldCls} {...register('region')}>
          <option value="">All regions</option>
          {REGIONS.map((r) => <option key={r}>{r}</option>)}
        </select>
      </div>

      <button
        type="submit"
        disabled={isSubmitting}
        className="w-full btn-primary disabled:opacity-50"
      >
        {isSubmitting ? 'Creating…' : '🔔 Create Alert'}
      </button>
    </form>
  );
};
```

#### Anomaly Indicator on PriceCard (Week 5, Monday)

Extend `PriceCard` to accept an optional `isAnomaly` flag — the parent page fetches anomaly data from `GET /api/anomalies` and passes it down:

```tsx
// src/hooks/useAnomalies.ts
import { useQuery } from '@tanstack/react-query';
import { apiService } from '../services/apiService';

export const useAnomalies = (materialName?: string, threshold = 20) => {
  return useQuery({
    queryKey:  ['anomalies', materialName, threshold],
    queryFn:   async () => {
      const { data } = await (apiService as any)['client'].get('/anomalies', {
        params: { material_name: materialName, threshold },
      });
      return data as { anomalies: Array<{ record_id: string; deviation_pct: number }> };
    },
    staleTime: 10 * 60 * 1000,
  });
};
```

```tsx
// Updated PriceCard signature — add isAnomaly prop and red ring
// In PriceCard.tsx, update the outer div:
// <div
//   className={`bg-white rounded-[10px] p-6 cursor-pointer hover:shadow-xl transition-shadow
//     ${isAnomaly ? 'ring-2 ring-red-400' : ''}`}
//   ...
// >
//   {isAnomaly && (
//     <div className="flex items-center gap-1 mb-2 text-xs text-red-600 font-semibold">
//       ⚠️ Price anomaly detected
//     </div>
//   )}

// Updated interface:
// interface PriceCardProps {
//   record:     PriceRecord;
//   isAnomaly?: boolean;
//   onClick?:   () => void;
// }
```

#### CSV Export Utility (Week 5, Tuesday)

```typescript
// src/utils/exportCsv.ts
import { PriceRecord } from '../types/PriceRecord';

const HEADERS: (keyof PriceRecord)[] = [
  'record_id', 'date', 'year', 'month', 'material_name', 'material_category',
  'supplier_name', 'region', 'province', 'price_zar', 'unit', 'price_per_kg_zar',
  'price_change_mom_pct', 'price_change_yoy_pct', 'stock_status', 'bulk_discount_available',
];

export const exportToCsv = (records: PriceRecord[], filename = 'buildmat-prices.csv') => {
  const escape = (v: string | number) => {
    const s = String(v);
    return s.includes(',') || s.includes('"') || s.includes('\n')
      ? `"${s.replace(/"/g, '""')}"`
      : s;
  };

  const rows = [
    HEADERS.join(','),
    ...records.map((r) => HEADERS.map((h) => escape(r[h])).join(',')),
  ];

  const blob = new Blob([rows.join('\n')], { type: 'text/csv;charset=utf-8;' });
  const url  = URL.createObjectURL(blob);
  const a    = document.createElement('a');
  a.href     = url;
  a.download = filename;
  a.click();
  URL.revokeObjectURL(url);
};
```

Add export button to `MaterialsTable`:

```tsx
// Add above the table in MaterialsTable.tsx
// <div className="flex justify-between items-center mb-3">
//   <p className="text-sm text-gray-500">{records.length} records</p>
//   <button
//     onClick={() => exportToCsv(records)}
//     className="btn-secondary text-sm flex items-center gap-2"
//   >
//     ⬇️ Export CSV
//   </button>
// </div>
```

#### Lazy Route Splitting + Skeleton Screens (Week 5, Wednesday–Friday)

```tsx
// src/App.tsx — replace static imports with lazy ones
import React, { lazy, Suspense } from 'react';
import { Routes, Route, Navigate } from 'react-router-dom';
import { Layout }       from './components/layout/Layout';
import { CardSkeleton } from './components/common/CardSkeleton';

const DashboardPage     = lazy(() => import('./pages/DashboardPage').then(m => ({ default: m.DashboardPage })));
const MaterialsPage     = lazy(() => import('./pages/MaterialsPage').then(m => ({ default: m.MaterialsPage })));
const MaterialDetailPage = lazy(() => import('./pages/MaterialDetailPage').then(m => ({ default: m.MaterialDetailPage })));
const ComparisonPage    = lazy(() => import('./pages/ComparisonPage').then(m => ({ default: m.ComparisonPage })));
const ForecastsPage     = lazy(() => import('./pages/ForecastsPage').then(m => ({ default: m.ForecastsPage })));
const AlertsPage        = lazy(() => import('./pages/AlertsPage').then(m => ({ default: m.AlertsPage })));

const PageFallback = () => (
  <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 pt-4">
    {[...Array(6)].map((_, i) => <CardSkeleton key={i} />)}
  </div>
);

const App: React.FC = () => (
  <Layout>
    <Suspense fallback={<PageFallback />}>
      <Routes>
        <Route path="/"                          element={<Navigate to="/dashboard" replace />} />
        <Route path="/dashboard"                 element={<DashboardPage />} />
        <Route path="/materials"                 element={<MaterialsPage />} />
        <Route path="/materials/:materialName"   element={<MaterialDetailPage />} />
        <Route path="/compare"                   element={<ComparisonPage />} />
        <Route path="/forecasts"                 element={<ForecastsPage />} />
        <Route path="/alerts"                    element={<AlertsPage />} />
      </Routes>
    </Suspense>
  </Layout>
);

export default App;
```

```tsx
// src/components/common/CardSkeleton.tsx
// Content-shaped skeleton — matches PriceCard dimensions
import React from 'react';

export const CardSkeleton: React.FC = () => (
  <div
    className="bg-white rounded-[10px] p-6 animate-pulse"
    style={{ boxShadow: '0 4px 12px rgba(0,0,0,0.1)' }}
  >
    <div className="h-5 bg-gray-200 rounded w-3/4 mb-2" />
    <div className="h-3 bg-gray-100 rounded w-1/3 mb-4" />
    <div className="h-8 bg-gray-200 rounded w-1/2 mb-1" />
    <div className="h-3 bg-gray-100 rounded w-1/4 mb-4" />
    <div className="flex gap-2 mb-3">
      <div className="h-5 bg-gray-100 rounded-lg w-20" />
      <div className="h-5 bg-gray-100 rounded-lg w-20" />
    </div>
    <div className="flex gap-2 mb-3">
      <div className="h-5 bg-green-100 rounded-full w-20" />
    </div>
    <div className="h-3 bg-gray-100 rounded w-2/3" />
  </div>
);
```

**ST1 Demo — April 1 🎯** — Full UI with live data, pagination, sorting, responsive layout, API integration.

**D2 Demo — April 8 🎯** — Dashboard KPIs, forecast chart live, CSV export, price alert form, anomaly indicators.

---

## 📅 WEEKS 6–8 — April 13–May 3, 2026
### Polish + PWA + MVP

#### Offline Detection Banner (Week 6)

```tsx
// src/hooks/useOnlineStatus.ts
import { useState, useEffect } from 'react';

export const useOnlineStatus = () => {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  useEffect(() => {
    const on  = () => setIsOnline(true);
    const off = () => setIsOnline(false);
    window.addEventListener('online',  on);
    window.addEventListener('offline', off);
    return () => { window.removeEventListener('online', on); window.removeEventListener('offline', off); };
  }, []);
  return isOnline;
};
```

```tsx
// src/components/layout/OfflineBanner.tsx
import React from 'react';
import { useOnlineStatus } from '../../hooks/useOnlineStatus';

export const OfflineBanner: React.FC = () => {
  const isOnline = useOnlineStatus();
  if (isOnline) return null;
  return (
    <div className="bg-amber-50 border-b border-amber-200 px-4 py-2 text-sm text-amber-800 flex items-center gap-2">
      ⚠️ You are offline — showing cached data. Prices may be outdated.
    </div>
  );
};
// Add <OfflineBanner /> to Layout.tsx, between <Header /> and <main>
```

#### Forecasts Page (Week 6)

```tsx
// src/pages/ForecastsPage.tsx
import React, { useState } from 'react';
import { useForecasts }     from '../hooks/useForecasts';
import { usePriceHistory }  from '../hooks/usePrices';
import { ForecastChart }    from '../components/charts/ForecastChart';
import { Card }             from '../components/common/Card';
import { LoadingSpinner }   from '../components/common/LoadingSpinner';

const MATERIALS = [
  'PPC Cement 50kg', 'River Sand', 'Crusher Stone 19mm',
  'Steel Rebar 12mm', 'Timber Pine 76x38',
];

export const ForecastsPage: React.FC = () => {
  const [material, setMaterial] = useState(MATERIALS[0]);
  const [days,     setDays]     = useState(30);

  const { data: hist,   isLoading: histLoading  } = usePriceHistory(material, 90);
  const { data: fcast,  isLoading: fcastLoading } = useForecasts(material, days);

  const isLoading = histLoading || fcastLoading;

  return (
    <div className="space-y-6">
      <Card title="Price Forecast">
        <div className="flex flex-wrap gap-4 mb-6">
          <select
            value={material}
            onChange={(e) => setMaterial(e.target.value)}
            className="border border-gray-300 rounded-lg px-4 py-2 text-sm flex-1 focus:outline-none focus:ring-2 focus:ring-primary-500"
          >
            {MATERIALS.map((m) => <option key={m}>{m}</option>)}
          </select>
          <div className="flex gap-2">
            {[7, 14, 30].map((d) => (
              <button
                key={d}
                onClick={() => setDays(d)}
                className={`px-4 py-2 rounded-lg text-sm font-medium transition-colors ${
                  days === d
                    ? 'bg-primary-600 text-white'
                    : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
                }`}
              >
                {d}d
              </button>
            ))}
          </div>
        </div>

        {isLoading && <LoadingSpinner />}

        {!isLoading && fcast?.forecasts?.length ? (
          <>
            <ForecastChart
              historical={hist?.history?.map((h) => ({ date: h.date, price_zar: h.avg_price })) ?? []}
              forecasts={fcast.forecasts}
              materialName={material}
            />
            {/* Forecast table */}
            <div className="mt-6 overflow-x-auto">
              <table className="w-full text-sm">
                <thead>
                  <tr className="border-b border-gray-200 text-left text-gray-500">
                    <th className="pb-2 pr-4">Date</th>
                    <th className="pb-2 pr-4">Forecast</th>
                    <th className="pb-2 pr-4">Lower CI</th>
                    <th className="pb-2 pr-4">Upper CI</th>
                    <th className="pb-2">Model</th>
                  </tr>
                </thead>
                <tbody>
                  {fcast.forecasts.map((f) => (
                    <tr key={f.forecast_date} className="border-b border-gray-100">
                      <td className="py-2 pr-4 text-gray-600">{f.forecast_date}</td>
                      <td className="py-2 pr-4 font-semibold" style={{ color: '#7c3aed' }}>
                        R{f.predicted_price.toFixed(2)}
                      </td>
                      <td className="py-2 pr-4 text-gray-400">
                        R{f.confidence_interval_lower?.toFixed(2) ?? '—'}
                      </td>
                      <td className="py-2 pr-4 text-gray-400">
                        R{f.confidence_interval_upper?.toFixed(2) ?? '—'}
                      </td>
                      <td className="py-2 text-gray-400 text-xs">{f.model_name}</td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </>
        ) : !isLoading ? (
          <p className="text-center text-gray-400 py-8">No forecast data available yet.</p>
        ) : null}
      </Card>
    </div>
  );
};
```

#### Alerts Page (Week 7)

```tsx
// src/pages/AlertsPage.tsx
import React from 'react';
import { Card }           from '../components/common/Card';
import { PriceAlertForm } from '../components/features/PriceAlertForm';

export const AlertsPage: React.FC = () => {
  return (
    <div className="max-w-xl mx-auto space-y-6">
      <Card title="Price Alerts" subtitle="Get notified when a material crosses your threshold">
        <PriceAlertForm onSuccess={() => alert('Alert created!')} />
      </Card>
      <Card title="How it works">
        <ol className="space-y-3 text-sm text-gray-600 list-decimal list-inside">
          <li>Choose a material and set your price threshold</li>
          <li>The backend checks new scraper data each night</li>
          <li>You receive a notification when the price crosses your threshold</li>
          <li>Alerts auto-deactivate after triggering once (re-enable manually)</li>
        </ol>
      </Card>
    </div>
  );
};
```

#### PWA Setup (Week 8)

```bash
# Rebuild with the PWA template (one-time only — do this on a fresh create-react-app)
npx create-react-app buildmat-dashboard --template cra-template-pwa --typescript

# OR add manually to existing project:
npm install workbox-core workbox-precaching workbox-routing workbox-strategies
```

```json
// public/manifest.json
{
  "short_name": "BuildMat",
  "name": "BuildMat Price Intelligence",
  "description": "Real-time SA building materials price intelligence",
  "icons": [
    { "src": "favicon.ico",  "sizes": "64x64",   "type": "image/x-icon" },
    { "src": "logo192.png",  "sizes": "192x192", "type": "image/png"    },
    { "src": "logo512.png",  "sizes": "512x512", "type": "image/png"    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#1e3a8a",
  "background_color": "#f9fafb"
}
```

```typescript
// src/serviceWorkerRegistration.ts (included in cra-template-pwa)
// Call serviceWorkerRegistration.register() in src/index.tsx to enable offline caching.
// The default workbox setup will cache the app shell — price data remains live via API.
```

**D3 (May 1) — MVP Complete** 🎉

---

## 📅 WEEKS 9–13 — May 4–June 9, 2026
### Testing & Final Polish

#### Unit Tests (Week 9)

```tsx
// src/hooks/__tests__/usePrices.test.ts
import { renderHook, waitFor } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { usePrices } from '../usePrices';
import { apiService } from '../../services/apiService';

jest.mock('../../services/apiService');

const wrapper = ({ children }: { children: React.ReactNode }) => {
  const qc = new QueryClient({ defaultOptions: { queries: { retry: false } } });
  return <QueryClientProvider client={qc}>{children}</QueryClientProvider>;
};

it('returns price records from API', async () => {
  const mockData = {
    success: true,
    data: [{ record_id: 'REC_001', material_name: 'PPC Cement 50kg', price_zar: 89.99 }],
    pagination: { page: 1, per_page: 30, total: 1, total_pages: 1 },
  };
  (apiService.getPrices as jest.Mock).mockResolvedValue(mockData);

  const { result } = renderHook(() => usePrices(), { wrapper });
  await waitFor(() => expect(result.current.isSuccess).toBe(true));

  expect(result.current.data?.data[0].material_name).toBe('PPC Cement 50kg');
  expect(result.current.data?.data[0].price_zar).toBe(89.99);
});
```

```tsx
// src/components/__tests__/PriceCard.test.tsx
import { render, screen } from '@testing-library/react';
import { PriceCard } from '../features/PriceCard';
import { PriceRecord } from '../../types/PriceRecord';

const record: PriceRecord = {
  record_id: 'REC_001', date: '2026-03-09', year: 2026, month: 3,
  material_name: 'PPC Cement 50kg', material_category: 'Cement',
  supplier_name: 'Builders Warehouse', region: 'Gauteng', province: 'Johannesburg',
  price_zar: 89.99, unit: '50kg bag', price_per_kg_zar: 1.7998,
  price_change_mom_pct: 2.3, price_change_yoy_pct: -1.1,
  stock_status: 'In Stock', bulk_discount_available: 'Yes',
};

it('renders material name and price', () => {
  render(<PriceCard record={record} />);
  expect(screen.getByText('PPC Cement 50kg')).toBeInTheDocument();
  expect(screen.getByText(/R89\.99/)).toBeInTheDocument();
});

it('shows bulk discount tag when available', () => {
  render(<PriceCard record={record} />);
  expect(screen.getByText('Bulk Discount')).toBeInTheDocument();
});

it('shows In Stock badge', () => {
  render(<PriceCard record={record} />);
  expect(screen.getByText('In Stock')).toBeInTheDocument();
});
```

```bash
# Run all tests
npm test -- --watchAll=false --coverage

# Lighthouse audit (run against production build)
npm run build && npx serve -s build
# Open Chrome → DevTools → Lighthouse → Generate report
# Targets: Performance >90, Accessibility >95, Best Practices >90
```

**ST2 (May 6)** — Testing report: Lighthouse scores, hook test coverage, cross-browser results  
**D4 (May 13)** — Final polish  
**Final (June 10)** — Championship demo

---

## 🎯 Performance Targets

| Metric | Target |
|---|---|
| Lighthouse Performance | > 90 |
| First Contentful Paint | < 1.5 s |
| Time to Interactive | < 3 s |
| Accessibility Score | > 95 |
| Bundle size (main chunk) | < 250 KB gzip |

---

## ⚠️ Bugs Fixed in This Version

| Original bug | Fix applied |
|---|---|
| `useQuery('key', fn, opts)` — v4 positional arg style | All hooks use v5 options-object: `useQuery({ queryKey, queryFn, ...opts })` |
| `cacheTime` in `usePriceComparison` — renamed in v5 | Changed to `gcTime` throughout |
| `usePriceHistory(materialId)` — used material_id, backend uses material_name | All hooks use `materialName: string` |
| `forecastsAPI.get()` called `/forecasts` — backend route is `/forecast` | Corrected to `/forecast` in `apiService.ts` |
| `getPriceHistory` sent `{ months }` param — backend accepts `days` | Changed to `days` parameter |
| `ForecastResponse` used `confidence_interval_lower/upper` conflicting with backend column names | Frontend keeps the readable names; `NOTE FOR MEMBER 1` added to type file to rename DB columns |
| `MaterialDetailPage` read `priceHistory?.prices` — API returns `{ history: [...] }` | Corrected to `histData?.history` matching the `HistoryResponse` shape |
| `PriceChart` plotted `dataKey="price"` — schema field is `price_zar` | All chart dataKeys updated to `price_zar`, `avg_price`, etc. |
| `MultiMaterialChart` used hardcoded dataKeys `"cement"`, `"bricks"` — API never returns that shape | Removed; replaced by `PriceHistoryChart` which uses the actual API response shape |
| `MaterialsPage` used `material.id` as React key — schema has `record_id`, not `id` | All `.map()` keys use `r.record_id` |
| `ForecastChart` rendered both `<Area>` bands with identical `fill="#7c3aed"` — lower erased upper | Lower band now uses `fill="white"` so purple upper band remains visible |
| `SupplierComparisonChart` plotted `price` field — schema uses `price_zar` | Changed to `price_zar` |

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active | **Next Milestone:** D1 — March 25, 2026
