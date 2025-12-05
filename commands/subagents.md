---
model: claude-sonnet-4-0
---

# /subagents

**Purpose**: Execute multiple tasks in parallel using subagents via the Task tool

**Usage**:
```bash
/subagents "Task 1" "Task 2" "Task 3"
/subagents Refactor auth module, Add unit tests for utils, Update API docs
/subagents "Fix type errors in src/api" "Add validation to forms" "Update README"
```

## What It Does

1. Analyzes tasks for dependencies and conflicts
2. Identifies which tasks can run in parallel vs. sequentially
3. Spawns multiple Task subagents simultaneously for independent work
4. Manages sequential execution for dependent tasks
5. Aggregates and reports results from all subagents

## Implementation

Ultrathink & Execute these tasks using parallel subagents via the Task tool:

$ARGUMENTS

### Step 1: Analyze Tasks

Parse `$ARGUMENTS` to identify individual tasks. For each task determine:
- What files/directories it will touch
- Whether it depends on another task's output
- Estimated scope (single file vs. multiple files)

### Step 2: Build Dependency Graph

Categorize tasks:
```
Independent (can run in parallel):
- Tasks touching different files
- Tasks in different parts of the codebase
- Read-only analysis tasks

Dependent (must run sequentially):
- Task B needs output from Task A
- Tasks modifying the same file
- Tasks with explicit order requirements
```

### Step 3: Check for Conflicts

**CRITICAL**: Never assign the same file to multiple subagents running in parallel.

If conflict detected:
```
⚠️ Conflict: Tasks 1 and 3 both modify src/utils/auth.ts
→ Running Task 1 first, then Task 3
```

### Step 4: Spawn Subagents

**PARALLELIZATION RULE**: Launch ALL independent tasks simultaneously in a single response with multiple Task tool calls.

For each independent task group:
```
Task tool call 1: "Implement [Task A]..."
Task tool call 2: "Implement [Task B]..."
Task tool call 3: "Implement [Task C]..."
(All in same response block)
```

Each subagent prompt should include:
- Clear, specific task description
- Files it should focus on
- Expected output/deliverables
- Instruction to report what was done

### Step 5: Handle Sequential Dependencies

After parallel batch completes:
1. Collect results from all subagents
2. Spawn next batch of now-unblocked tasks
3. Repeat until all tasks complete

### Step 6: Aggregate Results

After all subagents complete, summarize:

```markdown
## Subagent Execution Summary

### Tasks Completed
| Task | Status | Files Modified | Notes |
|------|--------|----------------|-------|
| Refactor auth | ✅ Done | 3 files | Extracted to separate module |
| Add tests | ✅ Done | 2 files | 15 new test cases |
| Update docs | ✅ Done | 1 file | Added API section |

### Execution Strategy
- Parallel batch 1: Tasks 1, 2, 3 (independent)
- Sequential: Task 4 (depended on Task 1)

### Files Created/Modified
- `src/auth/oauth.ts` (created)
- `src/auth/jwt.ts` (created)
- `tests/auth.test.ts` (modified)
- `docs/API.md` (modified)

### Issues Encountered
- None / [List any issues]

### Next Steps
- [Suggestions for follow-up if applicable]
```

## Critical Rules

| Rule | Description |
|------|-------------|
| **Parallelization** | Launch multiple Task tools AT ONCE for independent work - do NOT wait for one to finish before starting another |
| **Conflict Prevention** | Never assign the same file to multiple subagents - if overlap exists, sequence them |
| **Isolation** | Each subagent works in its own files/directories when possible |
| **Atomic** | Each task should be focused and complete one logical change |
| **Clear Prompts** | Each subagent gets explicit instructions about scope and expected output |

## Examples

### Example 1: Independent Tasks
```bash
/subagents "Add error handling to API routes" "Write tests for utils" "Update README"
```
→ All 3 spawn in parallel (different files)

### Example 2: Dependent Tasks
```bash
/subagents "Create user model" "Add user validation that uses the model"
```
→ Task 1 runs first, Task 2 waits for completion

### Example 3: Mixed
```bash
/subagents "Fix auth bug" "Add logging" "Refactor auth tests" "Update changelog"
```
→ "Fix auth bug" and "Refactor auth tests" may conflict → sequence them
→ "Add logging" and "Update changelog" run in parallel

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--dry-run` | Show execution plan without running | false |
| `--sequential` | Force all tasks to run sequentially | false |
| `--verbose` | Show detailed subagent prompts | false |
