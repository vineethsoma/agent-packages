# Feature Lead Agent

**Coordinate complex features across multiple fullstack engineers with parallel execution and spec alignment.**

## Overview

The Feature Lead agent orchestrates multi-story feature development by:
- Maintaining overall feature context across user stories
- Delegating stories to fullstack engineer agents
- Managing parallel execution with git worktree (WIP limit: 3)
- Ensuring consistency with constitution and spec
- Coordinating handoffs between dependent stories

## Installation

```bash
apm install vineethsoma/agent-packages/agents/feature-lead
```

## What's Included

- **Feature Lead Agent**: Coordination and orchestration persona
- **4 Skills**: feature-orchestration, git-worktree-workflow, task-delegation, spec-driven-development

## Usage

### As Feature Lead (Coordinating Multiple Engineers)

```bash
# Initialize feature
/speckit.specify  # Create feature spec
/speckit.plan     # Create technical plan
/speckit.tasks    # Break into user stories
/worktree.init    # Setup git worktrees

# Delegate stories (max 3 concurrent)
/delegate.assign us1 agent-fullstack-a
/delegate.assign us2 agent-fullstack-b
/delegate.assign us3 agent-fullstack-c

# Monitor progress
/feature.progress.status        # Check WIP and blockers
/feature.validate.consistency   # Cross-story checks

# Review and merge
/delegate.review us1            # Validate completion
/worktree.merge us1             # Merge to main
```

### As Fullstack Engineer (Receiving Delegated Stories)

```bash
# Accept story
/delegate.accept us1

# Work in assigned worktree
cd worktrees/feat-us1
# Write tests, implement, commit

# Report progress
/delegate.progress us1 "60% complete, on track"

# Complete story
/delegate.complete us1
```

## Architecture

```
Feature Lead (You)
    ↓
    ├─ Story 1 → worktrees/feat-us1 → Fullstack Engineer A
    ├─ Story 2 → worktrees/feat-us2 → Fullstack Engineer B
    └─ Story 3 → worktrees/feat-us3 → Fullstack Engineer C
         ↓
    Merge coordination & consistency validation
```

## Key Features

### 1. WIP Limit Enforcement
- Maximum 3 concurrent stories (strictly enforced)
- Prevents context overload and merge conflicts
- Automatic slot management

### 2. Git Worktree Isolation
- Each story gets its own working directory
- Zero branch switching or stashing
- Parallel testing across worktrees

### 3. Consistency Validation
- Cross-story API contract checks
- Data model alignment verification
- Constitution compliance audits

### 4. Complete Context Delegation
- Stories include full context, dependencies, and acceptance criteria
- Clear handoff requirements for next story
- Structured communication protocols

## Skills

| Skill | Purpose |
|-------|---------|
| **spec-driven-development** | Constitution, specs, plans, task breakdown |
| **feature-orchestration** | Context management, consistency checks, progress tracking |
| **git-worktree-workflow** | Parallel branch management with worktrees |
| **task-delegation** | Story assignment with complete context |

## Example: Bird Search Feature

**Feature**: Natural Language Bird Search  
**Stories**: 12 user stories  
**WIP Limit**: 3 concurrent

```markdown
## Progress Dashboard

WIP: 3/3 (AT LIMIT)
Completed: 5/12 stories

| Story | Status | Branch | Agent | ETA |
|-------|--------|--------|-------|-----|
| US1   | ✅ Done | merged | - | - |
| US2   | ✅ Done | merged | - | - |
| US3   | 🔄 WIP | feat/us3 | Agent-A | Today |
| US4   | 🔄 WIP | feat/us4 | Agent-B | Tomorrow |
| US5   | 🔄 WIP | feat/us5 | Agent-C | Tomorrow |
| US6   | 📋 Ready | - | - | After US3 |
```

## Best Practices

### ✅ Do This
- Maintain living feature context (update after each story)
- Enforce WIP limit strictly (no exceptions)
- Validate consistency before merging
- Document handoffs explicitly
- Use git worktree for isolation

### ❌ Don't Do This
- Write code yourself (delegate to engineers)
- Exceed WIP limit (reduces throughput)
- Skip consistency validation (causes conflicts)
- Merge without review (violates quality standards)

## Requirements

- Git 2.5+ (for git worktree support)
- Multiple AI agents or human engineers
- Spec-kit project structure (`.specify/` directory)
- Constitution file (`constitution.md`)

## Related

- **Fullstack Engineer Agent**: Story implementers ([vineethsoma/agent-packages/agents/fullstack-engineer](../fullstack-engineer))
- **Spec-Driven Development**: Workflow foundation ([vineethsoma/agent-packages/skills/spec-driven-development](../../skills/spec-driven-development))

## Version

1.0.0

## Author

Vineeth Soma

## License

MIT
