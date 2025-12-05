---
disable-model-invocation: true
---

# [Feature Name]

> **Status**: draft | review | approved | implemented
> **Author**: [name]
> **Created**: YYYY-MM-DD
> **Last Updated**: YYYY-MM-DD

## Summary

**What**: One paragraph describing what this feature does

**Why**: Business value and user benefit

**Scope**: What's included and explicitly NOT included

---

## Requirements

### Functional Requirements

| ID | Requirement | Priority |
|----|-------------|----------|
| R1 | [Requirement description] | Must Have |
| R2 | [Requirement description] | Must Have |
| R3 | [Requirement description] | Should Have |
| R4 | [Requirement description] | Nice to Have |

### Non-Functional Requirements

| ID | Requirement | Target |
|----|-------------|--------|
| NF1 | Performance | Response time < 200ms |
| NF2 | Scalability | Support 10k concurrent users |
| NF3 | Security | [Specific security requirement] |

---

## Technical Design

### Architecture

[High-level architecture diagram or description]

```
┌─────────┐     ┌─────────┐     ┌─────────┐
│ Client  │────▶│   API   │────▶│   DB    │
└─────────┘     └─────────┘     └─────────┘
```

### Key Components

| Component | Purpose | Technology |
|-----------|---------|------------|
| [Name] | [What it does] | [Tech choice] |

### Data Model

```sql
-- New tables or changes
CREATE TABLE example (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### API Design

```
POST /api/v1/resource
Request:
{
  "field": "value"
}

Response:
{
  "id": "uuid",
  "field": "value"
}
```

---

## Files to Create/Modify

| File | Action | Purpose |
|------|--------|---------|
| `src/api/feature.ts` | Create | API endpoints |
| `src/services/feature.ts` | Create | Business logic |
| `src/models/feature.ts` | Create | Data model |
| `src/middleware/auth.ts` | Modify | Add new permission |

---

## Acceptance Criteria

- [ ] [Criteria 1 - specific and testable]
- [ ] [Criteria 2 - specific and testable]
- [ ] [Criteria 3 - specific and testable]
- [ ] Unit tests cover happy path
- [ ] Integration tests cover API
- [ ] Documentation updated

---

## Edge Cases

| Scenario | Expected Behavior |
|----------|-------------------|
| [Edge case 1] | [What should happen] |
| [Edge case 2] | [What should happen] |
| [Invalid input] | [Error handling] |

---

## Security Considerations

- [ ] Authentication required for all endpoints
- [ ] Authorization checks for [specific actions]
- [ ] Input validation on [fields]
- [ ] [Other security considerations]

---

## Testing Strategy

### Unit Tests
- [What to unit test]

### Integration Tests
- [What to integration test]

### Manual Testing
- [ ] [Manual test scenario 1]
- [ ] [Manual test scenario 2]

---

## Rollout Plan

1. **Phase 1**: Deploy behind feature flag
2. **Phase 2**: Internal testing
3. **Phase 3**: Beta users (10%)
4. **Phase 4**: Full rollout

### Feature Flag
```
FEATURE_[NAME]_ENABLED=true
```

### Rollback Plan
[How to rollback if issues are found]

---

## Risks & Mitigations

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| [Risk 1] | High | Medium | [How to mitigate] |
| [Risk 2] | Medium | Low | [How to mitigate] |

---

## Dependencies

### External
- [External service/API]

### Internal
- [Other feature/service]

### Blockers
- [ ] [Blocker 1]
- [ ] [Blocker 2]

---

## Open Questions

- [ ] [Question 1]?
- [ ] [Question 2]?

---

## References

- [Link to related doc]
- [Link to external resource]

---

## Changelog

| Date | Author | Change |
|------|--------|--------|
| YYYY-MM-DD | [name] | Initial draft |
