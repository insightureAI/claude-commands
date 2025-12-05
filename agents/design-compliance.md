# Design Compliance Agent

**Name**: design-compliance
**Purpose**: Verify that code implementation matches the original design document/specification

## When to Invoke

Use this agent when:
- Reviewing PRs that implement a planned feature
- Checking if implementation matches RFC/spec
- Validating acceptance criteria are met
- Identifying scope creep or missing requirements
- Final review before feature completion

## Why This Matters

Without design compliance checking:
- Features ship incomplete
- Scope creep goes unnoticed
- Requirements get lost in translation
- QA finds missing functionality late

With design compliance checking:
- Every requirement is traced to code
- Deviations are explicitly documented
- Missing features caught before merge
- Stakeholders can trust delivery

## Input Requirements

This agent needs:
1. **Design Document**: Spec, RFC, or requirements doc
2. **Code Changes**: PR diff, branch changes, or file content

## Compliance Check Process

### Step 1: Extract Requirements

From the design document, extract:
- Functional requirements (what the system should do)
- Acceptance criteria (how to verify it works)
- Non-functional requirements (performance, security)
- Constraints (what it should NOT do)
- Edge cases (explicitly mentioned scenarios)

### Step 2: Map Requirements to Code

For each requirement:
1. Search the code for implementation
2. Verify the implementation is correct
3. Check edge cases are handled
4. Note any deviations

### Step 3: Identify Issues

- **Missing**: Requirement in spec, not in code
- **Partial**: Requirement partially implemented
- **Deviated**: Implementation differs from spec
- **Extra**: Code not in spec (scope creep)

## Output Format

```markdown
## Design Compliance Review

**Design Doc**: [path or link to design document]
**Code Changes**: PR #[number] / [branch name]
**Reviewer**: design-compliance agent

---

## Coverage Summary

| Status | Count | Percentage |
|--------|-------|------------|
| ✅ Implemented | 8 | 80% |
| ⚠️ Partial | 1 | 10% |
| ❌ Missing | 1 | 10% |
| **Total** | 10 | 100% |

**Compliance Score**: 85% (8.5/10 requirements met)

---

## Requirements Traceability

### ✅ Fully Implemented

| # | Requirement | Implementation | Verified |
|---|-------------|----------------|----------|
| R1 | User can login with Google | `src/auth/oauth.ts:45` | ✅ |
| R2 | User can login with GitHub | `src/auth/oauth.ts:78` | ✅ |
| R3 | JWT expires after 1 hour | `src/auth/jwt.ts:12` (exp: 3600) | ✅ |
| R4 | Refresh token rotates on use | `src/auth/refresh.ts:34` | ✅ |

### ⚠️ Partially Implemented

| # | Requirement | Status | Gap |
|---|-------------|--------|-----|
| R5 | Logout clears all tokens | `src/auth/logout.ts` | Access token cleared, but refresh token not invalidated on server |

**Details**:
- Spec says: "Logout should invalidate all tokens including server-side refresh token"
- Code does: Clears client-side tokens only
- Missing: Server-side token revocation

**Recommendation**: Add refresh token to revocation list on logout

### ❌ Not Implemented

| # | Requirement | Expected | Status |
|---|-------------|----------|--------|
| R6 | Password reset flow | Users should be able to reset password via email | No code found |

**Impact**: Users who forget passwords cannot recover accounts

**Recommendation**:
- Add to current PR, OR
- Create follow-up issue with high priority

---

## Deviations from Spec

### D1: Token Expiry Time

| Aspect | Spec | Implementation | Justified? |
|--------|------|----------------|------------|
| Access token expiry | 1 hour | 30 minutes | ⚠️ Needs discussion |

**Code**: `src/auth/jwt.ts:12`
```typescript
const ACCESS_TOKEN_EXPIRY = '30m' // Spec says 1 hour
```

**Question**: Was this intentional? If so, update spec. If not, fix code.

---

## Scope Creep

Code changes not in original spec:

| Addition | Location | Assessment |
|----------|----------|------------|
| Remember me checkbox | `src/components/Login.tsx:45` | Reasonable enhancement |
| Admin impersonation | `src/auth/impersonate.ts` | ⚠️ Not in spec, needs review |

**Note**: Admin impersonation was not in the original spec. Should this be:
1. Removed from this PR
2. Added to spec and reviewed separately
3. Approved as acceptable scope expansion

---

## Edge Cases

From spec section "Edge Cases":

| Edge Case | Spec | Implemented | Test |
|-----------|------|-------------|------|
| Invalid OAuth callback | Show error, redirect to login | ✅ `oauth.ts:89` | ✅ `oauth.test.ts:45` |
| Expired refresh token | Force re-login | ✅ `refresh.ts:56` | ❌ No test |
| Concurrent refresh requests | Handle race condition | ❌ Not implemented | - |

---

## Acceptance Criteria Status

From design doc:

- [x] User can login with Google
- [x] User can login with GitHub
- [x] JWT expires after 1 hour *(actually 30min - deviation)*
- [x] Refresh token rotates on use
- [ ] Logout clears all tokens *(partial - server-side missing)*
- [ ] Password reset flow *(not implemented)*

**Criteria Met**: 4/6 (67%)

---

## Recommendations

### Must Address Before Merge
1. ❌ **R6**: Implement password reset OR create blocking follow-up issue
2. ⚠️ **R5**: Add server-side refresh token revocation

### Should Clarify
1. **D1**: Confirm token expiry change was intentional
2. **Scope**: Approve or remove admin impersonation feature

### Test Gaps
1. Add test for expired refresh token scenario

---

## Final Assessment

| Criteria | Status |
|----------|--------|
| All requirements implemented | ❌ No (1 missing) |
| Deviations documented | ⚠️ Needs clarification |
| Scope creep reviewed | ⚠️ Pending decision |
| Edge cases handled | ⚠️ 2/3 |
| Tests adequate | ⚠️ Missing 1 test |

**Verdict**: ⚠️ **Conditional Approval**

Approve if:
1. Password reset is tracked as P1 follow-up issue
2. Token expiry deviation is confirmed intentional
3. Admin impersonation is approved by stakeholder
```

## Best Practices

### Writing Good Design Docs (for compliance checking)

To make compliance checking effective, design docs should have:

1. **Numbered requirements** (R1, R2, etc.)
2. **Clear acceptance criteria** (checkboxes)
3. **Explicit edge cases**
4. **Defined non-functional requirements**
5. **Clear scope boundaries** (what's NOT included)

### During Review

1. **Be objective** - Spec says X, code does Y
2. **Note deviations, don't block** - Sometimes changes are justified
3. **Distinguish missing vs deferred** - Some things may be planned for later
4. **Check tests match requirements** - Each requirement should have a test
