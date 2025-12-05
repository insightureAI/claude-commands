---
model: claude-sonnet-4-0
---

# /pr-enhance

**Purpose**: Auto-generate comprehensive PR description and checklist

**Usage**:
```bash
/pr-enhance              # Enhance current branch's PR
/pr-enhance 123          # Enhance existing PR #123
/pr-enhance --create     # Create new PR with enhanced description
```

## What It Does

1. Analyzes diff (files changed, additions/deletions)
2. Reads commit messages for context
3. Determines change type (feature/fix/refactor/docs/etc)
4. Generates comprehensive PR template
5. Adds relevant checklists based on what changed

## Implementation

### Step 1: Gather PR Context

**For current branch**:
```bash
# Get branch name
git branch --show-current

# Get diff against main
git diff main...HEAD --stat
git diff main...HEAD

# Get commit messages
git log main..HEAD --pretty=format:"%s%n%b"
```

**For existing PR**:
```bash
gh pr view 123 --json title,body,files,additions,deletions,commits
```

### Step 2: Analyze Changes

Categorize files changed:
- `src/**/*.ts` → Source code
- `**/*.test.ts`, `**/*.spec.ts` → Tests
- `*.json`, `*.yaml`, `*.env*` → Config
- `*.md`, `docs/**` → Documentation
- `Dockerfile`, `*.yml` (CI) → DevOps
- `*.css`, `*.scss` → Styles

Calculate metrics:
- Total lines changed
- Files by category
- Test coverage delta (if detectable)

### Step 3: Determine Change Type

Based on commits and files:
- `feat:` or new files → 🆕 Feature
- `fix:` or bug keywords → 🐛 Bug Fix
- `refactor:` or restructuring → ♻️ Refactor
- `docs:` or only .md files → 📝 Documentation
- `test:` or only test files → 🧪 Tests
- `chore:` or config only → 🔧 Chore
- `perf:` or optimization → ⚡ Performance

### Step 4: Generate PR Description

```markdown
## Summary

[2-3 sentence description of what this PR does and why]

Closes #[issue-number]

---

## Changes

### What
- [Bullet point of each logical change]
- [Group related file changes together]

### Why
[1-2 sentences on the motivation/context]

---

## Type of Change

- [ ] 🆕 New feature (non-breaking change adding functionality)
- [ ] 🐛 Bug fix (non-breaking change fixing an issue)
- [ ] 💥 Breaking change (fix or feature causing existing functionality to change)
- [ ] ♻️ Refactor (no functional changes)
- [ ] 📝 Documentation
- [ ] 🧪 Tests
- [ ] 🔧 Chore (build, CI, dependencies)

---

## Testing

### How to Test
1. [Step-by-step instructions]
2. [Include specific inputs/actions]
3. [Expected outcomes]

### Test Coverage
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

**Coverage**: [X%] → [Y%] ([+/-Z%])

---

## Checklist

### Code Quality
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] No console.logs or debug code

### Security (if applicable)
- [ ] No secrets or credentials committed
- [ ] Input validation added
- [ ] Authentication/authorization verified
- [ ] SQL injection prevented
- [ ] XSS prevented

### Documentation (if applicable)
- [ ] README updated
- [ ] API docs updated
- [ ] Inline comments added
- [ ] CHANGELOG updated

### Database (if applicable)
- [ ] Migrations are reversible
- [ ] Indexes added for new queries
- [ ] No breaking schema changes (or migration plan documented)

---

## Screenshots

<!-- Add before/after screenshots for UI changes -->

| Before | After |
|--------|-------|
| [screenshot] | [screenshot] |

---

## Dependencies

### New Dependencies
- `package-name@version` - [why needed]

### Removed Dependencies
- `old-package` - [why removed]

---

## Deployment Notes

<!-- Any special deployment considerations -->

- [ ] Environment variables needed: `NEW_VAR`
- [ ] Database migration required
- [ ] Feature flag: `FEATURE_X_ENABLED`
- [ ] Backward compatible: Yes/No

---

## Related

- Closes #[issue]
- Related to #[PR]
- Depends on #[PR]
```

### Step 5: Smart Checklist Selection

Only include relevant checklist sections:

| If PR Contains | Include Checklist |
|----------------|-------------------|
| Any code changes | Code Quality |
| Auth, API, user input | Security |
| .md files, docs/ | Documentation |
| migrations, schema | Database |
| package.json changes | Dependencies |
| UI components | Screenshots |

### Step 6: Offer Actions

```
PR description generated! Options:

1. Update existing PR
   → gh pr edit [number] --body "..."

2. Create new PR with this description
   → gh pr create --title "..." --body "..."

3. Copy to clipboard
   → For manual paste

4. Refine description
   → Ask for changes
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--create` | Create new PR immediately | false |
| `--minimal` | Short description only | false |
| `--no-checklist` | Skip checklists | false |
