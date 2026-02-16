# 🎨 Frontend Web Developer - Detailed Roadmap

**Team Member:** Member 3  
**Primary Tech Stack:** React, Tailwind CSS, Recharts, React Query  
**Timeline:** March 9 - June 9, 2026 (13 weeks)

---

## 🚀 Getting Started - March 8, 2026

**Complete this setup before Week 1 begins to be ready on March 9!**

### System Requirements
- **Operating System:** Windows 10/11, macOS 10.15+, or Ubuntu 20.04+
- **RAM:** Minimum 8GB (16GB recommended)
- **Storage:** At least 5GB free space for node_modules
- **Browser:** Chrome 90+, Firefox 88+, Edge 90+, or Safari 14+

### Step 1: Install Node.js and npm
```bash
# Download and install Node.js LTS (v18.x or v20.x)
# Visit: https://nodejs.org/

# Verify installation
node --version  # Should show v18.x.x or v20.x.x
npm --version   # Should show 9.x.x or higher

# Optional: Install pnpm (faster alternative to npm)
npm install -g pnpm
pnpm --version
```

### Step 2: Install Git
```bash
# Windows: Download from https://git-scm.com/
# macOS: Install via Homebrew
brew install git

# Linux (Ubuntu/Debian)
sudo apt update
sudo apt install git

# Verify installation
git --version
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Step 3: Install VS Code and Extensions
```bash
# Download VS Code from: https://code.visualstudio.com/

# Recommended Extensions (install via Extensions panel):
# - ES7+ React/Redux/React-Native snippets
# - Tailwind CSS IntelliSense
# - Prettier - Code formatter
# - ESLint
# - Auto Rename Tag
# - Color Highlight
# - GitLens
# - Path Intellisense
# - npm Intellisense
```

### Step 4: Create React Application
```bash
# Create project directory
mkdir -p ~/projects/buildmat-web
cd ~/projects/buildmat-web

# Create React app with TypeScript (optional but recommended)
npx create-react-app buildmat-dashboard --template typescript

# OR create with JavaScript only
npx create-react-app buildmat-dashboard

# Navigate to project
cd buildmat-dashboard
```

### Step 5: Install Core Dependencies
```bash
# Tailwind CSS for styling
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# React Router for navigation
npm install react-router-dom

# React Query for API state management
npm install @tanstack/react-query

# Axios for HTTP requests
npm install axios

# Recharts for data visualization
npm install recharts

# Headless UI for accessible components
npm install @headlessui/react

# Heroicons for icons
npm install @heroicons/react

# Date utilities
npm install date-fns

# Form handling
npm install react-hook-form

# Verify installation
npm list --depth=0
```

### Step 6: Install Development Dependencies
```bash
# Testing libraries (if not already included)
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event

# Prettier for code formatting
npm install --save-dev prettier

# ESLint plugins for React
npm install --save-dev eslint-plugin-react eslint-plugin-react-hooks
```

### Step 7: Configure Tailwind CSS
```bash
# Update tailwind.config.js
cat > tailwind.config.js << 'EOF'
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
        },
        secondary: {
          500: '#7c3aed',
          600: '#6d28d9',
          700: '#5b21b6',
        },
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
    },
  },
  plugins: [],
}
EOF

# Update src/index.css
cat > src/index.css << 'EOF'
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  body {
    @apply bg-gray-50 text-gray-900;
  }
}

@layer components {
  .btn-primary {
    @apply px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors font-medium;
  }
  
  .btn-secondary {
    @apply px-4 py-2 bg-gray-200 text-gray-800 rounded-lg hover:bg-gray-300 transition-colors font-medium;
  }
  
  .card {
    @apply bg-white rounded-lg shadow-md p-6;
  }
}
EOF
```

### Step 8: Set Up Project Structure
```bash
# Create organized folder structure
mkdir -p src/{components/{common,charts,layout,features},pages,services,hooks,utils,contexts,types,assets/{images,icons}}

# Create index files for easier imports
touch src/components/index.js
touch src/pages/index.js
touch src/services/index.js
touch src/hooks/index.js
touch src/utils/index.js
```

### Step 9: Create Essential Component Templates
```bash
# Create Button component
cat > src/components/common/Button.jsx << 'EOF'
import React from 'react';

export const Button = ({ 
  children, 
  variant = 'primary', 
  size = 'md',
  onClick,
  disabled = false,
  type = 'button',
  className = '',
  ...props 
}) => {
  const baseStyles = "font-medium transition-colors rounded-lg";
  
  const variants = {
    primary: "bg-primary-600 text-white hover:bg-primary-700 disabled:bg-gray-300",
    secondary: "bg-gray-200 text-gray-800 hover:bg-gray-300 disabled:bg-gray-100",
    outline: "border-2 border-primary-600 text-primary-600 hover:bg-primary-50",
    danger: "bg-red-600 text-white hover:bg-red-700",
  };
  
  const sizes = {
    sm: "px-3 py-1.5 text-sm",
    md: "px-4 py-2 text-base",
    lg: "px-6 py-3 text-lg",
  };
  
  return (
    <button
      type={type}
      onClick={onClick}
      disabled={disabled}
      className={`${baseStyles} ${variants[variant]} ${sizes[size]} ${className}`}
      {...props}
    >
      {children}
    </button>
  );
};
EOF

# Create Card component
cat > src/components/common/Card.jsx << 'EOF'
import React from 'react';

export const Card = ({ children, title, subtitle, className = '', headerAction }) => {
  return (
    <div className={`bg-white rounded-lg shadow-md ${className}`}>
      {(title || headerAction) && (
        <div className="border-b border-gray-200 px-6 py-4 flex justify-between items-center">
          <div>
            {title && <h3 className="text-lg font-semibold text-gray-900">{title}</h3>}
            {subtitle && <p className="text-sm text-gray-600 mt-1">{subtitle}</p>}
          </div>
          {headerAction && <div>{headerAction}</div>}
        </div>
      )}
      <div className="p-6">{children}</div>
    </div>
  );
};
EOF

# Create Loading Spinner component
cat > src/components/common/LoadingSpinner.jsx << 'EOF'
import React from 'react';

export const LoadingSpinner = ({ size = 'md', className = '' }) => {
  const sizes = {
    sm: 'h-4 w-4',
    md: 'h-8 w-8',
    lg: 'h-12 w-12',
  };
  
  return (
    <div className={`flex justify-center items-center ${className}`}>
      <div className={`${sizes[size]} border-4 border-primary-200 border-t-primary-600 rounded-full animate-spin`}></div>
    </div>
  );
};
EOF
```

### Step 10: Set Up API Service Layer
```bash
# Create API client
cat > src/services/api.js << 'EOF'
import axios from 'axios';

const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:3000/api';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
  },
  timeout: 10000,
});

// Request interceptor for adding auth token
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('authToken');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Response interceptor for handling errors
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Handle unauthorized
      localStorage.removeItem('authToken');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default apiClient;

// API service functions
export const priceService = {
  getPrices: (params) => apiClient.get('/prices', { params }),
  getPriceById: (id) => apiClient.get(`/prices/${id}`),
  createPrice: (data) => apiClient.post('/prices', data),
};

export const materialService = {
  getMaterials: () => apiClient.get('/materials'),
  getMaterialById: (id) => apiClient.get(`/materials/${id}`),
};

export const forecastService = {
  getForecast: (materialId, days = 30) => 
    apiClient.get(`/forecast/${materialId}`, { params: { days } }),
};
EOF
```

### Step 11: Configure Environment Variables
```bash
# Create .env file
cat > .env << 'EOF'
# API Configuration
REACT_APP_API_URL=http://localhost:3000/api

# Feature Flags
REACT_APP_ENABLE_ANALYTICS=false

# Environment
REACT_APP_ENV=development
EOF

# Create .env.example (for documentation)
cp .env .env.example

# Update .gitignore to exclude .env
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
```

### Step 12: Configure React Query
```bash
# Create query client setup
cat > src/config/queryClient.js << 'EOF'
import { QueryClient } from '@tanstack/react-query';

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      refetchOnWindowFocus: false,
      retry: 1,
      staleTime: 5 * 60 * 1000, // 5 minutes
    },
  },
});
EOF

# Update src/index.js to include QueryClientProvider
cat >> src/index.js << 'EOF'

import { QueryClientProvider } from '@tanstack/react-query';
import { queryClient } from './config/queryClient';

// Wrap your App with QueryClientProvider:
// <QueryClientProvider client={queryClient}>
//   <App />
// </QueryClientProvider>
EOF
```

### Step 13: Create Prettier Configuration
```bash
cat > .prettierrc << 'EOF'
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "always",
  "endOfLine": "lf"
}
EOF

# Create .prettierignore
cat > .prettierignore << 'EOF'
node_modules
build
dist
coverage
.env
*.min.js
EOF
```

### Step 14: Update package.json Scripts
```bash
# Add useful scripts
npm pkg set scripts.format="prettier --write 'src/**/*.{js,jsx,ts,tsx,css,md}'"
npm pkg set scripts.lint="eslint src/**/*.{js,jsx}"
npm pkg set scripts.lint:fix="eslint src/**/*.{js,jsx} --fix"
npm pkg set scripts.analyze="source-map-explorer 'build/static/js/*.js'"
```

### Step 15: Test Your Setup
```bash
# Start development server
npm start

# Server should start on http://localhost:3000
# React app should open in your browser

# In another terminal, test build
npm run build

# Expected: Successful build with optimized production files
```

### Checklist - Confirm Everything Works ✅
- [ ] Node.js and npm installed and working
- [ ] Git installed and configured
- [ ] VS Code with React extensions installed
- [ ] React project created successfully
- [ ] All npm packages installed without errors
- [ ] Tailwind CSS configured and working
- [ ] Project folder structure organized
- [ ] Component templates created
- [ ] API service layer set up
- [ ] Environment variables configured
- [ ] Development server runs successfully
- [ ] Production build completes without errors
- [ ] Prettier and ESLint configured

### Troubleshooting Common Issues

**Issue: npm install fails with permission errors**
```bash
# Don't use sudo! Fix npm permissions instead:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'

# Add to ~/.bashrc or ~/.zshrc:
export PATH=~/.npm-global/bin:$PATH

source ~/.bashrc
```

**Issue: Port 3000 already in use**
```bash
# Change the port by setting environment variable
# macOS/Linux:
PORT=3001 npm start

# Windows (PowerShell):
$env:PORT=3001; npm start

# Or kill the process using port 3000:
# macOS/Linux:
lsof -ti:3000 | xargs kill -9

# Windows:
netstat -ano | findstr :3000
taskkill /PID <process_id> /F
```

**Issue: Tailwind styles not applying**
```bash
# Ensure content paths in tailwind.config.js are correct
# Verify src/index.css has @tailwind directives
# Clear cache and restart:
rm -rf node_modules/.cache
npm start
```

**Issue: Module not found errors**
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install

# Or clear npm cache
npm cache clean --force
npm install
```

**Issue: React scripts failing to start**
```bash
# Update to latest React scripts
npm install --save react-scripts@latest

# Or create new app if issues persist
npx create-react-app new-app
# Then copy your src/ files to new project
```

### Quick Reference Commands 📝

```bash
# Start development server
npm start

# Create production build
npm run build

# Run tests
npm test

# Format code with Prettier
npm run format

# Check for linting errors
npm run lint

# Fix linting errors automatically
npm run lint:fix

# Install new package
npm install package-name

# Install dev dependency
npm install --save-dev package-name

# Update all packages
npm update

# Check for outdated packages
npm outdated
```

### Browser DevTools Setup

**Install React Developer Tools:**
- Chrome: https://chrome.google.com/webstore (search "React Developer Tools")
- Firefox: https://addons.mozilla.org/en-US/firefox/ (search "React Developer Tools")

**Install Redux DevTools** (if using Redux later):
- Chrome/Firefox: Search for "Redux DevTools"

### Ready for Week 1! 🎉
Your development environment is fully configured. You're ready to start building on March 9, 2026!

---

## 🎯 Role Overview

As the Frontend Web Developer, you create the user-facing web interface that visualizes price data, forecasts, and analytics. Your work must be responsive, performant, and production-ready.

**Core Responsibilities:**
- React application architecture
- Tailwind CSS styling
- Data visualization (Recharts)
- API integration
- State management (React Query)
- PWA implementation

---

## 📅 WEEK 1: March 9-15, 2026
### Theme: Project Setup & Design System

#### Monday (March 9)
**Morning (2-3 hours):**
```bash
# Create React project
npx create-react-app buildmat-dashboard
cd buildmat-dashboard

# Install dependencies
npm install tailwindcss postcss autoprefixer
npm install recharts axios react-query react-router-dom
npm install @headlessui/react @heroicons/react

# Initialize Tailwind
npx tailwindcss init -p
```

**Afternoon (2-3 hours):**
Configure Tailwind in `tailwind.config.js`:
```javascript
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    extend: {
      colors: {
        primary: '#2563eb',
        secondary: '#7c3aed',
      }
    },
  },
  plugins: [],
}
```

Create project structure:
```
src/
├── components/
│   ├── common/
│   ├── charts/
│   ├── layout/
│   └── features/
├── pages/
├── services/
├── hooks/
├── utils/
└── App.js
```

**Deliverable:** React project initialized

#### Tuesday-Wednesday (March 10-11)
**Focus: Design System & Components**

Create reusable components:
```javascript
// src/components/common/Button.jsx
export const Button = ({ children, variant = 'primary', ...props }) => {
  const baseStyles = "px-4 py-2 rounded-lg font-medium transition-colors";
  const variants = {
    primary: "bg-primary text-white hover:bg-blue-700",
    secondary: "bg-gray-200 text-gray-800 hover:bg-gray-300",
  };
  
  return (
    <button className={`${baseStyles} ${variants[variant]}`} {...props}>
      {children}
    </button>
  );
};

// src/components/common/Card.jsx
export const Card = ({ children, title, className = '' }) => {
  return (
    <div className={`bg-white rounded-lg shadow-md p-6 ${className}`}>
      {title && <h3 className="text-lg font-semibold mb-4">{title}</h3>}
      {children}
    </div>
  );
};

// src/components/common/LoadingSpinner.jsx
export const LoadingSpinner = () => {
  return (
    <div className="flex justify-center items-center p-8">
      <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-primary"></div>
    </div>
  );
};
```

**Deliverable:** Design system components

#### Thursday-Friday (March 12-13)
**Focus: Layout & Navigation**

```javascript
// src/components/layout/Header.jsx
export const Header = () => {
  return (
    <header className="bg-white shadow-sm">
      <nav className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex justify-between h-16 items-center">
          <div className="flex items-center">
            <h1 className="text-2xl font-bold text-primary">BuildMat Intelligence</h1>
          </div>
          <div className="flex gap-4">
            <a href="/dashboard" className="text-gray-700 hover:text-primary">Dashboard</a>
            <a href="/materials" className="text-gray-700 hover:text-primary">Materials</a>
            <a href="/forecasts" className="text-gray-700 hover:text-primary">Forecasts</a>
          </div>
        </div>
      </nav>
    </header>
  );
};

// src/components/layout/Layout.jsx
export const Layout = ({ children }) => {
  return (
    <div className="min-h-screen bg-gray-50">
      <Header />
      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        {children}
      </main>
    </div>
  );
};
```

**Deliverable:** App layout structure

---

## 📅 WEEK 2: March 16-22, 2026
### Theme: API Integration & State Management

#### Monday-Tuesday (March 16-17)
**Focus: API Service Layer**

```javascript
// src/services/api.js
import axios from 'axios';

const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:3000/api';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor for auth token
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor for error handling
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export const materialsAPI = {
  getAll: () => apiClient.get('/materials'),
  getById: (id) => apiClient.get(`/materials/${id}`),
};

export const pricesAPI = {
  getHistory: (params) => apiClient.get('/prices/history', { params }),
  compare: (params) => apiClient.get('/prices/compare', { params }),
};

export const forecastsAPI = {
  get: (params) => apiClient.get('/forecasts', { params }),
};
```

**React Query Setup:**
```javascript
// src/hooks/useAPI.js
import { useQuery, useMutation } from 'react-query';
import { materialsAPI, pricesAPI, forecastsAPI } from '../services/api';

export const useMaterials = () => {
  return useQuery('materials', materialsAPI.getAll, {
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
};

export const usePriceHistory = (materialId, days = 30) => {
  return useQuery(
    ['priceHistory', materialId, days],
    () => pricesAPI.getHistory({ material_id: materialId, days }),
    {
      enabled: !!materialId,
      staleTime: 60 * 1000, // 1 minute
    }
  );
};

export const useForecasts = (materialId, days = 7) => {
  return useQuery(
    ['forecasts', materialId, days],
    () => forecastsAPI.get({ material_id: materialId, days }),
    {
      enabled: !!materialId,
      staleTime: 6 * 60 * 60 * 1000, // 6 hours (matches backend cache)
    }
  );
};
```

**Deliverable:** API integration layer

#### Wednesday-Friday (March 18-20)
**Focus: Basic Data Display**

```javascript
// src/pages/MaterialsPage.jsx
import { useMaterials } from '../hooks/useAPI';
import { Card, LoadingSpinner } from '../components/common';

export const MaterialsPage = () => {
  const { data: materials, isLoading, error } = useMaterials();
  
  if (isLoading) return <LoadingSpinner />;
  if (error) return <div>Error loading materials</div>;
  
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      {materials?.data.map((material) => (
        <Card key={material.id} className="hover:shadow-lg transition-shadow cursor-pointer">
          <h3 className="text-xl font-bold mb-2">{material.name}</h3>
          <p className="text-gray-600">{material.category}</p>
          <p className="text-sm text-gray-500 mt-2">Unit: {material.unit}</p>
        </Card>
      ))}
    </div>
  );
};
```

**Deliverable:** Materials listing page

---

## 📅 WEEK 3: March 23-29, 2026
### Theme: Charts & Visualizations + D1

#### Monday-Tuesday (March 23-24)
**Focus: Price History Charts**

```javascript
// src/components/charts/PriceChart.jsx
import React from 'react';
import { 
  LineChart, Line, XAxis, YAxis, CartesianGrid, 
  Tooltip, Legend, ResponsiveContainer 
} from 'recharts';
import { format, parseISO } from 'date-fns';

export const PriceChart = ({ data, title = "Price History" }) => {
  // Custom tooltip
  const CustomTooltip = ({ active, payload }) => {
    if (active && payload && payload.length) {
      return (
        <div className="bg-white p-4 rounded-lg shadow-lg border border-gray-200">
          <p className="font-semibold text-gray-900">
            {format(parseISO(payload[0].payload.date), 'MMM dd, yyyy')}
          </p>
          <p className="text-primary-600">
            Price: R{payload[0].value.toFixed(2)}
          </p>
        </div>
      );
    }
    return null;
  };

  return (
    <div className="w-full">
      <h3 className="text-lg font-semibold mb-4">{title}</h3>
      <ResponsiveContainer width="100%" height={400}>
        <LineChart data={data} margin={{ top: 5, right: 30, left: 20, bottom: 5 }}>
          <CartesianGrid strokeDasharray="3 3" stroke="#e5e7eb" />
          <XAxis 
            dataKey="date" 
            tickFormatter={(date) => format(parseISO(date), 'MMM dd')}
            stroke="#6b7280"
          />
          <YAxis 
            label={{ value: 'Price (ZAR)', angle: -90, position: 'insideLeft' }}
            stroke="#6b7280"
          />
          <Tooltip content={<CustomTooltip />} />
          <Legend />
          <Line 
            type="monotone" 
            dataKey="price" 
            stroke="#2563eb" 
            strokeWidth={2}
            dot={{ r: 3 }}
            activeDot={{ r: 6 }}
            name="Price (ZAR)"
          />
        </LineChart>
      </ResponsiveContainer>
    </div>
  );
};

// src/components/charts/MultiMaterialChart.jsx
export const MultiMaterialChart = ({ data }) => {
  // Data format: [{ date, cement, bricks, sand, ... }]
  
  const colors = {
    cement: '#2563eb',
    bricks: '#dc2626',
    sand: '#f59e0b',
    steel: '#059669',
    timber: '#8b5cf6',
  };

  return (
    <ResponsiveContainer width="100%" height={400}>
      <LineChart data={data}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis 
          dataKey="date" 
          tickFormatter={(date) => format(parseISO(date), 'MMM dd')}
        />
        <YAxis label={{ value: 'Price (ZAR)', angle: -90, position: 'insideLeft' }} />
        <Tooltip />
        <Legend />
        {Object.keys(colors).map((material) => (
          <Line
            key={material}
            type="monotone"
            dataKey={material}
            stroke={colors[material]}
            strokeWidth={2}
            dot={false}
            name={material.charAt(0).toUpperCase() + material.slice(1)}
          />
        ))}
      </LineChart>
    </ResponsiveContainer>
  );
};

// src/components/charts/BarComparisonChart.jsx
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';

export const BarComparisonChart = ({ data }) => {
  // Data format: [{ supplier: 'Builder Warehouse', price: 85.50 }, ...]
  
  return (
    <ResponsiveContainer width="100%" height={400}>
      <BarChart data={data} margin={{ top: 20, right: 30, left: 20, bottom: 5 }}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="supplier" />
        <YAxis label={{ value: 'Price (ZAR)', angle: -90, position: 'insideLeft' }} />
        <Tooltip 
          formatter={(value) => `R${value.toFixed(2)}`}
          contentStyle={{ backgroundColor: '#fff', border: '1px solid #e5e7eb' }}
        />
        <Legend />
        <Bar dataKey="price" fill="#2563eb" radius={[8, 8, 0, 0]} />
      </BarChart>
    </ResponsiveContainer>
  );
};

// src/pages/MaterialDetailPage.jsx
import { useParams } from 'react-router-dom';
import { usePriceHistory } from '../hooks/useAPI';
import { Card } from '../components/common/Card';
import { PriceChart } from '../components/charts/PriceChart';
import { LoadingSpinner } from '../components/common/LoadingSpinner';

export const MaterialDetailPage = () => {
  const { id } = useParams();
  const { data: priceHistory, isLoading, error } = usePriceHistory(id, 30);
  
  if (isLoading) return <LoadingSpinner size="lg" />;
  if (error) return <div className="text-red-600">Error loading data: {error.message}</div>;
  
  const material = priceHistory?.material;
  const prices = priceHistory?.prices || [];
  
  // Calculate stats
  const currentPrice = prices[prices.length - 1]?.price || 0;
  const previousPrice = prices[prices.length - 2]?.price || 0;
  const priceChange = currentPrice - previousPrice;
  const priceChangePercent = previousPrice ? ((priceChange / previousPrice) * 100).toFixed(2) : 0;
  
  return (
    <div className="space-y-6">
      {/* Header with stats */}
      <Card>
        <div className="flex justify-between items-start">
          <div>
            <h1 className="text-3xl font-bold text-gray-900">{material?.name}</h1>
            <p className="text-gray-600 mt-1">{material?.description}</p>
          </div>
          <div className="text-right">
            <p className="text-3xl font-bold text-primary-600">R{currentPrice.toFixed(2)}</p>
            <p className={`text-sm ${priceChange >= 0 ? 'text-green-600' : 'text-red-600'}`}>
              {priceChange >= 0 ? '↑' : '↓'} R{Math.abs(priceChange).toFixed(2)} ({priceChangePercent}%)
            </p>
          </div>
        </div>
      </Card>
      
      {/* Price chart */}
      <Card title="30-Day Price History">
        <PriceChart data={prices} />
      </Card>
      
      {/* Price statistics */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <Card title="Average Price">
          <p className="text-2xl font-bold text-gray-900">
            R{(prices.reduce((sum, p) => sum + p.price, 0) / prices.length).toFixed(2)}
          </p>
        </Card>
        <Card title="Lowest Price">
          <p className="text-2xl font-bold text-green-600">
            R{Math.min(...prices.map(p => p.price)).toFixed(2)}
          </p>
        </Card>
        <Card title="Highest Price">
          <p className="text-2xl font-bold text-red-600">
            R{Math.max(...prices.map(p => p.price)).toFixed(2)}
          </p>
        </Card>
      </div>
    </div>
  );
};
```

**Deliverable:** Price visualization

#### Wednesday (March 25) - **D1 DEMO DAY** 🎯
**Morning:** Final polish for demo
- Test all components
- Verify API integration
- Practice demo flow

**Afternoon:** D1 Presentation

**Deliverable:** Working dashboard demo

#### Thursday-Friday (March 26-27)
**Focus: Forecast Visualization**

```javascript
// src/components/charts/ForecastChart.jsx
export const ForecastChart = ({ historical, forecast }) => {
  const combinedData = [
    ...historical.map(d => ({ ...d, type: 'historical' })),
    ...forecast.map(d => ({ ...d, type: 'forecast' }))
  ];
  
  return (
    <ResponsiveContainer width="100%" height={400}>
      <LineChart data={combinedData}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="date" />
        <YAxis />
        <Tooltip />
        <Legend />
        <Line 
          dataKey="price" 
          stroke="#2563eb" 
          strokeWidth={2}
          dot={false}
        />
        <Line 
          dataKey="predicted_price" 
          stroke="#7c3aed" 
          strokeWidth={2}
          strokeDasharray="5 5"
          dot={false}
        />
      </LineChart>
    </ResponsiveContainer>
  );
};
```

**Deliverable:** Forecast charts

---

## 📅 WEEK 4-5: March 30-April 12
### Theme: Dashboard Features + ST1/D2

#### Week 4: Dashboard Components
- Price comparison tables
- Supplier cards
- Search and filter UI
- Regional filtering
- Date range pickers

#### ST1 (April 1) Presentation
- Showcase UI/UX design
- Demonstrate responsive layout
- Show API integration

#### Week 5: Advanced Features
- Multi-material comparison
- Anomaly alerts display
- Export functionality (CSV)
- Performance optimization

#### D2 (April 8) Demo
- Complete dashboard with live data
- Real-time forecast visualization
- Smooth user experience

---

## 📅 WEEKS 6-8: April 13-May 3
### Theme: Polish & PWA + MVP

**Key Activities:**
1. PWA implementation
2. Offline support
3. Responsive design refinement
4. Loading states
5. Error handling
6. Accessibility improvements

**D3 (May 1) - MVP COMPLETE** 🎉

---

## 📅 WEEKS 9-13: May 4-June 9
### Theme: Testing & Final Polish

**Focus:**
1. Cross-browser testing
2. Performance optimization
3. UI/UX refinements
4. Documentation
5. Presentation preparation

**ST2 (May 6)** - Testing Report  
**D4 (May 13)** - Final Polish  
**Final (June 10)** - Championship Demo

---

## 🎯 Performance Targets

| Metric | Target |
|--------|--------|
| Lighthouse Performance | >90 |
| First Contentful Paint | <1.5s |
| Time to Interactive | <3s |
| Accessibility Score | >95 |

---

**Last Updated:** March 9, 2026  
**Status:** 🟢 Active | **Next Milestone:** D1 (March 25, 2026)
