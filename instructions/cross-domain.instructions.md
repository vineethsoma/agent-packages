---
applyTo: "**"
description: Cross-cutting principles that apply to all agent-packages development
---

# Cross-Domain Development Principles

These principles apply universally across all agents, skills, and primitives in this repository.

## Package Structure Standards

Every package MUST follow this structure:
```
package-name/
├── SKILL.md                 # Package meta-guide (required)
├── apm.yml                  # Package manifest (required)
├── README.md                # Full documentation (recommended)
└── .apm/                    # Primitives directory (required with content)
    ├── instructions/        # Guidelines (optional)
    ├── prompts/             # Workflows (optional)
    ├── agents/              # Personas (optional)
    └── contexts/            # Knowledge (optional)
```

**Critical**: `.apm/` directories must contain at least one primitive file with actual content. Empty `.apm/` directories will cause validation failures.

## Naming Conventions

### File Naming
- **Instructions**: `topic-name.instructions.md` (kebab-case)
- **Prompts**: `action-name.prompt.md` (kebab-case, verb-based)
- **Agents**: `role-name.agent.md` (kebab-case, noun-based)
- **Contexts**: `domain-knowledge.context.md` (kebab-case)

### Package Naming
- Use descriptive, specific names: `spec-driven-development`, `tdd-workflow`
- Avoid generic names: `utils`, `helpers`, `common`
- Use hyphens, not underscores: `git-worktree-workflow` ✅, `git_worktree_workflow` ❌

## Content Quality Standards

### SKILL.md Files
- Must have YAML frontmatter with `name` and `description`
- Should provide clear overview of what the skill does
- Should list included primitives
- Should explain when to use this skill

### Agent Files
- Must include expertise areas
- Must define tool boundaries (what agent CAN and CANNOT do)
- Should reference relevant instructions and prompts
- Should specify appropriate LLM model if needed

### Instruction Files
- Must use `applyTo` patterns for targeted application
- Should be focused on specific domain (not kitchen-sink)
- Should include rationale for guidelines
- Should provide examples of good vs bad practices

### Prompt Files
- Must include clear step-by-step process
- Should define validation gates for human approval
- Should specify required context and tools
- Should have measurable success criteria

## Version Control

- Use semantic versioning: `MAJOR.MINOR.PATCH`
- Update `apm.yml` version when publishing changes
- Tag releases in git: `v1.0.0`, `v2.1.0`
- Document breaking changes in CHANGELOG.md

## Testing Before Publishing

Before pushing changes to GitHub:
1. Validate locally: Create test project and `apm install` from local path
2. Check structure: Verify `.apm/` has actual content files
3. Test integration: Confirm prompts, agents, instructions work as expected
4. Review dependencies: Ensure all skill dependencies are populated

## Metadata Standards

Every `apm.yml` must include:
```yaml
name: package-name
version: 1.0.0
description: Clear, concise description (1-2 sentences)
author: Your Name
type: skill  # or hybrid, instructions, prompts
dependencies:
  apm: []  # List dependencies
  mcp: []  # List MCP servers if needed
```

## Documentation Requirements

- **SKILL.md**: Package overview and usage guide
- **README.md**: Comprehensive documentation with examples
- **Comments**: All YAML frontmatter fields explained
- **Examples**: Show how to use the skill/agent in practice

## Anti-Patterns to Avoid

❌ Empty `.apm/` directories (will fail validation)
❌ Missing YAML frontmatter in primitive files
❌ Generic, vague descriptions in `apm.yml`
❌ Copying examples without customizing for domain
❌ Not testing locally before publishing
❌ Mixing multiple concerns in single file
❌ No clear separation between instructions, prompts, agents

## Principle: Single Responsibility

Each package, each primitive file should do ONE thing well:
- **Instructions**: Set guidelines for a specific domain
- **Prompts**: Execute a specific workflow
- **Agents**: Embody a specific persona/role
- **Skills**: Provide a specific capability

Don't create monolithic packages. Create composable, focused units.
