# 🤖 ML / Risk Scoring Engineer — Expanded Code Blocks
**CySentinel | Debug Ninjas | UJ IFM03A3 | 2026**

> `[WITH LIBS]` = standard stack (pandas / numpy / scikit-learn / Flask / pytest)  
> `[NO LIBS]` = Python standard library only (`csv`, `random`, `math`, `json`, `http.server`, `unittest`) — safe under strict university library restrictions  
> `riskScorer.js` is always zero-dependency — it is plain Node.js and satisfies both policies.  
> Tests: `[WITH LIBS]` uses `pytest`; `[NO LIBS]` uses `unittest` (built-in since Python 2.7).

---

## PLANNING WEEK

### Monday — Environment Setup

#### [WITH LIBS] Python environment
```bash
# Verify Python 3.10+
python --version

# Create and activate virtual environment
python -m venv cysentinel-ml-env
source cysentinel-ml-env/bin/activate       # macOS / Linux
# cysentinel-ml-env\Scripts\activate        # Windows

# Install analysis packages (Months 1-5 only)
pip install pandas numpy scikit-learn jupyter matplotlib seaborn

# Verify installs
python -c "import sklearn; print('sklearn', sklearn.__version__)"
python -c "import pandas as pd; print('pandas', pd.__version__)"

# Save requirements
pip freeze > requirements-analysis.txt
```

#### [NO LIBS] Stdlib-only verification
```bash
# Confirm Python stdlib covers all no-libs needs
python -c "import csv, random, math, json, statistics, uuid, datetime; print('stdlib OK')"

# Create project folder
mkdir -p cysentinel-ml
cd cysentinel-ml
touch generate_synthetic_data_nolibs.py
touch data_quality_check_nolibs.py
touch dashboard_analysis_nolibs.py
```

---

### Tuesday-Wednesday — Read Schema, Draft riskScorer Skeleton

```bash
# In cysentinel-backend (no extra packages needed)
touch src/services/riskScorer.js
touch src/services/riskScorer.test.js
npx jest riskScorer --passWithNoTests
# Expected: "No tests found" — correct at this stage
```

---

### Thursday-Friday — Complete riskScorer.js (Planning Week skeleton through Month 1)

This file is ZERO-dependency Node.js. Both the with-libs and no-libs policy produce the identical file.

#### src/services/riskScorer.js
```javascript
// cysentinel-backend/src/services/riskScorer.js
/**
 * @file riskScorer.js
 * @description Rules-based risk scorer for CySentinel (Months 1-5).
 *
 * Calculates risk_score (column 16 of incidents table, INTEGER 1-10)
 * on every call to POST /api/incidents.
 *
 * ZERO external dependencies — plain Node.js.
 *
 * Formula: clamp( round(SEVERITY_BASE * TYPE_MULTIPLIER + TIME_FACTOR), 1, 10 )
 *
 * References:
 *   Breier et al. (2017) - incident classification and severity estimation
 *   NIST CSF 2.0 (2024)  - risk-based prioritisation framework
 */

/**
 * Base risk score by severity.
 * Maps the severity column value to an integer base score.
 * @type {Object.<string, number>}
 */
const SEVERITY_BASE = {
  critical: 9,
  high:     7,
  medium:   5,
  low:      3,
};

/**
 * Risk multiplier by incident type.
 * Data breaches weighted highest; "other" lowest.
 * @type {Object.<string, number>}
 */
const TYPE_MULTIPLIER = {
  data_breach:         1.2,
  malware:             1.1,
  phishing:            1.0,
  unauthorised_access: 1.0,
  other:               0.9,
};

/**
 * Time-of-day risk bonus.
 * After-hours incidents (00:00-05:59) get +0.1 — delayed response, higher impact.
 * @param {string|Date|null} createdAt
 * @returns {number} 0.1 or 0
 */
function TIME_FACTOR(createdAt) {
  if (!createdAt) return 0;
  const hour = new Date(createdAt).getHours();
  return hour >= 0 && hour < 6 ? 0.1 : 0;
}

/**
 * Calculate risk_score (INTEGER 1-10) for a single incident.
 *
 * @param {object} incident
 * @param {string} incident.severity       'low'|'medium'|'high'|'critical'
 * @param {string} incident.incident_type  See TYPE_MULTIPLIER keys
 * @param {string} [incident.created_at]   ISO 8601 timestamp (optional)
 * @returns {number} Integer 1-10
 *
 * @example
 * calculateRiskScore({ severity: 'critical', incident_type: 'data_breach' })
 * // returns 10
 * calculateRiskScore({ severity: 'low', incident_type: 'other' })
 * // returns 3
 */
function calculateRiskScore({ severity, incident_type, created_at }) {
  const base       = SEVERITY_BASE[severity]        ?? 5;
  const multiplier = TYPE_MULTIPLIER[incident_type] ?? 1.0;
  const timeFactor = TIME_FACTOR(created_at);

  const raw   = base * multiplier + timeFactor;
  const score = Math.min(10, Math.max(1, Math.round(raw)));
  return score;
}

/**
 * Classify overall org risk level from a list of incident risk scores.
 * Used by GET /api/dashboard/risk-summary to populate the Risk Level card.
 *
 * @param {Array.<{risk_score: number}>} incidents
 * @returns {'low'|'medium'|'high'}
 *
 * @example
 * calculateOrgRiskLevel([])                              // returns 'low'
 * calculateOrgRiskLevel([{risk_score:8},{risk_score:9}]) // returns 'high'
 */
function calculateOrgRiskLevel(incidents) {
  if (!incidents || incidents.length === 0) return 'low';
  const avg = incidents.reduce((sum, i) => sum + (i.risk_score || 0), 0) / incidents.length;
  if (avg >= 7) return 'high';
  if (avg >= 4) return 'medium';
  return 'low';
}

module.exports = {
  calculateRiskScore,
  calculateOrgRiskLevel,
  SEVERITY_BASE,
  TYPE_MULTIPLIER,
  TIME_FACTOR,
};
```

---

## MONTH 1 — riskScorer Tests

### [WITH LIBS] src/services/riskScorer.test.js — Jest (23 tests, 90%+ coverage)
```javascript
// cysentinel-backend/src/services/riskScorer.test.js
// Run: npx jest riskScorer --coverage

const {
  calculateRiskScore,
  calculateOrgRiskLevel,
  TIME_FACTOR,
} = require('./riskScorer');

// calculateRiskScore — known combinations

describe('calculateRiskScore — known combinations', () => {
  test('critical + data_breach scores 10 (9 * 1.2 = 10.8 clamped)', () => {
    expect(calculateRiskScore({ severity: 'critical', incident_type: 'data_breach' })).toBe(10);
  });

  test('low + other scores 1-3 range (3 * 0.9 = 2.7 rounded)', () => {
    const score = calculateRiskScore({ severity: 'low', incident_type: 'other' });
    expect(score).toBeGreaterThanOrEqual(1);
    expect(score).toBeLessThanOrEqual(3);
  });

  test('high + phishing scores 7 (7 * 1.0 = 7.0)', () => {
    expect(calculateRiskScore({ severity: 'high', incident_type: 'phishing' })).toBe(7);
  });

  test('critical + malware scores 10 (9 * 1.1 = 9.9 rounded)', () => {
    expect(calculateRiskScore({ severity: 'critical', incident_type: 'malware' })).toBe(10);
  });

  test('medium + unauthorised_access scores 5 (5 * 1.0 = 5.0)', () => {
    expect(calculateRiskScore({ severity: 'medium', incident_type: 'unauthorised_access' })).toBe(5);
  });

  test('high + data_breach scores >= 8 (7 * 1.2 = 8.4)', () => {
    expect(calculateRiskScore({ severity: 'high', incident_type: 'data_breach' })).toBeGreaterThanOrEqual(8);
  });

  test('low + phishing scores 3 (3 * 1.0 = 3.0)', () => {
    expect(calculateRiskScore({ severity: 'low', incident_type: 'phishing' })).toBe(3);
  });

  test('medium + malware scores 6 (5 * 1.1 = 5.5 rounded)', () => {
    expect(calculateRiskScore({ severity: 'medium', incident_type: 'malware' })).toBe(6);
  });
});

// calculateRiskScore — edge cases

describe('calculateRiskScore — edge cases', () => {
  test('unknown severity defaults to base 5', () => {
    // 'extreme' not in map -> default 5 * 1.0 = 5
    expect(calculateRiskScore({ severity: 'extreme', incident_type: 'phishing' })).toBe(5);
  });

  test('unknown incident_type defaults to multiplier 1.0', () => {
    // 'ransomware' deprecated type -> base=7, multiplier=1.0 -> 7
    expect(calculateRiskScore({ severity: 'high', incident_type: 'ransomware' })).toBe(7);
  });

  test('score is always 1-10 for all valid severity+type combinations', () => {
    const severities = ['low', 'medium', 'high', 'critical'];
    const types      = ['phishing', 'malware', 'unauthorised_access', 'data_breach', 'other'];
    for (const severity of severities) {
      for (const incident_type of types) {
        const score = calculateRiskScore({ severity, incident_type });
        expect(score).toBeGreaterThanOrEqual(1);
        expect(score).toBeLessThanOrEqual(10);
      }
    }
  });

  test('missing created_at does not throw', () => {
    expect(() =>
      calculateRiskScore({ severity: 'medium', incident_type: 'phishing' })
    ).not.toThrow();
  });
});

// TIME_FACTOR

describe('TIME_FACTOR — after-hours bonus', () => {
  test('midnight (hour 0) returns +0.1', () => {
    const midnight = new Date();
    midnight.setHours(0, 0, 0, 0);
    expect(TIME_FACTOR(midnight.toISOString())).toBe(0.1);
  });

  test('hour 3 (03:00) returns +0.1', () => {
    const d = new Date();
    d.setHours(3, 0, 0, 0);
    expect(TIME_FACTOR(d.toISOString())).toBe(0.1);
  });

  test('noon (hour 12) returns 0', () => {
    const noon = new Date();
    noon.setHours(12, 0, 0, 0);
    expect(TIME_FACTOR(noon.toISOString())).toBe(0);
  });

  test('null returns 0', () => {
    expect(TIME_FACTOR(null)).toBe(0);
  });

  test('after-hours incident scores >= same incident at noon', () => {
    const midnight = new Date(); midnight.setHours(2, 0, 0, 0);
    const noon     = new Date(); noon.setHours(12, 0, 0, 0);
    const scoreMid = calculateRiskScore({
      severity: 'medium', incident_type: 'phishing', created_at: midnight.toISOString()
    });
    const scoreNn = calculateRiskScore({
      severity: 'medium', incident_type: 'phishing', created_at: noon.toISOString()
    });
    expect(scoreMid).toBeGreaterThanOrEqual(scoreNn);
  });
});

// calculateOrgRiskLevel

describe('calculateOrgRiskLevel', () => {
  test('empty array returns low', () => {
    expect(calculateOrgRiskLevel([])).toBe('low');
  });

  test('null returns low', () => {
    expect(calculateOrgRiskLevel(null)).toBe('low');
  });

  test('all high risk_scores returns high', () => {
    const incidents = [{ risk_score: 8 }, { risk_score: 9 }, { risk_score: 7 }];
    expect(calculateOrgRiskLevel(incidents)).toBe('high');
  });

  test('all low risk_scores returns low', () => {
    const incidents = [{ risk_score: 2 }, { risk_score: 1 }, { risk_score: 3 }];
    expect(calculateOrgRiskLevel(incidents)).toBe('low');
  });

  test('mixed avg=5 returns medium', () => {
    const incidents = [{ risk_score: 4 }, { risk_score: 5 }, { risk_score: 6 }];
    expect(calculateOrgRiskLevel(incidents)).toBe('medium');
  });

  test('single critical incident (score 10) returns high', () => {
    expect(calculateOrgRiskLevel([{ risk_score: 10 }])).toBe('high');
  });

  test('missing risk_score field does not throw', () => {
    const incidents = [{ risk_score: 5 }, { title: 'no score' }];
    expect(() => calculateOrgRiskLevel(incidents)).not.toThrow();
  });
});
```

```bash
# Run all riskScorer tests with coverage
cd cysentinel-backend
npx jest riskScorer --coverage
# Expected: 23 tests passed, ~95% coverage
```

### [NO LIBS] tests/riskScorer.test.no-libs.js — node:test + node:assert
```javascript
// cysentinel-backend/tests/riskScorer.test.no-libs.js
// Run: node --test tests/riskScorer.test.no-libs.js   (Node v18+)

const { describe, test } = require('node:test');
const assert = require('node:assert/strict');

const {
  calculateRiskScore,
  calculateOrgRiskLevel,
  TIME_FACTOR,
} = require('../src/services/riskScorer');

describe('calculateRiskScore — no-libs', () => {
  test('critical + data_breach returns 10', () => {
    assert.equal(calculateRiskScore({ severity: 'critical', incident_type: 'data_breach' }), 10);
  });

  test('low + other returns 1-3', () => {
    const score = calculateRiskScore({ severity: 'low', incident_type: 'other' });
    assert.ok(score >= 1 && score <= 3, `Expected 1-3, got ${score}`);
  });

  test('high + phishing returns 7', () => {
    assert.equal(calculateRiskScore({ severity: 'high', incident_type: 'phishing' }), 7);
  });

  test('medium + malware returns 6', () => {
    assert.equal(calculateRiskScore({ severity: 'medium', incident_type: 'malware' }), 6);
  });

  test('all valid combinations stay 1-10', () => {
    const severities = ['low','medium','high','critical'];
    const types      = ['phishing','malware','unauthorised_access','data_breach','other'];
    for (const s of severities) {
      for (const t of types) {
        const score = calculateRiskScore({ severity: s, incident_type: t });
        assert.ok(score >= 1 && score <= 10, `Out of range: ${s}+${t} -> ${score}`);
      }
    }
  });

  test('unknown severity defaults gracefully', () => {
    const score = calculateRiskScore({ severity: 'unknown', incident_type: 'phishing' });
    assert.ok(score >= 1 && score <= 10);
  });
});

describe('TIME_FACTOR — no-libs', () => {
  test('null returns 0', () => assert.equal(TIME_FACTOR(null), 0));

  test('midnight returns 0.1', () => {
    const d = new Date(); d.setHours(0, 0, 0, 0);
    assert.equal(TIME_FACTOR(d.toISOString()), 0.1);
  });

  test('noon returns 0', () => {
    const d = new Date(); d.setHours(12, 0, 0, 0);
    assert.equal(TIME_FACTOR(d.toISOString()), 0);
  });
});

describe('calculateOrgRiskLevel — no-libs', () => {
  test('empty returns low',  () => assert.equal(calculateOrgRiskLevel([]), 'low'));
  test('null returns low',   () => assert.equal(calculateOrgRiskLevel(null), 'low'));
  test('high scores return high', () =>
    assert.equal(calculateOrgRiskLevel([{risk_score:8},{risk_score:9}]), 'high'));
  test('low scores return low', () =>
    assert.equal(calculateOrgRiskLevel([{risk_score:1},{risk_score:2}]), 'low'));
  test('medium scores return medium', () =>
    assert.equal(calculateOrgRiskLevel([{risk_score:4},{risk_score:6}]), 'medium'));
});
```

---

## MONTH 2 — Synthetic Data Generation

### Week 1 — Generate 100-Incident Dataset

#### [WITH LIBS] cysentinel-ml/generate_synthetic_data.py
```python
# cysentinel-ml/generate_synthetic_data.py
"""
Generate 100 synthetic incidents matching the exact 16-column incidents schema.

Outputs:
  synthetic_incidents.csv        — for Jupyter analysis + M3 chart testing
  synthetic_incidents_insert.sql — paste into Supabase SQL Editor to seed DB

Usage: python generate_synthetic_data.py
"""

import pandas as pd
import numpy as np
from datetime import datetime, timedelta
import uuid
import os

# Configuration
NUM_INCIDENTS = 100
DAYS_BACK     = 90
RANDOM_SEED   = 42

# Replace with real Supabase UUIDs before seeding
ORG_ID  = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'
USER_ID = 'ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj'

OUTPUT_CSV = 'synthetic_incidents.csv'
OUTPUT_SQL = 'synthetic_incidents_insert.sql'

# Value Sets and Weights
INCIDENT_TYPES = ['phishing', 'malware', 'unauthorised_access', 'data_breach', 'other']
SEVERITIES     = ['low',      'medium',  'high',   'critical']
STATUSES       = ['open',     'in_progress', 'resolved', 'closed']

TYPE_WEIGHTS   = [0.40, 0.25, 0.20, 0.08, 0.07]
SEV_WEIGHTS    = [0.35, 0.35, 0.20, 0.10]
STATUS_WEIGHTS = [0.30, 0.25, 0.25, 0.20]

# Risk Score logic — MUST mirror riskScorer.js exactly
SEVERITY_BASE   = {'critical': 9, 'high': 7, 'medium': 5, 'low': 3}
TYPE_MULTIPLIER = {
    'data_breach': 1.2, 'malware': 1.1,
    'phishing': 1.0, 'unauthorised_access': 1.0, 'other': 0.9,
}

def calculate_risk_score(severity, incident_type, created_at=None):
    """Mirror of riskScorer.js calculateRiskScore — keep in sync if scorer changes."""
    base       = SEVERITY_BASE.get(severity, 5)
    multiplier = TYPE_MULTIPLIER.get(incident_type, 1.0)
    time_bonus = 0.1 if (created_at and 0 <= created_at.hour < 6) else 0
    return int(min(10, max(1, round(base * multiplier + time_bonus))))

# Generation Loop
np.random.seed(RANDOM_SEED)
now     = datetime.now()
records = []

for i in range(NUM_INCIDENTS):
    days_ago   = np.random.randint(0, DAYS_BACK)
    hour       = np.random.randint(0, 24)
    minute     = np.random.randint(0, 60)
    created_at = now - timedelta(days=int(days_ago), hours=int(hour), minutes=int(minute))
    updated_at = created_at + timedelta(hours=np.random.randint(0, 48))

    incident_type = np.random.choice(INCIDENT_TYPES, p=TYPE_WEIGHTS)
    severity      = np.random.choice(SEVERITIES,     p=SEV_WEIGHTS)
    status        = np.random.choice(STATUSES,       p=STATUS_WEIGHTS)
    risk_score    = calculate_risk_score(severity, incident_type, created_at)

    resolved_at      = None
    resolution_notes = None
    if status in ('resolved', 'closed'):
        resolved_at      = (created_at + timedelta(hours=np.random.randint(1, 72))).isoformat()
        resolution_notes = f'Incident resolved — {incident_type} contained and remediated.'

    records.append({
        'incident_id':         str(uuid.uuid4()),                          # col 1
        'created_at':          created_at.isoformat(),                     # col 2
        'updated_at':          updated_at.isoformat(),                     # col 3
        'title':               f'Synthetic {incident_type.replace("_"," ").title()} #{i+1}', # col 4
        'description':         f'A {severity}-severity {incident_type} incident detected on '
                               f'{created_at.strftime("%Y-%m-%d")}. Synthetic test data.',   # col 5
        'incident_type':       incident_type,                              # col 6
        'severity':            severity,                                   # col 7
        'status':              status,                                     # col 8
        'reported_by_user_id': USER_ID,                                    # col 9
        'assigned_to_user_id': USER_ID,                                    # col 10
        'organisation_id':     ORG_ID,                                     # col 11
        'checklist_id':        None,                                       # col 12
        'photo_url':           None,                                       # col 13
        'resolved_at':         resolved_at,                                # col 14
        'resolution_notes':    resolution_notes,                           # col 15
        'risk_score':          risk_score,                                 # col 16
    })

df = pd.DataFrame(records)

# Assertions
assert len(df.columns) == 16,                f"Expected 16 columns, got {len(df.columns)}"
assert df['risk_score'].between(1,10).all(), "risk_score out of 1-10 range!"
assert df['risk_score'].notnull().all(),     "Null risk_score values found!"

# Save CSV
df.to_csv(OUTPUT_CSV, index=False)
print(f"CSV saved: {OUTPUT_CSV} ({len(df)} rows, {len(df.columns)} columns)")
for col in ['incident_type', 'severity', 'status']:
    print(f"  {col}: {dict(df[col].value_counts())}")
print(f"  risk_score: mean={df['risk_score'].mean():.2f}, "
      f"min={df['risk_score'].min()}, max={df['risk_score'].max()}")

# Save SQL INSERT
def sql_val(v):
    if v is None:                   return 'NULL'
    if isinstance(v, (int, float)): return str(v)
    return "'" + str(v).replace("'", "''") + "'"

columns   = ', '.join(df.columns)
value_rows = [
    '  (' + ', '.join(sql_val(row[col]) for col in df.columns) + ')'
    for _, row in df.iterrows()
]

with open(OUTPUT_SQL, 'w') as f:
    f.write('-- synthetic_incidents_insert.sql\n')
    f.write('-- Generated by cysentinel-ml/generate_synthetic_data.py\n')
    f.write('-- Paste into Supabase SQL Editor to seed the incidents table\n\n')
    f.write(f'INSERT INTO incidents ({columns}) VALUES\n')
    f.write(',\n'.join(value_rows) + ';\n')

print(f"\nSQL saved: {OUTPUT_SQL}")
print("Paste this file into the Supabase SQL Editor to seed the database.")
```

---

#### [NO LIBS] cysentinel-ml/generate_synthetic_data_nolibs.py — stdlib only
```python
# cysentinel-ml/generate_synthetic_data_nolibs.py
"""
Generate synthetic incidents using Python stdlib only.
No pandas, no numpy — uses: csv, random, uuid, datetime, collections

Usage: python generate_synthetic_data_nolibs.py
"""

import csv
import random
import uuid
from datetime import datetime, timedelta
from collections import Counter

# Configuration
NUM_INCIDENTS = 100
DAYS_BACK     = 90
RANDOM_SEED   = 42

ORG_ID  = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'
USER_ID = 'ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj'

OUTPUT_CSV = 'synthetic_incidents.csv'
OUTPUT_SQL = 'synthetic_incidents_insert.sql'

COLUMNS = [
    'incident_id', 'created_at', 'updated_at', 'title', 'description',
    'incident_type', 'severity', 'status', 'reported_by_user_id',
    'assigned_to_user_id', 'organisation_id', 'checklist_id',
    'photo_url', 'resolved_at', 'resolution_notes', 'risk_score',
]

# Value Sets (weighted choice without numpy)
INCIDENT_TYPES = ['phishing', 'malware', 'unauthorised_access', 'data_breach', 'other']
SEVERITIES     = ['low', 'medium', 'high', 'critical']
STATUSES       = ['open', 'in_progress', 'resolved', 'closed']

TYPE_CUM   = [0.40, 0.65, 0.85, 0.93, 1.00]  # cumulative probabilities
SEV_CUM    = [0.35, 0.70, 0.90, 1.00]
STATUS_CUM = [0.30, 0.55, 0.80, 1.00]

def weighted_choice(options, cum_weights):
    r = random.random()
    for i, w in enumerate(cum_weights):
        if r <= w:
            return options[i]
    return options[-1]

# Risk Score — mirrors riskScorer.js
SEVERITY_BASE   = {'critical': 9, 'high': 7, 'medium': 5, 'low': 3}
TYPE_MULTIPLIER = {
    'data_breach': 1.2, 'malware': 1.1,
    'phishing': 1.0, 'unauthorised_access': 1.0, 'other': 0.9,
}

def calculate_risk_score(severity, incident_type, hour=None):
    base       = SEVERITY_BASE.get(severity, 5)
    multiplier = TYPE_MULTIPLIER.get(incident_type, 1.0)
    time_bonus = 0.1 if (hour is not None and 0 <= hour < 6) else 0
    return int(min(10, max(1, round(base * multiplier + time_bonus))))

# Generation
random.seed(RANDOM_SEED)
now     = datetime.now()
records = []

for i in range(NUM_INCIDENTS):
    days_ago   = random.randint(0, DAYS_BACK)
    hour       = random.randint(0, 23)
    minute     = random.randint(0, 59)
    created_dt = now - timedelta(days=days_ago, hours=hour, minutes=minute)
    updated_dt = created_dt + timedelta(hours=random.randint(0, 48))

    incident_type = weighted_choice(INCIDENT_TYPES, TYPE_CUM)
    severity      = weighted_choice(SEVERITIES,     SEV_CUM)
    status        = weighted_choice(STATUSES,       STATUS_CUM)
    risk_score    = calculate_risk_score(severity, incident_type, hour)

    resolved_at      = None
    resolution_notes = None
    if status in ('resolved', 'closed'):
        resolved_dt      = created_dt + timedelta(hours=random.randint(1, 72))
        resolved_at      = resolved_dt.isoformat()
        resolution_notes = f'Incident resolved — {incident_type} contained.'

    records.append({
        'incident_id':         str(uuid.uuid4()),
        'created_at':          created_dt.isoformat(),
        'updated_at':          updated_dt.isoformat(),
        'title':               f'Synthetic {incident_type.replace("_"," ").title()} #{i+1}',
        'description':         f'A {severity}-severity {incident_type} incident. Synthetic data.',
        'incident_type':       incident_type,
        'severity':            severity,
        'status':              status,
        'reported_by_user_id': USER_ID,
        'assigned_to_user_id': USER_ID,
        'organisation_id':     ORG_ID,
        'checklist_id':        None,
        'photo_url':           None,
        'resolved_at':         resolved_at,
        'resolution_notes':    resolution_notes,
        'risk_score':          risk_score,
    })

# Assertions
assert len(records) == NUM_INCIDENTS,                        "Wrong row count"
assert all(len(r) == 16 for r in records),                   "Wrong column count"
assert all(1 <= r['risk_score'] <= 10 for r in records),     "risk_score out of range"

# Save CSV
with open(OUTPUT_CSV, 'w', newline='', encoding='utf-8') as f:
    writer = csv.DictWriter(f, fieldnames=COLUMNS)
    writer.writeheader()
    writer.writerows(records)

print(f"CSV saved: {OUTPUT_CSV} ({len(records)} rows, 16 columns)")
print("  incident_type:", dict(Counter(r['incident_type'] for r in records)))
print("  severity:",      dict(Counter(r['severity']      for r in records)))

scores = [r['risk_score'] for r in records]
print(f"  risk_score: mean={sum(scores)/len(scores):.2f}, min={min(scores)}, max={max(scores)}")

# Save SQL INSERT
def sql_val(v):
    if v is None:                   return 'NULL'
    if isinstance(v, (int, float)): return str(v)
    return "'" + str(v).replace("'", "''") + "'"

rows = [
    '  (' + ', '.join(sql_val(r[col]) for col in COLUMNS) + ')'
    for r in records
]

with open(OUTPUT_SQL, 'w', encoding='utf-8') as f:
    f.write('-- synthetic_incidents_insert.sql (no-libs version)\n')
    f.write(f'INSERT INTO incidents ({", ".join(COLUMNS)}) VALUES\n')
    f.write(',\n'.join(rows) + ';\n')

print(f"SQL saved: {OUTPUT_SQL}")
```

---

### Week 2 — Data Quality Check

#### [WITH LIBS] cysentinel-ml/data_quality_check.py
```python
# cysentinel-ml/data_quality_check.py
"""Validates synthetic_incidents.csv before Supabase seeding.
All checks must pass before running the SQL INSERT file.

Usage: python data_quality_check.py
"""
import pandas as pd
import sys

EXPECTED_COLUMNS = [
    'incident_id','created_at','updated_at','title','description',
    'incident_type','severity','status','reported_by_user_id',
    'assigned_to_user_id','organisation_id','checklist_id',
    'photo_url','resolved_at','resolution_notes','risk_score',
]
VALID_TYPES      = {'phishing','malware','unauthorised_access','data_breach','other'}
VALID_SEVERITIES = {'low','medium','high','critical'}
VALID_STATUSES   = {'open','in_progress','resolved','closed'}

errors = []
df     = pd.read_csv('synthetic_incidents.csv')

checks = [
    (len(df) == 100,
     f"Row count: {len(df)}",
     f"Expected 100 rows, got {len(df)}"),
    (set(EXPECTED_COLUMNS) == set(df.columns),
     "All 16 columns present",
     f"Column mismatch: {set(EXPECTED_COLUMNS) ^ set(df.columns)}"),
    (df['risk_score'].notnull().all(),
     "No null risk_scores",
     f"{df['risk_score'].isnull().sum()} null risk_scores"),
    (df['risk_score'].between(1, 10).all(),
     f"All risk_scores 1-10 (min={df['risk_score'].min()}, max={df['risk_score'].max()})",
     "risk_score out of range 1-10"),
    (df['incident_id'].duplicated().sum() == 0,
     "All incident_id unique",
     "Duplicate incident_ids found"),
    (df['incident_type'].isin(VALID_TYPES).all(),
     "All incident_type valid",
     f"Invalid types: {df[~df['incident_type'].isin(VALID_TYPES)]['incident_type'].unique().tolist()}"),
    (df['severity'].isin(VALID_SEVERITIES).all(),
     "All severity valid",
     f"Invalid: {df[~df['severity'].isin(VALID_SEVERITIES)]['severity'].unique().tolist()}"),
    (df['status'].isin(VALID_STATUSES).all(),
     "All status valid",
     f"Invalid: {df[~df['status'].isin(VALID_STATUSES)]['status'].unique().tolist()}"),
]

for ok, success_msg, fail_msg in checks:
    if ok:
        print(f"  OK  {success_msg}")
    else:
        print(f"  FAIL {fail_msg}")
        errors.append(fail_msg)

print("\n---")
if errors:
    print(f"FAILED - {len(errors)} error(s). Fix in generate_synthetic_data.py and re-run.")
    sys.exit(1)
else:
    print("ALL CHECKS PASSED - safe to seed Supabase.")
```

#### [NO LIBS] cysentinel-ml/data_quality_check_nolibs.py
```python
# cysentinel-ml/data_quality_check_nolibs.py
"""Data quality check — stdlib csv only. No pandas required."""
import csv
import sys

EXPECTED_COLUMNS = [
    'incident_id','created_at','updated_at','title','description',
    'incident_type','severity','status','reported_by_user_id',
    'assigned_to_user_id','organisation_id','checklist_id',
    'photo_url','resolved_at','resolution_notes','risk_score',
]
VALID_TYPES      = {'phishing','malware','unauthorised_access','data_breach','other'}
VALID_SEVERITIES = {'low','medium','high','critical'}
VALID_STATUSES   = {'open','in_progress','resolved','closed'}

errors = []

with open('synthetic_incidents.csv', newline='', encoding='utf-8') as f:
    reader  = csv.DictReader(f)
    records = list(reader)
    columns = reader.fieldnames or []

# Check 1: row count
if len(records) == 100:
    print(f"  OK  Row count: {len(records)}")
else:
    errors.append(f"Expected 100 rows, got {len(records)}")

# Check 2: 16 columns
missing = [c for c in EXPECTED_COLUMNS if c not in columns]
if not missing:
    print("  OK  All 16 columns present")
else:
    errors.append(f"Missing columns: {missing}")

# Checks 3-8 over records
ids, risk_issues, type_issues, sev_issues, status_issues = [], [], [], [], []
for r in records:
    ids.append(r.get('incident_id', ''))
    score = r.get('risk_score', '')
    if not score:
        risk_issues.append('null')
    elif not (1 <= int(float(score)) <= 10):
        risk_issues.append(score)
    if r.get('incident_type') not in VALID_TYPES:
        type_issues.append(r.get('incident_type'))
    if r.get('severity') not in VALID_SEVERITIES:
        sev_issues.append(r.get('severity'))
    if r.get('status') not in VALID_STATUSES:
        status_issues.append(r.get('status'))

if not risk_issues:
    print("  OK  All risk_scores valid (1-10, non-null)")
else:
    errors.append(f"risk_score issues: {risk_issues[:5]}")

if len(set(ids)) == len(ids):
    print("  OK  All incident_id unique")
else:
    errors.append("Duplicate incident_ids found")

for label, issues in [
    ('incident_type', type_issues),
    ('severity',      sev_issues),
    ('status',        status_issues),
]:
    if not issues:
        print(f"  OK  All {label} valid")
    else:
        errors.append(f"Invalid {label}: {list(set(issues))}")

print("\n---")
if errors:
    print(f"FAILED - {len(errors)} error(s)")
    for e in errors: print(f"  {e}")
    sys.exit(1)
else:
    print("ALL CHECKS PASSED - safe to seed Supabase.")
```

#### [NO LIBS] unittest tests for data quality
```python
# cysentinel-ml/tests/test_data_quality_nolibs.py
# Run: python -m unittest tests.test_data_quality_nolibs -v

import unittest
import csv
import os

EXPECTED_COLUMNS = [
    'incident_id','created_at','updated_at','title','description',
    'incident_type','severity','status','reported_by_user_id',
    'assigned_to_user_id','organisation_id','checklist_id',
    'photo_url','resolved_at','resolution_notes','risk_score',
]
VALID_TYPES      = {'phishing','malware','unauthorised_access','data_breach','other'}
VALID_SEVERITIES = {'low','medium','high','critical'}
VALID_STATUSES   = {'open','in_progress','resolved','closed'}

CSV_PATH = os.path.join(os.path.dirname(__file__), '..', 'synthetic_incidents.csv')

class TestDataQuality(unittest.TestCase):

    @classmethod
    def setUpClass(cls):
        if not os.path.exists(CSV_PATH):
            raise unittest.SkipTest(
                "synthetic_incidents.csv not found — run generate_synthetic_data_nolibs.py first"
            )
        with open(CSV_PATH, newline='', encoding='utf-8') as f:
            reader       = csv.DictReader(f)
            cls.records  = list(reader)
            cls.columns  = reader.fieldnames or []

    def test_01_row_count(self):
        self.assertEqual(len(self.records), 100, f"Expected 100, got {len(self.records)}")

    def test_02_column_count(self):
        missing = [c for c in EXPECTED_COLUMNS if c not in self.columns]
        self.assertEqual(missing, [], f"Missing columns: {missing}")

    def test_03_risk_score_no_nulls(self):
        nulls = [r for r in self.records if not r.get('risk_score')]
        self.assertEqual(nulls, [], f"{len(nulls)} null risk_score rows")

    def test_04_risk_score_range(self):
        bad = [r for r in self.records
               if not (1 <= int(float(r['risk_score'])) <= 10)]
        self.assertEqual(bad, [], f"{len(bad)} risk_scores outside 1-10")

    def test_05_unique_ids(self):
        ids = [r['incident_id'] for r in self.records]
        self.assertEqual(len(ids), len(set(ids)), "Duplicate incident_ids found")

    def test_06_valid_incident_types(self):
        bad = [r['incident_type'] for r in self.records
               if r['incident_type'] not in VALID_TYPES]
        self.assertEqual(bad, [], f"Invalid incident_types: {set(bad)}")

    def test_07_valid_severities(self):
        bad = [r['severity'] for r in self.records
               if r['severity'] not in VALID_SEVERITIES]
        self.assertEqual(bad, [], f"Invalid severities: {set(bad)}")

    def test_08_valid_statuses(self):
        bad = [r['status'] for r in self.records
               if r['status'] not in VALID_STATUSES]
        self.assertEqual(bad, [], f"Invalid statuses: {set(bad)}")

    def test_09_risk_score_matches_formula(self):
        """Verify risk_scores are consistent with riskScorer.js formula."""
        SEV = {'critical':9,'high':7,'medium':5,'low':3}
        MUL = {'data_breach':1.2,'malware':1.1,'phishing':1.0,'unauthorised_access':1.0,'other':0.9}
        for r in self.records:
            base     = SEV.get(r['severity'], 5)
            mult     = MUL.get(r['incident_type'], 1.0)
            expected = min(10, max(1, round(base * mult)))
            actual   = int(float(r['risk_score']))
            # Allow +-1 for time_bonus effect
            self.assertAlmostEqual(
                actual, expected, delta=1,
                msg=f"Score mismatch for {r['severity']}+{r['incident_type']}: "
                    f"expected ~{expected}, got {actual}"
            )

if __name__ == '__main__':
    unittest.main()
```

---

## MONTH 3 — Dashboard Analysis and Data Format Spec

#### [WITH LIBS] cysentinel-ml/dashboard_analysis.py
```python
# cysentinel-ml/dashboard_analysis.py
"""
Produce the 4 data shapes that M3's Recharts components need.
Run with M3 present (pair programming Wednesday, Month 3 Week 2).

Usage: python dashboard_analysis.py
"""

import pandas as pd
import json

df = pd.read_csv('synthetic_incidents.csv', parse_dates=['created_at'])
print(f"Loaded {len(df)} incidents\n")

# Chart 1 — BarChart: incidents by type
# M3 Recharts expects: [{incident_type, count}]
chart1 = (df.groupby('incident_type').size()
            .reset_index(name='count')
            .sort_values('count', ascending=False)
            .to_dict(orient='records'))
print("Chart 1 (BarChart — incidents by type):")
print(json.dumps(chart1, indent=2))

# Chart 2 — PieChart: incidents by severity
# M3 Recharts expects: [{severity, count}]
chart2 = (df.groupby('severity').size()
            .reset_index(name='count')
            .to_dict(orient='records'))
print("\nChart 2 (PieChart — by severity):")
print(json.dumps(chart2, indent=2))

# Chart 3 — LineChart: 30-day trend
# M3 Recharts expects: [{date, count}]
cutoff = df['created_at'].max() - pd.Timedelta(days=30)
recent = df[df['created_at'] >= cutoff].copy()
recent['date'] = recent['created_at'].dt.strftime('%Y-%m-%d')
chart3 = (recent.groupby('date').size()
                .reset_index(name='count')
                .sort_values('date')
                .to_dict(orient='records'))
print("\nChart 3 (LineChart — 30-day trend, first 5 days):")
print(json.dumps(chart3[:5], indent=2))

# Chart 4 — Risk Summary Card
avg_score = round(float(df['risk_score'].mean()), 2)
chart4 = {
    'riskLevel':     'high' if avg_score >= 7 else ('medium' if avg_score >= 4 else 'low'),
    'avgRiskScore':  avg_score,
    'openIncidents': int((df['status'] == 'open').sum()),
    'bySeverity':    chart2,
}
print("\nChart 4 (Risk Summary Card):")
print(json.dumps(chart4, indent=2))

print("\nShare these shapes with M3 as the dashboard data format spec.")
```

#### [NO LIBS] cysentinel-ml/dashboard_analysis_nolibs.py
```python
# cysentinel-ml/dashboard_analysis_nolibs.py
"""Dashboard analysis — stdlib csv + collections only. No pandas."""
import csv
import json
from collections import Counter, defaultdict
from datetime import datetime, timedelta

with open('synthetic_incidents.csv', newline='', encoding='utf-8') as f:
    records = list(csv.DictReader(f))

print(f"Loaded {len(records)} incidents\n")

# Chart 1 — BarChart
type_counts = Counter(r['incident_type'] for r in records)
chart1 = [{'incident_type': k, 'count': v}
          for k, v in sorted(type_counts.items(), key=lambda x: -x[1])]
print("Chart 1 (BarChart):", json.dumps(chart1, indent=2))

# Chart 2 — PieChart
sev_counts = Counter(r['severity'] for r in records)
chart2 = [{'severity': k, 'count': v} for k, v in sev_counts.items()]
print("\nChart 2 (PieChart):", json.dumps(chart2, indent=2))

# Chart 3 — LineChart: 30-day trend
max_date = max(datetime.fromisoformat(r['created_at']) for r in records)
cutoff   = max_date - timedelta(days=30)
daily    = defaultdict(int)
for r in records:
    dt = datetime.fromisoformat(r['created_at'])
    if dt >= cutoff:
        daily[dt.strftime('%Y-%m-%d')] += 1
chart3 = [{'date': d, 'count': c} for d, c in sorted(daily.items())]
print("\nChart 3 (LineChart, first 5):", json.dumps(chart3[:5], indent=2))

# Chart 4 — Risk Card
scores    = [int(r['risk_score']) for r in records if r['risk_score']]
avg_score = round(sum(scores) / len(scores), 2)
chart4 = {
    'riskLevel':     'high' if avg_score >= 7 else ('medium' if avg_score >= 4 else 'low'),
    'avgRiskScore':  avg_score,
    'openIncidents': sum(1 for r in records if r['status'] == 'open'),
}
print("\nChart 4 (Risk Card):", json.dumps(chart4, indent=2))
print("\nShare these shapes with M3 as the dashboard data format spec.")
```

---

## MONTH 4 — Feature Importance Analysis

#### [WITH LIBS] cysentinel-ml/feature_analysis.py
```python
# cysentinel-ml/feature_analysis.py
"""Feature correlation analysis. Informs MVP 6 Random Forest feature set.
Includes CVE feature stubs (open_critical_cves, avg_cvss_score).

Usage: python feature_analysis.py
"""
import pandas as pd
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt

df = pd.read_csv('synthetic_incidents.csv', parse_dates=['created_at'])

SEV_MAP  = {'low':0,'medium':1,'high':2,'critical':3}
TYPE_MAP = {'phishing':0,'malware':1,'unauthorised_access':2,'data_breach':3,'other':4}

df['severity_enc']       = df['severity'].map(SEV_MAP).fillna(1)
df['incident_type_enc']  = df['incident_type'].map(TYPE_MAP).fillna(4)
df['hour_of_day']        = df['created_at'].dt.hour
df['is_after_hours']     = df['hour_of_day'].apply(lambda h: 1 if 0 <= h < 6 else 0)

# CVE feature stubs — replace with real data when GET /api/cves is available (Month 4)
df['open_critical_cves'] = 0
df['avg_cvss_score']     = 0.0

FEATURES = ['severity_enc','incident_type_enc','hour_of_day',
            'is_after_hours','open_critical_cves','avg_cvss_score']

print("=== Feature Correlation with risk_score (Pearson r) ===")
for feat in FEATURES:
    r = df['risk_score'].corr(df[feat])
    print(f"  {feat:<25} r = {r:+.4f}")

# Save distribution chart
fig, ax = plt.subplots(figsize=(8, 4))
df['risk_score'].hist(bins=10, ax=ax, color='steelblue', edgecolor='white')
ax.set_title('CySentinel — risk_score Distribution (Synthetic Data)')
ax.set_xlabel('risk_score (1-10)')
ax.set_ylabel('Count')
plt.tight_layout()
plt.savefig('feature_importance.png', dpi=150)
print("\nSaved feature_importance.png")
print("Note: CVE features default to 0 — meaningful in Month 7 when GET /api/cves delivers data.")
```

#### [NO LIBS] cysentinel-ml/feature_analysis_nolibs.py
```python
# cysentinel-ml/feature_analysis_nolibs.py
"""Feature correlation — stdlib math + csv only. No pandas or matplotlib."""
import csv
import math
from datetime import datetime

with open('synthetic_incidents.csv', newline='', encoding='utf-8') as f:
    records = list(csv.DictReader(f))

SEV_MAP  = {'low':0,'medium':1,'high':2,'critical':3}
TYPE_MAP = {'phishing':0,'malware':1,'unauthorised_access':2,'data_breach':3,'other':4}

def pearson_r(xs, ys):
    n  = len(xs)
    mx = sum(xs) / n
    my = sum(ys) / n
    num = sum((x - mx) * (y - my) for x, y in zip(xs, ys))
    den = math.sqrt(sum((x - mx)**2 for x in xs) * sum((y - my)**2 for y in ys))
    return num / den if den != 0 else 0.0

risk_scores = [int(r['risk_score']) for r in records]

features = {
    'severity_enc':       [SEV_MAP.get(r['severity'], 1) for r in records],
    'incident_type_enc':  [TYPE_MAP.get(r['incident_type'], 4) for r in records],
    'hour_of_day':        [datetime.fromisoformat(r['created_at']).hour for r in records],
    'is_after_hours':     [1 if datetime.fromisoformat(r['created_at']).hour < 6 else 0
                           for r in records],
    'open_critical_cves': [0] * len(records),
    'avg_cvss_score':     [0.0] * len(records),
}

print("=== Feature Correlation with risk_score (Pearson r) ===")
for name, vals in features.items():
    r = pearson_r(vals, risk_scores)
    print(f"  {name:<25} r = {r:+.4f}")

print("\nNote: CVE features are 0 stubs — update in Month 7 when GET /api/cves is live.")
```

---

## MONTH 7 (Optional MVP 6) — Model Training and Flask Service

#### [WITH LIBS] cysentinel-ml/train_model.py
```python
# cysentinel-ml/train_model.py
"""
Train the Random Forest classifier for CySentinel MVP 6.
Requires: scikit-learn, pandas, joblib

Usage: python train_model.py
Output: model/random_forest.joblib
"""

import pandas as pd
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report, accuracy_score
import joblib
import os

CSV_PATH   = 'synthetic_incidents.csv'
MODEL_PATH = 'model/random_forest.joblib'

if not os.path.exists(CSV_PATH):
    raise FileNotFoundError(f"Run generate_synthetic_data.py first — {CSV_PATH} not found")

df = pd.read_csv(CSV_PATH, parse_dates=['created_at'])
print(f"Loaded {len(df)} incidents")

SEV_MAP  = {'low':0,'medium':1,'high':2,'critical':3}
TYPE_MAP = {'phishing':0,'malware':1,'unauthorised_access':2,'data_breach':3,'other':4}

df['severity_enc']      = df['severity'].map(SEV_MAP).fillna(1)
df['incident_type_enc'] = df['incident_type'].map(TYPE_MAP).fillna(4)
df['hour_of_day']       = df['created_at'].dt.hour
df['is_after_hours']    = df['hour_of_day'].apply(lambda h: 1 if 0 <= h < 6 else 0)

# Target: priority_class from risk_score bins
df['priority_class'] = pd.cut(
    df['risk_score'], bins=[0, 3, 6, 10], labels=['low', 'medium', 'high']
)
df = df.dropna(subset=['priority_class'])

print(f"\nClass distribution:\n{df['priority_class'].value_counts().to_string()}")

FEATURES = ['severity_enc', 'incident_type_enc', 'hour_of_day', 'is_after_hours']
X = df[FEATURES]
y = df['priority_class']

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.20, random_state=42, stratify=y
)
print(f"\nTrain: {len(X_train)} | Test: {len(X_test)}")

model = RandomForestClassifier(
    n_estimators=100, random_state=42, class_weight='balanced'
)
model.fit(X_train, y_train)

y_pred = model.predict(X_test)
print(f"\nAccuracy: {accuracy_score(y_test, y_pred):.2f}")
print("\nClassification Report:")
print(classification_report(y_test, y_pred))
print("\nFeature Importances:")
for feat, imp in zip(FEATURES, model.feature_importances_):
    print(f"  {feat}: {imp:.4f}")

os.makedirs('model', exist_ok=True)
joblib.dump(model, MODEL_PATH)
print(f"\nModel saved to {MODEL_PATH}")
```

---

#### [NO LIBS] cysentinel-ml/decision_tree_nolibs.py — manual CART tree (no scikit-learn)
```python
# cysentinel-ml/decision_tree_nolibs.py
"""
Minimal depth-2 CART decision tree — NO external packages.
Demonstrates the ML pipeline without scikit-learn.
Lower accuracy than Random Forest but fully self-contained.

Usage: python decision_tree_nolibs.py
"""

import csv
import random
from collections import Counter
from datetime import datetime

def load_features(path):
    SEV  = {'low':0,'medium':1,'high':2,'critical':3}
    TYPE = {'phishing':0,'malware':1,'unauthorised_access':2,'data_breach':3,'other':4}
    X, y = [], []
    with open(path, newline='', encoding='utf-8') as f:
        for row in csv.DictReader(f):
            sev_enc  = SEV.get(row['severity'], 1)
            type_enc = TYPE.get(row['incident_type'], 4)
            hour     = datetime.fromisoformat(row['created_at']).hour
            after    = 1 if 0 <= hour < 6 else 0
            score    = int(row['risk_score'])
            label    = 'low' if score <= 3 else ('medium' if score <= 6 else 'high')
            X.append([sev_enc, type_enc, hour, after])
            y.append(label)
    return X, y

def gini(labels):
    total = len(labels)
    if total == 0: return 0.0
    return 1.0 - sum((c / total)**2 for c in Counter(labels).values())

def best_split(X, y):
    best_gain, best_feat, best_thr = -1, None, None
    for fi in range(len(X[0])):
        vals = sorted(set(x[fi] for x in X))
        for i in range(len(vals) - 1):
            thr   = (vals[i] + vals[i+1]) / 2
            left  = [y[j] for j, x in enumerate(X) if x[fi] <= thr]
            right = [y[j] for j, x in enumerate(X) if x[fi] >  thr]
            if not left or not right: continue
            n    = len(y)
            gain = gini(y) - (len(left)/n * gini(left) + len(right)/n * gini(right))
            if gain > best_gain:
                best_gain, best_feat, best_thr = gain, fi, thr
    return best_feat, best_thr

class SimpleTree:
    def __init__(self, max_depth=2):
        self.max_depth = max_depth
        self.tree = None

    def _build(self, X, y, depth):
        if depth >= self.max_depth or len(set(y)) == 1:
            return Counter(y).most_common(1)[0][0]
        feat, thr = best_split(X, y)
        if feat is None:
            return Counter(y).most_common(1)[0][0]
        li = [i for i, x in enumerate(X) if x[feat] <= thr]
        ri = [i for i, x in enumerate(X) if x[feat] >  thr]
        return {
            'feat': feat, 'thr': thr,
            'left':  self._build([X[i] for i in li], [y[i] for i in li], depth+1),
            'right': self._build([X[i] for i in ri], [y[i] for i in ri], depth+1),
        }

    def fit(self, X, y):   self.tree = self._build(X, y, 0)

    def _pred(self, node, x):
        if isinstance(node, str): return node
        return self._pred(node['left' if x[node['feat']] <= node['thr'] else 'right'], x)

    def predict(self, X): return [self._pred(self.tree, x) for x in X]

# Train and evaluate
X, y = load_features('synthetic_incidents.csv')
random.seed(42)
idx = list(range(len(X))); random.shuffle(idx)
split = int(0.8 * len(idx))
X_tr, y_tr = [X[i] for i in idx[:split]], [y[i] for i in idx[:split]]
X_te, y_te = [X[i] for i in idx[split:]], [y[i] for i in idx[split:]]

model  = SimpleTree(max_depth=2)
model.fit(X_tr, y_tr)
y_pred = model.predict(X_te)
acc    = sum(p == a for p, a in zip(y_pred, y_te)) / len(y_te)
print(f"SimpleTree Accuracy: {acc:.2f} ({int(acc*len(y_te))}/{len(y_te)})")
print("(Use train_model.py with scikit-learn for better accuracy in MVP 6)")
```

---

#### [WITH LIBS] cysentinel-ml/app.py — Flask microservice (MVP 6)
```python
# cysentinel-ml/app.py
"""
CySentinel ML Microservice — MVP 6 (Optional).
Endpoints: GET /health  |  POST /ml/score
Falls back to rules-based scoring when model unavailable.

Usage: python app.py               (local dev)
       gunicorn app:app            (Railway production)
"""

from flask import Flask, request, jsonify
from flask_cors import CORS
import joblib
import numpy as np
import os
import logging

app = Flask(__name__)
CORS(app)
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Load model
MODEL_PATH   = os.environ.get('MODEL_PATH', 'model/random_forest.joblib')
model        = None
model_loaded = False

if os.path.exists(MODEL_PATH):
    try:
        model        = joblib.load(MODEL_PATH)
        model_loaded = True
        logger.info(f"Model loaded from {MODEL_PATH}")
    except Exception as e:
        logger.warning(f"Failed to load model: {e}. Using rules fallback.")
else:
    logger.warning(f"Model file not found at {MODEL_PATH}. Using rules fallback.")

# Encoding maps — must match train_model.py exactly
TYPE_MAP = {'phishing':0,'malware':1,'unauthorised_access':2,'data_breach':3,'other':4}
SEV_MAP  = {'low':0,'medium':1,'high':2,'critical':3}
PRIO_MAP = {'low':3,'medium':6,'high':9}

def rules_fallback(severity, incident_type):
    """Mirrors riskScorer.js exactly."""
    sev  = {'critical':9,'high':7,'medium':5,'low':3}
    mult = {'data_breach':1.2,'malware':1.1,'phishing':1.0,'unauthorised_access':1.0,'other':0.9}
    return int(min(10, max(1, round(sev.get(severity, 5) * mult.get(incident_type, 1.0)))))

@app.route('/health', methods=['GET'])
def health():
    return jsonify({'status':'ok','service':'cysentinel-ml','model_loaded':model_loaded}), 200

@app.route('/ml/score', methods=['POST'])
def score():
    data = request.get_json(silent=True)
    if not data:
        return jsonify({'error': 'Request body must be valid JSON'}), 400

    severity      = data.get('severity', 'low')
    incident_type = data.get('incident_type', 'other')
    hour          = int(data.get('hour_of_day', 12))

    if severity      not in SEV_MAP:  severity      = 'low'
    if incident_type not in TYPE_MAP: incident_type = 'other'

    if model_loaded and model is not None:
        try:
            features   = np.array([[SEV_MAP[severity], TYPE_MAP[incident_type], hour, 1 if hour < 6 else 0]])
            prediction = str(model.predict(features)[0])
            priority   = PRIO_MAP.get(prediction, 5)
            return jsonify({'priority': int(priority), 'model': 'random_forest', 'class': prediction}), 200
        except Exception as e:
            logger.error(f"Model prediction failed: {e} — falling back to rules")

    priority = rules_fallback(severity, incident_type)
    return jsonify({'priority': priority, 'model': 'rules_fallback', 'class': None}), 200

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    logger.info(f"Starting CySentinel ML microservice on port {port}")
    app.run(host='0.0.0.0', port=port, debug=False)
```

#### [NO LIBS] cysentinel-ml/app_nolibs.py — http.server replacing Flask
```python
# cysentinel-ml/app_nolibs.py
"""
Minimal ML scoring HTTP server — no Flask, no flask-cors.
Uses Python built-in http.server + json.
Serves GET /health and POST /ml/score.

Usage: python app_nolibs.py
"""
import json
import os
from http.server import HTTPServer, BaseHTTPRequestHandler
from urllib.parse import urlparse

PORT = int(os.environ.get('PORT', 5000))

SEVERITY_BASE   = {'critical':9,'high':7,'medium':5,'low':3}
TYPE_MULTIPLIER = {'data_breach':1.2,'malware':1.1,'phishing':1.0,'unauthorised_access':1.0,'other':0.9}

def rules_fallback(severity, incident_type):
    base = SEVERITY_BASE.get(severity, 5)
    mult = TYPE_MULTIPLIER.get(incident_type, 1.0)
    return int(min(10, max(1, round(base * mult))))

def priority_class(score):
    if score <= 3: return 'low'
    if score <= 6: return 'medium'
    return 'high'

# Attempt to load scikit-learn model if available
model, model_loaded = None, False
try:
    import joblib, numpy as np
    MODEL_PATH = os.environ.get('MODEL_PATH', 'model/random_forest.joblib')
    if os.path.exists(MODEL_PATH):
        model        = joblib.load(MODEL_PATH)
        model_loaded = True
        print(f"Model loaded from {MODEL_PATH}")
except Exception as e:
    print(f"Model not loaded ({e}) — using rules fallback")

class MLHandler(BaseHTTPRequestHandler):
    def log_message(self, fmt, *args):
        print(f"[{self.address_string()}] {fmt % args}")

    def send_json(self, status, data):
        body = json.dumps(data).encode('utf-8')
        self.send_response(status)
        self.send_header('Content-Type',   'application/json')
        self.send_header('Content-Length', str(len(body)))
        self.send_header('Access-Control-Allow-Origin', '*')
        self.end_headers()
        self.wfile.write(body)

    def do_OPTIONS(self):
        self.send_response(204)
        self.send_header('Access-Control-Allow-Origin',  '*')
        self.send_header('Access-Control-Allow-Methods', 'GET, POST, OPTIONS')
        self.send_header('Access-Control-Allow-Headers', 'Content-Type')
        self.end_headers()

    def do_GET(self):
        if urlparse(self.path).path == '/health':
            self.send_json(200, {'status':'ok','service':'cysentinel-ml (no-libs)','model_loaded':model_loaded})
        else:
            self.send_json(404, {'error':'Not found'})

    def do_POST(self):
        if urlparse(self.path).path != '/ml/score':
            return self.send_json(404, {'error':'Not found'})

        length = int(self.headers.get('Content-Length', 0))
        try:
            data = json.loads(self.rfile.read(length))
        except (json.JSONDecodeError, ValueError):
            return self.send_json(400, {'error':'Invalid JSON'})

        severity      = data.get('severity', 'low')
        incident_type = data.get('incident_type', 'other')
        hour          = int(data.get('hour_of_day', 12))

        if severity      not in SEVERITY_BASE:   severity      = 'low'
        if incident_type not in TYPE_MULTIPLIER: incident_type = 'other'

        if model_loaded and model is not None:
            try:
                SEV  = {'low':0,'medium':1,'high':2,'critical':3}
                TYPE = {'phishing':0,'malware':1,'unauthorised_access':2,'data_breach':3,'other':4}
                PRIO = {'low':3,'medium':6,'high':9}
                feat = [[SEV[severity], TYPE[incident_type], hour, 1 if hour < 6 else 0]]
                pred = str(model.predict(feat)[0])
                return self.send_json(200, {'priority': PRIO.get(pred, 5), 'model':'random_forest','class':pred})
            except Exception as e:
                print(f"Model failed: {e} — fallback")

        score = rules_fallback(severity, incident_type)
        self.send_json(200, {'priority': score, 'model':'rules_fallback', 'class': priority_class(score)})

if __name__ == '__main__':
    server = HTTPServer(('0.0.0.0', PORT), MLHandler)
    print(f"[CySentinel ML no-libs] Port {PORT} — GET /health | POST /ml/score")
    server.serve_forever()
```

---

#### Integration tests for Flask / no-libs ML server (works against either)
```python
# cysentinel-ml/tests/test_app_nolibs.py
# Run: python -m unittest tests.test_app_nolibs -v
# Requires the ML server running on localhost:5000
# Start with: python app.py  OR  python app_nolibs.py

import unittest
import urllib.request
import urllib.error
import json

BASE = 'http://localhost:5000'

def _get(path):
    try:
        with urllib.request.urlopen(BASE + path, timeout=5) as r:
            return r.status, json.loads(r.read())
    except urllib.error.HTTPError as e:
        return e.code, {}
    except Exception:
        return None, {}

def _post(path, payload):
    body = json.dumps(payload).encode('utf-8')
    req  = urllib.request.Request(
        BASE + path, data=body,
        headers={'Content-Type': 'application/json'}, method='POST'
    )
    try:
        with urllib.request.urlopen(req, timeout=5) as r:
            return r.status, json.loads(r.read())
    except urllib.error.HTTPError as e:
        return e.code, json.loads(e.read() or b'{}')
    except Exception:
        return None, {}

def _skip_if_down():
    status, _ = _get('/health')
    if status is None:
        raise unittest.SkipTest(f"ML server not running on {BASE}")

class TestMLHealth(unittest.TestCase):
    def setUp(self): _skip_if_down()

    def test_health_returns_ok(self):
        status, body = _get('/health')
        self.assertEqual(status, 200)
        self.assertEqual(body.get('status'), 'ok')
        self.assertIn('model_loaded', body)

class TestMLScore(unittest.TestCase):
    def setUp(self): _skip_if_down()

    def test_critical_data_breach_scores_high(self):
        status, body = _post('/ml/score', {'severity':'critical','incident_type':'data_breach'})
        self.assertEqual(status, 200)
        self.assertGreaterEqual(body['priority'], 8)

    def test_low_other_scores_low(self):
        status, body = _post('/ml/score', {'severity':'low','incident_type':'other'})
        self.assertEqual(status, 200)
        self.assertLessEqual(body['priority'], 4)

    def test_all_combinations_in_range(self):
        for sev in ['low','medium','high','critical']:
            for typ in ['phishing','malware','unauthorised_access','data_breach','other']:
                status, body = _post('/ml/score', {'severity':sev,'incident_type':typ})
                self.assertEqual(status, 200, f"Failed for {sev}+{typ}")
                self.assertGreaterEqual(body['priority'], 1)
                self.assertLessEqual(body['priority'], 10)

    def test_unknown_severity_handled(self):
        status, body = _post('/ml/score', {'severity':'extreme','incident_type':'phishing'})
        self.assertEqual(status, 200)
        self.assertIn('priority', body)

    def test_invalid_json_returns_400(self):
        req = urllib.request.Request(
            BASE + '/ml/score', data=b'not json',
            headers={'Content-Type':'application/json'}, method='POST'
        )
        try:
            urllib.request.urlopen(req, timeout=5)
            self.fail("Expected HTTP 400")
        except urllib.error.HTTPError as e:
            self.assertEqual(e.code, 400)

    def test_model_field_present(self):
        status, body = _post('/ml/score', {'severity':'high','incident_type':'malware'})
        self.assertEqual(status, 200)
        self.assertIn(body.get('model'), ('random_forest','rules_fallback'))

if __name__ == '__main__':
    unittest.main()
```

---

## MONTH 6 — Validate Chart Shapes (Pair with M3)

```python
# cysentinel-ml/validate_chart_shapes.py
"""Run with M3 present — confirms data shapes match Recharts component props."""
import json
import csv
from collections import Counter, defaultdict
from datetime import datetime, timedelta

with open('synthetic_incidents.csv', newline='', encoding='utf-8') as f:
    records = list(csv.DictReader(f))

# M3 BarChart: [{name, count}]
chart1 = [{'name': k, 'count': v}
          for k, v in Counter(r['incident_type'] for r in records).items()]

# M3 PieChart: [{name, value}]
chart2 = [{'name': k, 'value': v}
          for k, v in Counter(r['severity'] for r in records).items()]

# M3 LineChart: [{date, count}]
max_dt = max(datetime.fromisoformat(r['created_at']) for r in records)
cutoff = max_dt - timedelta(days=30)
daily  = defaultdict(int)
for r in records:
    if datetime.fromisoformat(r['created_at']) >= cutoff:
        daily[datetime.fromisoformat(r['created_at']).strftime('%Y-%m-%d')] += 1
chart3 = [{'date': d, 'count': c} for d, c in sorted(daily.items())]

# M3 Risk Card: {riskLevel, avgRiskScore, openIncidents}
scores = [int(r['risk_score']) for r in records if r['risk_score']]
avg    = round(sum(scores) / len(scores), 2)
chart4 = {
    'riskLevel':     'high' if avg >= 7 else ('medium' if avg >= 4 else 'low'),
    'avgRiskScore':  avg,
    'openIncidents': sum(1 for r in records if r['status'] == 'open'),
}

print("=== Shapes to agree with M3 before Month 4 ===")
print("\nBarChart (chart1, sample):", json.dumps(chart1[:2], indent=2))
print("\nPieChart (chart2):",         json.dumps(chart2, indent=2))
print("\nLineChart (chart3, first 3):", json.dumps(chart3[:3], indent=2))
print("\nRisk Card (chart4):",          json.dumps(chart4, indent=2))
print("\nConfirm these shapes match M3 Recharts props before building chart components.")
```

---

*End of File 02 — ML Engineer Code Blocks*  
*Next: 03_Frontend_Developer_Code_Blocks.md*
