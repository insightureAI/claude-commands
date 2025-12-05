---
model: claude-sonnet-4-0
---

# /docs-audit

**Purpose**: Find duplicate/messy docs and propose consolidation

**Usage**:
```bash
/docs-audit                    # Audit all docs
/docs-audit docs/              # Audit specific directory
/docs-audit docs/design/       # Focus on design docs
/docs-audit --fix              # Auto-fix simple issues
```

## What It Does

1. Scans all markdown files
2. Detects exact and semantic duplicates
3. Finds orphan docs (no links pointing to them)
4. Identifies off-topic content
5. Proposes consolidation and new structure
6. Optionally auto-fixes simple issues

## Why This Matters

Documentation debt is real:
- Same thing explained in 3 places → Gets out of sync
- Old docs nobody links to → Confuse new developers
- Design docs mixed with meeting notes → Hard to find what you need

## Implementation

[Use extended thinking for thorough analysis]

### Step 1: Inventory All Docs

Find all markdown files:
```bash
find . -name "*.md" -type f | grep -v node_modules | grep -v .git
```

For each file, extract:
- Path
- Title (first H1)
- Word count
- Last modified date
- Links to other docs
- Links from other docs (incoming)

### Step 2: Detect Duplications

#### Exact Duplicates
Hash content and compare:
```python
# Pseudocode
for file in files:
    content_hash = hash(file.content)
    if content_hash in seen_hashes:
        mark_as_duplicate(file, seen_hashes[content_hash])
```

#### Near Duplicates (Same Content, Minor Differences)
Compare normalized content (lowercase, no whitespace):
- Files with >80% similar content → Flag as near-duplicate

#### Semantic Duplicates (Same Concept, Different Words)
Use Claude to compare:

**Prompt**:
```
Compare these two documents. Are they explaining the same concept?

Document 1: [title and first 500 words]
Document 2: [title and first 500 words]

Respond with:
- DUPLICATE: Same concept, should merge
- RELATED: Similar topic, should link
- DISTINCT: Different topics
```

### Step 3: Find Orphan Docs

Build link graph:
```python
# For each file
incoming_links = count links TO this file from other files
outgoing_links = count links FROM this file to other files

if incoming_links == 0 and file not in [README.md, index.md]:
    mark_as_orphan(file)
```

### Step 4: Detect Off-Topic Content

Check if docs are in appropriate directories:

| Directory | Expected Content |
|-----------|------------------|
| `docs/design/` | Architecture, specs, RFCs |
| `docs/guides/` | How-to tutorials |
| `docs/api/` | API documentation |
| `docs/meeting-notes/` | Meeting summaries |

Use Claude to classify:

**Prompt**:
```
What type of document is this?

Title: [title]
Content: [first 500 words]

Categories:
- design: Architecture decisions, technical specs
- guide: How-to tutorials, setup instructions
- api: API reference documentation
- reference: Configuration, options
- meeting: Meeting notes, discussions
- archive: Outdated, historical
```

Flag docs in wrong directory.

### Step 5: Check for Stale Docs

Detect potentially outdated docs:
- Last modified > 6 months ago
- References deprecated technologies
- Links to files that no longer exist

### Step 6: Generate Report

```markdown
# Documentation Audit Report

**Scope**: [path audited]
**Date**: [today]
**Total Files**: 47
**Issues Found**: 12

---

## Summary

| Issue Type | Count | Action |
|------------|-------|--------|
| Exact duplicates | 2 | Merge |
| Semantic duplicates | 3 | Review & merge |
| Orphan docs | 4 | Link or archive |
| Off-topic placement | 2 | Move |
| Stale docs | 5 | Review |
| Broken links | 3 | Fix |

**Health Score**: 🟡 72/100 (Fair)

---

## Duplications Found

### Exact Duplicates

| Content | File 1 | File 2 | Recommendation |
|---------|--------|--------|----------------|
| Auth setup guide | `docs/auth.md` | `docs/guides/authentication.md` | Keep guides/, delete docs/auth.md |

### Semantic Duplicates

| Concept | Files | Recommendation |
|---------|-------|----------------|
| Database schema | `docs/db.md`, `docs/models.md` | Merge into `docs/database/schema.md` |
| API authentication | `docs/api/auth.md`, `docs/security/api-auth.md` | Keep api/, link from security/ |

---

## Orphan Documents

No incoming links found for these files:

| File | Last Modified | Recommendation |
|------|---------------|----------------|
| `docs/old-architecture.md` | 8 months ago | Archive or delete |
| `docs/brainstorm-2024.md` | 6 months ago | Move to archive/ |
| `docs/temp-notes.md` | 3 months ago | Review for useful content |

---

## Off-Topic Content

Files in wrong directories:

| File | Current Location | Should Be |
|------|------------------|-----------|
| `docs/design/meeting-jan-15.md` | design/ | meeting-notes/ |
| `docs/guides/architecture.md` | guides/ | design/ |

---

## Broken Links

| Source File | Broken Link | Suggestion |
|-------------|-------------|------------|
| `docs/README.md:15` | `./setup.md` | File deleted, update link |
| `docs/api/users.md:45` | `../models/user.md` | Moved to `../database/models.md` |

---

## Stale Documents

Files not updated in 6+ months:

| File | Last Modified | Status |
|------|---------------|--------|
| `docs/v1-migration.md` | 10 months | Likely obsolete |
| `docs/old-api.md` | 8 months | Check if still needed |

---

## Proposed New Structure

Current:
```
docs/
├── auth.md
├── api/
├── guides/
├── random-notes.md
└── ...
```

Recommended:
```
docs/
├── README.md              # Index with links to all docs
├── design/                # Architecture, specs
│   ├── architecture.md
│   └── database-schema.md
├── guides/                # How-to tutorials
│   ├── getting-started.md
│   └── authentication.md
├── api/                   # API reference
│   └── endpoints.md
├── reference/             # Configuration, options
│   └── environment.md
└── archive/               # Old docs kept for history
    └── v1-migration.md
```

---

## Quick Actions

### Auto-Fixable (with --fix flag)
- [ ] Delete exact duplicate `docs/auth.md`
- [ ] Fix 3 broken links
- [ ] Move meeting notes to `meeting-notes/`

### Manual Review Required
- [ ] Merge semantic duplicates (need content decision)
- [ ] Review orphan docs for archival
- [ ] Update stale documentation

---

## Next Steps

1. **Run with --fix**: `/docs-audit --fix` to auto-fix simple issues
2. **Review duplicates**: Decide which version to keep
3. **Archive stale docs**: Move to archive/ or delete
4. **Create index**: Generate docs/README.md with links
```

### Step 7: Offer Actions

```
Audit complete! What would you like to do?

1. Auto-fix simple issues (delete duplicates, fix links)
   → Will show preview before applying

2. Generate new docs/README.md index
   → Creates table of contents with all docs

3. Create archive/ and move stale docs
   → Preserves history, cleans up structure

4. Export report to docs/audit-YYYY-MM-DD.md

5. Focus on specific issue type
   → Re-run focusing only on duplicates/orphans/etc
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--fix` | Auto-fix simple issues (with confirmation) | false |
| `--duplicates-only` | Only check for duplicates | false |
| `--orphans-only` | Only find orphan docs | false |
| `--json` | Output as JSON for tooling | false |
