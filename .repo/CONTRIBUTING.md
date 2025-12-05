---
disable-model-invocation: true
---

# Contributing to InsightureAI Claude Commands

## Adding a New Command

### 1. Create the Command File

```bash
touch commands/your-command.md
```

### 2. Use This Template

```markdown
---
model: claude-sonnet-4-0
---

# /your-command

**Purpose**: One-line description of what this command does

**Usage**:
\`\`\`bash
/your-command <required-arg> [optional-arg]
\`\`\`

## What It Does

1. Step one
2. Step two
3. Step three

## Implementation

[Use extended thinking for complex analysis]

### Step 1: Gather Context

- Read relevant files
- Search codebase for patterns

### Step 2: Process

- Apply logic
- Generate output

### Step 3: Output

Present results in this format:

\`\`\`markdown
# Your Command Output

## Section 1
...

## Section 2
...
\`\`\`

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--flag` | What it does | `false` |
```

### 3. Test Your Command

```bash
# In Claude Code, run:
/your-command test-input
```

### 4. Submit PR

- Add entry to CHANGELOG.md
- Update README.md command table
- Create PR with description of use case

## Modifying Existing Commands

1. Read the current command file
2. Make your changes
3. Test thoroughly
4. Submit PR with before/after examples

## Command Naming Conventions

- Use kebab-case: `docs-audit`, `tech-debt`
- Be descriptive but concise
- Avoid abbreviations unless universally understood

## Agent Guidelines

Agents in `agents/` are specialized reviewers. When creating:

1. Define clear expertise area
2. List specific checks performed
3. Provide output format template
4. Include severity classification

## Questions?

Open an issue or ask in team Slack.
