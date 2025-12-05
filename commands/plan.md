---
model: claude-sonnet-4-0
---

# /plan

**Purpose**: Transform a feature idea into an actionable implementation plan

**Usage**:
```bash
/plan <feature-description>
/plan "Add user authentication with OAuth2 and JWT tokens"
/plan Add real-time notifications using WebSockets
```

## What It Does

1. Searches codebase for related existing code and patterns
2. Researches best practices (if needed)
3. Generates structured plan with acceptance criteria
4. Offers to create GitHub issue or save locally

## Implementation

[Use extended thinking for thorough planning]

### Step 1: Understand the Feature

Parse `$ARGUMENTS` to extract:
- Core feature description
- Any constraints mentioned
- Implied requirements

If description is vague, ask clarifying questions:
- What problem does this solve?
- Who is the user?
- Any technical constraints?

### Step 2: Research Codebase

Use Glob and Grep to find:
- Similar existing features (patterns to follow)
- Related code that will be affected
- Existing utilities/helpers to reuse
- Test patterns used in similar features

Example searches:
```
Glob: **/*auth*.ts, **/*login*.tsx
Grep: "JWT", "OAuth", "session"
```

### Step 3: Research Best Practices (if needed)

For complex features, use WebSearch to find:
- Security best practices (for auth, data handling)
- Performance patterns (for real-time, caching)
- Industry standards (for APIs, protocols)

### Step 4: Generate Plan

Create structured plan document:

```markdown
# [Feature Name]

## Summary
**What**: One paragraph describing the feature
**Why**: Business/user value this provides
**Scope**: What's included and explicitly excluded

## Technical Approach

### Architecture
- High-level design decisions
- Components involved
- Data flow

### Key Decisions
| Decision | Choice | Rationale |
|----------|--------|-----------|
| Auth method | JWT | Stateless, scales well |
| Storage | httpOnly cookie | XSS protection |

### Files to Create/Modify
| File | Action | Purpose |
|------|--------|---------|
| `src/auth/oauth.ts` | Create | OAuth callback handler |
| `src/middleware/auth.ts` | Modify | Add JWT validation |

## Acceptance Criteria
- [ ] User can login with Google
- [ ] User can login with GitHub
- [ ] JWT expires after 1 hour
- [ ] Refresh token rotates on use
- [ ] Logout clears all tokens
- [ ] Unit tests cover happy path
- [ ] Integration tests cover OAuth flow

## Implementation Steps

### Phase 1: Foundation
1. Set up OAuth app credentials in providers
2. Create environment variables
3. Implement callback handler skeleton

### Phase 2: Core Logic
4. Implement JWT generation
5. Implement JWT validation middleware
6. Add refresh token rotation

### Phase 3: Integration
7. Connect to existing user model
8. Add logout endpoint
9. Update frontend auth state

### Phase 4: Quality
10. Write unit tests
11. Write integration tests
12. Manual testing checklist

## Testing Strategy
- Unit: JWT generation/validation functions
- Integration: Full OAuth flow with mocked provider
- E2E: Real login flow (manual or Playwright)

## Rollout Plan
1. Deploy behind feature flag
2. Internal testing
3. Beta users (10%)
4. Full rollout

## Risks & Mitigations
| Risk | Impact | Mitigation |
|------|--------|------------|
| Token theft via XSS | High | httpOnly cookies, CSP headers |
| OAuth provider downtime | Medium | Graceful error handling, fallback |

## Open Questions
- [ ] Which OAuth providers to support initially?
- [ ] Token expiry duration preference?

## Dependencies
- External: Google OAuth, GitHub OAuth
- Internal: User model, session middleware

## References
- [OAuth 2.0 Spec](https://oauth.net/2/)
- [JWT Best Practices](https://auth0.com/blog/jwt-security-best-practices/)
```

### Step 5: Offer Output Options

Present options:
```
Plan generated! What would you like to do?

1. Create GitHub Issue
   → gh issue create --title "[Feature Name]" --body "..."

2. Save Locally
   → docs/plans/YYYY-MM-DD-feature-name.md

3. Display Only
   → Show in chat (already done)

4. Refine Plan
   → Ask questions or request changes
```

If user selects GitHub issue:
```bash
gh issue create --title "Feature: [Name]" --body "$(cat <<'EOF'
[PLAN CONTENT]
EOF
)"
```

If user selects local save:
- Create directory if needed: `mkdir -p docs/plans`
- Save with timestamp: `docs/plans/2025-12-05-oauth-authentication.md`

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--minimal` | Quick plan (summary, criteria, steps only) | false |
| `--detailed` | Full plan with all sections | true |
| `--issue` | Create GitHub issue immediately | false |
