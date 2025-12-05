---
model: claude-sonnet-4-0
---

# /tech-debt

**Purpose**: Assess technical debt with severity scoring and prioritized remediation plan

**Usage**:
```bash
/tech-debt                 # Analyze entire codebase
/tech-debt src/            # Analyze specific directory
/tech-debt src/legacy/     # Focus on known problem area
```

## What It Does

1. Scans for code smells and debt indicators
2. Categorizes findings by severity
3. Calculates priority score for each item
4. Generates remediation roadmap

## Implementation

[Use extended thinking for thorough analysis]

### Step 1: Automated Scans

Run these searches to identify debt:

**TODO/FIXME/HACK comments**:
```bash
grep -rn "TODO\|FIXME\|HACK\|XXX\|TEMP" --include="*.ts" --include="*.js" --include="*.py"
```

**Large files** (>500 lines):
```bash
find . -name "*.ts" -o -name "*.js" -o -name "*.py" | xargs wc -l | sort -rn | head -20
```

**Deeply nested code** (search for 4+ levels of indentation):
```bash
grep -rn "^            " --include="*.ts" --include="*.js"
```

**High import counts** (coupling indicator):
```bash
grep -c "^import\|^from" *.ts | sort -t: -k2 -rn | head -20
```

**Old files** (untouched 6+ months):
```bash
find . -name "*.ts" -mtime +180 -type f
```

**Potential duplication** (similar function names):
Use Grep to find repeated patterns.

### Step 2: Categorize Findings

| Category | Indicators | Risk Level |
|----------|------------|------------|
| 🔴 Critical | Security holes, data corruption risks, blocking bugs | Must fix now |
| 🟠 Significant | Missing tests on critical paths, tight coupling, outdated deps | Fix this sprint |
| 🟡 Moderate | Duplication, outdated patterns, minor complexity | Fix this quarter |
| 🟢 Minor | Style issues, old TODOs, naming inconsistencies | Fix opportunistically |

### Step 3: Calculate Priority Score

For each finding, calculate:

```
Priority Score = (Impact × Likelihood) / Effort

Impact (1-5): How bad if this causes a problem?
Likelihood (1-5): How likely to cause a problem?
Effort (1-5): How hard to fix? (1=easy, 5=hard)
```

Higher score = fix first

Example:
- SQL injection in user API: (5 × 5) / 2 = **12.5** (Critical, easy fix)
- 800-line god class: (3 × 4) / 8 = **1.5** (Important but big effort)

### Step 4: Generate Report

```markdown
# Technical Debt Assessment

**Scope**: [path analyzed]
**Date**: [today]
**Overall Health**: 🔴 Poor / 🟠 Fair / 🟡 Moderate / 🟢 Good

## Executive Summary

| Category | Count | Estimated Effort |
|----------|-------|------------------|
| 🔴 Critical | 2 | 4 hours |
| 🟠 Significant | 8 | 3 days |
| 🟡 Moderate | 15 | 2 weeks |
| 🟢 Minor | 23 | 1 week |
| **Total** | 48 | ~4 weeks |

---

## Top Priority Items

| # | Issue | Location | Score | Effort | Category |
|---|-------|----------|-------|--------|----------|
| 1 | No input validation on user API | `src/api/users.ts:23` | 12.5 | 2h | 🔴 Security |
| 2 | SQL concatenation | `src/db/queries.ts:89` | 10.0 | 1h | 🔴 Security |
| 3 | 800-line OrderService | `src/services/order.ts` | 4.0 | 8h | 🟠 Complexity |
| 4 | No tests for payment flow | `src/services/payment.ts` | 3.8 | 4h | 🟠 Testing |
| 5 | Duplicated validation logic | `src/api/*.ts` | 2.5 | 3h | 🟡 Duplication |

---

## Quick Wins (< 1 day total)

Items with high impact but low effort:

1. **Add input validation to user API** - 2h
   - Location: `src/api/users.ts:23`
   - Fix: Add zod schema validation

2. **Fix SQL concatenation** - 1h
   - Location: `src/db/queries.ts:89`
   - Fix: Use parameterized queries

3. **Remove unused imports** - 30m
   - 12 files with dead imports
   - Fix: Run `eslint --fix`

---

## Sprint-Sized Work (1-3 days each)

1. **Split OrderService into smaller services** - 8h
   - Current: 800 lines, handles orders, payments, notifications
   - Target: OrderService, PaymentService, NotificationService
   - Approach: Extract one service at a time with tests

2. **Add tests for payment flow** - 4h
   - Current coverage: 0%
   - Target coverage: 80%
   - Critical paths: charge, refund, webhook handling

---

## Detailed Findings

### 🔴 Critical (Must Fix)

#### 1. SQL Injection Risk
- **Location**: `src/db/queries.ts:89`
- **Issue**: User input concatenated into SQL query
- **Impact**: Full database compromise possible
- **Fix**: Use parameterized queries
```typescript
// Before (vulnerable)
db.query(`SELECT * FROM users WHERE id = ${userId}`)

// After (safe)
db.query('SELECT * FROM users WHERE id = $1', [userId])
```

### 🟠 Significant (Fix Soon)

#### 1. God Class: OrderService
- **Location**: `src/services/order.ts`
- **Issue**: 800 lines, 15+ methods, mixed responsibilities
- **Impact**: Hard to test, change, understand
- **Fix**: Extract PaymentService, NotificationService

[Continue for each category...]

---

## Trends

If this is a repeat scan, show trends:
- Debt score: 48 → 42 (improving)
- Critical items: 5 → 2 (improving)
- New items this month: 3

---

## Recommended Roadmap

### This Week
- [ ] Fix all Critical items (4h)

### This Sprint
- [ ] Fix Significant items (3d)
- [ ] Establish testing baseline

### This Quarter
- [ ] Address Moderate items
- [ ] Set up automated debt tracking

---

## Prevention Recommendations

1. **Add pre-commit hooks** for lint/security checks
2. **Set complexity limits** in ESLint (max-lines, max-depth)
3. **Require tests** for new code (coverage gate)
4. **Monthly debt review** - run this command monthly
```

### Step 5: Offer Actions

```
What would you like to do?

1. Create GitHub issues for top items
2. Export full report to docs/tech-debt/YYYY-MM-DD.md
3. Focus on specific category (Critical/Significant/etc)
4. Generate fix PR for quick wins
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--critical-only` | Show only critical items | false |
| `--quick-wins` | Show only items < 2h effort | false |
| `--json` | Output as JSON for tooling | false |
