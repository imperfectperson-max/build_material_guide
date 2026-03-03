# 📱 Mobile Developer — Expanded Code Blocks
**CySentinel | Debug Ninjas | UJ IFM03A3 | 2026**

> Role split: **Months 1–5 = testing/QA support**. **Months 6–7 = React Native mobile build (MVP 6)**.  
> Every code block is copy-paste ready.  
> `[WITH LIBS]` = Expo + axios + React Navigation (standard stack).  
> `[NO LIBS]` = plain `fetch` + no React Navigation (pure React Native + native stack) — use only if libraries are restricted.  
> Tests: Jest + `@testing-library/react-native` `[WITH LIBS]`, plain Jest function tests `[NO LIBS]`.

---

## 🗓️ PLANNING WEEK

### Monday–Thursday — Environment Setup

```bash
# Verify Node.js v18+
node --version && npm --version

# Install EAS CLI globally
npm install -g eas-cli

# Create the Expo app (blank template — recommended for custom navigation)
npx create-expo-app cysentinel-mobile --template blank
cd cysentinel-mobile

# Install Expo packages
npx expo install expo-notifications expo-secure-store

# Install axios
npm install axios

# Install React Navigation
npm install @react-navigation/native @react-navigation/stack
npx expo install react-native-screens react-native-safe-area-context react-native-gesture-handler

# Install testing dependencies
npm install --save-dev jest @testing-library/react-native @testing-library/jest-native

# Start dev server
npx expo start
# Scan QR code with Expo Go app on physical device
```

```bash
# Create folder structure
mkdir -p src/{screens,services,utils,hooks}

touch src/screens/LoginScreen.jsx
touch src/screens/QuickReportScreen.jsx
touch src/screens/MyItemsScreen.jsx
touch src/screens/RiskSummaryScreen.jsx

touch src/services/api.js
touch src/services/authService.js
touch src/services/notificationService.js

touch src/utils/secureStorage.js
touch src/utils/offlineCache.js

touch src/hooks/useAuth.js
```

```json
// package.json — add jest config (merge into existing)
{
  "jest": {
    "preset": "react-native",
    "setupFilesAfterFramework": ["@testing-library/jest-native/extend-expect"],
    "transformIgnorePatterns": [
      "node_modules/(?!(expo|expo-secure-store|expo-notifications|@expo|react-native|@react-native|@testing-library)/)"
    ]
  }
}
```

```
# .env
API_URL_DEV=http://192.168.x.x:5000
API_URL_PROD=https://cysentinel-backend-production.up.railway.app
```

### Saturday — App.js Navigation Setup

#### [WITH LIBS] App.js — React Navigation stack

```jsx
// App.js
// IMPORTANT: react-native-gesture-handler must be the FIRST import
import 'react-native-gesture-handler';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator }  from '@react-navigation/stack';

import LoginScreen       from './src/screens/LoginScreen';
import QuickReportScreen from './src/screens/QuickReportScreen';
import MyItemsScreen     from './src/screens/MyItemsScreen';
import RiskSummaryScreen from './src/screens/RiskSummaryScreen';

const Stack = createStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        initialRouteName="Login"
        screenOptions={{
          headerStyle:     { backgroundColor: '#1e3a5f' },
          headerTintColor: '#ffffff',
          headerTitleStyle:{ fontWeight: '700' },
        }}
      >
        <Stack.Screen name="Login"       component={LoginScreen}       options={{ headerShown: false }} />
        <Stack.Screen name="MyItems"     component={MyItemsScreen}     options={{ title: 'My Open Items' }} />
        <Stack.Screen name="QuickReport" component={QuickReportScreen} options={{ title: 'Report Incident' }} />
        <Stack.Screen name="RiskSummary" component={RiskSummaryScreen} options={{ title: 'Risk Overview' }} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

#### [NO LIBS] App.js — no React Navigation (manual screen state)

```jsx
// App.js — no react-navigation; manual screen management via state
import { useState } from 'react';
import { SafeAreaView, StyleSheet } from 'react-native';

import LoginScreen       from './src/screens/LoginScreen';
import MyItemsScreen     from './src/screens/MyItemsScreen';
import QuickReportScreen from './src/screens/QuickReportScreen';
import RiskSummaryScreen from './src/screens/RiskSummaryScreen';

export default function App() {
  const [screen, setScreen] = useState('Login');

  const navigate = (name) => setScreen(name);
  const goBack   = () => setScreen('MyItems');  // default "back" target

  const screens = {
    Login:       <LoginScreen navigate={navigate} />,
    MyItems:     <MyItemsScreen navigate={navigate} />,
    QuickReport: <QuickReportScreen navigate={navigate} goBack={goBack} />,
    RiskSummary: <RiskSummaryScreen navigate={navigate} goBack={goBack} />,
  };

  return (
    <SafeAreaView style={styles.root}>
      {screens[screen] || screens.Login}
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  root: { flex: 1, backgroundColor: '#fff' },
});
```

---

## 🗓️ MONTHS 1–5 — Testing & UAT Support Role

### Month 1 — Auth API Testing

#### Automated curl test script: `testing/month1_auth_tests.sh`

```bash
#!/bin/bash
# testing/month1_auth_tests.sh
# Run: bash testing/month1_auth_tests.sh
# Replace BASE_URL with your backend URL

BASE_URL="http://localhost:5000"
PASS=0
FAIL=0

echo "============================================"
echo " CySentinel Month 1 — Auth API Tests"
echo "============================================"

check() {
  local name="$1"
  local expected="$2"
  local actual="$3"
  if echo "$actual" | grep -q "$expected"; then
    echo "  ✅ PASS  $name"
    PASS=$((PASS + 1))
  else
    echo "  ❌ FAIL  $name"
    echo "     Expected to find: $expected"
    echo "     Got: $actual"
    FAIL=$((FAIL + 1))
  fi
}

# 1. Register a new user
echo ""
echo "[ POST /api/auth/register ]"
REGISTER_RESP=$(curl -s -w "\n%{http_code}" -X POST "$BASE_URL/api/auth/register" \
  -H "Content-Type: application/json" \
  -d '{"email":"m4test@cysentinel.test","password":"TestPass123!","full_name":"M4 Tester","organisation_name":"Test SME"}')
REGISTER_CODE=$(echo "$REGISTER_RESP" | tail -1)
REGISTER_BODY=$(echo "$REGISTER_RESP" | head -1)
check "Returns 201 status" "201" "$REGISTER_CODE"
check "Contains user_id or userId" "user" "$REGISTER_BODY"

# 2. Login with correct credentials
echo ""
echo "[ POST /api/auth/login ]"
LOGIN_RESP=$(curl -s -X POST "$BASE_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"m4test@cysentinel.test","password":"TestPass123!"}')
check "Login returns token or requires_2fa" '"token"\|"requires_2fa"' "$LOGIN_RESP"
TOKEN=$(echo "$LOGIN_RESP" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d.get('token',''))" 2>/dev/null)

# 3. Login with wrong password → 401
echo ""
echo "[ POST /api/auth/login — wrong password ]"
BAD_LOGIN=$(curl -s -w "\n%{http_code}" -X POST "$BASE_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"m4test@cysentinel.test","password":"WrongPassword"}')
BAD_CODE=$(echo "$BAD_LOGIN" | tail -1)
check "Wrong password returns 401" "401" "$BAD_CODE"

# 4. Access incidents without JWT → 401
echo ""
echo "[ GET /api/incidents — no token ]"
NO_AUTH=$(curl -s -w "\n%{http_code}" -X GET "$BASE_URL/api/incidents")
NO_AUTH_CODE=$(echo "$NO_AUTH" | tail -1)
check "No token returns 401" "401" "$NO_AUTH_CODE"

# 5. 2FA setup (only if token exists)
if [ -n "$TOKEN" ]; then
  echo ""
  echo "[ POST /api/auth/2fa/setup ]"
  FA_RESP=$(curl -s -X POST "$BASE_URL/api/auth/2fa/setup" \
    -H "Authorization: Bearer $TOKEN")
  check "2FA setup returns qr or secret" '"qr"\|"secret"' "$FA_RESP"
fi

echo ""
echo "============================================"
echo " Results: $PASS passed, $FAIL failed"
echo "============================================"
[ $FAIL -eq 0 ] && exit 0 || exit 1
```

#### Documentation template: `testing/month1_auth_tests.md`

```markdown
# Month 1 — Auth API Test Results
**Date:** ____  **Tester:** Member 4  **Backend URL:** _______________

## POST /api/auth/register
- Status expected: 201  Status received: ___
- Body contains `user_id`: [ ] YES  [ ] NO
- Notes: ___

## POST /api/auth/login (valid)
- Status expected: 200  Status received: ___
- Body contains `token` or `requires_2fa`: [ ] YES  [ ] NO
- Notes: ___

## POST /api/auth/login (wrong password)
- Status expected: 401  Status received: ___
- Error message in response: [ ] YES  [ ] NO
- Notes: ___

## GET /api/incidents (no JWT)
- Status expected: 401  Status received: ___
- Notes: ___

## POST /api/auth/2fa/setup
- Status expected: 200  Status received: ___
- `qr` field present: [ ] YES  [ ] NO
- `secret` field present: [ ] YES  [ ] NO
- Notes: ___

## Bugs found (report to Member 1)
1. ___
2. ___

## Overall: [ ] ALL PASS   [ ] FAILURES — see above
```

### Month 2 — Incidents API Testing

#### `testing/month2_incidents_tests.sh`

```bash
#!/bin/bash
# testing/month2_incidents_tests.sh

BASE_URL="http://localhost:5000"
PASS=0; FAIL=0

echo "============================================"
echo " CySentinel Month 2 — Incidents API Tests"
echo "============================================"

check() {
  local name="$1"; local expected="$2"; local actual="$3"
  if echo "$actual" | grep -q "$expected"; then
    echo "  ✅ PASS  $name"; PASS=$((PASS + 1))
  else
    echo "  ❌ FAIL  $name"
    echo "     Expected: $expected"; echo "     Got: $actual"
    FAIL=$((FAIL + 1))
  fi
}

# Get token
TOKEN=$(curl -s -X POST "$BASE_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"m4test@cysentinel.test","password":"TestPass123!"}' \
  | python3 -c "import sys,json; print(json.load(sys.stdin).get('token',''))" 2>/dev/null)

if [ -z "$TOKEN" ]; then
  echo "❌ Cannot get token — run month1 tests first and ensure test user exists"; exit 1
fi

echo "  Token acquired ✅"

# Create incident
echo ""
echo "[ POST /api/incidents ]"
CREATE_RESP=$(curl -s -w "\n%{http_code}" -X POST "$BASE_URL/api/incidents" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title":"Test phishing via mobile","incident_type":"phishing","severity":"high","description":"Month 2 automated test"}')
CREATE_CODE=$(echo "$CREATE_RESP" | tail -1)
CREATE_BODY=$(echo "$CREATE_RESP" | head -1)
check "Create returns 201"      "201"        "$CREATE_CODE"
check "Response has incident_id" "incident_id" "$CREATE_BODY"
check "Response has risk_score"  "risk_score"  "$CREATE_BODY"

INCIDENT_ID=$(echo "$CREATE_BODY" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d.get('incident_id',''))" 2>/dev/null)

# List incidents
echo ""
echo "[ GET /api/incidents ]"
LIST_RESP=$(curl -s -w "\n%{http_code}" -X GET "$BASE_URL/api/incidents" \
  -H "Authorization: Bearer $TOKEN")
LIST_CODE=$(echo "$LIST_RESP" | tail -1)
LIST_BODY=$(echo "$LIST_RESP" | head -1)
check "List returns 200"        "200"  "$LIST_CODE"
check "Body is array or object" "\[{\|{\"data\"" "$LIST_BODY"

# Filter by severity
echo ""
echo "[ GET /api/incidents?severity=high ]"
FILTER_RESP=$(curl -s -w "\n%{http_code}" -X GET "$BASE_URL/api/incidents?severity=high" \
  -H "Authorization: Bearer $TOKEN")
FILTER_CODE=$(echo "$FILTER_RESP" | tail -1)
check "Filter returns 200" "200" "$FILTER_CODE"

# Filter by status
echo ""
echo "[ GET /api/incidents?status=open ]"
STATUS_RESP=$(curl -s -w "\n%{http_code}" -X GET "$BASE_URL/api/incidents?status=open" \
  -H "Authorization: Bearer $TOKEN")
check "Status filter returns 200" "200" "$(echo "$STATUS_RESP" | tail -1)"

# Update status
if [ -n "$INCIDENT_ID" ]; then
  echo ""
  echo "[ PATCH /api/incidents/:id ]"
  PATCH_RESP=$(curl -s -w "\n%{http_code}" -X PATCH "$BASE_URL/api/incidents/$INCIDENT_ID" \
    -H "Authorization: Bearer $TOKEN" \
    -H "Content-Type: application/json" \
    -d '{"status":"in_progress"}')
  check "PATCH returns 200" "200" "$(echo "$PATCH_RESP" | tail -1)"
  check "Updated status in response" "in_progress" "$(echo "$PATCH_RESP" | head -1)"
fi

# Validate risk_score range
echo ""
echo "[ Validate risk_score is INTEGER 1–10 ]"
RISK=$(echo "$CREATE_BODY" | python3 -c "
import sys, json
d = json.load(sys.stdin)
rs = d.get('risk_score')
print('VALID' if isinstance(rs, (int, float)) and 1 <= rs <= 10 else f'INVALID: {rs}')
" 2>/dev/null)
check "risk_score is integer 1-10" "VALID" "$RISK"

echo ""
echo "============================================"
echo " Results: $PASS passed, $FAIL failed"
echo "============================================"
[ $FAIL -eq 0 ] && exit 0 || exit 1
```

### Month 3 — Checklist API Testing

#### `testing/month3_checklist_tests.sh`

```bash
#!/bin/bash
# testing/month3_checklist_tests.sh

BASE_URL="http://localhost:5000"
PASS=0; FAIL=0

check() {
  local name="$1"; local expected="$2"; local actual="$3"
  if echo "$actual" | grep -q "$expected"; then
    echo "  ✅ PASS  $name"; PASS=$((PASS + 1))
  else
    echo "  ❌ FAIL  $name"
    echo "     Expected: $expected"; echo "     Got: $actual"
    FAIL=$((FAIL + 1))
  fi
}

echo "============================================"
echo " CySentinel Month 3 — Checklist API Tests"
echo "============================================"

TOKEN=$(curl -s -X POST "$BASE_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"m4test@cysentinel.test","password":"TestPass123!"}' \
  | python3 -c "import sys,json; print(json.load(sys.stdin).get('token',''))" 2>/dev/null)

# Get first incident
INCIDENT_ID=$(curl -s -X GET "$BASE_URL/api/incidents?limit=1" \
  -H "Authorization: Bearer $TOKEN" \
  | python3 -c "
import sys, json
body = json.load(sys.stdin)
items = body if isinstance(body, list) else body.get('data', [])
print(items[0]['incident_id'] if items else '')
" 2>/dev/null)

if [ -z "$INCIDENT_ID" ]; then
  echo "❌ No incidents found — seed data first"; exit 1
fi

echo "  Using incident_id: $INCIDENT_ID"

# Fetch checklist
echo ""
echo "[ GET /api/incidents/:id/checklist ]"
CL_RESP=$(curl -s -w "\n%{http_code}" -X GET "$BASE_URL/api/incidents/$INCIDENT_ID/checklist" \
  -H "Authorization: Bearer $TOKEN")
CL_CODE=$(echo "$CL_RESP" | tail -1)
CL_BODY=$(echo "$CL_RESP" | head -1)
check "Checklist returns 200" "200" "$CL_CODE"
check "Response has progress or checklist" "progress\|checklist\|step" "$CL_BODY"

# Get first step_index for toggle test
STEP_INDEX=$(echo "$CL_BODY" | python3 -c "
import sys, json
body = json.load(sys.stdin)
progress = body.get('progress', []) if isinstance(body, dict) else body
if progress: print(progress[0].get('step_index', 0))
else: print(0)
" 2>/dev/null)

# Toggle checklist step
echo ""
echo "[ PATCH /api/incidents/:id/checklist/:step_index ]"
TOGGLE_RESP=$(curl -s -w "\n%{http_code}" \
  -X PATCH "$BASE_URL/api/incidents/$INCIDENT_ID/checklist/$STEP_INDEX" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"completed": true}')
check "Toggle returns 200" "200" "$(echo "$TOGGLE_RESP" | tail -1)"

# Re-fetch and verify persistence
echo ""
echo "[ Verify completed state persists ]"
VERIFY_RESP=$(curl -s -X GET "$BASE_URL/api/incidents/$INCIDENT_ID/checklist" \
  -H "Authorization: Bearer $TOKEN")
check "Persisted completed: true in re-fetch" "true" "$VERIFY_RESP"

echo ""
echo "============================================"
echo " Results: $PASS passed, $FAIL failed"
echo "============================================"
[ $FAIL -eq 0 ] && exit 0 || exit 1
```

### Month 4 — Dashboard API Testing

#### `testing/month4_dashboard_tests.sh`

```bash
#!/bin/bash
# testing/month4_dashboard_tests.sh

BASE_URL="http://localhost:5000"
PASS=0; FAIL=0

check() {
  local name="$1"; local expected="$2"; local actual="$3"
  if echo "$actual" | grep -q "$expected"; then
    echo "  ✅ PASS  $name"; PASS=$((PASS + 1))
  else
    echo "  ❌ FAIL  $name"
    echo "     Expected: $expected"; echo "     Got: $actual"
    FAIL=$((FAIL + 1))
  fi
}

echo "============================================"
echo " CySentinel Month 4 — Dashboard API Tests"
echo "============================================"

TOKEN=$(curl -s -X POST "$BASE_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"m4test@cysentinel.test","password":"TestPass123!"}' \
  | python3 -c "import sys,json; print(json.load(sys.stdin).get('token',''))" 2>/dev/null)

# Risk summary
echo ""
echo "[ GET /api/dashboard/risk-summary ]"
SUMMARY_RESP=$(curl -s -w "\n%{http_code}" -X GET "$BASE_URL/api/dashboard/risk-summary" \
  -H "Authorization: Bearer $TOKEN")
SUMMARY_CODE=$(echo "$SUMMARY_RESP" | tail -1)
SUMMARY_BODY=$(echo "$SUMMARY_RESP" | head -1)
check "Risk summary returns 200"         "200"           "$SUMMARY_CODE"
check "Has total_open or openIncidents"  "open"          "$SUMMARY_BODY"
check "Has avg_risk_score"               "avg_risk\|avgRisk" "$SUMMARY_BODY"
check "Has by_severity or bySeverity"    "severity"      "$SUMMARY_BODY"

# Validate riskLevel value
RISK_LEVEL=$(echo "$SUMMARY_BODY" | python3 -c "
import sys, json
d = json.load(sys.stdin)
rl = d.get('riskLevel') or d.get('risk_level','')
print('VALID' if rl in ['low','medium','high','critical'] else f'INVALID: {rl}')
" 2>/dev/null)
check "riskLevel is valid enum value" "VALID" "$RISK_LEVEL"

# Trends
echo ""
echo "[ GET /api/dashboard/trends ]"
TRENDS_RESP=$(curl -s -w "\n%{http_code}" -X GET "$BASE_URL/api/dashboard/trends" \
  -H "Authorization: Bearer $TOKEN")
TRENDS_CODE=$(echo "$TRENDS_RESP" | tail -1)
TRENDS_BODY=$(echo "$TRENDS_RESP" | head -1)
check "Trends returns 200"             "200"   "$TRENDS_CODE"
check "Trends body has date or count"  "date\|count" "$TRENDS_BODY"

echo ""
echo "============================================"
echo " Results: $PASS passed, $FAIL failed"
echo "============================================"
[ $FAIL -eq 0 ] && exit 0 || exit 1
```

### Month 5 — Reports + UAT

#### `testing/month5_reports_tests.sh`

```bash
#!/bin/bash
# testing/month5_reports_tests.sh

BASE_URL="http://localhost:5000"
PASS=0; FAIL=0

check() {
  if echo "$2" | grep -q "$3"; then echo "  ✅ PASS  $1"; PASS=$((PASS + 1))
  else echo "  ❌ FAIL  $1 (expected: $3)"; FAIL=$((FAIL + 1)); fi
}

echo "============================================"
echo " CySentinel Month 5 — Reports API Tests"
echo "============================================"

TOKEN=$(curl -s -X POST "$BASE_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"m4test@cysentinel.test","password":"TestPass123!"}' \
  | python3 -c "import sys,json; print(json.load(sys.stdin).get('token',''))" 2>/dev/null)

# CSV download
echo ""
echo "[ POST /api/reports/csv ]"
curl -s -X POST "$BASE_URL/api/reports/csv" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"from_date":"2026-01-01","to_date":"2026-12-31"}' \
  --output /tmp/test_incidents.csv
CSV_LINES=$(wc -l < /tmp/test_incidents.csv 2>/dev/null || echo 0)
check "CSV file downloaded and has content" "1" "$([ "$CSV_LINES" -gt 1 ] && echo 1 || echo 0)"
check "CSV has header row"                  "incident_id\|title\|severity" "$(head -1 /tmp/test_incidents.csv 2>/dev/null)"

# PDF download
echo ""
echo "[ POST /api/reports/pdf ]"
curl -s -X POST "$BASE_URL/api/reports/pdf" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"from_date":"2026-01-01","to_date":"2026-12-31"}' \
  --output /tmp/test_report.pdf
PDF_MAGIC=$(head -c 4 /tmp/test_report.pdf 2>/dev/null)
check "PDF file starts with PDF magic bytes" "%PDF" "$PDF_MAGIC"

echo ""
echo "============================================"
echo " Results: $PASS passed, $FAIL failed"
echo "============================================"
[ $FAIL -eq 0 ] && exit 0 || exit 1
```

#### UAT Script: `testing/UAT_script.md`

```markdown
# CySentinel — User Acceptance Test Script
**Version:** 1.0  **Date:** ____  **Tester:** ____  **Role:** Sponsor/End User

> Instructions: Follow each step exactly. Mark ✅ if it works as expected, ❌ if not.
> Record any error messages or unexpected behaviour in the Notes column.

---

## Section 1 — Register & Login

| # | Step | Expected Result | Pass/Fail | Notes |
|---|------|----------------|-----------|-------|
| 1.1 | Open the web app in a browser | Login page with CySentinel logo appears | | |
| 1.2 | Click "Register" link | Registration form with 4 fields appears | | |
| 1.3 | Fill in name, org name, email, password (min 8 chars) and click "Create Account" | Redirect to login page; no error | | |
| 1.4 | Enter your email and password on the login page and click "Sign In" | Either redirect to dashboard OR 2FA screen | | |
| 1.5 | If 2FA screen appears: scan QR code with Google Authenticator; enter 6-digit code | Redirect to Risk Dashboard | | |
| 1.6 | Verify your name/email appears in the top navigation bar | Your email is visible in the NavBar | | |
| 1.7 | Click "Logout" in the top navigation | Redirected back to login page | | |

---

## Section 2 — Log a Phishing Incident

| # | Step | Expected Result | Pass/Fail | Notes |
|---|------|----------------|-----------|-------|
| 2.1 | Log in and click "Incidents" in the navigation | Incidents list page with "+ New Incident" button appears | | |
| 2.2 | Click "+ New Incident" | Modal form opens with Title, Type, Severity, Description fields | | |
| 2.3 | Leave all fields blank and click "Log Incident" | Error message: "Title, type, and severity are required" | | |
| 2.4 | Fill Title: "Phishing test", Type: "phishing", Severity: "high" and click "Log Incident" | Incident created; modal closes; incident appears in list | | |
| 2.5 | Verify the incident appears in the list with a Risk Score between 1 and 10 | Risk Score column shows a number (e.g. "7/10") | | |
| 2.6 | Click on the incident row | Incident detail page opens showing all fields | | |
| 2.7 | On the detail page, change Status to "In Progress" | Status updates; page reflects new status | | |

---

## Section 3 — Complete a Response Checklist

| # | Step | Expected Result | Pass/Fail | Notes |
|---|------|----------------|-----------|-------|
| 3.1 | Open an existing incident detail page | Incident details are visible | | |
| 3.2 | Scroll down to "Response Checklist" section | Checklist panel with steps is visible | | |
| 3.3 | Observe progress bar at top of checklist | Shows "0/N steps complete (0%)" | | |
| 3.4 | Click on the first checklist step | Step shows ✅ and text is struck through | | |
| 3.5 | Refresh the page | The completed step remains ✅ (state persisted) | | |
| 3.6 | Complete all remaining steps | Progress bar fills to 100% | | |

---

## Section 4 — View Risk Dashboard

| # | Step | Expected Result | Pass/Fail | Notes |
|---|------|----------------|-----------|-------|
| 4.1 | Click "Dashboard" in the navigation | Risk Dashboard page loads | | |
| 4.2 | Observe the Risk Summary card | Shows risk level badge, open incident count, avg risk score | | |
| 4.3 | Observe the bar chart | Shows incidents grouped by type (phishing, malware, etc.) | | |
| 4.4 | Observe the line chart | Shows 30-day trend with dates on X-axis | | |
| 4.5 | Observe the pie chart | Shows incidents by severity with colour coding | | |
| 4.6 | Resize browser window to a smaller size | Charts remain readable and layout stacks vertically | | |

---

## Section 5 — Download Reports

| # | Step | Expected Result | Pass/Fail | Notes |
|---|------|----------------|-----------|-------|
| 5.1 | Click "Reports" in the navigation | Reports page with date pickers appears | | |
| 5.2 | Click "Export CSV" without selecting dates | Error: "Please select both start and end dates" | | |
| 5.3 | Select a start date of 2026-01-01 and end date of today | Date fields show selected dates | | |
| 5.4 | Click "Export CSV" | CSV file downloads to your computer | | |
| 5.5 | Open the CSV in Excel or a text editor | File contains incident data with headers | | |
| 5.6 | Click "Export PDF" | PDF file downloads | | |
| 5.7 | Open the PDF | PDF contains a formatted incident report | | |

---

## UAT Sign-Off

**Tested by:** ____________________  **Date:** ________________

**Overall result:**  [ ] ALL PASS — ready for go-live  [ ] FAILURES — see notes above

**Sponsor sign-off:** ____________________
```

---

## 🗓️ MONTHS 6–7 — Mobile App (MVP 6)

### Service Layer

#### src/services/api.js

```javascript
// src/services/api.js
import axios from 'axios';
import * as SecureStore from 'expo-secure-store';

// Replace 192.168.x.x with your laptop's local IP when dev testing
// Find it with: ipconfig getifaddr en0 (macOS) or hostname -I (Linux)
const BASE_URL = __DEV__
  ? 'http://192.168.x.x:5000'
  : 'https://cysentinel-backend-production.up.railway.app';

const api = axios.create({ baseURL: BASE_URL });

// Attach JWT token to every request
api.interceptors.request.use(async (config) => {
  const token = await SecureStore.getItemAsync('cysentinel_token');
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// Handle 401 globally — clear token so useAuth re-renders to login
api.interceptors.response.use(
  response => response,
  async (error) => {
    if (error.response?.status === 401) {
      await SecureStore.deleteItemAsync('cysentinel_token').catch(() => {});
    }
    return Promise.reject(error);
  }
);

export default api;
```

#### [NO LIBS] src/services/api.no-libs.js — plain fetch

```javascript
// src/services/api.no-libs.js
// No axios — plain fetch wrapper with same interface
import * as SecureStore from 'expo-secure-store';

const BASE_URL = __DEV__
  ? 'http://192.168.x.x:5000'
  : 'https://cysentinel-backend-production.up.railway.app';

async function request(method, path, body = null) {
  const token = await SecureStore.getItemAsync('cysentinel_token').catch(() => null);
  const headers = { 'Content-Type': 'application/json' };
  if (token) headers['Authorization'] = `Bearer ${token}`;

  const config = { method, headers };
  if (body) config.body = JSON.stringify(body);

  const response = await fetch(`${BASE_URL}${path}`, config);

  if (response.status === 401) {
    await SecureStore.deleteItemAsync('cysentinel_token').catch(() => {});
  }

  if (!response.ok) {
    const err = await response.json().catch(() => ({ error: response.statusText }));
    throw Object.assign(new Error(err.error || 'Request failed'), { response: { data: err, status: response.status } });
  }

  return { data: await response.json() };
}

const api = {
  get:   (path)       => request('GET',   path),
  post:  (path, body) => request('POST',  path, body),
  patch: (path, body) => request('PATCH', path, body),
};

export default api;
```

#### src/utils/secureStorage.js

```javascript
// src/utils/secureStorage.js
import * as SecureStore from 'expo-secure-store';

// Stores JWT in hardware-backed keychain (safer than AsyncStorage)
export const saveToken   = async (token)  => SecureStore.setItemAsync('cysentinel_token', token);
export const getToken    = async ()       => SecureStore.getItemAsync('cysentinel_token');
export const deleteToken = async ()       => SecureStore.deleteItemAsync('cysentinel_token');
```

#### src/services/authService.js

```javascript
// src/services/authService.js
import api from './api';
import { saveToken, deleteToken } from '../utils/secureStorage';

export const login = async (email, password) => {
  const { data } = await api.post('/api/auth/login', { email, password });
  if (data.token) await saveToken(data.token);
  return data;   // may include requires_2fa: true
};

export const logout = async () => {
  await deleteToken();
};

export const getProfile = async () => {
  const { data } = await api.get('/api/auth/profile');
  return data;   // { user_id, email, role, organisation_id }
};
```

#### src/hooks/useAuth.js

```javascript
// src/hooks/useAuth.js
import { useState, useEffect } from 'react';
import { getToken, deleteToken } from '../utils/secureStorage';

export function useAuth() {
  const [token,   setToken]   = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    getToken()
      .then(t => setToken(t))
      .catch(() => setToken(null))
      .finally(() => setLoading(false));
  }, []);

  const logout = async () => {
    await deleteToken().catch(() => {});
    setToken(null);
  };

  return { token, isAuthenticated: !!token, loading, logout };
}
```

#### src/utils/offlineCache.js

```javascript
// src/utils/offlineCache.js
// Uses AsyncStorage for offline incident queue
// Install: npx expo install @react-native-async-storage/async-storage
import AsyncStorage from '@react-native-async-storage/async-storage';

const QUEUE_KEY = 'cysentinel:offline_queue';

export async function queueIncident(incident) {
  const existing = await getQueue();
  const entry = {
    ...incident,
    // Local-only ID — backend assigns real ID on sync
    id:        `offline-${Date.now()}-${Math.random().toString(36).slice(2, 7)}`,
    createdAt: new Date().toISOString(),
  };
  await AsyncStorage.setItem(QUEUE_KEY, JSON.stringify([...existing, entry]));
  return entry;
}

export async function getQueue() {
  const raw = await AsyncStorage.getItem(QUEUE_KEY);
  return raw ? JSON.parse(raw) : [];
}

export async function flushQueue(submitFn) {
  const queue    = await getQueue();
  let sent       = 0;
  let failed     = 0;
  const remaining = [];

  for (const item of queue) {
    try {
      await submitFn(item);
      sent++;
    } catch {
      remaining.push(item);
      failed++;
    }
  }

  await AsyncStorage.setItem(QUEUE_KEY, JSON.stringify(remaining));
  return { sent, failed };
}

export async function getQueueCount() {
  const q = await getQueue();
  return q.length;
}
```

#### src/services/notificationService.js

```javascript
// src/services/notificationService.js
import * as Notifications from 'expo-notifications';
import { Platform }        from 'react-native';
import api                 from './api';

// How to handle notifications when the app is in the foreground
Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowAlert: true,
    shouldPlaySound: true,
    shouldSetBadge:  true,
  }),
});

export async function registerForPushNotifications() {
  // Check existing permission
  const { status: existing } = await Notifications.getPermissionsAsync();
  let finalStatus = existing;

  if (existing !== 'granted') {
    const { status } = await Notifications.requestPermissionsAsync();
    finalStatus = status;
  }

  if (finalStatus !== 'granted') {
    console.warn('[CySentinel] Push notification permission not granted');
    return null;
  }

  // Get Expo push token
  const { data: expoPushToken } = await Notifications.getExpoPushTokenAsync();

  // Set up Android notification channel
  if (Platform.OS === 'android') {
    await Notifications.setNotificationChannelAsync('incidents', {
      name:             'Incident Alerts',
      importance:       Notifications.AndroidImportance.HIGH,
      vibrationPattern: [0, 250, 250, 250],
      lightColor:       '#dc2626',
    });
  }

  // Register token with backend
  // Member 1 may use either endpoint name — try both if needed
  try {
    await api.post('/api/notifications/register', {
      token:    expoPushToken,
      platform: Platform.OS,
    });
  } catch {
    // Fallback endpoint name
    await api.post('/api/users/push-token', {
      token:    expoPushToken,
      platform: Platform.OS,
    });
  }

  return expoPushToken;
}
```

---

### Screens

#### src/screens/LoginScreen.jsx — Full

```jsx
// src/screens/LoginScreen.jsx
import { useState } from 'react';
import { View, Text, TextInput, TouchableOpacity, StyleSheet, Alert, KeyboardAvoidingView, Platform } from 'react-native';
import { saveToken }   from '../utils/secureStorage';
import api             from '../services/api';
import { registerForPushNotifications } from '../services/notificationService';

export default function LoginScreen({ navigation }) {
  const [email,    setEmail]    = useState('');
  const [password, setPassword] = useState('');
  const [loading,  setLoading]  = useState(false);

  const handleLogin = async () => {
    if (!email.trim() || !password) {
      Alert.alert('Required', 'Please enter your email and password.');
      return;
    }
    setLoading(true);
    try {
      const { data } = await api.post('/api/auth/login', { email: email.trim(), password });

      if (data.requires_2fa) {
        // 2FA not supported in mobile app — direct user to web
        Alert.alert(
          '2FA Required',
          'This account uses two-factor authentication. Please sign in via the web dashboard at least once, then return here.',
          [{ text: 'OK' }]
        );
        return;
      }

      if (data.token) {
        await saveToken(data.token);
        // Register for push notifications after successful login
        // (non-blocking — failure does not prevent navigation)
        registerForPushNotifications().catch(console.warn);
        navigation.replace('MyItems');
      }
    } catch (err) {
      const msg = err.response?.data?.error || 'Invalid email or password.';
      Alert.alert('Login Failed', msg);
    } finally {
      setLoading(false);
    }
  };

  return (
    <KeyboardAvoidingView
      style={styles.outer}
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
    >
      <View style={styles.container}>
        <Text style={styles.logo}>🛡️</Text>
        <Text style={styles.title}>CySentinel</Text>
        <Text style={styles.subtitle}>Incident Management</Text>

        <TextInput
          style={styles.input}
          placeholder="Work email"
          value={email}
          onChangeText={setEmail}
          keyboardType="email-address"
          autoCapitalize="none"
          autoCorrect={false}
          accessibilityLabel="Email address"
          placeholderTextColor="#9ca3af"
        />
        <TextInput
          style={styles.input}
          placeholder="Password"
          value={password}
          onChangeText={setPassword}
          secureTextEntry
          accessibilityLabel="Password"
          placeholderTextColor="#9ca3af"
        />

        <TouchableOpacity
          style={[styles.button, loading && styles.buttonDisabled]}
          onPress={handleLogin}
          disabled={loading}
          accessibilityLabel="Sign In"
          accessibilityRole="button"
        >
          <Text style={styles.buttonText}>{loading ? 'Signing in...' : 'Sign In'}</Text>
        </TouchableOpacity>
      </View>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  outer:          { flex: 1, backgroundColor: '#f9fafb' },
  container:      { flex: 1, justifyContent: 'center', padding: 28, backgroundColor: '#f9fafb' },
  logo:           { fontSize: 52, textAlign: 'center', marginBottom: 8 },
  title:          { fontSize: 30, fontWeight: '800', textAlign: 'center', color: '#1e3a5f', marginBottom: 4 },
  subtitle:       { fontSize: 15, textAlign: 'center', color: '#6b7280', marginBottom: 36 },
  input:          { borderWidth: 1, borderColor: '#d1d5db', borderRadius: 10, padding: 15, marginBottom: 14, fontSize: 16, backgroundColor: '#fff', color: '#111827' },
  button:         { backgroundColor: '#1e3a5f', padding: 16, borderRadius: 10, alignItems: 'center', marginTop: 6 },
  buttonDisabled: { opacity: 0.6 },
  buttonText:     { color: '#fff', fontWeight: '700', fontSize: 17 },
});
```

#### src/screens/QuickReportScreen.jsx — Month 6 (online only)

```jsx
// src/screens/QuickReportScreen.jsx  (Month 6 — no offline support yet)
import { useState } from 'react';
import { View, Text, TouchableOpacity, TextInput, StyleSheet, ScrollView, Alert } from 'react-native';
import api from '../services/api';

const INCIDENT_TYPES = ['phishing','malware','unauthorised_access','data_breach','other'];
const SEVERITIES     = ['low','medium','high','critical'];

export default function QuickReportScreen({ navigation }) {
  const [type,        setType]        = useState('');
  const [severity,    setSeverity]    = useState('');
  const [description, setDescription] = useState('');
  const [submitting,  setSubmitting]  = useState(false);

  const submit = async () => {
    if (!type || !severity) {
      Alert.alert('Required', 'Please select both incident type and severity.');
      return;
    }
    setSubmitting(true);
    try {
      await api.post('/api/incidents', {
        title:         `${type.replace(/_/g, ' ')} incident — mobile report`,
        incident_type: type,
        severity,
        description:   description.trim() || 'Incident reported via CySentinel mobile app',
      });
      Alert.alert('✅ Reported', 'Incident submitted successfully.', [
        { text: 'OK', onPress: () => navigation.navigate('MyItems') }
      ]);
    } catch {
      Alert.alert('Error', 'Could not submit. Please try again or check your connection.');
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <ScrollView style={styles.container} contentContainerStyle={styles.content}>
      <Text style={styles.label}>Incident Type *</Text>
      <View style={styles.optionRow}>
        {INCIDENT_TYPES.map(t => (
          <TouchableOpacity
            key={t}
            style={[styles.chip, type === t && styles.chipSelected]}
            onPress={() => setType(t)}
            accessibilityLabel={`Incident type: ${t}`}
            accessibilityState={{ selected: type === t }}
          >
            <Text style={[styles.chipText, type === t && styles.chipTextSelected]}>
              {t.replace(/_/g, ' ')}
            </Text>
          </TouchableOpacity>
        ))}
      </View>

      <Text style={styles.label}>Severity *</Text>
      <View style={styles.optionRow}>
        {SEVERITIES.map(s => (
          <TouchableOpacity
            key={s}
            style={[styles.chip, severity === s && styles.chipSelected, styles[`sev_${s}`]]}
            onPress={() => setSeverity(s)}
            accessibilityLabel={`Severity: ${s}`}
            accessibilityState={{ selected: severity === s }}
          >
            <Text style={[styles.chipText, severity === s && styles.chipTextSelected]}>{s}</Text>
          </TouchableOpacity>
        ))}
      </View>

      <Text style={styles.label}>Description (optional)</Text>
      <TextInput
        style={styles.textarea}
        placeholder="What happened? Include relevant details..."
        value={description}
        onChangeText={setDescription}
        multiline
        numberOfLines={4}
        accessibilityLabel="Incident description"
        placeholderTextColor="#9ca3af"
      />

      <TouchableOpacity
        style={[styles.submitBtn, submitting && styles.submitDisabled]}
        onPress={submit}
        disabled={submitting}
        accessibilityLabel="Submit incident report"
        accessibilityRole="button"
      >
        <Text style={styles.submitText}>{submitting ? 'Submitting...' : '🚨 Submit Report'}</Text>
      </TouchableOpacity>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container:        { flex: 1, backgroundColor: '#fff' },
  content:          { padding: 20, paddingBottom: 40 },
  label:            { fontSize: 15, fontWeight: '700', color: '#374151', marginTop: 20, marginBottom: 10 },
  optionRow:        { flexDirection: 'row', flexWrap: 'wrap', gap: 8 },
  chip:             { borderWidth: 1.5, borderColor: '#d1d5db', borderRadius: 20, paddingHorizontal: 14, paddingVertical: 9 },
  chipSelected:     { backgroundColor: '#1e3a5f', borderColor: '#1e3a5f' },
  chipText:         { color: '#374151', fontSize: 13, fontWeight: '500' },
  chipTextSelected: { color: '#fff', fontWeight: '700' },
  textarea:         { borderWidth: 1, borderColor: '#d1d5db', borderRadius: 10, padding: 14, height: 110, textAlignVertical: 'top', fontSize: 15, color: '#111827', marginTop: 2 },
  submitBtn:        { backgroundColor: '#dc2626', padding: 18, borderRadius: 10, alignItems: 'center', marginTop: 28 },
  submitDisabled:   { opacity: 0.6 },
  submitText:       { color: '#fff', fontWeight: '800', fontSize: 17 },
});
```

#### src/screens/QuickReportScreen.jsx — Month 7 update (offline-first)

```jsx
// src/screens/QuickReportScreen.jsx  (Month 7 — offline-first version)
import { useState } from 'react';
import { View, Text, TouchableOpacity, TextInput, StyleSheet, ScrollView, Alert } from 'react-native';
import NetInfo from '@react-native-community/netinfo';
import api    from '../services/api';
import { queueIncident, flushQueue, getQueueCount } from '../utils/offlineCache';

const INCIDENT_TYPES = ['phishing','malware','unauthorised_access','data_breach','other'];
const SEVERITIES     = ['low','medium','high','critical'];

// Install: npx expo install @react-native-community/netinfo

export default function QuickReportScreen({ navigation }) {
  const [type,        setType]       = useState('');
  const [severity,    setSeverity]   = useState('');
  const [description, setDesc]       = useState('');
  const [submitting,  setSubmitting] = useState(false);

  const submit = async () => {
    if (!type || !severity) {
      Alert.alert('Required', 'Please select incident type and severity.');
      return;
    }
    setSubmitting(true);

    const incidentData = {
      title:         `${type.replace(/_/g, ' ')} incident — mobile report`,
      incident_type: type,
      severity,
      description:   description.trim() || 'Incident reported via CySentinel mobile app',
    };

    try {
      const net = await NetInfo.fetch();

      if (!net.isConnected) {
        // Offline path — queue for later
        await queueIncident(incidentData);
        const count = await getQueueCount();
        Alert.alert(
          '📥 Saved Offline',
          `No connection. This report is saved and will sync automatically when you're back online. (${count} item${count !== 1 ? 's' : ''} queued)`,
          [{ text: 'OK', onPress: () => navigation.navigate('MyItems') }]
        );
        return;
      }

      // Online path — try to flush any queued items first, then submit
      const { sent, failed } = await flushQueue(async (item) => {
        await api.post('/api/incidents', {
          title:         item.title,
          incident_type: item.incident_type,
          severity:      item.severity,
          description:   item.description,
        });
      });

      await api.post('/api/incidents', incidentData);

      const msg = sent > 0
        ? `Report submitted. Also synced ${sent} previously queued item${sent !== 1 ? 's' : ''}.`
        : 'Incident submitted successfully.';

      Alert.alert('✅ Reported', msg, [
        { text: 'OK', onPress: () => navigation.navigate('MyItems') }
      ]);
    } catch {
      // Online but submission failed — queue it
      await queueIncident(incidentData);
      Alert.alert(
        '⚠️ Saved for Retry',
        'Could not reach the server. Report saved and will sync automatically.',
        [{ text: 'OK', onPress: () => navigation.navigate('MyItems') }]
      );
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <ScrollView style={styles.container} contentContainerStyle={styles.content}>
      <Text style={styles.label}>Incident Type *</Text>
      <View style={styles.optionRow}>
        {INCIDENT_TYPES.map(t => (
          <TouchableOpacity key={t}
            style={[styles.chip, type === t && styles.chipSelected]}
            onPress={() => setType(t)} accessibilityLabel={`Incident type: ${t}`}>
            <Text style={[styles.chipText, type === t && styles.chipTextSelected]}>
              {t.replace(/_/g, ' ')}
            </Text>
          </TouchableOpacity>
        ))}
      </View>

      <Text style={styles.label}>Severity *</Text>
      <View style={styles.optionRow}>
        {SEVERITIES.map(s => (
          <TouchableOpacity key={s}
            style={[styles.chip, severity === s && styles.chipSelected]}
            onPress={() => setSeverity(s)} accessibilityLabel={`Severity: ${s}`}>
            <Text style={[styles.chipText, severity === s && styles.chipTextSelected]}>{s}</Text>
          </TouchableOpacity>
        ))}
      </View>

      <Text style={styles.label}>Description (optional)</Text>
      <TextInput
        style={styles.textarea}
        placeholder="What happened?"
        value={description}
        onChangeText={setDesc}
        multiline
        numberOfLines={4}
        placeholderTextColor="#9ca3af"
      />

      <TouchableOpacity
        style={[styles.submitBtn, submitting && styles.submitDisabled]}
        onPress={submit}
        disabled={submitting}
        accessibilityLabel="Submit incident report">
        <Text style={styles.submitText}>{submitting ? 'Submitting...' : '🚨 Submit Report'}</Text>
      </TouchableOpacity>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container:        { flex: 1, backgroundColor: '#fff' },
  content:          { padding: 20, paddingBottom: 40 },
  label:            { fontSize: 15, fontWeight: '700', color: '#374151', marginTop: 20, marginBottom: 10 },
  optionRow:        { flexDirection: 'row', flexWrap: 'wrap', gap: 8 },
  chip:             { borderWidth: 1.5, borderColor: '#d1d5db', borderRadius: 20, paddingHorizontal: 14, paddingVertical: 9 },
  chipSelected:     { backgroundColor: '#1e3a5f', borderColor: '#1e3a5f' },
  chipText:         { color: '#374151', fontSize: 13, fontWeight: '500' },
  chipTextSelected: { color: '#fff', fontWeight: '700' },
  textarea:         { borderWidth: 1, borderColor: '#d1d5db', borderRadius: 10, padding: 14, height: 100, textAlignVertical: 'top', fontSize: 15 },
  submitBtn:        { backgroundColor: '#dc2626', padding: 18, borderRadius: 10, alignItems: 'center', marginTop: 28 },
  submitDisabled:   { opacity: 0.6 },
  submitText:       { color: '#fff', fontWeight: '800', fontSize: 17 },
});
```

#### src/screens/MyItemsScreen.jsx — Full

```jsx
// src/screens/MyItemsScreen.jsx
import { useState, useEffect, useCallback } from 'react';
import { View, Text, FlatList, TouchableOpacity, StyleSheet, RefreshControl, Alert } from 'react-native';
import api from '../services/api';
import { getQueueCount } from '../utils/offlineCache';

const SEV_COLORS = { critical: '#991b1b', high: '#c2410c', medium: '#b45309', low: '#15803d' };

export default function MyItemsScreen({ navigation }) {
  const [incidents,   setIncidents]  = useState([]);
  const [refreshing,  setRefreshing] = useState(false);
  const [queuedCount, setQueued]     = useState(0);

  const fetchIncidents = useCallback(async () => {
    try {
      const { data } = await api.get('/api/incidents?status=open');
      // API may return flat array or { data: [] }
      setIncidents(Array.isArray(data) ? data : data.data ?? []);
    } catch (err) {
      // Show error only if we had data before (not on first load)
      console.error('Failed to fetch incidents:', err.message);
    }
  }, []);

  const refreshQueueCount = useCallback(async () => {
    const count = await getQueueCount().catch(() => 0);
    setQueued(count);
  }, []);

  useEffect(() => {
    fetchIncidents();
    refreshQueueCount();
  }, [fetchIncidents, refreshQueueCount]);

  const onRefresh = useCallback(async () => {
    setRefreshing(true);
    await Promise.all([fetchIncidents(), refreshQueueCount()]);
    setRefreshing(false);
  }, [fetchIncidents, refreshQueueCount]);

  const fmt = (dateStr) => dateStr
    ? new Date(dateStr).toLocaleDateString('en-ZA', { day: 'numeric', month: 'short', year: 'numeric' })
    : '—';

  return (
    <View style={styles.container}>
      {queuedCount > 0 && (
        <View style={styles.offlineBanner} accessibilityRole="alert">
          <Text style={styles.offlineBannerText}>
            📥 {queuedCount} incident{queuedCount !== 1 ? 's' : ''} queued offline — will sync when online
          </Text>
        </View>
      )}

      <FlatList
        data={incidents}
        keyExtractor={item => item.incident_id}
        refreshControl={<RefreshControl refreshing={refreshing} onRefresh={onRefresh} tintColor="#1e3a5f" />}
        ListHeaderComponent={
          <TouchableOpacity
            style={styles.reportBtn}
            onPress={() => navigation.navigate('QuickReport')}
            accessibilityLabel="Report new incident"
            accessibilityRole="button"
          >
            <Text style={styles.reportBtnText}>+ Report New Incident</Text>
          </TouchableOpacity>
        }
        renderItem={({ item }) => (
          <View style={styles.card}>
            <View style={styles.cardHeader}>
              <Text style={styles.cardTitle} numberOfLines={2}>{item.title}</Text>
              <Text style={[styles.sevBadge, { color: SEV_COLORS[item.severity] || '#374151' }]}>
                {item.severity?.toUpperCase()}
              </Text>
            </View>
            <Text style={styles.cardMeta}>
              {item.incident_type?.replace(/_/g, ' ')} · {fmt(item.created_at)}
            </Text>
            <Text style={styles.riskScore}>
              Risk Score: <Text style={{ fontWeight: '700' }}>{item.risk_score ?? '—'}</Text>/10
            </Text>
          </View>
        )}
        ListEmptyComponent={
          !refreshing
            ? <Text style={styles.empty}>🎉 No open incidents right now</Text>
            : null
        }
        ListFooterComponent={
          <TouchableOpacity
            style={styles.riskBtn}
            onPress={() => navigation.navigate('RiskSummary')}
            accessibilityLabel="View risk summary"
            accessibilityRole="button"
          >
            <Text style={styles.riskBtnText}>📊 View Risk Summary</Text>
          </TouchableOpacity>
        }
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container:         { flex: 1, backgroundColor: '#f9fafb' },
  offlineBanner:     { backgroundColor: '#fef9c3', padding: 12, borderBottomWidth: 1, borderBottomColor: '#fde68a' },
  offlineBannerText: { color: '#92400e', fontSize: 13, textAlign: 'center', fontWeight: '600' },
  reportBtn:         { backgroundColor: '#1e3a5f', margin: 16, padding: 15, borderRadius: 10, alignItems: 'center' },
  reportBtnText:     { color: '#fff', fontWeight: '700', fontSize: 15 },
  card:              { backgroundColor: '#fff', borderRadius: 10, padding: 16, marginHorizontal: 16, marginBottom: 10, shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 6, elevation: 2 },
  cardHeader:        { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'flex-start' },
  cardTitle:         { fontSize: 14, fontWeight: '700', color: '#111827', flex: 1, marginRight: 8 },
  sevBadge:          { fontSize: 11, fontWeight: '800' },
  cardMeta:          { fontSize: 12, color: '#6b7280', marginTop: 6 },
  riskScore:         { fontSize: 12, color: '#9ca3af', marginTop: 3 },
  empty:             { textAlign: 'center', color: '#9ca3af', padding: 48, fontSize: 16 },
  riskBtn:           { margin: 16, backgroundColor: '#fff', borderWidth: 1, borderColor: '#e5e7eb', padding: 14, borderRadius: 10, alignItems: 'center' },
  riskBtnText:       { color: '#374151', fontWeight: '600', fontSize: 14 },
});
```

#### src/screens/RiskSummaryScreen.jsx — Full

```jsx
// src/screens/RiskSummaryScreen.jsx
import { useState, useEffect } from 'react';
import { View, Text, StyleSheet, ActivityIndicator, TouchableOpacity, ScrollView } from 'react-native';
import api from '../services/api';

const RISK_COLORS = { critical: '#7c3aed', high: '#dc2626', medium: '#d97706', low: '#16a34a' };

export default function RiskSummaryScreen({ navigation }) {
  const [summary, setSummary] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error,   setError]   = useState(null);

  useEffect(() => {
    api.get('/api/dashboard/risk-summary')
      .then(({ data }) => setSummary(data))
      .catch(() => setError('Failed to load risk summary. Check your connection.'))
      .finally(() => setLoading(false));
  }, []);

  if (loading) {
    return (
      <View style={styles.center}>
        <ActivityIndicator size="large" color="#1e3a5f" accessibilityLabel="Loading risk summary" />
      </View>
    );
  }

  if (error) {
    return (
      <View style={styles.center}>
        <Text style={styles.errorText}>{error}</Text>
        <TouchableOpacity style={styles.backBtn} onPress={() => navigation.goBack()}>
          <Text style={styles.backBtnText}>← Go Back</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // Normalise field names — backend may use camelCase or snake_case
  const riskLevel   = summary?.riskLevel   ?? summary?.risk_level   ?? 'unknown';
  const openCount   = summary?.openIncidents ?? summary?.total_open  ?? '—';
  const avgRisk     = summary?.avgRiskScore  ?? summary?.avg_risk_score ?? '—';
  const bySeverity  = summary?.bySeverity   ?? summary?.by_severity  ?? [];
  const riskColor   = RISK_COLORS[riskLevel] || '#6b7280';

  return (
    <ScrollView contentContainerStyle={styles.container}>
      <Text style={styles.heading}>Organisation Risk Overview</Text>

      <View style={[styles.riskCard, { borderColor: riskColor }]}>
        <Text style={styles.riskLabel}>Current Risk Level</Text>
        <Text style={[styles.riskValue, { color: riskColor }]}>
          {riskLevel.toUpperCase()}
        </Text>
      </View>

      <View style={styles.statRow}>
        <View style={styles.stat}>
          <Text style={styles.statValue}>{openCount}</Text>
          <Text style={styles.statLabel}>Open Incidents</Text>
        </View>
        <View style={styles.stat}>
          <Text style={styles.statValue}>
            {typeof avgRisk === 'number' ? avgRisk.toFixed(1) : avgRisk}
          </Text>
          <Text style={styles.statLabel}>Avg Risk Score</Text>
        </View>
      </View>

      {bySeverity.length > 0 && (
        <View style={styles.sevSection}>
          <Text style={styles.sevHeading}>By Severity</Text>
          {bySeverity.map((item) => (
            <View key={item.severity} style={styles.sevRow}>
              <Text style={[styles.sevLabel, { color: RISK_COLORS[item.severity] || '#374151' }]}>
                {item.severity?.toUpperCase()}
              </Text>
              <Text style={styles.sevCount}>{item.count}</Text>
            </View>
          ))}
        </View>
      )}

      <Text style={styles.readOnly}>
        Read-only view. Use the web dashboard for full incident management.
      </Text>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container:   { padding: 24, paddingBottom: 40 },
  center:      { flex: 1, justifyContent: 'center', alignItems: 'center', padding: 24 },
  heading:     { fontSize: 20, fontWeight: '800', color: '#111827', marginBottom: 20 },
  riskCard:    { borderWidth: 2, borderRadius: 12, padding: 24, alignItems: 'center', marginBottom: 20 },
  riskLabel:   { fontSize: 13, color: '#6b7280', marginBottom: 4 },
  riskValue:   { fontSize: 40, fontWeight: '800', letterSpacing: 1 },
  statRow:     { flexDirection: 'row', gap: 12, marginBottom: 20 },
  stat:        { flex: 1, backgroundColor: '#f3f4f6', borderRadius: 10, padding: 16, alignItems: 'center' },
  statValue:   { fontSize: 30, fontWeight: '800', color: '#111827' },
  statLabel:   { fontSize: 12, color: '#6b7280', marginTop: 4 },
  sevSection:  { backgroundColor: '#fff', borderRadius: 10, borderWidth: 1, borderColor: '#e5e7eb', padding: 16, marginBottom: 20 },
  sevHeading:  { fontSize: 14, fontWeight: '700', color: '#374151', marginBottom: 10 },
  sevRow:      { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', paddingVertical: 6, borderBottomWidth: 1, borderBottomColor: '#f3f4f6' },
  sevLabel:    { fontWeight: '700', fontSize: 13 },
  sevCount:    { fontSize: 18, fontWeight: '800', color: '#111827' },
  readOnly:    { textAlign: 'center', color: '#9ca3af', fontSize: 12, marginTop: 8, lineHeight: 18 },
  errorText:   { color: '#dc2626', fontSize: 15, textAlign: 'center', marginBottom: 20 },
  backBtn:     { backgroundColor: '#1e3a5f', padding: 14, borderRadius: 10, paddingHorizontal: 32 },
  backBtnText: { color: '#fff', fontWeight: '700', fontSize: 15 },
});
```

### EAS Build Configuration

#### eas.json

```json
{
  "cli": { "version": ">= 5.9.1" },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "android": { "buildType": "apk" },
      "distribution": "internal"
    },
    "production": {}
  },
  "submit": {
    "production": {}
  }
}
```

```bash
# Build Android APK for testing (no Play Store needed)
eas build --platform android --profile preview

# Build for iOS (requires Apple Developer account)
eas build --platform ios --profile preview

# Download APK and install:
# 1. Open the EAS build URL (printed after build completes)
# 2. Download .apk file on Android device
# 3. Enable "Install unknown apps" in Settings → Security
# 4. Open the .apk to install
```

---

## 🧪 Tests

### [WITH LIBS] src/utils/__tests__/secureStorage.test.js

```javascript
// src/utils/__tests__/secureStorage.test.js
import * as SecureStore from 'expo-secure-store';
import { saveToken, getToken, deleteToken } from '../secureStorage';

jest.mock('expo-secure-store');

describe('secureStorage', () => {
  beforeEach(() => jest.clearAllMocks());

  it('saveToken calls setItemAsync with correct key', async () => {
    await saveToken('jwt-abc');
    expect(SecureStore.setItemAsync).toHaveBeenCalledWith('cysentinel_token', 'jwt-abc');
  });

  it('getToken calls getItemAsync with correct key', async () => {
    SecureStore.getItemAsync.mockResolvedValue('jwt-abc');
    const result = await getToken();
    expect(SecureStore.getItemAsync).toHaveBeenCalledWith('cysentinel_token');
    expect(result).toBe('jwt-abc');
  });

  it('getToken returns null when no token stored', async () => {
    SecureStore.getItemAsync.mockResolvedValue(null);
    const result = await getToken();
    expect(result).toBeNull();
  });

  it('deleteToken calls deleteItemAsync with correct key', async () => {
    await deleteToken();
    expect(SecureStore.deleteItemAsync).toHaveBeenCalledWith('cysentinel_token');
  });
});
```

### [WITH LIBS] src/utils/__tests__/offlineCache.test.js

```javascript
// src/utils/__tests__/offlineCache.test.js
import AsyncStorage from '@react-native-async-storage/async-storage';
import { queueIncident, getQueue, flushQueue, getQueueCount } from '../offlineCache';

jest.mock('@react-native-async-storage/async-storage');

describe('offlineCache', () => {
  beforeEach(() => {
    jest.clearAllMocks();
    AsyncStorage.getItem.mockResolvedValue(null);
    AsyncStorage.setItem.mockResolvedValue(undefined);
  });

  it('queueIncident adds item with id and createdAt', async () => {
    await queueIncident({ title: 'T', incident_type: 'phishing', severity: 'high', description: 'D' });
    expect(AsyncStorage.setItem).toHaveBeenCalled();
    const stored = JSON.parse(AsyncStorage.setItem.mock.calls[0][1]);
    expect(stored).toHaveLength(1);
    expect(stored[0]).toHaveProperty('id');
    expect(stored[0]).toHaveProperty('createdAt');
    expect(stored[0].title).toBe('T');
  });

  it('getQueue returns empty array when nothing stored', async () => {
    AsyncStorage.getItem.mockResolvedValue(null);
    expect(await getQueue()).toEqual([]);
  });

  it('getQueue returns parsed array', async () => {
    AsyncStorage.getItem.mockResolvedValue(JSON.stringify([{ id: 'offline-1', title: 'T' }]));
    const q = await getQueue();
    expect(q).toHaveLength(1);
    expect(q[0].id).toBe('offline-1');
  });

  it('getQueueCount returns 0 when empty', async () => {
    AsyncStorage.getItem.mockResolvedValue(null);
    expect(await getQueueCount()).toBe(0);
  });

  it('flushQueue calls submitFn and clears successful items', async () => {
    const item = { id: 'offline-1', title: 'T', incident_type: 'phishing', severity: 'high', description: 'D' };
    AsyncStorage.getItem.mockResolvedValue(JSON.stringify([item]));
    const submitFn = jest.fn().mockResolvedValue(undefined);

    const result = await flushQueue(submitFn);

    expect(submitFn).toHaveBeenCalledWith(item);
    expect(result.sent).toBe(1);
    expect(result.failed).toBe(0);
    const stored = JSON.parse(AsyncStorage.setItem.mock.calls[0][1]);
    expect(stored).toEqual([]);
  });

  it('flushQueue keeps failed items in queue', async () => {
    const item = { id: 'offline-1', title: 'T', incident_type: 'malware', severity: 'critical', description: 'D' };
    AsyncStorage.getItem.mockResolvedValue(JSON.stringify([item]));
    const submitFn = jest.fn().mockRejectedValue(new Error('Network error'));

    const result = await flushQueue(submitFn);

    expect(result.sent).toBe(0);
    expect(result.failed).toBe(1);
    const stored = JSON.parse(AsyncStorage.setItem.mock.calls[0][1]);
    expect(stored).toHaveLength(1);
    expect(stored[0].id).toBe('offline-1');
  });
});
```

### [WITH LIBS] src/services/__tests__/authService.test.js

```javascript
// src/services/__tests__/authService.test.js
import api from '../api';
import { saveToken, deleteToken } from '../../utils/secureStorage';
import { login, logout, getProfile } from '../authService';

jest.mock('../api');
jest.mock('../../utils/secureStorage');

describe('authService', () => {
  beforeEach(() => jest.clearAllMocks());

  it('login saves token when API returns token', async () => {
    api.post.mockResolvedValue({ data: { token: 'jwt-xyz' } });
    const result = await login('user@test.com', 'pass123');
    expect(api.post).toHaveBeenCalledWith('/api/auth/login', { email: 'user@test.com', password: 'pass123' });
    expect(saveToken).toHaveBeenCalledWith('jwt-xyz');
    expect(result.token).toBe('jwt-xyz');
  });

  it('login does not save token when 2FA required', async () => {
    api.post.mockResolvedValue({ data: { requires_2fa: true } });
    const result = await login('user@test.com', 'pass123');
    expect(saveToken).not.toHaveBeenCalled();
    expect(result.requires_2fa).toBe(true);
  });

  it('logout calls deleteToken', async () => {
    await logout();
    expect(deleteToken).toHaveBeenCalled();
  });

  it('getProfile calls correct endpoint and returns data', async () => {
    api.get.mockResolvedValue({ data: { email: 'user@test.com', role: 'analyst' } });
    const profile = await getProfile();
    expect(api.get).toHaveBeenCalledWith('/api/auth/profile');
    expect(profile.email).toBe('user@test.com');
  });
});
```

### [NO LIBS] Pure function unit tests — no mocking required

```javascript
// src/utils/__tests__/pure.test.js
// Tests for deterministic pure functions — no mocks, no libs, just Jest

// ── offlineCache helpers ──────────────────────────────────────────────────────
function buildQueueEntry(incident) {
  return {
    ...incident,
    id:        `offline-${Date.now()}-test`,
    createdAt: new Date().toISOString(),
  };
}

describe('buildQueueEntry', () => {
  it('adds id field starting with offline-', () => {
    const entry = buildQueueEntry({ title: 'Test', incident_type: 'phishing', severity: 'high' });
    expect(entry.id).toMatch(/^offline-/);
  });

  it('adds createdAt ISO timestamp', () => {
    const entry = buildQueueEntry({ title: 'Test' });
    expect(new Date(entry.createdAt).toISOString()).toBe(entry.createdAt);
  });

  it('preserves all original fields', () => {
    const entry = buildQueueEntry({ title: 'T', incident_type: 'malware', severity: 'critical' });
    expect(entry.title).toBe('T');
    expect(entry.incident_type).toBe('malware');
    expect(entry.severity).toBe('critical');
  });
});

// ── Risk score display color logic ────────────────────────────────────────────
function riskScoreColor(score) {
  if (score === null || score === undefined) return '#9ca3af';
  if (score >= 8) return '#dc2626';   // red — critical
  if (score >= 5) return '#d97706';   // amber — high/medium
  return '#16a34a';                    // green — low
}

describe('riskScoreColor', () => {
  it('returns red for score 8+', () => {
    expect(riskScoreColor(8)).toBe('#dc2626');
    expect(riskScoreColor(10)).toBe('#dc2626');
  });

  it('returns amber for score 5–7', () => {
    expect(riskScoreColor(5)).toBe('#d97706');
    expect(riskScoreColor(7)).toBe('#d97706');
  });

  it('returns green for score 1–4', () => {
    expect(riskScoreColor(1)).toBe('#16a34a');
    expect(riskScoreColor(4)).toBe('#16a34a');
  });

  it('returns grey for null or undefined', () => {
    expect(riskScoreColor(null)).toBe('#9ca3af');
    expect(riskScoreColor(undefined)).toBe('#9ca3af');
  });
});

// ── Date formatter ────────────────────────────────────────────────────────────
function formatIncidentDate(dateStr) {
  if (!dateStr) return '—';
  return new Date(dateStr).toLocaleDateString('en-ZA', {
    day: 'numeric', month: 'short', year: 'numeric'
  });
}

describe('formatIncidentDate', () => {
  it('returns em-dash for null', () => {
    expect(formatIncidentDate(null)).toBe('—');
  });

  it('returns formatted date string containing year', () => {
    const result = formatIncidentDate('2026-03-15T10:00:00Z');
    expect(result).toMatch(/2026/);
  });
});
```

### Running Tests

```bash
# Run all tests
npx jest

# Run with coverage
npx jest --coverage

# Run specific file
npx jest src/utils/__tests__/offlineCache.test.js

# Watch mode
npx jest --watch
```

---

## 🐛 Troubleshooting

### Expo Go QR code won't connect
```bash
# Force tunnel mode (bypasses LAN network issues)
npx expo start --tunnel

# Get your local IP for direct connection
ipconfig getifaddr en0   # macOS
hostname -I | awk '{print $1}'  # Linux
```

### SecureStore throws on Android emulator
```javascript
// In your jest setup or test file — mock SecureStore for emulator tests
jest.mock('expo-secure-store', () => ({
  setItemAsync:    jest.fn().mockResolvedValue(undefined),
  getItemAsync:    jest.fn().mockResolvedValue(null),
  deleteItemAsync: jest.fn().mockResolvedValue(undefined),
}));
```

### API calls fail with "Network request failed"
```bash
# 1. Get your actual laptop IP
ipconfig getifaddr en0  # macOS

# 2. Update src/services/api.js with the real IP
# Change: 'http://192.168.x.x:5000'  →  'http://192.168.1.25:5000' (your actual IP)

# 3. Make sure backend is running
cd ../cysentinel-backend && npm start
```

```json
// app.json — allow cleartext HTTP for dev testing (Android only)
{
  "expo": {
    "android": {
      "usesCleartextTraffic": true
    }
  }
}
```

### Navigation crashes — gesture handler missing
```bash
npx expo install react-native-gesture-handler
```
```jsx
// App.js — this import MUST be the very first line
import 'react-native-gesture-handler';
// ... rest of imports
```

### EAS build fails with "missing credentials"
```bash
# Let EAS auto-manage credentials (recommended)
eas build --platform android --profile preview --non-interactive
# EAS generates and stores Android keystore automatically
```

---

*End of File 04 — Mobile Developer Code Blocks*  
*All four role files complete: 01 Backend · 02 ML Engineer · 03 Frontend · 04 Mobile*
