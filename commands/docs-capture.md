---
model: claude-sonnet-4-0
---

# /docs-capture

**Purpose**: Capture ideas, findings, or insights into clean, organized documentation

**Usage**:
```bash
/docs-capture We should use Kalman filtering for sensor fusion
/docs-capture [design] Haptic feedback needs <50ms latency
/docs-capture [decision] Going with PostgreSQL over MongoDB
/docs-capture --quick "check MediaPipe vs OpenPose"
```

## What It Does

1. Categorizes the idea (design/research/decision/idea)
2. Checks for related existing docs
3. Writes structured doc with metadata
4. Places in correct folder
5. Updates index

## Why This Matters

Ideas get lost in:
- Slack messages that scroll away
- Meeting notes nobody reads
- Your brain at 2am

Capture them immediately → Find them later → Build on them.

## Categories

| Tag | Folder | Purpose |
|-----|--------|---------|
| `[design]` | `docs/design/` | Architecture decisions, technical specs |
| `[research]` | `docs/research/` | Investigation findings, comparisons |
| `[decision]` | `docs/decisions/` | ADRs (Architecture Decision Records) |
| `[idea]` | `docs/ideas/` | Brainstorms, possibilities |
| (no tag) | Auto-detect based on content |

## Implementation

### Step 1: Parse Input

From `$ARGUMENTS`, extract:
- Category tag (if present): `[design]`, `[research]`, `[decision]`, `[idea]`
- Quick mode flag: `--quick`
- The actual content

Examples:
```
"[design] Haptic feedback needs <50ms latency"
→ category: design
→ content: "Haptic feedback needs <50ms latency"

"We should use Kalman filtering"
→ category: auto-detect
→ content: "We should use Kalman filtering"
```

### Step 2: Auto-Detect Category (if not specified)

Use Claude to classify:

**Prompt**:
```
Classify this note into one category:

Note: "[content]"

Categories:
- design: Technical specifications, architecture decisions, requirements
- research: Investigations, comparisons, findings from exploration
- decision: Choices made between alternatives (ADR style)
- idea: Brainstorms, possibilities, things to explore later

Respond with just the category name.
```

### Step 3: Check for Related Docs

Search existing docs for similar content:

```bash
# Find potentially related docs
grep -rli "kalman\|sensor\|fusion" docs/
```

If related docs found, show them:
```
Found related documentation:
- docs/research/sensor-fusion-options.md (80% relevant)
- docs/design/imu-processing.md (60% relevant)

Options:
1. Create new doc (will link to these)
2. Add to existing doc
3. View related docs first
```

### Step 4: Expand Content (unless --quick)

If not quick mode, use Claude to expand the note:

**Prompt**:
```
Expand this brief note into a structured document.

Note: "[user's input]"

Create sections for:
- Summary (1-2 sentences)
- Context (why this matters)
- Details (technical specifics if applicable)
- Trade-offs (pros/cons if it's a choice)
- Open Questions (what still needs to be figured out)
- Related Topics (what else connects to this)

Keep it concise but complete. Use bullet points.
```

### Step 5: Generate Document

#### For Design Notes

```markdown
# [Title Extracted from Content]

> **Status**: draft | review | approved
> **Created**: YYYY-MM-DD
> **Author**: [git user]
> **Tags**: #sensors #algorithms #mvp

## Summary

[1-2 sentence summary of the design point]

## Context

Why this matters:
- [Bullet points on background]
- [Problem being solved]

## Specification

[Technical details]

### Requirements
- Latency: <50ms
- Accuracy: ±2%

### Constraints
- Must work on mobile devices
- Battery impact consideration

## Trade-offs

| Option | Pros | Cons |
|--------|------|------|
| Option A | Fast | Complex |
| Option B | Simple | Slower |

## Decision

[If a decision was made, document it]

**Chosen approach**: [X]
**Rationale**: [Why]

## Open Questions

- [ ] Question 1?
- [ ] Question 2?

## Related

- [Link to related doc](./related.md)
- [External reference](https://...)

---
*Last updated: YYYY-MM-DD*
```

#### For Research Notes

```markdown
# Research: [Topic]

> **Status**: in-progress | complete
> **Created**: YYYY-MM-DD
> **Tags**: #research #[topic]

## Question

What we're trying to find out:
[The research question]

## Findings

### [Finding 1]
- Detail
- Detail

### [Finding 2]
- Detail

## Comparison (if applicable)

| Criteria | Option A | Option B | Option C |
|----------|----------|----------|----------|
| Performance | ⭐⭐⭐ | ⭐⭐ | ⭐ |
| Ease of use | ⭐ | ⭐⭐⭐ | ⭐⭐ |

## Recommendation

Based on findings: [recommendation]

## Sources

- [Source 1](url)
- [Source 2](url)

## Next Steps

- [ ] Action item 1
- [ ] Action item 2
```

#### For Decision Records (ADR)

```markdown
# ADR-[number]: [Decision Title]

> **Status**: proposed | accepted | deprecated | superseded
> **Created**: YYYY-MM-DD
> **Deciders**: [who was involved]

## Context

What is the issue that we're seeing that is motivating this decision?

## Decision

What is the change that we're proposing and/or doing?

## Consequences

What becomes easier or more difficult to do because of this change?

### Positive
- [Benefit 1]
- [Benefit 2]

### Negative
- [Drawback 1]
- [Drawback 2]

### Neutral
- [Side effect 1]

## Alternatives Considered

### [Alternative 1]
- Why not chosen

### [Alternative 2]
- Why not chosen
```

#### For Ideas (Quick Capture)

```markdown
# Idea: [Title]

> **Status**: captured | exploring | parked | implemented
> **Created**: YYYY-MM-DD
> **Tags**: #idea #[topic]

## The Idea

[1-2 paragraph description]

## Why It Might Work

- [Reason 1]
- [Reason 2]

## Concerns

- [Risk or unknown 1]
- [Risk or unknown 2]

## To Explore

- [ ] Research item 1
- [ ] Prototype item 2

## Related

- [Related idea or doc]
```

### Step 6: Save Document

Generate filename:
```python
# Sanitize title for filename
filename = title.lower().replace(" ", "-").replace("/", "-")
date = today.strftime("%Y-%m-%d")
path = f"docs/{category}/{date}-{filename}.md"
```

Create directory if needed:
```bash
mkdir -p docs/{category}
```

Write file.

### Step 7: Update Index (if exists)

If `docs/{category}/README.md` or `docs/{category}/index.md` exists:
- Add link to new doc
- Keep sorted by date (newest first)

### Step 8: Confirm and Offer Actions

```
✅ Captured: docs/design/2025-12-05-haptic-feedback-latency.md

What's next?
1. Open in editor
2. Link to existing doc
3. Share link (copy to clipboard)
4. Capture another idea
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--quick` | Minimal expansion, just capture | false |
| `--no-expand` | Save exactly as typed | false |
| `--edit` | Open in $EDITOR after creating | false |

## Quick Mode

With `--quick`, skip expansion and just save:

```bash
/docs-capture --quick "check MediaPipe vs OpenPose for pose estimation"
```

Creates minimal file:
```markdown
# Quick Note: MediaPipe vs OpenPose

> **Created**: 2025-12-05
> **Status**: captured

## Note

Check MediaPipe vs OpenPose for pose estimation

## TODO

- [ ] Research this
```
