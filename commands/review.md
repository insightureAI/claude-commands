---
model: claude-sonnet-4-0
---

# /review

**Purpose**: Multi-perspective code review with design doc compliance checking

**Usage**:
```bash
/review <PR-number>                      # Review PR
/review <PR-number> --design <doc-path>  # Review with design doc compliance
/review <file-or-directory>              # Review specific code
/review current                          # Review current branch vs main
```

## What Makes This Different

Most review tools check code quality. This one also checks **if the code matches the original spec**.

## Implementation

[Use extended thinking for comprehensive analysis]

### Step 1: Determine Review Target

Parse `$ARGUMENTS` to identify:
- PR number → Fetch via `gh pr view <number> --json files,body,title,additions,deletions`
- File path → Read directly
- "current" → Compare current branch to main via `git diff main...HEAD`
- `--design <path>` → Load design doc for compliance checking

### Step 2: Detect Design Doc

If `--design` flag provided, use that path.

Otherwise, auto-search in order:
1. PR body for "Design Doc:" or "Spec:" links
2. `docs/design/` directory for matching feature name
3. `docs/rfc/` directory
4. `docs/specs/` directory

If no design doc found, note this in output and proceed with other review passes.

### Step 3: Run 5 Review Passes

Execute sequentially using Task tool with specialized prompts:

#### Pass 1: Design Compliance (if design doc available)

Use Task tool with subagent_type="general-purpose":

**Prompt**:
```
You are a requirements analyst. Compare this code change against the design document.

Design Document:
[INSERT DESIGN DOC CONTENT]

Code Changes:
[INSERT DIFF OR FILE CONTENT]

For each requirement/acceptance criterion in the design doc:
1. Mark as ✅ if implemented correctly
2. Mark as ❌ if missing or incorrectly implemented
3. Mark as ⚠️ if partially implemented

Also identify:
- Scope creep: Code that wasn't in the spec
- Deviations: Implementation differs from spec (note if justified)

Output format:
## Design Compliance
**Coverage**: X/Y requirements met

### Requirements Status
- ✅ [Requirement 1] - Implemented in `file.ts:line`
- ❌ [Requirement 2] - Not found in code
- ⚠️ [Requirement 3] - Partial: missing edge case handling

### Scope Creep
- `newFeature()` in file.ts - Not in original spec

### Deviations
- Spec said X, code does Y - [Justified/Needs Discussion]
```

#### Pass 2: Security Review

Use Task tool with subagent_type="general-purpose":

**Prompt**:
```
You are a security auditor. Review this code for vulnerabilities.

Code:
[INSERT CODE]

Check for:
- SQL/NoSQL injection
- XSS (reflected, stored, DOM-based)
- Authentication/authorization flaws
- Hardcoded secrets or credentials
- CSRF vulnerabilities
- Insecure direct object references
- Input validation gaps
- Cryptography misuse

For each finding:
1. Severity: 🔴 Critical / 🟠 High / 🟡 Medium / 🔵 Low
2. Location: file:line
3. Issue: What's wrong
4. Attack: How it could be exploited
5. Fix: Specific remediation

Output format:
## Security Review
**Risk Level**: [🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low]

### Findings
1. 🔴 **SQL Injection** - `src/db/users.ts:34`
   - Issue: User input concatenated into query
   - Attack: `'; DROP TABLE users; --`
   - Fix: Use parameterized query

### Verified Secure
- JWT tokens use RS256 ✓
- Passwords hashed with bcrypt ✓
```

#### Pass 3: Performance Review

Use Task tool with subagent_type="general-purpose":

**Prompt**:
```
You are a performance engineer. Review this code for efficiency issues.

Code:
[INSERT CODE]

Check for:
- N+1 query patterns
- Missing database indexes (suggest based on queries)
- Synchronous operations that should be async
- Memory leaks (unclosed resources, growing collections)
- Unnecessary re-renders (React) or recomputation
- Large payload sizes
- Missing pagination
- Inefficient algorithms (O(n²) when O(n) possible)

For each finding:
1. Impact: High/Medium/Low
2. Location: file:line
3. Issue: What's inefficient
4. Suggestion: How to optimize

Output format:
## Performance Review

### Issues Found
1. ⚡ **N+1 Query** - `src/api/users.ts:45`
   - Issue: Fetching related data in loop
   - Impact: ~100ms per item at scale
   - Fix: Use JOIN or batch fetch

### Recommendations
- Add index on `users.email` (queried frequently)
```

#### Pass 4: Architecture Review

Use Task tool with subagent_type="general-purpose":

**Prompt**:
```
You are a software architect. Review this code for structural issues.

Code:
[INSERT CODE]

Check for:
- SOLID principle violations
- Tight coupling between modules
- Missing abstraction layers
- God classes/functions (too many responsibilities)
- Circular dependencies
- Layer violations (e.g., UI calling DB directly)
- Inconsistency with existing codebase patterns

For each finding:
1. Severity: High/Medium/Low
2. Location: file:line
3. Issue: What's wrong architecturally
4. Suggestion: How to restructure

Output format:
## Architecture Review

### Issues
1. 🏗️ **God Class** - `src/services/OrderService.ts`
   - Issue: 800 lines, handles ordering, payments, notifications
   - Suggestion: Split into OrderService, PaymentService, NotificationService

### Positive Patterns
- Clean separation between API and business logic ✓
```

#### Pass 5: Code Quality Review

Use Task tool with subagent_type="general-purpose":

**Prompt**:
```
You are a senior developer. Review this code for quality and maintainability.

Code:
[INSERT CODE]

Check for:
- Unclear naming (variables, functions, classes)
- Missing or excessive comments
- Code duplication
- Dead code
- Overly complex logic (high cyclomatic complexity)
- Missing error handling
- Test coverage gaps
- Style inconsistencies

For each finding:
1. Type: Naming/Duplication/Complexity/Testing/Style
2. Location: file:line
3. Issue: What's wrong
4. Suggestion: How to improve

Output format:
## Code Quality Review

### Issues
1. 📝 **Unclear Naming** - `src/utils/helpers.ts:12`
   - `processData()` - What data? What processing?
   - Suggestion: `validateUserInput()` or `transformOrderPayload()`

### Test Coverage
- New code has X% coverage
- Missing tests for: [list edge cases]

### Positive Notes
- Consistent error handling pattern ✓
```

### Step 4: Consolidate Results

Combine all pass results into final report:

```markdown
# Code Review Summary

**Target**: PR #[number] / [file/branch]
**Design Doc**: [path or "Not found"]
**Overall Risk**: 🔴/🟠/🟡/🟢

---

## Design Compliance
[Pass 1 results - if design doc available]

---

## Critical Issues (Must Fix Before Merge)
[Aggregate 🔴 items from all passes]

1. 🔒 [Security issue]
2. 📋 [Missing requirement]

---

## Recommended Changes
[Aggregate 🟠/🟡 items]

1. ⚡ [Performance issue]
2. 🏗️ [Architecture issue]

---

## Suggestions (Nice to Have)
[Lower priority items]

---

## Approval Status

| Perspective | Status |
|-------------|--------|
| Design Compliance | ✅ Approved / ⚠️ Needs Discussion / ❌ Needs Changes |
| Security | ✅ / ⚠️ / ❌ |
| Performance | ✅ / ⚠️ / ❌ |
| Architecture | ✅ / ⚠️ / ❌ |
| Code Quality | ✅ / ⚠️ / ❌ |

**Overall**: ✅ Approve / ⚠️ Approve with Comments / ❌ Request Changes
```

### Step 5: Offer Actions

After presenting review, ask:
```
What would you like to do?
1. Post as PR comment (gh pr comment)
2. Create issues for findings
3. Re-review specific section
4. Discuss a finding
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--design <path>` | Path to design doc for compliance check | Auto-detect |
| `--security-only` | Run only security pass | false |
| `--quick` | Skip architecture and quality passes | false |
