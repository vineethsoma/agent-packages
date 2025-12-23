---
name: Skill Development Patterns
description: Proven patterns and anti-patterns for creating reusable skills
tags: [skills, patterns, best-practices]
---

# Skill Development Patterns

Knowledge base of effective patterns for creating reusable, composable agent skills.

## Pattern: Single Capability Skill

**When to Use**: Creating a skill that provides ONE clear capability

**Structure**:
```
skill-name/
├── SKILL.md                 # What this skill does
├── apm.yml                  # type: skill
└── .apm/
    ├── instructions/        # Guidelines for using this capability
    │   └── main.instructions.md
    ├── prompts/             # Workflows that use this capability
    │   ├── execute.prompt.md
    │   └── validate.prompt.md
    └── agents/              # Persona that embodies this capability
        └── specialist.agent.md
```

**Example**: `tdd-workflow` skill
- **Instructions**: TDD principles (red-green-refactor)
- **Prompts**: Write test, implement code, refactor
- **Agent**: TDD specialist who enforces test-first

## Pattern: Composite Skill

**When to Use**: Skill that orchestrates multiple other skills

**Structure**:
```
composite-skill/
├── SKILL.md
├── apm.yml                  # Lists skill dependencies
└── .apm/
    ├── instructions/        # Integration guidelines
    ├── prompts/             # Orchestration workflows
    └── agents/              # Coordinator agent
```

**Example**: `feature-lead` agent (depends on multiple skills)
- Depends on: `spec-driven-development`, `git-worktree-workflow`, `task-delegation`
- Orchestrates: Planning, parallel execution, progress tracking

## Pattern: Domain-Specific Instructions

**When to Use**: Guidelines that apply to specific file types or directories

**Structure**:
```
domain-skill/
└── .apm/
    └── instructions/
        ├── frontend.instructions.md      # applyTo: "**/*.{jsx,tsx}"
        ├── backend.instructions.md       # applyTo: "**/*.{py,go}"
        └── testing.instructions.md       # applyTo: "**/test/**"
```

**Benefits**:
- Context optimization (only relevant instructions loaded)
- Clear domain boundaries
- Easier maintenance

## Pattern: Workflow Library

**When to Use**: Collection of related workflows for a domain

**Structure**:
```
workflow-library/
└── .apm/
    └── prompts/
        ├── create-feature.prompt.md
        ├── review-code.prompt.md
        ├── refactor-module.prompt.md
        └── write-tests.prompt.md
```

**Example**: `refactoring-patterns` skill
- Multiple refactoring workflows
- Each prompt is self-contained
- Can be used independently or in sequence

## Anti-Pattern: Kitchen Sink Skill

**Problem**: Skill tries to do everything

```
❌ mega-skill/
    └── .apm/
        ├── instructions/
        │   ├── frontend.instructions.md
        │   ├── backend.instructions.md
        │   ├── testing.instructions.md
        │   ├── deployment.instructions.md
        │   └── documentation.instructions.md  # TOO MUCH
        ├── prompts/
        │   ├── (20+ different workflows)
        └── agents/
            └── (5+ different personas)
```

**Why Bad**:
- Hard to maintain
- Users get unwanted functionality
- Difficult to test
- Context pollution

**Solution**: Split into focused skills:
- `frontend-development`
- `backend-development`
- `testing-workflows`
- `deployment-automation`
- `documentation-standards`

## Anti-Pattern: Empty Placeholder

**Problem**: Creating structure without content

```
❌ new-skill/
    ├── SKILL.md             # ✅ Has content
    ├── apm.yml              # ✅ Has content
    └── .apm/
        ├── instructions/    # ❌ Empty directory
        ├── prompts/         # ❌ Empty directory
        └── agents/          # ❌ Empty directory
```

**Result**: Validation fails - "Missing required directory: .apm/"

**Solution**: Only create directories you populate immediately

## Anti-Pattern: Generic Naming

**Problem**: Vague, non-descriptive names

```
❌ development-utils
❌ helper-tools
❌ common-patterns
```

**Why Bad**: Users can't tell what the skill does

**Solution**: Specific, descriptive names
```
✅ spec-driven-development
✅ tdd-workflow
✅ git-worktree-workflow
```

## Pattern: Progressive Enhancement

**When to Use**: Building skills incrementally

**Approach**:
1. **v1.0**: Basic capability with minimal primitives
   ```
   skill/
   └── .apm/
       └── instructions/
           └── basics.instructions.md
   ```

2. **v1.1**: Add workflow
   ```
   skill/
   └── .apm/
       ├── instructions/
       │   └── basics.instructions.md
       └── prompts/
           └── execute.prompt.md
   ```

3. **v2.0**: Add specialized agent
   ```
   skill/
   └── .apm/
       ├── instructions/
       ├── prompts/
       └── agents/
           └── specialist.agent.md
   ```

**Benefits**: Ship early, iterate based on feedback

## Pattern: Dependency Chain

**When to Use**: Skill builds on others' capabilities

**Example**: Feature Lead depends on foundational skills
```yaml
name: feature-lead
dependencies:
  apm:
    - vineethsoma/agent-packages/skills/spec-driven-development
    - vineethsoma/agent-packages/skills/git-worktree-workflow
    - vineethsoma/agent-packages/skills/task-delegation
```

**Guidelines**:
- Keep chain depth reasonable (< 3 levels)
- Avoid circular dependencies
- Document why each dependency is needed

## Pattern: Skill Variants

**When to Use**: Similar skills for different contexts

**Example**: Framework-specific test workflows
```
skills/
├── tdd-workflow/           # Generic TDD
├── tdd-react/              # React-specific TDD
└── tdd-python/             # Python-specific TDD
```

**Alternative**: Single skill with context-aware instructions
```
tdd-workflow/
└── .apm/
    └── instructions/
        ├── generic.instructions.md       # applyTo: "**"
        ├── react.instructions.md         # applyTo: "**/*.{jsx,tsx}"
        └── python.instructions.md        # applyTo: "**/*.py"
```

## Testing Patterns

### Pattern: Local Development Loop

1. Create skill structure
2. Populate with content
3. Test locally: `apm install /absolute/path/to/skill`
4. Verify integration in test project
5. Commit and push
6. Test from GitHub: `apm install owner/repo/skills/skill-name`

### Pattern: Curated Test Queries

For skills with prompts, maintain test queries:
```
skill/
└── tests/
    └── queries.md         # 10-20 representative use cases
```

Run each query, verify output meets expectations.

## Composition Guidelines

**Good Composition**: Each skill is independently useful
```
spec-driven-development → Useful alone
git-worktree-workflow → Useful alone
feature-lead → Combines both, useful alone
```

**Bad Composition**: Skills only work together
```
❌ skill-part-1 → Incomplete alone
❌ skill-part-2 → Incomplete alone
❌ skill-combined → Only this works
```

**Rule**: Every skill must provide value independently, even if designed to work with others.
