---
name: Agent Packages Lessons Learned
description: Project knowledge and decisions accumulated over time
date_created: 2025-12-23
last_updated: 2025-12-23
---

# Lessons Learned: Agent Packages Development

This memory file captures key insights, decisions, and learnings from developing the agent-packages repository.

## Critical Insight: Empty .apm/ Directories Fail Validation

**Date**: 2025-12-23
**Context**: Initial skill creation with structure but no content

**Problem**: 
Created skill packages with proper directory structure (`.apm/instructions/`, `.apm/prompts/`, etc.) but no actual `.md` files inside. APM validation failed with "Missing required directory: .apm/" even though directory existed.

**Root Cause**:
APM validation requires:
1. `.apm/` directory exists
2. At least one primitive type folder exists
3. **At least one `.md` file with content exists in a primitive folder**

Empty directories don't satisfy validation.

**Solution**:
Before pushing to GitHub, ensure every skill has actual content files:
```
spec-driven-development/
└── .apm/
    ├── instructions/
    │   └── spec-first.instructions.md    # ✅ Actual file
    ├── prompts/
    │   ├── create-spec.prompt.md         # ✅ Actual file
    │   └── clarify-spec.prompt.md        # ✅ Actual file
    └── agents/
        └── spec-author.agent.md           # ✅ Actual file
```

**Prevention**: Test locally with `apm install /path/to/skill` before pushing.

---

## Lesson: SKILL.md Location Matters

**Date**: 2025-12-23
**Context**: Confusion about SKILL.md placement

**Discovery**:
APM expects `SKILL.md` at package root, NOT inside `.apm/` directory.

**Correct Structure**:
```
skill-name/
├── SKILL.md              # ✅ At root
├── apm.yml               # ✅ At root
└── .apm/                 # Primitives folder
    ├── instructions/
    ├── prompts/
    └── agents/
```

**Incorrect**:
```
skill-name/
├── apm.yml
└── .apm/
    ├── skills/           # ❌ Wrong
    │   └── SKILL.md
    ├── instructions/
    └── prompts/
```

**Rationale**: `SKILL.md` is package metadata, not a primitive. It describes the package, while primitives (in `.apm/`) are the actual capabilities.

---

## Lesson: Agent Files Live in .apm/agents/

**Date**: 2025-12-23
**Context**: Organizing agent definitions

**Discovery**:
Agent `.agent.md` files belong in `.apm/agents/` directory. APM's discovery patterns specifically search:
- `.apm/agents/*.agent.md`
- `.github/agents/*.agent.md` (after integration)

**Structure**:
```
feature-lead/
├── SKILL.md
├── apm.yml
└── .apm/
    └── agents/
        └── feature-lead.agent.md    # ✅ Correct location
```

**Integration**: When installed, copies to `.github/agents/feature-lead-apm.agent.md`

---

## Decision: Flat Organization by Primitive Type

**Date**: 2025-12-23
**Context**: Repository organization strategy

**Decision**: Organize top-level by primitive type (agents/, skills/), not by domain

**Rationale**:
1. **Clear separation**: Agents vs skills vs instructions have different purposes
2. **Reusability**: Skills can be mixed and matched across projects
3. **Discoverability**: Easy to browse all agents or all skills
4. **Composition**: Projects depend on specific primitives, not domains

**Structure**:
```
agent-packages/
├── agents/              # ✅ By type
│   ├── feature-lead/
│   └── fullstack-engineer/
└── skills/              # ✅ By type
    ├── spec-driven-development/
    ├── tdd-workflow/
    └── git-worktree-workflow/
```

**Alternative Rejected**: Domain-based organization
```
❌ agent-packages/
    ├── development/
    │   ├── fullstack-engineer/
    │   └── tdd-workflow/
    └── management/
        ├── feature-lead/
        └── task-delegation/
```

**Why Rejected**: Domains are subjective. Types are objective. TDD is both a development skill AND used by feature leads.

---

## Lesson: Skills vs Agents Distinction

**Date**: 2025-12-23
**Context**: Deciding what goes in agents/ vs skills/

**Distinction**:

**Agents** (`agents/` folder):
- Complete personas with defined expertise
- Have tool boundaries
- Specify appropriate LLM model
- Example: `fullstack-engineer`, `feature-lead`
- Can depend on skills

**Skills** (`skills/` folder):
- Focused capabilities
- Collection of related primitives (instructions, prompts, agents)
- Can be used independently or combined
- Example: `tdd-workflow`, `spec-driven-development`
- Agents use skills

**Rule of Thumb**: 
- "I need a [ROLE]" → Agent
- "I want to [CAPABILITY]" → Skill

---

## Insight: Agents Can Depend on Skills

**Date**: 2025-12-23
**Context**: Feature Lead dependencies

**Discovery**:
Agents can list skills as dependencies. This composes capabilities into personas.

**Example**:
```yaml
# agents/feature-lead/apm.yml
name: feature-lead
dependencies:
  apm:
    - vineethsoma/agent-packages/skills/spec-driven-development
    - vineethsoma/agent-packages/skills/git-worktree-workflow
    - vineethsoma/agent-packages/skills/task-delegation
```

**Benefit**: Feature Lead inherits all capabilities from dependent skills while adding orchestration logic.

---

## Lesson: Quick Fix vs Complete Fix

**Date**: 2025-12-23
**Context**: Handling unpopulated skills during development

**Situation**: Created 8 skills but only populated 1 (spec-driven-development). Other 7 had empty `.apm/` directories.

**Quick Fix Applied**:
Remove unpopulated skills from dependent projects' `apm.yml` temporarily:
```yaml
# Before
dependencies:
  apm:
    - skills/spec-driven-development
    - skills/tdd-workflow          # ❌ Empty
    - skills/git-worktree-workflow # ❌ Empty
    # ... 5 more empty

# After (Quick Fix)
dependencies:
  apm:
    - skills/spec-driven-development  # ✅ Only populated one
```

**Complete Fix** (To Do):
Populate all 8 skills with actual content, then restore to dependencies.

**Lesson**: Quick fix unblocks development. Complete fix comes iteratively.

---

## Future Considerations

### Cross-Cutting Instructions
Consider adding root-level `instructions/` folder for guidelines that apply across all packages.

### Shared Contexts
Add `contexts/` folder for knowledge that multiple skills reference (like APM architecture, skill development patterns).

### Project Memory
This file! Capture learnings as they happen, not retrospectively.

---

## Questions to Resolve

1. **Skill granularity**: How focused should each skill be? One workflow vs multiple related workflows?
2. **Version strategy**: When to bump major vs minor versions?
3. **Testing approach**: Automated tests for skill integration?
4. **Documentation**: Separate README vs inline in SKILL.md?

---

*This memory file should be updated whenever significant insights are gained or decisions are made.*
