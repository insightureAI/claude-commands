---
disable-model-invocation: true
---

# InsightureAI Claude Commands

> **Design-doc-driven development toolkit for tech leads**

A curated set of Claude Code commands focused on what tech leads actually need: planning, reviewing against specs, documentation hygiene, and team workflows.

## Philosophy

- **Design doc compliance** — Every review checks if code matches the original spec
- **Documentation hygiene** — Find duplicates, capture ideas, keep docs clean
- **Framework-agnostic** — Works with any stack, not opinionated about Rails/React/etc
- **Focused, not exhaustive** — 9 commands that matter, not 57 you'll never use

## Installation

```bash
# Clone to Claude's commands directory
git clone https://github.com/insightureAI/claude-commands.git ~/.claude/commands

# Restart Claude Code to pick up new commands
```

### Update

```bash
cd ~/.claude/commands && git pull
```

## Commands

### Core Workflow

| Command | Purpose | Example |
|---------|---------|---------|
| `/plan <feature>` | Idea → actionable implementation plan | `/plan Add OAuth2 with JWT tokens` |
| `/review [PR]` | 5-perspective review with design doc compliance | `/review 123 --design docs/specs/auth.md` |
| `/standup [days]` | Git activity → standup notes | `/standup` or `/standup 3` |
| `/subagents <tasks>` | Execute multiple tasks in parallel via subagents | `/subagents "Fix auth" "Add tests" "Update docs"` |

### Code Quality

| Command | Purpose | Example |
|---------|---------|---------|
| `/tech-debt [path]` | Find and prioritize technical debt | `/tech-debt src/` |
| `/pr-enhance [PR]` | Auto-generate PR description | `/pr-enhance current` |

### Documentation

| Command | Purpose | Example |
|---------|---------|---------|
| `/docs-audit [path]` | Find duplicates, orphans, propose cleanup | `/docs-audit docs/` |
| `/docs-capture <idea>` | Capture idea → structured doc | `/docs-capture Use Kalman filter for sensor fusion` |

### Team

| Command | Purpose | Example |
|---------|---------|---------|
| `/onboard [focus]` | Generate new developer onboarding guide | `/onboard` or `/onboard backend` |

## What Makes This Different

### Design Doc Compliance in Reviews

Most review tools check code quality. Ours also checks **if the code matches the spec**:

```bash
/review 123 --design docs/specs/auth.md
```

Output includes:
```markdown
## Design Compliance
Requirements coverage: 4/5 met
- ✅ User can login with Google
- ✅ User can login with GitHub
- ✅ JWT expires after 1 hour
- ❌ Missing: "Password reset flow" not implemented
```

### Docs Audit

Nobody else has this. Find documentation mess before it becomes tech debt:

```bash
/docs-audit
```

Output:
```markdown
## Duplications Found
| Content | File 1 | File 2 | Action |
|---------|--------|--------|--------|
| Auth flow explanation | docs/auth.md | docs/api/auth.md | Merge |

## Orphan Docs (no links pointing to them)
- docs/old-architecture.md → Archive or delete?
```

## Agents

These agents are invoked by commands or can be used directly:

| Agent | Expertise | Invoked By |
|-------|-----------|------------|
| `code-reviewer` | Clean code, patterns, testability | `/review` |
| `security-auditor` | OWASP, auth, secrets, injection | `/review` |
| `design-compliance` | Requirements tracing, spec coverage | `/review --design` |

## Templates

Pre-built templates in `templates/`:

- `design-doc.md` — Standard design document structure
- `pr-template.md` — PR description template

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add or modify commands.

## Attribution

Built by [InsightureAI](https://github.com/insightureAI).

Inspired by patterns from:
- [wshobson/commands](https://github.com/wshobson/commands) (MIT License)
- [EveryInc/compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin)

## License

MIT License - see [LICENSE](LICENSE)
