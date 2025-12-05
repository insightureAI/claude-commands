---
model: claude-sonnet-4-0
---

# /standup

**Purpose**: Generate standup notes from git activity

**Usage**:
```bash
/standup              # Yesterday's activity
/standup 3            # Last 3 days
/standup --slack      # Slack-friendly format
```

## What It Does

1. Gets recent commits by current user
2. Checks open/merged PRs
3. Identifies current branch work
4. Flags blockers (stale PRs, uncommitted changes)

## Implementation

### Step 1: Get Git User Info

```bash
git config user.name
git config user.email
```

### Step 2: Gather Activity Data

Run these commands to collect data:

**Recent commits** (default: 1 day, or $ARGUMENTS days):
```bash
git log --author="$(git config user.email)" --since="$DAYS days ago" --pretty=format:"%h %s" --no-merges
```

**Merged PRs** (requires gh cli):
```bash
gh pr list --author="@me" --state=merged --json number,title,mergedAt --limit 10
```

**Open PRs**:
```bash
gh pr list --author="@me" --state=open --json number,title,createdAt,reviewDecision
```

**Current branch**:
```bash
git branch --show-current
git log main..HEAD --oneline
```

**Uncommitted changes**:
```bash
git status --porcelain
```

### Step 3: Identify Blockers

Flag as blockers:
- PRs awaiting review > 2 days
- PRs with changes requested
- Uncommitted changes on feature branch

### Step 4: Generate Output

**Standard Format**:

```markdown
# Standup - [Date]

## ✅ Completed
- Merged PR #123: OAuth2 implementation
- Fixed token refresh bug (abc1234)
- Code review for PR #125

## 🔄 In Progress
- Working on: password reset flow (`feature/password-reset`)
  - 3 commits, +245/-12 lines
- PR #126 open, awaiting review

## 📋 Today's Plan
- Complete password reset UI
- Write integration tests
- Review PR #128

## 🚧 Blockers
- PR #126 awaiting review (2 days)
- Waiting on DevOps for OAuth credentials
```

**Slack Format** (if `--slack` flag):

```
*Standup [Date]*

✅ *Done:* Merged OAuth PR #123, fixed token refresh bug
🔄 *Today:* Password reset flow, integration tests
🚧 *Blocked:* PR #126 awaiting review (2d), need OAuth creds from DevOps
```

### Step 5: Offer Actions

```
Standup generated! Options:
1. Copy to clipboard
2. Save to file (standup/YYYY-MM-DD.md)
3. Post to Slack (if webhook configured)
```

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--slack` | Compact Slack-friendly format | false |
| `--include-reviews` | Include PRs you reviewed | false |
| `--no-blockers` | Skip blocker detection | false |

## Notes

- Requires `git` for commit history
- Requires `gh` CLI for PR information (optional but recommended)
- Works without GitHub - just shows git commits
