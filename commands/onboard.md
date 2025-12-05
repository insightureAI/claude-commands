---
model: claude-sonnet-4-0
---

# /onboard

**Purpose**: Generate onboarding guide for new developers joining the project

**Usage**:
```bash
/onboard                 # Full onboarding guide
/onboard frontend        # Frontend-focused guide
/onboard backend         # Backend-focused guide
/onboard --quick         # Quick start only
```

## What It Does

1. Analyzes project structure and tech stack
2. Detects package managers, frameworks, databases
3. Reads existing README, CONTRIBUTING files
4. Extracts available npm/make scripts
5. Identifies environment requirements
6. Generates comprehensive onboarding doc

## Implementation

[Use extended thinking for thorough analysis]

### Step 1: Detect Tech Stack

**Package manager**:
```bash
ls package-lock.json yarn.lock pnpm-lock.yaml bun.lockb 2>/dev/null
```

**Language/Framework**:
- `package.json` → Node.js, check for React/Vue/Next.js
- `requirements.txt` / `pyproject.toml` → Python
- `go.mod` → Go
- `Cargo.toml` → Rust
- `Gemfile` → Ruby

**Database**:
- Search for postgres, mysql, mongodb, redis in configs
- Check docker-compose.yml for database services

**Infrastructure**:
- `Dockerfile` → Docker
- `docker-compose.yml` → Docker Compose
- `*.tf` → Terraform
- `.github/workflows` → GitHub Actions

### Step 2: Extract Existing Documentation

Read if exists:
- `README.md`
- `CONTRIBUTING.md`
- `docs/setup.md`
- `.env.example`

### Step 3: Get Available Scripts

**package.json scripts**:
```bash
cat package.json | jq '.scripts'
```

**Makefile targets**:
```bash
grep "^[a-z].*:" Makefile
```

### Step 4: Analyze Project Structure

Use Glob to map:
```
src/
├── api/        → API routes/controllers
├── services/   → Business logic
├── models/     → Data models
├── utils/      → Helpers
└── ...
```

### Step 5: Generate Onboarding Guide

```markdown
# Developer Onboarding Guide

> Welcome to [Project Name]! This guide will help you get up and running.

## Tech Stack Overview

| Layer | Technology |
|-------|------------|
| Frontend | React 18, TypeScript, Tailwind CSS |
| Backend | Node.js, Express, TypeScript |
| Database | PostgreSQL 14, Redis |
| Infrastructure | Docker, GitHub Actions |

---

## Prerequisites

Before you begin, ensure you have:

- [ ] **Node.js** 18+ ([install](https://nodejs.org/))
- [ ] **pnpm** ([install](https://pnpm.io/installation))
- [ ] **Docker** & Docker Compose ([install](https://docs.docker.com/get-docker/))
- [ ] **PostgreSQL** 14+ (or use Docker)
- [ ] **Git** configured with SSH key for GitHub

### Verify Installation

```bash
node --version    # Should be 18+
pnpm --version    # Should be 8+
docker --version  # Should be 20+
```

---

## Quick Start

### 1. Clone the Repository

```bash
git clone git@github.com:org/repo.git
cd repo
```

### 2. Install Dependencies

```bash
pnpm install
```

### 3. Set Up Environment

```bash
cp .env.example .env
```

Edit `.env` and fill in required values:
```
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname
REDIS_URL=redis://localhost:6379
API_KEY=your-api-key-here
```

### 4. Start Infrastructure

```bash
docker-compose up -d
```

This starts:
- PostgreSQL on port 5432
- Redis on port 6379

### 5. Set Up Database

```bash
pnpm db:migrate    # Run migrations
pnpm db:seed       # Seed test data (optional)
```

### 6. Start Development Server

```bash
pnpm dev
```

Visit: http://localhost:3000

---

## Project Structure

```
├── src/
│   ├── api/              # API routes and controllers
│   │   ├── users/        # User endpoints
│   │   └── auth/         # Authentication endpoints
│   ├── services/         # Business logic layer
│   ├── models/           # Database models/entities
│   ├── middleware/       # Express middleware
│   ├── utils/            # Shared utilities
│   └── types/            # TypeScript type definitions
├── tests/
│   ├── unit/             # Unit tests
│   └── integration/      # Integration tests
├── docs/                 # Documentation
├── scripts/              # Build/deploy scripts
└── docker/               # Docker configurations
```

### Key Files

| File | Purpose |
|------|---------|
| `src/api/index.ts` | API route registration |
| `src/services/` | Core business logic |
| `src/middleware/auth.ts` | Authentication middleware |
| `.env.example` | Environment variable template |

---

## Available Commands

| Command | Description |
|---------|-------------|
| `pnpm dev` | Start development server with hot reload |
| `pnpm build` | Build for production |
| `pnpm start` | Start production server |
| `pnpm test` | Run test suite |
| `pnpm test:watch` | Run tests in watch mode |
| `pnpm lint` | Run linter |
| `pnpm lint:fix` | Auto-fix lint issues |
| `pnpm db:migrate` | Run database migrations |
| `pnpm db:seed` | Seed database with test data |
| `pnpm db:reset` | Reset database (⚠️ destroys data) |

---

## Development Workflow

### Branch Naming

```
feature/description    # New features
fix/description        # Bug fixes
refactor/description   # Code improvements
docs/description       # Documentation
```

### Commit Messages

We use [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add user registration
fix: resolve login redirect issue
docs: update API documentation
refactor: extract validation logic
test: add payment flow tests
```

### Pull Request Process

1. Create branch from `main`
2. Make changes with tests
3. Run `pnpm lint && pnpm test`
4. Push and create PR
5. Request review from team
6. Squash and merge after approval

---

## Common Tasks

### Adding a New API Endpoint

1. Create route in `src/api/[resource]/routes.ts`
2. Add handler in `src/api/[resource]/handlers.ts`
3. Add service logic in `src/services/[resource].ts`
4. Add tests in `tests/unit/services/[resource].test.ts`

### Adding a Database Migration

```bash
pnpm db:migration:create add_users_table
# Edit the migration file
pnpm db:migrate
```

### Running Specific Tests

```bash
pnpm test -- --grep "UserService"
pnpm test -- tests/unit/services/user.test.ts
```

---

## Troubleshooting

### Port Already in Use

```bash
lsof -i :3000
kill -9 <PID>
```

### Database Connection Failed

1. Check Docker is running: `docker ps`
2. Check DATABASE_URL in `.env`
3. Try: `docker-compose down && docker-compose up -d`

### Node Modules Issues

```bash
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

---

## First Week Checklist

- [ ] Complete local setup (this guide)
- [ ] Run the test suite successfully
- [ ] Read through `docs/architecture.md`
- [ ] Pick up a "good first issue" from GitHub
- [ ] Make your first PR
- [ ] Set up IDE extensions (ESLint, Prettier)
- [ ] Join team Slack channels

---

## Resources

### Internal
- [Architecture Overview](docs/architecture.md)
- [API Documentation](docs/api.md)
- [Design Documents](docs/design/)

### External
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)

---

## Getting Help

- **Slack**: #team-engineering
- **Documentation**: `docs/` folder
- **Questions**: Open a GitHub Discussion
- **Bugs**: Create a GitHub Issue

Welcome to the team! 🎉
```

### Step 6: Focus-Specific Additions

If `$ARGUMENTS` contains "frontend":
- Add frontend-specific setup (npm scripts, component library)
- Include Storybook setup if detected
- Add browser extension recommendations

If `$ARGUMENTS` contains "backend":
- Add API testing setup (Postman/Insomnia)
- Include database GUI tools
- Add debugging configuration

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--quick` | Just Quick Start section | false |
| `--save` | Save to docs/onboarding.md | false |
