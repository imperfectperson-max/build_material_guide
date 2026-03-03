# 🎨 Frontend Developer — Expanded Code Blocks
**CySentinel | Debug Ninjas | UJ IFM03A3 | 2026**

> Every block is copy-paste ready.  
> `[WITH LIBS]` = React + axios + react-router-dom + Recharts (standard stack).  
> `[NO LIBS / MINIMAL]` = plain `fetch`, CSS variables, no external UI libraries — safe if axios/recharts are restricted.  
> Tests: React Testing Library (CRA default) `[WITH LIBS]` + manual DOM assertions `[MINIMAL]`.

---

## 🗓️ PLANNING WEEK

### Monday — CRA Setup

```bash
node --version   # must be v18+

npx create-react-app cysentinel-web
cd cysentinel-web

# [WITH LIBS] — full stack
npm install recharts react-router-dom axios
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event

# [MINIMAL] — only react-router-dom (routing is needed; recharts and axios replaceable)
npm install react-router-dom

npm start   # opens http://localhost:3000
```

### Tuesday — Folder Structure

```bash
mkdir -p src/{components,pages,services,hooks}

# Components
touch src/components/ProtectedRoute.jsx
touch src/components/NavBar.jsx src/components/NavBar.module.css
touch src/components/RiskBadge.jsx src/components/RiskBadge.module.css
touch src/components/IncidentTable.jsx src/components/IncidentTable.module.css
touch src/components/IncidentForm.jsx src/components/IncidentForm.module.css
touch src/components/ChecklistPanel.jsx src/components/ChecklistPanel.module.css
touch src/components/RiskSummaryCard.jsx src/components/RiskSummaryCard.module.css

# Pages
touch src/pages/LoginPage.jsx src/pages/LoginPage.module.css
touch src/pages/RegisterPage.jsx
touch src/pages/Setup2FAPage.jsx
touch src/pages/DashboardPage.jsx src/pages/DashboardPage.module.css
touch src/pages/IncidentsPage.jsx src/pages/IncidentsPage.module.css
touch src/pages/IncidentDetailPage.jsx
touch src/pages/QuickLogPage.jsx
touch src/pages/ConsultantEscalationPage.jsx
touch src/pages/CVETrackerPage.jsx
touch src/pages/ReportsPage.jsx src/pages/ReportsPage.module.css
touch src/pages/OrgSettingsPage.jsx

# Services
touch src/services/api.js
touch src/services/authService.js
touch src/services/incidentService.js
touch src/services/dashboardService.js
touch src/services/cveService.js

# Hooks
touch src/hooks/useAuth.js
touch src/hooks/useIncidents.js
touch src/hooks/useDarkMode.js

# Env files
echo "REACT_APP_API_URL=http://localhost:5000" > .env.development
echo "REACT_APP_API_URL=https://cysentinel-backend-production.up.railway.app" > .env.production
```

### Thursday — API Service Layer

#### [WITH LIBS] src/services/api.js — axios instance

```javascript
// src/services/api.js
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL || 'http://localhost:5000',
});

// Attach JWT to every request
api.interceptors.request.use(config => {
  const token = sessionStorage.getItem('cysentinel_token');
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// Handle 401 globally
api.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 401) {
      sessionStorage.removeItem('cysentinel_token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

#### [NO LIBS] src/services/api.no-libs.js — plain fetch wrapper

```javascript
// src/services/api.no-libs.js
// Drop-in fetch wrapper — no axios. Same interface as api.js above.

const BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:5000';

function getToken() {
  return sessionStorage.getItem('cysentinel_token');
}

async function request(method, path, body = null, options = {}) {
  const headers = { 'Content-Type': 'application/json' };
  const token = getToken();
  if (token) headers['Authorization'] = `Bearer ${token}`;

  const config = {
    method,
    headers,
    ...(body ? { body: JSON.stringify(body) } : {}),
    ...options,
  };

  const response = await fetch(`${BASE_URL}${path}`, config);

  if (response.status === 401) {
    sessionStorage.removeItem('cysentinel_token');
    window.location.href = '/login';
    return;
  }

  if (!response.ok) {
    const err = await response.json().catch(() => ({ error: response.statusText }));
    throw Object.assign(new Error(err.error || 'Request failed'), { response: { data: err, status: response.status } });
  }

  // Handle blob responses (CSV/PDF downloads)
  const contentType = response.headers.get('Content-Type') || '';
  if (contentType.includes('text/csv') || contentType.includes('application/pdf')) {
    return { data: await response.blob() };
  }

  return { data: await response.json() };
}

const api = {
  get:    (path)         => request('GET',    path),
  post:   (path, body, options)  => request('POST',   path, body, options),
  patch:  (path, body)   => request('PATCH',  path, body),
  delete: (path)         => request('DELETE', path),
};

export default api;
```

### Saturday — ProtectedRoute + App Router

#### src/components/ProtectedRoute.jsx

```jsx
// src/components/ProtectedRoute.jsx
import { Navigate } from 'react-router-dom';

function ProtectedRoute({ children, allowedRoles }) {
  const token = sessionStorage.getItem('cysentinel_token');
  if (!token) return <Navigate to="/login" replace />;

  // Decode JWT payload (client-side only — not verification)
  try {
    const payload = JSON.parse(atob(token.split('.')[1]));
    if (allowedRoles && !allowedRoles.includes(payload.role)) {
      return <Navigate to="/dashboard" replace />;
    }
  } catch {
    return <Navigate to="/login" replace />;
  }

  return children;
}

export default ProtectedRoute;
```

#### src/App.jsx — Month 1 (eager loading)

```jsx
// src/App.jsx
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom';
import LoginPage               from './pages/LoginPage';
import RegisterPage            from './pages/RegisterPage';
import Setup2FAPage            from './pages/Setup2FAPage';
import DashboardPage           from './pages/DashboardPage';
import IncidentsPage           from './pages/IncidentsPage';
import IncidentDetailPage      from './pages/IncidentDetailPage';
import QuickLogPage            from './pages/QuickLogPage';
import ConsultantEscalationPage from './pages/ConsultantEscalationPage';
import CVETrackerPage          from './pages/CVETrackerPage';
import ReportsPage             from './pages/ReportsPage';
import OrgSettingsPage         from './pages/OrgSettingsPage';
import ProtectedRoute          from './components/ProtectedRoute';

const ROLES = {
  dash:    ['admin','analyst','consultant','viewer'],
  analyst: ['admin','analyst'],
  admin:   ['admin'],
  cve:     ['admin','analyst'],
};

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/login"     element={<LoginPage />} />
        <Route path="/register"  element={<RegisterPage />} />
        <Route path="/setup-2fa" element={<Setup2FAPage />} />

        <Route path="/dashboard" element={
          <ProtectedRoute allowedRoles={ROLES.dash}><DashboardPage /></ProtectedRoute>
        } />
        <Route path="/incidents" element={
          <ProtectedRoute allowedRoles={ROLES.dash}><IncidentsPage /></ProtectedRoute>
        } />
        <Route path="/incidents/:id" element={
          <ProtectedRoute allowedRoles={ROLES.dash}><IncidentDetailPage /></ProtectedRoute>
        } />
        <Route path="/incidents/:id/escalate" element={
          <ProtectedRoute allowedRoles={ROLES.analyst}><ConsultantEscalationPage /></ProtectedRoute>
        } />
        <Route path="/quick-log" element={
          <ProtectedRoute allowedRoles={ROLES.analyst}><QuickLogPage /></ProtectedRoute>
        } />
        <Route path="/cve-tracker" element={
          <ProtectedRoute allowedRoles={ROLES.cve}><CVETrackerPage /></ProtectedRoute>
        } />
        <Route path="/reports" element={
          <ProtectedRoute allowedRoles={ROLES.dash}><ReportsPage /></ProtectedRoute>
        } />
        <Route path="/settings" element={
          <ProtectedRoute allowedRoles={ROLES.admin}><OrgSettingsPage /></ProtectedRoute>
        } />
        <Route path="/" element={<Navigate to="/dashboard" replace />} />
      </Routes>
    </Router>
  );
}

export default App;
```

#### src/App.jsx — Month 6 update (lazy loading for performance)

```jsx
// src/App.jsx — Month 6 performance update
import { lazy, Suspense } from 'react';
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom';
import LoginPage    from './pages/LoginPage';
import RegisterPage from './pages/RegisterPage';
import Setup2FAPage from './pages/Setup2FAPage';
import ProtectedRoute from './components/ProtectedRoute';

// Lazy-loaded routes — each becomes a separate JS chunk
const DashboardPage           = lazy(() => import('./pages/DashboardPage'));
const IncidentsPage           = lazy(() => import('./pages/IncidentsPage'));
const IncidentDetailPage      = lazy(() => import('./pages/IncidentDetailPage'));
const QuickLogPage            = lazy(() => import('./pages/QuickLogPage'));
const ConsultantEscalationPage = lazy(() => import('./pages/ConsultantEscalationPage'));
const CVETrackerPage          = lazy(() => import('./pages/CVETrackerPage'));
const ReportsPage             = lazy(() => import('./pages/ReportsPage'));
const OrgSettingsPage         = lazy(() => import('./pages/OrgSettingsPage'));

const Loading = () => <div style={{ padding: 40, textAlign: 'center', color: '#6b7280' }}>Loading...</div>;

function App() {
  return (
    <Router>
      <Suspense fallback={<Loading />}>
        <Routes>
          <Route path="/login"     element={<LoginPage />} />
          <Route path="/register"  element={<RegisterPage />} />
          <Route path="/setup-2fa" element={<Setup2FAPage />} />
          <Route path="/dashboard" element={
            <ProtectedRoute allowedRoles={['admin','analyst','consultant','viewer']}>
              <DashboardPage />
            </ProtectedRoute>
          } />
          <Route path="/incidents" element={
            <ProtectedRoute allowedRoles={['admin','analyst','consultant','viewer']}>
              <IncidentsPage />
            </ProtectedRoute>
          } />
          <Route path="/incidents/:id" element={
            <ProtectedRoute allowedRoles={['admin','analyst','consultant','viewer']}>
              <IncidentDetailPage />
            </ProtectedRoute>
          } />
          <Route path="/quick-log" element={
            <ProtectedRoute allowedRoles={['admin','analyst']}><QuickLogPage /></ProtectedRoute>
          } />
          <Route path="/incidents/:id/escalate" element={
            <ProtectedRoute allowedRoles={['admin','analyst']}><ConsultantEscalationPage /></ProtectedRoute>
          } />
          <Route path="/cve-tracker" element={
            <ProtectedRoute allowedRoles={['admin','analyst']}><CVETrackerPage /></ProtectedRoute>
          } />
          <Route path="/reports" element={
            <ProtectedRoute allowedRoles={['admin','analyst','consultant','viewer']}>
              <ReportsPage />
            </ProtectedRoute>
          } />
          <Route path="/settings" element={
            <ProtectedRoute allowedRoles={['admin']}><OrgSettingsPage /></ProtectedRoute>
          } />
          <Route path="/" element={<Navigate to="/dashboard" replace />} />
        </Routes>
      </Suspense>
    </Router>
  );
}

export default App;
```

---

## 🗓️ MONTH 1 — MVP 1: Auth Pages

### src/hooks/useAuth.js

```javascript
// src/hooks/useAuth.js
import { useMemo } from 'react';

export function useAuth() {
  const token = sessionStorage.getItem('cysentinel_token');

  const user = useMemo(() => {
    if (!token) return null;
    try {
      // Decode JWT payload — client-side only (not verification)
      return JSON.parse(atob(token.split('.')[1]));
    } catch {
      return null;
    }
  }, [token]);

  const logout = () => {
    sessionStorage.removeItem('cysentinel_token');
    window.location.href = '/login';
  };

  return { user, isAuthenticated: !!token, logout };
}
```

### src/services/authService.js

```javascript
// src/services/authService.js
import api from './api';

export const login = async (email, password) => {
  const { data } = await api.post('/api/auth/login', { email, password });
  if (data.token) sessionStorage.setItem('cysentinel_token', data.token);
  return data;   // may include requires_2fa: true + partial_token
};

export const register = async (email, password, full_name, organisation_name) => {
  const { data } = await api.post('/api/auth/register',
    { email, password, full_name, organisation_name });
  return data;
};

export const setup2FA = async () => {
  const { data } = await api.post('/api/auth/2fa/setup');
  return data;   // { secret, qr }
};

export const verify2FA = async (partial_token, token) => {
  const { data } = await api.post('/api/auth/2fa/verify', { partial_token, token });
  if (data.token) sessionStorage.setItem('cysentinel_token', data.token);
  return data;
};

export const logout = () => {
  sessionStorage.removeItem('cysentinel_token');
  window.location.href = '/login';
};
```

### src/pages/LoginPage.jsx — Full

```jsx
// src/pages/LoginPage.jsx
import { useState } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { login } from '../services/authService';
import styles from './LoginPage.module.css';

function LoginPage() {
  const navigate = useNavigate();
  const [email, setEmail]     = useState('');
  const [password, setPassword] = useState('');
  const [error, setError]     = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');
    if (!email || !password) {
      setError('Email and password are required.');
      return;
    }
    setLoading(true);
    try {
      const data = await login(email, password);
      if (data.requires_2fa) {
        // Store partial token for 2FA verification step
        sessionStorage.setItem('cysentinel_partial_token', data.partial_token);
        navigate('/setup-2fa');
      } else {
        navigate('/dashboard');
      }
    } catch (err) {
      setError(err.response?.data?.error || 'Login failed. Check your credentials.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className={styles.container}>
      <div className={styles.card}>
        <h1 className={styles.title}>🛡️ CySentinel</h1>
        <p className={styles.subtitle}>Sign in to your account</p>
        <form onSubmit={handleSubmit} className={styles.form}>
          <label className={styles.label} htmlFor="email">Email</label>
          <input
            id="email"
            className={styles.input}
            type="email"
            value={email}
            onChange={e => setEmail(e.target.value)}
            placeholder="you@company.com"
            aria-label="Email address"
            disabled={loading}
          />
          <label className={styles.label} htmlFor="password">Password</label>
          <input
            id="password"
            className={styles.input}
            type="password"
            value={password}
            onChange={e => setPassword(e.target.value)}
            placeholder="••••••••"
            aria-label="Password"
            disabled={loading}
          />
          {error && <p className={styles.error} role="alert">{error}</p>}
          <button className={styles.button} type="submit" disabled={loading}>
            {loading ? 'Signing in...' : 'Sign In'}
          </button>
        </form>
        <p className={styles.link}>
          Don't have an account? <Link to="/register">Register</Link>
        </p>
      </div>
    </div>
  );
}

export default LoginPage;
```

```css
/* src/pages/LoginPage.module.css */
.container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f3f4f6;
}
.card {
  background: #fff;
  border-radius: 12px;
  padding: 40px;
  width: 100%;
  max-width: 420px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.08);
}
.title    { font-size: 1.75rem; font-weight: 800; color: #1e3a5f; text-align: center; margin: 0 0 4px; }
.subtitle { text-align: center; color: #6b7280; margin: 0 0 24px; }
.form     { display: flex; flex-direction: column; gap: 12px; }
.label    { font-size: 0.875rem; font-weight: 600; color: #374151; }
.input    { padding: 11px 14px; border: 1px solid #d1d5db; border-radius: 8px; font-size: 0.95rem; }
.input:focus { outline: none; border-color: #3b82f6; box-shadow: 0 0 0 3px rgba(59,130,246,0.15); }
.error    { color: #dc2626; font-size: 0.875rem; padding: 8px 12px; background: #fee2e2; border-radius: 6px; margin: 0; }
.button   { background: #1e3a5f; color: #fff; border: none; padding: 13px; border-radius: 8px; font-weight: 700; font-size: 1rem; cursor: pointer; margin-top: 4px; }
.button:hover:not(:disabled) { background: #162e4d; }
.button:disabled { opacity: 0.6; cursor: not-allowed; }
.link     { text-align: center; margin-top: 16px; font-size: 0.875rem; color: #6b7280; }
.link a   { color: #3b82f6; text-decoration: none; font-weight: 600; }
```

### src/pages/RegisterPage.jsx

```jsx
// src/pages/RegisterPage.jsx
import { useState } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { register } from '../services/authService';
import styles from './LoginPage.module.css';   // reuse login styles

function RegisterPage() {
  const navigate = useNavigate();
  const [form, setForm]     = useState({ email: '', password: '', full_name: '', organisation_name: '' });
  const [error, setError]   = useState('');
  const [loading, setLoading] = useState(false);

  const update = (key) => (e) => setForm(prev => ({ ...prev, [key]: e.target.value }));

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');
    const { email, password, full_name, organisation_name } = form;
    if (!email || !password || !full_name || !organisation_name) {
      setError('All fields are required.');
      return;
    }
    if (password.length < 8) { setError('Password must be at least 8 characters.'); return; }

    setLoading(true);
    try {
      await register(email, password, full_name, organisation_name);
      navigate('/login');
    } catch (err) {
      setError(err.response?.data?.error || 'Registration failed.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className={styles.container}>
      <div className={styles.card}>
        <h1 className={styles.title}>🛡️ CySentinel</h1>
        <p className={styles.subtitle}>Create your organisation account</p>
        <form onSubmit={handleSubmit} className={styles.form}>
          {[
            { id: 'full_name',         label: 'Full Name',           type: 'text',     placeholder: 'Jane Doe' },
            { id: 'organisation_name', label: 'Organisation Name',   type: 'text',     placeholder: 'ACME Security Ltd' },
            { id: 'email',             label: 'Work Email',          type: 'email',    placeholder: 'jane@acme.com' },
            { id: 'password',          label: 'Password (min 8 chars)', type: 'password', placeholder: '••••••••' },
          ].map(({ id, label, type, placeholder }) => (
            <div key={id}>
              <label className={styles.label} htmlFor={id}>{label}</label>
              <input
                id={id}
                className={styles.input}
                type={type}
                value={form[id]}
                onChange={update(id)}
                placeholder={placeholder}
                aria-label={label}
                disabled={loading}
              />
            </div>
          ))}
          {error && <p className={styles.error} role="alert">{error}</p>}
          <button className={styles.button} type="submit" disabled={loading}>
            {loading ? 'Creating account...' : 'Create Account'}
          </button>
        </form>
        <p className={styles.link}>
          Already have an account? <Link to="/login">Sign in</Link>
        </p>
      </div>
    </div>
  );
}

export default RegisterPage;
```

### src/pages/Setup2FAPage.jsx

```jsx
// src/pages/Setup2FAPage.jsx
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { setup2FA, verify2FA } from '../services/authService';
import styles from './LoginPage.module.css';

function Setup2FAPage() {
  const navigate = useNavigate();
  const [qr, setQr]         = useState('');
  const [secret, setSecret] = useState('');
  const [token, setToken]   = useState('');
  const [error, setError]   = useState('');
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setup2FA()
      .then(data => { setQr(data.qr); setSecret(data.secret); })
      .catch(() => setError('Failed to load QR code. Please try again.'));
  }, []);

  const handleVerify = async (e) => {
    e.preventDefault();
    if (token.length !== 6) { setError('Enter the 6-digit code.'); return; }
    setLoading(true);
    try {
      const partial = sessionStorage.getItem('cysentinel_partial_token') || '';
      await verify2FA(partial, token);
      sessionStorage.removeItem('cysentinel_partial_token');
      navigate('/dashboard');
    } catch {
      setError('Invalid code. Check your authenticator app and try again.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className={styles.container}>
      <div className={styles.card}>
        <h1 className={styles.title}>🔐 Two-Factor Auth</h1>
        <p className={styles.subtitle}>Scan with Google Authenticator or Authy</p>
        {qr && (
          <img
            src={qr}
            alt="TOTP QR Code"
            style={{ display: 'block', margin: '0 auto 12px', width: 180, height: 180 }}
          />
        )}
        {secret && (
          <p style={{ textAlign: 'center', fontSize: '0.78rem', color: '#6b7280', marginBottom: 16 }}>
            Manual key: <code style={{ background: '#f3f4f6', padding: '2px 6px', borderRadius: 4 }}>{secret}</code>
          </p>
        )}
        <form onSubmit={handleVerify} className={styles.form}>
          <label className={styles.label} htmlFor="totp">6-Digit Code</label>
          <input
            id="totp"
            className={styles.input}
            type="text"
            inputMode="numeric"
            maxLength={6}
            value={token}
            onChange={e => setToken(e.target.value.replace(/\D/g, ''))}
            placeholder="123456"
            aria-label="TOTP code"
          />
          {error && <p className={styles.error} role="alert">{error}</p>}
          <button className={styles.button} type="submit" disabled={loading}>
            {loading ? 'Verifying...' : 'Verify & Continue'}
          </button>
        </form>
      </div>
    </div>
  );
}

export default Setup2FAPage;
```

### src/components/NavBar.jsx + CSS

```jsx
// src/components/NavBar.jsx
import { Link, useLocation } from 'react-router-dom';
import { useAuth } from '../hooks/useAuth';
import styles from './NavBar.module.css';

function NavBar() {
  const { user, logout } = useAuth();
  const location = useLocation();

  const navLinks = [
    { path: '/dashboard', label: 'Dashboard' },
    { path: '/incidents', label: 'Incidents' },
    { path: '/reports',   label: 'Reports'   },
  ];

  if (user?.role === 'analyst' || user?.role === 'admin') {
    navLinks.push({ path: '/cve-tracker', label: 'CVE Tracker' });
  }
  if (user?.role === 'admin') {
    navLinks.push({ path: '/settings', label: 'Settings' });
  }

  return (
    <nav className={styles.nav} role="navigation" aria-label="Main navigation">
      <div className={styles.brand}>🛡️ CySentinel</div>
      <ul className={styles.links}>
        {navLinks.map(link => (
          <li key={link.path}>
            <Link
              to={link.path}
              className={`${styles.link} ${location.pathname === link.path ? styles.active : ''}`}
              aria-current={location.pathname === link.path ? 'page' : undefined}
            >
              {link.label}
            </Link>
          </li>
        ))}
      </ul>
      <div className={styles.userSection}>
        {user && (
          <>
            <span className={styles.email}>{user.email || user.sub}</span>
            <span className={`${styles.roleBadge} ${styles[user.role]}`}>
              {user.role}
            </span>
          </>
        )}
        <button className={styles.logoutBtn} onClick={logout} aria-label="Logout">
          Logout
        </button>
      </div>
    </nav>
  );
}

export default NavBar;
```

```css
/* src/components/NavBar.module.css */
.nav {
  display: flex;
  align-items: center;
  padding: 0 24px;
  height: 60px;
  background: #1e3a5f;
  color: #fff;
  gap: 24px;
  position: sticky;
  top: 0;
  z-index: 100;
}
.brand { font-size: 1.1rem; font-weight: 800; flex-shrink: 0; }
.links { display: flex; gap: 4px; list-style: none; margin: 0; padding: 0; flex: 1; }
.link {
  color: #cbd5e1;
  text-decoration: none;
  padding: 6px 14px;
  border-radius: 6px;
  font-size: 0.88rem;
  transition: background 0.15s;
}
.link:hover  { background: rgba(255,255,255,0.10); color: #fff; }
.active      { background: rgba(255,255,255,0.15); color: #fff; font-weight: 700; }
.userSection { display: flex; align-items: center; gap: 12px; flex-shrink: 0; }
.email       { font-size: 0.82rem; color: #cbd5e1; }
.roleBadge   { font-size: 0.72rem; font-weight: 700; padding: 2px 8px; border-radius: 10px; background: #3b82f6; color: #fff; }
.admin       { background: #dc2626; }
.analyst     { background: #d97706; }
.consultant  { background: #059669; }
.viewer      { background: #6366f1; }
.logoutBtn   {
  background: transparent;
  border: 1px solid rgba(255,255,255,0.3);
  color: #cbd5e1;
  padding: 6px 14px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.82rem;
}
.logoutBtn:hover { background: rgba(255,255,255,0.10); color: #fff; }
```

---

## 🗓️ MONTH 2 — MVP 2: Incident CRUD

### src/components/RiskBadge.jsx + CSS

```jsx
// src/components/RiskBadge.jsx
import styles from './RiskBadge.module.css';

const SEV_MAP = { critical: 'critical', high: 'high', medium: 'medium', low: 'low' };

function RiskBadge({ level }) {
  const cls = SEV_MAP[level] || 'low';
  return (
    <span className={`${styles.badge} ${styles[cls]}`} aria-label={`Severity: ${level}`}>
      {level ? level.charAt(0).toUpperCase() + level.slice(1) : 'Unknown'}
    </span>
  );
}

export default RiskBadge;
```

```css
/* src/components/RiskBadge.module.css */
.badge    { padding: 3px 10px; border-radius: 12px; font-weight: 700; font-size: 0.8rem; }
.critical { background: #fee2e2; color: #991b1b; }
.high     { background: #ffedd5; color: #9a3412; }
.medium   { background: #fef9c3; color: #854d0e; }
.low      { background: #dcfce7; color: #166534; }
```

### src/services/incidentService.js

```javascript
// src/services/incidentService.js
import api from './api';

export const getIncidents = async (filters = {}) => {
  // Remove empty filter values before building query string
  const params = new URLSearchParams(
    Object.fromEntries(Object.entries(filters).filter(([, v]) => v))
  ).toString();
  const { data } = await api.get(`/api/incidents${params ? '?' + params : ''}`);
  return data;   // { data: [...], total, page, limit }
};

export const getIncident = async (id) => {
  const { data } = await api.get(`/api/incidents/${id}`);
  return data;
};

export const createIncident = async (incident) => {
  const { data } = await api.post('/api/incidents', incident);
  return data;   // full 16-field incident object
};

export const updateIncident = async (id, updates) => {
  const { data } = await api.patch(`/api/incidents/${id}`, updates);
  return data;
};

export const uploadPhoto = async (file) => {
  const formData = new FormData();
  formData.append('photo', file);
  // FormData must bypass JSON Content-Type header
  const token = sessionStorage.getItem('cysentinel_token');
  const res = await fetch(
    `${process.env.REACT_APP_API_URL || 'http://localhost:5000'}/api/files/upload`,
    {
      method: 'POST',
      headers: { Authorization: `Bearer ${token}` },
      body: formData,
    }
  );
  if (!res.ok) throw new Error('Upload failed');
  const json = await res.json();
  return json.url;   // Cloudinary URL
};
```

### src/hooks/useIncidents.js

```javascript
// src/hooks/useIncidents.js
import { useState, useEffect, useCallback } from 'react';
import { getIncidents } from '../services/incidentService';

export function useIncidents(filters = {}) {
  const [incidents, setIncidents] = useState([]);
  const [loading, setLoading]     = useState(true);
  const [error, setError]         = useState(null);

  const fetchIncidents = useCallback(async () => {
    setLoading(true);
    setError(null);
    try {
      const result = await getIncidents(filters);
      // API returns { data: [], total, page, limit } OR flat array
      setIncidents(Array.isArray(result) ? result : result.data ?? []);
    } catch (err) {
      setError(err.message || 'Failed to load incidents');
    } finally {
      setLoading(false);
    }
  // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [filters.severity, filters.incident_type, filters.status]);

  useEffect(() => { fetchIncidents(); }, [fetchIncidents]);

  return { incidents, loading, error, refresh: fetchIncidents };
}
```

### src/components/IncidentTable.jsx + CSS

```jsx
// src/components/IncidentTable.jsx
import RiskBadge from './RiskBadge';
import styles from './IncidentTable.module.css';

function IncidentTable({ incidents, onRowClick }) {
  if (!incidents || incidents.length === 0) {
    return <p className={styles.empty}>No incidents found.</p>;
  }

  const fmt = (d) => d
    ? new Date(d).toLocaleDateString('en-ZA', { year: 'numeric', month: 'short', day: 'numeric' })
    : '—';

  return (
    <div className={styles.tableWrapper} role="region" aria-label="Incidents list">
      <table className={styles.table}>
        <thead>
          <tr>
            <th className={styles.th} scope="col">Title</th>
            <th className={styles.th} scope="col">Type</th>
            <th className={styles.th} scope="col">Severity</th>
            <th className={styles.th} scope="col">Status</th>
            <th className={styles.th} scope="col">Risk Score</th>
            <th className={styles.th} scope="col">Created</th>
          </tr>
        </thead>
        <tbody>
          {incidents.map(inc => (
            <tr
              key={inc.incident_id}
              className={styles.row}
              onClick={() => onRowClick && onRowClick(inc)}
              style={{ cursor: onRowClick ? 'pointer' : 'default' }}
              tabIndex={onRowClick ? 0 : undefined}
              onKeyDown={e => e.key === 'Enter' && onRowClick && onRowClick(inc)}
              aria-label={`Incident: ${inc.title}`}
            >
              <td className={styles.td}>{inc.title}</td>
              <td className={styles.td}>{inc.incident_type?.replace(/_/g, ' ')}</td>
              <td className={styles.td}><RiskBadge level={inc.severity} /></td>
              <td className={styles.td}>
                <span className={`${styles.statusBadge} ${styles[inc.status?.replace('_', '-')]}`}>
                  {inc.status?.replace('_', ' ')}
                </span>
              </td>
              <td className={styles.td}>
                <strong style={{ color: inc.risk_score >= 8 ? '#dc2626' : inc.risk_score >= 5 ? '#d97706' : '#166534' }}>
                  {inc.risk_score ?? '—'}
                </strong>/10
              </td>
              <td className={styles.td}>{fmt(inc.created_at)}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}

export default IncidentTable;
```

```css
/* src/components/IncidentTable.module.css */
.tableWrapper { overflow-x: auto; border-radius: 10px; border: 1px solid #e5e7eb; }
.table        { width: 100%; border-collapse: collapse; font-size: 0.88rem; }
.th           { padding: 12px 16px; background: #f9fafb; text-align: left; font-weight: 700; color: #374151; border-bottom: 1px solid #e5e7eb; white-space: nowrap; }
.td           { padding: 12px 16px; color: #111827; border-bottom: 1px solid #f3f4f6; }
.row:hover    { background: #f9fafb; }
.row:focus    { outline: 2px solid #3b82f6; outline-offset: -2px; }
.statusBadge  { padding: 3px 8px; border-radius: 10px; font-size: 0.78rem; font-weight: 700; background: #e5e7eb; color: #374151; }
.open         { background: #dbeafe; color: #1d4ed8; }
.in-progress  { background: #fef9c3; color: #854d0e; }
.resolved     { background: #dcfce7; color: #166534; }
.closed       { background: #f3f4f6; color: #6b7280; }
.empty        { text-align: center; color: #9ca3af; padding: 48px; font-size: 0.95rem; }
```

### src/components/IncidentForm.jsx + CSS

```jsx
// src/components/IncidentForm.jsx
import { useState } from 'react';
import { createIncident, uploadPhoto } from '../services/incidentService';
import styles from './IncidentForm.module.css';

const INCIDENT_TYPES = ['phishing','malware','unauthorised_access','data_breach','other'];
const SEVERITIES     = ['low','medium','high','critical'];

function IncidentForm({ onCreated }) {
  const [form, setForm]           = useState({ title: '', incident_type: '', severity: '', description: '' });
  const [photoFile, setPhotoFile] = useState(null);
  const [photoPreview, setPreview] = useState(null);
  const [error, setError]         = useState('');
  const [loading, setLoading]     = useState(false);

  const update = (key) => (e) => setForm(prev => ({ ...prev, [key]: e.target.value }));

  const handlePhoto = (e) => {
    const file = e.target.files[0];
    if (file) {
      if (photoPreview) URL.revokeObjectURL(photoPreview);
      setPhotoFile(file);
      setPreview(URL.createObjectURL(file));
    }
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');
    if (!form.title || !form.incident_type || !form.severity) {
      setError('Title, type, and severity are required.');
      return;
    }
    setLoading(true);
    try {
      let photo_url = null;
      if (photoFile) photo_url = await uploadPhoto(photoFile);
      await createIncident({ ...form, photo_url });
      onCreated && onCreated();
    } catch (err) {
      setError(err.response?.data?.error || 'Failed to create incident.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className={styles.form} aria-label="Log new incident">
      <h2 className={styles.heading}>Log New Incident</h2>

      <label className={styles.label} htmlFor="inc-title">Title *</label>
      <input
        id="inc-title"
        className={styles.input}
        value={form.title}
        onChange={update('title')}
        placeholder="Brief description of the incident"
        aria-label="Incident title"
        maxLength={255}
      />

      <label className={styles.label} htmlFor="inc-type">Incident Type *</label>
      <select id="inc-type" className={styles.input} value={form.incident_type}
        onChange={update('incident_type')} aria-label="Incident type">
        <option value="">Select type...</option>
        {INCIDENT_TYPES.map(t => <option key={t} value={t}>{t.replace(/_/g, ' ')}</option>)}
      </select>

      <label className={styles.label} htmlFor="inc-sev">Severity *</label>
      <select id="inc-sev" className={styles.input} value={form.severity}
        onChange={update('severity')} aria-label="Severity level">
        <option value="">Select severity...</option>
        {SEVERITIES.map(s => <option key={s} value={s}>{s}</option>)}
      </select>

      <label className={styles.label} htmlFor="inc-desc">Description</label>
      <textarea id="inc-desc" className={`${styles.input} ${styles.textarea}`}
        value={form.description} onChange={update('description')}
        placeholder="What happened? Include any relevant details..." rows={4}
        aria-label="Incident description" />

      <label className={styles.label} htmlFor="inc-photo">Photo Evidence (optional)</label>
      <input id="inc-photo" type="file" accept="image/*" onChange={handlePhoto}
        className={styles.fileInput} aria-label="Photo upload" />
      {photoPreview && (
        <img src={photoPreview} alt="Preview" className={styles.preview} />
      )}

      {error && <p className={styles.error} role="alert">{error}</p>}
      <button className={styles.button} type="submit" disabled={loading}>
        {loading ? 'Submitting...' : 'Log Incident'}
      </button>
    </form>
  );
}

export default IncidentForm;
```

```css
/* src/components/IncidentForm.module.css */
.form      { display: flex; flex-direction: column; gap: 10px; }
.heading   { font-size: 1.35rem; font-weight: 700; color: #111827; margin: 0 0 8px; }
.label     { font-size: 0.875rem; font-weight: 600; color: #374151; }
.input     { padding: 10px 12px; border: 1px solid #d1d5db; border-radius: 8px; font-size: 0.95rem; width: 100%; box-sizing: border-box; }
.input:focus { outline: none; border-color: #3b82f6; box-shadow: 0 0 0 3px rgba(59,130,246,0.15); }
.textarea  { resize: vertical; font-family: inherit; }
.fileInput { font-size: 0.88rem; color: #374151; }
.preview   { width: 120px; height: 90px; object-fit: cover; border-radius: 6px; border: 1px solid #e5e7eb; }
.error     { color: #dc2626; font-size: 0.875rem; padding: 8px 12px; background: #fee2e2; border-radius: 6px; margin: 0; }
.button    { background: #1e3a5f; color: #fff; border: none; padding: 13px; border-radius: 8px; font-weight: 700; font-size: 1rem; cursor: pointer; margin-top: 4px; }
.button:hover:not(:disabled) { background: #162e4d; }
.button:disabled { opacity: 0.6; cursor: not-allowed; }
```

### src/pages/IncidentsPage.jsx + CSS

```jsx
// src/pages/IncidentsPage.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import NavBar from '../components/NavBar';
import IncidentTable from '../components/IncidentTable';
import IncidentForm  from '../components/IncidentForm';
import { useIncidents } from '../hooks/useIncidents';
import styles from './IncidentsPage.module.css';

function IncidentsPage() {
  const navigate = useNavigate();
  const [filters, setFilters] = useState({ severity: '', incident_type: '', status: '' });
  const [showForm, setShowForm] = useState(false);
  const { incidents, loading, error, refresh } = useIncidents(filters);

  const setFilter = (key) => (e) => setFilters(prev => ({ ...prev, [key]: e.target.value }));

  return (
    <div>
      <NavBar />
      <div className={styles.page}>
        <div className={styles.header}>
          <h1 className={styles.title}>Incidents</h1>
          <button className={styles.newBtn} onClick={() => setShowForm(true)}>
            + New Incident
          </button>
        </div>

        <div className={styles.filterBar} role="group" aria-label="Filter incidents">
          <select aria-label="Filter by severity" value={filters.severity} onChange={setFilter('severity')} className={styles.filter}>
            <option value="">All Severities</option>
            {['critical','high','medium','low'].map(v => <option key={v} value={v}>{v}</option>)}
          </select>
          <select aria-label="Filter by type" value={filters.incident_type} onChange={setFilter('incident_type')} className={styles.filter}>
            <option value="">All Types</option>
            {['phishing','malware','unauthorised_access','data_breach','other'].map(v =>
              <option key={v} value={v}>{v.replace(/_/g,' ')}</option>)}
          </select>
          <select aria-label="Filter by status" value={filters.status} onChange={setFilter('status')} className={styles.filter}>
            <option value="">All Statuses</option>
            {['open','in_progress','resolved','closed'].map(v =>
              <option key={v} value={v}>{v.replace(/_/g,' ')}</option>)}
          </select>
        </div>

        {error   && <p className={styles.error} role="alert">Failed to load incidents: {error}</p>}
        {loading ? <p className={styles.loading}>Loading incidents...</p>
                 : <IncidentTable incidents={incidents} onRowClick={inc => navigate(`/incidents/${inc.incident_id}`)} />}

        {showForm && (
          <div className={styles.modal} role="dialog" aria-label="Create incident">
            <div className={styles.modalContent}>
              <button className={styles.closeBtn} onClick={() => setShowForm(false)} aria-label="Close modal">✕</button>
              <IncidentForm onCreated={() => { setShowForm(false); refresh(); }} />
            </div>
          </div>
        )}
      </div>
    </div>
  );
}

export default IncidentsPage;
```

```css
/* src/pages/IncidentsPage.module.css */
.page        { padding: 24px; max-width: 1200px; margin: 0 auto; }
.header      { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
.title       { font-size: 1.75rem; font-weight: 800; color: #111827; margin: 0; }
.newBtn      { background: #1e3a5f; color: #fff; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-weight: 700; font-size: 0.95rem; }
.newBtn:hover { background: #162e4d; }
.filterBar   { display: flex; gap: 12px; margin-bottom: 20px; flex-wrap: wrap; }
.filter      { padding: 8px 12px; border: 1px solid #d1d5db; border-radius: 6px; font-size: 0.88rem; background: #fff; }
.error       { color: #dc2626; padding: 12px; background: #fee2e2; border-radius: 8px; margin-bottom: 16px; }
.loading     { color: #6b7280; text-align: center; padding: 48px; }
.modal       { position: fixed; inset: 0; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 200; }
.modalContent { background: #fff; border-radius: 12px; padding: 32px; width: 100%; max-width: 560px; position: relative; max-height: 90vh; overflow-y: auto; }
.closeBtn    { position: absolute; top: 14px; right: 14px; background: none; border: none; font-size: 1.3rem; cursor: pointer; color: #6b7280; padding: 4px; }
.closeBtn:hover { color: #111827; }
```

---

## 🗓️ MONTH 3 — MVP 3: Checklist

### src/components/ChecklistPanel.jsx + CSS

```jsx
// src/components/ChecklistPanel.jsx
import { useState, useEffect } from 'react';
import api from '../services/api';
import styles from './ChecklistPanel.module.css';

function ChecklistPanel({ incidentId, readOnly = false }) {
  const [checklist, setChecklist] = useState(null);
  const [progress, setProgress]   = useState([]);
  const [loading, setLoading]     = useState(true);

  useEffect(() => {
    api.get(`/api/incidents/${incidentId}/checklist`)
      .then(r => {
        setChecklist(r.data.checklist);
        setProgress(r.data.progress || []);
      })
      .finally(() => setLoading(false));
  }, [incidentId]);

  const toggle = async (stepIndex) => {
    if (readOnly) return;
    const current = progress.find(p => p.step_index === stepIndex);
    const completed = !(current?.completed);
    try {
      const { data } = await api.patch(
        `/api/incidents/${incidentId}/checklist/${stepIndex}`,
        { completed }
      );
      setProgress(prev =>
        prev.some(p => p.step_index === stepIndex)
          ? prev.map(p => p.step_index === stepIndex ? data : p)
          : [...prev, data]
      );
    } catch {
      alert('Failed to update step — please retry.');
    }
  };

  if (loading)      return <p className={styles.empty}>Loading checklist...</p>;
  if (!checklist)   return <p className={styles.empty}>No checklist assigned to this incident.</p>;

  const steps         = checklist.steps || [];
  const completedCount = progress.filter(p => p.completed).length;
  const pct           = steps.length > 0 ? Math.round((completedCount / steps.length) * 100) : 0;

  return (
    <div className={styles.panel} aria-label="Response checklist">
      <h3 className={styles.heading}>{checklist.title}</h3>

      <div className={styles.progressBar} role="progressbar" aria-valuenow={pct} aria-valuemin={0} aria-valuemax={100}>
        <div className={styles.progressFill} style={{ width: `${pct}%` }} />
      </div>
      <p className={styles.progressText}>
        {completedCount}/{steps.length} steps complete ({pct}%)
      </p>

      <ul className={styles.stepList}>
        {steps.map((step, idx) => {
          const done = progress.find(p => p.step_index === idx)?.completed ?? false;
          return (
            <li
              key={idx}
              onClick={() => toggle(idx)}
              className={`${styles.step} ${done ? styles.done : ''} ${readOnly ? '' : styles.clickable}`}
              aria-label={`Step ${idx + 1}: ${step.text}. ${done ? 'Completed' : 'Not completed'}`}
              role={readOnly ? 'listitem' : 'checkbox'}
              aria-checked={done}
              tabIndex={readOnly ? undefined : 0}
              onKeyDown={e => !readOnly && e.key === 'Enter' && toggle(idx)}
            >
              <span className={styles.icon}>{done ? '✅' : '⬜'}</span>
              <span className={styles.stepText}>{step.text}</span>
            </li>
          );
        })}
      </ul>
    </div>
  );
}

export default ChecklistPanel;
```

```css
/* src/components/ChecklistPanel.module.css */
.panel        { border: 1px solid #e5e7eb; border-radius: 10px; padding: 20px; background: #fff; }
.heading      { margin: 0 0 14px; font-size: 1.1rem; font-weight: 700; color: #111827; }
.progressBar  { height: 8px; background: #e5e7eb; border-radius: 4px; overflow: hidden; margin-bottom: 6px; }
.progressFill { height: 100%; background: #22c55e; border-radius: 4px; transition: width 0.3s ease; }
.progressText { font-size: 0.85rem; color: #6b7280; margin: 0 0 16px; }
.stepList     { list-style: none; padding: 0; margin: 0; display: flex; flex-direction: column; gap: 8px; }
.step         { display: flex; align-items: center; gap: 10px; padding: 10px 12px; border-radius: 6px; border: 1px solid #f3f4f6; font-size: 0.9rem; color: #374151; }
.clickable    { cursor: pointer; }
.clickable:hover { background: #f9fafb; }
.clickable:focus { outline: 2px solid #3b82f6; outline-offset: 1px; }
.done         { color: #9ca3af; background: #f9fafb; }
.done .stepText { text-decoration: line-through; }
.icon         { font-size: 1rem; flex-shrink: 0; }
.stepText     { flex: 1; }
.empty        { text-align: center; color: #9ca3af; font-size: 0.9rem; padding: 20px 0; }
```

---

## 🗓️ MONTH 4 — MVP 4: Risk Dashboard

### src/services/dashboardService.js + cveService.js

```javascript
// src/services/dashboardService.js
import api from './api';

export const getRiskSummary = async () => {
  const { data } = await api.get('/api/dashboard/risk-summary');
  return data;
  // Expected shape: { total_open, by_severity:[{severity,count}],
  //                   by_status:[...], avg_risk_score, max_risk_score }
};

export const getTrends = async () => {
  const { data } = await api.get('/api/dashboard/trends');
  return data;   // [{date, count}]
};

export const getRiskByType = async () => {
  const { data } = await api.get('/api/dashboard/risk-by-type');
  return data;   // [{incident_type, avg_risk, count}]
};
```

```javascript
// src/services/cveService.js
import api from './api';

export const getCVEs = async () => {
  const { data } = await api.get('/api/cves');
  return data;
};

export const updateCVE = async (id, updates) => {
  const { data } = await api.patch(`/api/cves/${id}`, updates);
  return data;
};
```

### src/components/RiskSummaryCard.jsx + CSS

```jsx
// src/components/RiskSummaryCard.jsx
import RiskBadge from './RiskBadge';
import styles from './RiskSummaryCard.module.css';

function RiskSummaryCard({ summary }) {
  if (!summary) return null;

  // Map avg_risk_score → riskLevel label for RiskBadge
  const avg  = parseFloat(summary.avg_risk_score ?? 0);
  const level = avg >= 7 ? 'high' : avg >= 4 ? 'medium' : 'low';

  return (
    <div className={styles.card}>
      <h2 className={styles.heading}>Current Risk Level</h2>
      <RiskBadge level={level} />
      <div className={styles.stats}>
        <div className={styles.stat}>
          <span className={styles.value}>{summary.total_open ?? '—'}</span>
          <span className={styles.statLabel}>Open Incidents</span>
        </div>
        <div className={styles.stat}>
          <span className={styles.value}>
            {avg.toFixed(1)}
            <span className={styles.unit}>/10</span>
          </span>
          <span className={styles.statLabel}>Avg Risk Score</span>
        </div>
        <div className={styles.stat}>
          <span className={styles.value}>{summary.max_risk_score ?? '—'}</span>
          <span className={styles.statLabel}>Max Risk Score</span>
        </div>
      </div>
    </div>
  );
}

export default RiskSummaryCard;
```

```css
/* src/components/RiskSummaryCard.module.css */
.card      { background: #fff; border-radius: 12px; padding: 24px; border: 1px solid #e5e7eb; }
.heading   { font-size: 0.95rem; font-weight: 700; color: #6b7280; margin: 0 0 12px; }
.stats     { display: flex; gap: 24px; margin-top: 16px; flex-wrap: wrap; }
.stat      { display: flex; flex-direction: column; }
.value     { font-size: 2rem; font-weight: 800; color: #111827; line-height: 1; }
.unit      { font-size: 1rem; color: #9ca3af; }
.statLabel { font-size: 0.78rem; color: #9ca3af; margin-top: 4px; }
```

### src/pages/DashboardPage.jsx + CSS

```jsx
// src/pages/DashboardPage.jsx
import { useState, useEffect } from 'react';
import {
  BarChart, Bar, LineChart, Line, PieChart, Pie, Cell,
  XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer
} from 'recharts';
import { getRiskSummary, getTrends, getRiskByType } from '../services/dashboardService';
import NavBar from '../components/NavBar';
import RiskSummaryCard from '../components/RiskSummaryCard';
import styles from './DashboardPage.module.css';

const SEV_COLORS = { critical: '#dc2626', high: '#ea580c', medium: '#d97706', low: '#16a34a' };
const PIE_COLORS = ['#dc2626', '#ea580c', '#d97706', '#16a34a', '#6366f1'];

function DashboardPage() {
  const [summary,   setSummary]   = useState(null);
  const [trends,    setTrends]    = useState([]);
  const [byType,    setByType]    = useState([]);
  const [loading,   setLoading]   = useState(true);
  const [error,     setError]     = useState(null);

  useEffect(() => {
    Promise.all([getRiskSummary(), getTrends(), getRiskByType()])
      .then(([s, t, bt]) => { setSummary(s); setTrends(t); setByType(bt); })
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <><NavBar /><p className={styles.loading}>Loading dashboard...</p></>;
  if (error)   return <><NavBar /><p className={styles.error} role="alert">Dashboard error: {error}</p></>;

  // Pie chart data from summary.by_severity
  const pieData = (summary?.by_severity ?? []).map((entry, i) => ({
    name:  entry.severity,
    value: parseInt(entry.count, 10),
    fill:  SEV_COLORS[entry.severity] || PIE_COLORS[i],
  }));

  // Bar chart data from getRiskByType
  const barData = byType.map(bt => ({ type: bt.incident_type?.replace(/_/g, ' '), count: parseInt(bt.count, 10) }));

  // Line chart data — last 30 days
  const lineData = [...trends].sort((a, b) => a.date.localeCompare(b.date)).slice(-30);

  return (
    <div>
      <NavBar />
      <div className={styles.dashboard}>
        <h1 className={styles.pageTitle}>Risk Dashboard</h1>

        <div className={styles.grid}>
          <div className={styles.span2}>
            <RiskSummaryCard summary={summary} />
          </div>

          <div className={styles.chartCard}>
            <h3 className={styles.chartTitle}>Incidents by Type</h3>
            <ResponsiveContainer width="100%" height={220}>
              <BarChart data={barData} margin={{ top: 4, right: 8, bottom: 0, left: 0 }}>
                <CartesianGrid strokeDasharray="3 3" stroke="#f3f4f6" />
                <XAxis dataKey="type" tick={{ fontSize: 11 }} />
                <YAxis allowDecimals={false} tick={{ fontSize: 11 }} />
                <Tooltip />
                <Bar dataKey="count" fill="#3b82f6" radius={[4,4,0,0]} />
              </BarChart>
            </ResponsiveContainer>
          </div>

          <div className={styles.chartCard}>
            <h3 className={styles.chartTitle}>30-Day Incident Trend</h3>
            <ResponsiveContainer width="100%" height={220}>
              <LineChart data={lineData} margin={{ top: 4, right: 8, bottom: 0, left: 0 }}>
                <CartesianGrid strokeDasharray="3 3" stroke="#f3f4f6" />
                <XAxis dataKey="date" tick={{ fontSize: 10 }} />
                <YAxis allowDecimals={false} tick={{ fontSize: 11 }} />
                <Tooltip />
                <Line type="monotone" dataKey="count" stroke="#3b82f6" strokeWidth={2} dot={false} />
              </LineChart>
            </ResponsiveContainer>
          </div>

          <div className={styles.chartCard}>
            <h3 className={styles.chartTitle}>By Severity</h3>
            <ResponsiveContainer width="100%" height={220}>
              <PieChart>
                <Pie data={pieData} cx="50%" cy="50%" outerRadius={80}
                     dataKey="value" nameKey="name" label>
                  {pieData.map((entry, i) => <Cell key={i} fill={entry.fill} />)}
                </Pie>
                <Tooltip />
                <Legend />
              </PieChart>
            </ResponsiveContainer>
          </div>
        </div>
      </div>
    </div>
  );
}

export default DashboardPage;
```

```css
/* src/pages/DashboardPage.module.css */
.dashboard { padding: 24px; max-width: 1400px; margin: 0 auto; }
.pageTitle { font-size: 1.75rem; font-weight: 800; color: #111827; margin: 0 0 24px; }
.loading   { text-align: center; color: #6b7280; padding: 80px; font-size: 1rem; }
.error     { color: #dc2626; padding: 16px; background: #fee2e2; border-radius: 8px; margin: 24px; }
.grid      { display: grid; grid-template-columns: repeat(auto-fill, minmax(360px, 1fr)); gap: 20px; }
.span2     { grid-column: 1 / -1; }
.chartCard {
  background: #fff;
  border-radius: 12px;
  padding: 20px;
  border: 1px solid #e5e7eb;
}
.chartTitle { font-size: 0.92rem; font-weight: 700; color: #374151; margin: 0 0 14px; }
```

#### [MINIMAL] DashboardPage — no Recharts (SVG bar chart from scratch)

```jsx
// src/pages/DashboardPage.minimal.jsx
// No recharts — renders a basic SVG bar chart using only React + CSS
// Use this if recharts is restricted

import { useState, useEffect } from 'react';
import { getRiskSummary, getTrends } from '../services/dashboardService';
import NavBar from '../components/NavBar';
import RiskSummaryCard from '../components/RiskSummaryCard';
import styles from './DashboardPage.module.css';

function SVGBarChart({ data, dataKey, labelKey, color = '#3b82f6', height = 200 }) {
  if (!data || data.length === 0) return <p style={{ color: '#9ca3af', textAlign: 'center' }}>No data</p>;
  const maxVal = Math.max(...data.map(d => d[dataKey]));
  const barW   = Math.floor(360 / data.length) - 4;

  return (
    <svg width="100%" height={height} viewBox={`0 0 360 ${height}`}>
      {data.map((d, i) => {
        const barH  = maxVal > 0 ? Math.round((d[dataKey] / maxVal) * (height - 40)) : 0;
        const x     = i * (barW + 4) + 2;
        const y     = height - 30 - barH;
        return (
          <g key={i}>
            <rect x={x} y={y} width={barW} height={barH} fill={color} rx={3} />
            <text x={x + barW / 2} y={height - 14} textAnchor="middle"
                  fontSize="10" fill="#6b7280" style={{ fontFamily: 'inherit' }}>
              {String(d[labelKey]).slice(0, 8)}
            </text>
            <text x={x + barW / 2} y={y - 4} textAnchor="middle"
                  fontSize="10" fill="#374151" fontWeight="600">
              {d[dataKey]}
            </text>
          </g>
        );
      })}
    </svg>
  );
}

function DashboardPage() {
  const [summary, setSummary] = useState(null);
  const [trends,  setTrends]  = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    Promise.all([getRiskSummary(), getTrends()])
      .then(([s, t]) => { setSummary(s); setTrends(t); })
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <><NavBar /><p className={styles.loading}>Loading dashboard...</p></>;

  const trendData = [...trends].sort((a, b) => a.date.localeCompare(b.date)).slice(-14);
  const byType    = (summary?.by_severity ?? []).map(d => ({ ...d, count: parseInt(d.count, 10) }));

  return (
    <div>
      <NavBar />
      <div className={styles.dashboard}>
        <h1 className={styles.pageTitle}>Risk Dashboard</h1>
        <div className={styles.grid}>
          <div className={styles.span2}><RiskSummaryCard summary={summary} /></div>
          <div className={styles.chartCard}>
            <h3 className={styles.chartTitle}>By Severity (no-libs bar chart)</h3>
            <SVGBarChart data={byType} dataKey="count" labelKey="severity" color="#3b82f6" />
          </div>
          <div className={styles.chartCard}>
            <h3 className={styles.chartTitle}>14-Day Trend (no-libs)</h3>
            <SVGBarChart data={trendData} dataKey="count" labelKey="date" color="#22c55e" />
          </div>
        </div>
      </div>
    </div>
  );
}

export default DashboardPage;
```

---

## 🗓️ MONTH 5 — MVP 5: Reports

### src/pages/ReportsPage.jsx + CSS (already shown above in roadmap — reproduced here for completeness)

```jsx
// src/pages/ReportsPage.jsx
import { useState } from 'react';
import NavBar from '../components/NavBar';
import api    from '../services/api';
import styles from './ReportsPage.module.css';

function ReportsPage() {
  const [startDate, setStartDate] = useState('');
  const [endDate,   setEndDate]   = useState('');
  const [loading,   setLoading]   = useState({ csv: false, pdf: false });
  const [error,     setError]     = useState('');

  const downloadBlob = (data, filename, mimeType) => {
    const url = window.URL.createObjectURL(new Blob([data], { type: mimeType }));
    const a   = document.createElement('a');
    a.href = url; a.download = filename; a.click();
    window.URL.revokeObjectURL(url);
  };

  const download = (type) => async () => {
    if (!startDate || !endDate) { setError('Please select both start and end dates.'); return; }
    setError('');
    setLoading(prev => ({ ...prev, [type]: true }));
    try {
      const endpoint = type === 'csv' ? '/api/reports/csv' : '/api/reports/pdf';
      const mime     = type === 'csv' ? 'text/csv' : 'application/pdf';
      const filename = `cysentinel-${startDate}-to-${endDate}.${type}`;
      const response = await api.post(endpoint, { from_date: startDate, to_date: endDate },
        { responseType: 'blob' });
      downloadBlob(response.data, filename, mime);
    } catch {
      setError(`Failed to download ${type.toUpperCase()}. Please try again.`);
    } finally {
      setLoading(prev => ({ ...prev, [type]: false }));
    }
  };

  return (
    <div>
      <NavBar />
      <div className={styles.page}>
        <h1 className={styles.title}>Reports</h1>
        <div className={styles.controls}>
          {[['startDate', 'From', startDate, setStartDate], ['endDate', 'To', endDate, setEndDate]]
            .map(([id, label, val, setter]) => (
              <div key={id} className={styles.dateGroup}>
                <label className={styles.label} htmlFor={id}>{label}</label>
                <input id={id} type="date" value={val} onChange={e => setter(e.target.value)}
                  className={styles.dateInput} aria-label={`${label} date`} />
              </div>
            ))}
        </div>
        {error && <p className={styles.error} role="alert">{error}</p>}
        <div className={styles.buttons}>
          <button className={`${styles.btn} ${styles.csvBtn}`} onClick={download('csv')}
            disabled={loading.csv} aria-label="Export CSV">
            {loading.csv ? 'Exporting...' : '📄 Export CSV'}
          </button>
          <button className={`${styles.btn} ${styles.pdfBtn}`} onClick={download('pdf')}
            disabled={loading.pdf} aria-label="Export PDF">
            {loading.pdf ? 'Generating...' : '📋 Export PDF'}
          </button>
        </div>
      </div>
    </div>
  );
}

export default ReportsPage;
```

```css
/* src/pages/ReportsPage.module.css */
.page      { padding: 24px; max-width: 800px; margin: 0 auto; }
.title     { font-size: 1.75rem; font-weight: 800; color: #111827; margin: 0 0 24px; }
.controls  { display: flex; gap: 20px; margin-bottom: 20px; flex-wrap: wrap; }
.dateGroup { display: flex; flex-direction: column; gap: 6px; }
.label     { font-size: 0.875rem; font-weight: 600; color: #374151; }
.dateInput { padding: 10px 12px; border: 1px solid #d1d5db; border-radius: 8px; font-size: 0.95rem; }
.error     { color: #dc2626; padding: 10px 14px; background: #fee2e2; border-radius: 6px; margin-bottom: 16px; }
.buttons   { display: flex; gap: 12px; flex-wrap: wrap; }
.btn       { padding: 12px 24px; border: none; border-radius: 8px; font-weight: 700; cursor: pointer; font-size: 0.95rem; }
.btn:disabled { opacity: 0.6; cursor: not-allowed; }
.csvBtn    { background: #059669; color: #fff; }
.csvBtn:hover:not(:disabled) { background: #047857; }
.pdfBtn    { background: #dc2626; color: #fff; }
.pdfBtn:hover:not(:disabled) { background: #b91c1c; }
```

---

## 🗓️ MONTH 7 (Optional) — Dark Mode

### src/hooks/useDarkMode.js

```javascript
// src/hooks/useDarkMode.js
import { useState, useEffect } from 'react';

function useDarkMode() {
  const [dark, setDark] = useState(
    () => localStorage.getItem('cysentinel_theme') === 'dark'
  );

  useEffect(() => {
    document.documentElement.setAttribute('data-theme', dark ? 'dark' : 'light');
    localStorage.setItem('cysentinel_theme', dark ? 'dark' : 'light');
  }, [dark]);

  return [dark, setDark];
}

export default useDarkMode;
```

```css
/* src/App.css — CSS variables for light/dark themes */
:root {
  --bg-primary:    #ffffff;
  --bg-secondary:  #f3f4f6;
  --bg-card:       #ffffff;
  --text-primary:  #111827;
  --text-secondary: #6b7280;
  --border:        #e5e7eb;
  --nav-bg:        #1e3a5f;
  --accent:        #3b82f6;
}

[data-theme="dark"] {
  --bg-primary:    #111827;
  --bg-secondary:  #1f2937;
  --bg-card:       #1f2937;
  --text-primary:  #f9fafb;
  --text-secondary: #9ca3af;
  --border:        #374151;
  --nav-bg:        #0f1f35;
  --accent:        #60a5fa;
}

body { background: var(--bg-primary); color: var(--text-primary); transition: background 0.2s, color 0.2s; }
```

---

## 🧪 Tests

### [WITH LIBS] src/pages/LoginPage.test.js — React Testing Library

```javascript
// src/pages/LoginPage.test.js
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { MemoryRouter } from 'react-router-dom';
import LoginPage from './LoginPage';
import { login } from '../services/authService';

jest.mock('../services/authService');

const renderLogin = () => render(<MemoryRouter><LoginPage /></MemoryRouter>);

describe('LoginPage', () => {
  beforeEach(() => jest.clearAllMocks());

  it('renders email and password inputs', () => {
    renderLogin();
    expect(screen.getByLabelText('Email address')).toBeInTheDocument();
    expect(screen.getByLabelText('Password')).toBeInTheDocument();
  });

  it('shows "Sign In" button', () => {
    renderLogin();
    expect(screen.getByRole('button', { name: /sign in/i })).toBeInTheDocument();
  });

  it('shows validation error when fields are empty', async () => {
    renderLogin();
    fireEvent.click(screen.getByRole('button', { name: /sign in/i }));
    await waitFor(() => {
      expect(screen.getByRole('alert')).toHaveTextContent('Email and password are required');
    });
  });

  it('calls login service with correct credentials', async () => {
    login.mockResolvedValue({ token: 'mock.jwt.token' });
    renderLogin();
    fireEvent.change(screen.getByLabelText('Email address'), { target: { value: 'user@test.com' } });
    fireEvent.change(screen.getByLabelText('Password'),      { target: { value: 'password123' } });
    fireEvent.click(screen.getByRole('button', { name: /sign in/i }));
    await waitFor(() => expect(login).toHaveBeenCalledWith('user@test.com', 'password123'));
  });

  it('shows API error on login failure', async () => {
    login.mockRejectedValue({ response: { data: { error: 'Invalid email or password' } } });
    renderLogin();
    fireEvent.change(screen.getByLabelText('Email address'), { target: { value: 'bad@test.com' } });
    fireEvent.change(screen.getByLabelText('Password'),      { target: { value: 'wrong' } });
    fireEvent.click(screen.getByRole('button', { name: /sign in/i }));
    await waitFor(() => {
      expect(screen.getByRole('alert')).toHaveTextContent('Invalid email or password');
    });
  });

  it('disables button while loading', async () => {
    login.mockImplementation(() => new Promise(() => {})); // never resolves
    renderLogin();
    fireEvent.change(screen.getByLabelText('Email address'), { target: { value: 'a@b.com' } });
    fireEvent.change(screen.getByLabelText('Password'),      { target: { value: 'pass1234' } });
    fireEvent.click(screen.getByRole('button', { name: /sign in/i }));
    await waitFor(() => {
      expect(screen.getByRole('button', { name: /signing in/i })).toBeDisabled();
    });
  });
});
```

### [WITH LIBS] src/components/IncidentTable.test.js

```javascript
// src/components/IncidentTable.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import IncidentTable from './IncidentTable';

const mockIncidents = [
  {
    incident_id: 'aaa-111',
    title: 'Phishing Campaign',
    incident_type: 'phishing',
    severity: 'high',
    status: 'open',
    risk_score: 7,
    created_at: '2026-03-01T08:00:00Z',
  },
  {
    incident_id: 'bbb-222',
    title: 'Data Breach Detected',
    incident_type: 'data_breach',
    severity: 'critical',
    status: 'in_progress',
    risk_score: 10,
    created_at: '2026-03-02T14:30:00Z',
  },
];

describe('IncidentTable', () => {
  it('renders column headers', () => {
    render(<IncidentTable incidents={mockIncidents} />);
    expect(screen.getByText('Title')).toBeInTheDocument();
    expect(screen.getByText('Severity')).toBeInTheDocument();
    expect(screen.getByText('Risk Score')).toBeInTheDocument();
  });

  it('renders all incident rows', () => {
    render(<IncidentTable incidents={mockIncidents} />);
    expect(screen.getByText('Phishing Campaign')).toBeInTheDocument();
    expect(screen.getByText('Data Breach Detected')).toBeInTheDocument();
  });

  it('renders risk_score values', () => {
    render(<IncidentTable incidents={mockIncidents} />);
    expect(screen.getByText('7')).toBeInTheDocument();
    expect(screen.getByText('10')).toBeInTheDocument();
  });

  it('shows "No incidents found" when list is empty', () => {
    render(<IncidentTable incidents={[]} />);
    expect(screen.getByText('No incidents found.')).toBeInTheDocument();
  });

  it('calls onRowClick with incident when row clicked', () => {
    const handler = jest.fn();
    render(<IncidentTable incidents={mockIncidents} onRowClick={handler} />);
    fireEvent.click(screen.getByText('Phishing Campaign'));
    expect(handler).toHaveBeenCalledWith(mockIncidents[0]);
  });

  it('renders severity badge for each row', () => {
    render(<IncidentTable incidents={mockIncidents} />);
    expect(screen.getByLabelText('Severity: high')).toBeInTheDocument();
    expect(screen.getByLabelText('Severity: critical')).toBeInTheDocument();
  });
});
```

### [WITH LIBS] src/components/ChecklistPanel.test.js

```javascript
// src/components/ChecklistPanel.test.js
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import ChecklistPanel from './ChecklistPanel';
import api from '../services/api';

jest.mock('../services/api');

const mockChecklist = {
  checklist_id: 'cl-1',
  title: 'Phishing Response Checklist',
  steps: [
    { index: 0, text: 'Block sender domain' },
    { index: 1, text: 'Warn all users' },
  ],
};
const mockProgress = [
  { step_index: 0, completed: false },
  { step_index: 1, completed: true },
];

describe('ChecklistPanel', () => {
  beforeEach(() => {
    api.get.mockResolvedValue({
      data: { checklist: mockChecklist, progress: mockProgress, percent_complete: 50 },
    });
    api.patch.mockResolvedValue({
      data: { step_index: 0, completed: true },
    });
  });

  it('renders checklist title', async () => {
    render(<ChecklistPanel incidentId="inc-1" />);
    await waitFor(() => {
      expect(screen.getByText('Phishing Response Checklist')).toBeInTheDocument();
    });
  });

  it('renders steps', async () => {
    render(<ChecklistPanel incidentId="inc-1" />);
    await waitFor(() => {
      expect(screen.getByText('Block sender domain')).toBeInTheDocument();
      expect(screen.getByText('Warn all users')).toBeInTheDocument();
    });
  });

  it('shows progress percentage', async () => {
    render(<ChecklistPanel incidentId="inc-1" />);
    await waitFor(() => {
      expect(screen.getByText(/50%/)).toBeInTheDocument();
    });
  });

  it('calls PATCH when step is toggled', async () => {
    render(<ChecklistPanel incidentId="inc-1" />);
    await waitFor(() => screen.getByText('Block sender domain'));
    fireEvent.click(screen.getByLabelText(/Step 1: Block sender domain/i));
    await waitFor(() => {
      expect(api.patch).toHaveBeenCalledWith(
        '/api/incidents/inc-1/checklist/0',
        { completed: true }
      );
    });
  });

  it('does not call PATCH when readOnly is true', async () => {
    render(<ChecklistPanel incidentId="inc-1" readOnly />);
    await waitFor(() => screen.getByText('Block sender domain'));
    fireEvent.click(screen.getByText('Block sender domain'));
    expect(api.patch).not.toHaveBeenCalled();
  });

  it('shows empty state when no checklist assigned', async () => {
    api.get.mockResolvedValue({ data: { checklist: null, progress: [] } });
    render(<ChecklistPanel incidentId="inc-2" />);
    await waitFor(() => {
      expect(screen.getByText('No checklist assigned to this incident.')).toBeInTheDocument();
    });
  });
});
```

### [WITH LIBS] src/components/IncidentForm.test.js

```javascript
// src/components/IncidentForm.test.js
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import IncidentForm from './IncidentForm';
import { createIncident } from '../services/incidentService';

jest.mock('../services/incidentService');

describe('IncidentForm', () => {
  beforeEach(() => jest.clearAllMocks());

  it('renders all required fields', () => {
    render(<IncidentForm />);
    expect(screen.getByLabelText('Incident title')).toBeInTheDocument();
    expect(screen.getByLabelText('Incident type')).toBeInTheDocument();
    expect(screen.getByLabelText('Severity level')).toBeInTheDocument();
  });

  it('shows error when required fields missing', async () => {
    render(<IncidentForm />);
    fireEvent.click(screen.getByRole('button', { name: /log incident/i }));
    await waitFor(() => {
      expect(screen.getByRole('alert')).toHaveTextContent('Title, type, and severity are required');
    });
  });

  it('calls createIncident with form data on submit', async () => {
    createIncident.mockResolvedValue({ incident_id: 'new-1', risk_score: 7 });
    render(<IncidentForm onCreated={jest.fn()} />);

    fireEvent.change(screen.getByLabelText('Incident title'),   { target: { value: 'Phishing test' } });
    fireEvent.change(screen.getByLabelText('Incident type'),    { target: { value: 'phishing' } });
    fireEvent.change(screen.getByLabelText('Severity level'),   { target: { value: 'high' } });
    fireEvent.change(screen.getByLabelText('Incident description'), { target: { value: 'Test desc' } });
    fireEvent.click(screen.getByRole('button', { name: /log incident/i }));

    await waitFor(() => {
      expect(createIncident).toHaveBeenCalledWith(expect.objectContaining({
        title: 'Phishing test',
        incident_type: 'phishing',
        severity: 'high',
      }));
    });
  });

  it('calls onCreated callback after successful submit', async () => {
    createIncident.mockResolvedValue({ incident_id: 'new-2' });
    const onCreated = jest.fn();
    render(<IncidentForm onCreated={onCreated} />);

    fireEvent.change(screen.getByLabelText('Incident title'),  { target: { value: 'Malware alert' } });
    fireEvent.change(screen.getByLabelText('Incident type'),   { target: { value: 'malware' } });
    fireEvent.change(screen.getByLabelText('Severity level'),  { target: { value: 'critical' } });
    fireEvent.click(screen.getByRole('button', { name: /log incident/i }));

    await waitFor(() => expect(onCreated).toHaveBeenCalled());
  });
});
```

### [WITH LIBS] src/components/RiskBadge.test.js

```javascript
// src/components/RiskBadge.test.js
import { render, screen } from '@testing-library/react';
import RiskBadge from './RiskBadge';

describe('RiskBadge', () => {
  it.each(['low','medium','high','critical'])('renders %s severity', (level) => {
    render(<RiskBadge level={level} />);
    expect(screen.getByText(level.charAt(0).toUpperCase() + level.slice(1))).toBeInTheDocument();
  });

  it('renders "Unknown" for null level', () => {
    render(<RiskBadge level={null} />);
    expect(screen.getByText('Unknown')).toBeInTheDocument();
  });

  it('has correct aria-label', () => {
    render(<RiskBadge level="critical" />);
    expect(screen.getByLabelText('Severity: critical')).toBeInTheDocument();
  });
});
```

### [MINIMAL / NO LIBS] Plain DOM tests — no React Testing Library

```javascript
// src/__tests__/utils.no-libs.test.js
// Run: npm test -- --testPathPattern=no-libs
// Tests pure utility logic that does not require a React renderer

// ── useAuth logic (pure function extraction) ──────────────────────────────────
function decodeToken(token) {
  if (!token) return null;
  try { return JSON.parse(atob(token.split('.')[1])); }
  catch { return null; }
}

describe('decodeToken (useAuth logic)', () => {
  it('returns null for null token', () => {
    expect(decodeToken(null)).toBeNull();
  });

  it('returns null for malformed token', () => {
    expect(decodeToken('not.a.token')).toBeNull();
  });

  it('decodes valid JWT payload', () => {
    // Create a fake JWT with known payload
    const payload   = { sub: 'user-1', role: 'admin', org: 'org-1' };
    const b64       = btoa(JSON.stringify(payload));
    const fakeToken = `header.${b64}.signature`;
    const decoded   = decodeToken(fakeToken);
    expect(decoded.sub).toBe('user-1');
    expect(decoded.role).toBe('admin');
  });
});

// ── Date formatter (used in IncidentTable) ────────────────────────────────────
function formatDate(dateStr) {
  if (!dateStr) return '—';
  return new Date(dateStr).toLocaleDateString('en-ZA', {
    year: 'numeric', month: 'short', day: 'numeric'
  });
}

describe('formatDate', () => {
  it('returns em-dash for null', () => {
    expect(formatDate(null)).toBe('—');
  });

  it('returns formatted date string', () => {
    const result = formatDate('2026-03-01T08:00:00Z');
    expect(result).toMatch(/2026/);
    expect(result).toMatch(/Mar/);
  });
});

// ── Filter utility (used in incidentService) ─────────────────────────────────
function buildQueryString(filters) {
  return new URLSearchParams(
    Object.fromEntries(Object.entries(filters).filter(([, v]) => v))
  ).toString();
}

describe('buildQueryString', () => {
  it('returns empty string for empty filters', () => {
    expect(buildQueryString({})).toBe('');
  });

  it('excludes empty values', () => {
    const qs = buildQueryString({ severity: 'high', status: '', incident_type: '' });
    expect(qs).toBe('severity=high');
  });

  it('includes multiple non-empty values', () => {
    const qs = buildQueryString({ severity: 'high', status: 'open' });
    expect(qs).toContain('severity=high');
    expect(qs).toContain('status=open');
  });
});
```

---

## 🗓️ Run All Tests

```bash
# Run all React Testing Library tests (requires CRA default setup)
cd cysentinel-web
npm test -- --watchAll=false

# Run with coverage report
npm test -- --watchAll=false --coverage

# Run only a specific file
npm test -- --watchAll=false --testPathPattern=LoginPage

# Run no-libs utility tests
npm test -- --watchAll=false --testPathPattern=no-libs
```

---

*End of File 03 — Frontend Developer Code Blocks*  
*Next: 04_Mobile_Testing_Code_Blocks.md*
