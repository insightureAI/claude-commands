# Changelog

All notable changes to InsightureAI Claude Commands will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added
- `/subagents` - Execute multiple tasks in parallel using subagents via the Task tool

## [1.0.0] - 2025-12-05

### Added

#### Commands
- `/plan` - Transform feature ideas into actionable implementation plans
- `/review` - Multi-perspective code review with **design doc compliance checking**
- `/standup` - Generate standup notes from git activity
- `/tech-debt` - Assess technical debt with severity scoring
- `/pr-enhance` - Auto-generate comprehensive PR descriptions
- `/onboard` - Generate new developer onboarding guides
- `/docs-audit` - Find duplicate/messy docs and propose consolidation
- `/docs-capture` - Capture ideas into clean, organized documentation

#### Agents
- `code-reviewer` - Comprehensive code quality review
- `security-auditor` - Security vulnerability assessment (OWASP Top 10)
- `design-compliance` - Verify code matches design specifications

#### Templates
- `design-doc.md` - Standard design document template
- `pr-template.md` - Pull request description template

### Unique Features
- **Design doc compliance in reviews** - No other tool checks if code matches the spec
- **Documentation audit** - Find duplicates, orphans, and propose cleanup
- **Idea capture system** - Structure and organize ideas automatically

---

## Version History

| Version | Date | Highlights |
|---------|------|------------|
| 1.0.0 | 2025-12-05 | Initial release with 8 commands, 3 agents |
| 1.1.0 | Unreleased | Added `/subagents` for parallel task execution |
