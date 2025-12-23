# Agent Packages

**Reusable agent packages for AI-native development with APM**

A curated collection of production-ready skills, agents, and development patterns for building software with AI coding assistants (GitHub Copilot, Cursor, Claude Code).

## What's Inside

This repository provides composable packages that follow the [Agent Skills](https://agentskills.io) specification and integrate with [APM (Agent Package Manager)](https://github.com/danielmeppiel/apm).

### Agents

Specialized AI personas for different development roles:

| Agent | Purpose | Key Dependencies |
|-------|---------|------------------|
| [Feature Lead](agents/feature-lead/) | Orchestrates multi-story features using spec-kit and coordinates agent delegation | spec-driven-development, feature-orchestration, git-worktree-workflow, task-delegation |
| [Fullstack Engineer](agents/fullstack-engineer/) | Implements features across frontend, backend, and database | claude-framework, tdd-workflow, refactoring-patterns, fullstack-expertise |
| [TDD Specialist](agents/tdd-specialist/) | Enforces test-first discipline with Red-Green-Refactor cycles | tdd-workflow, refactoring-patterns, claude-framework |
| [Package Manager](agents/package-manager/) | Creates and validates APM packages with proper structure | - |

**Note**: The `claude-framework` skill includes a Code Quality Auditor agent. Install it to enable code review handoffs.

### Skills

Reusable development capabilities:

| Skill | What It Provides |
|-------|------------------|
| [CLAUDE Framework](skills/claude-framework/) | Production coding standards (code quality, naming, error handling, security, testing, database, logging) |
| [TDD Workflow](skills/tdd-workflow/) | Test-Driven Development discipline with Red-Green-Refactor cycles |
| [Spec-Driven Development](skills/spec-driven-development/) | Specification-first workflow using GitHub spec-kit |
| [Refactoring Patterns](skills/refactoring-patterns/) | Martin Fowler's refactoring catalog with incremental change discipline |
| [Git Worktree Workflow](skills/git-worktree-workflow/) | Parallel development with isolated story branches |
| [Task Delegation](skills/task-delegation/) | Multi-agent coordination with clear handoff protocols |
| [Feature Orchestration](skills/feature-orchestration/) | Cross-story consistency and progress tracking |
| [Fullstack Expertise](skills/fullstack-expertise/) | Backend, frontend, database, DevOps domain knowledge |

## Installation

### Install Individual Packages

```bash
# Install a specific agent
apm install vineethsoma/agent-packages/agents/feature-lead

# Install a specific skill
apm install vineethsoma/agent-packages/skills/tdd-workflow

# Install multiple packages
apm install \
  vineethsoma/agent-packages/agents/fullstack-engineer \
  vineethsoma/agent-packages/skills/claude-framework \
  vineethsoma/agent-packages/skills/tdd-workflow
```

### Install via apm.yml

Add to your project's `apm.yml`:

```yaml
name: my-project
dependencies:
  apm:
    - vineethsoma/agent-packages/agents/feature-lead
    - vineethsoma/agent-packages/skills/tdd-workflow
    - vineethsoma/agent-packages/skills/claude-framework
```

Then run:

```bash
apm install
apm compile
```

## Usage

### With VS Code (GitHub Copilot)

After installation and compilation, packages integrate natively:

- **Agents**: Available in VS Code chat dropdown
- **Prompts**: Available via `/` command (type `/` and select from list, e.g., `/start-tdd`)
- **Instructions**: Auto-applied based on file patterns in YAML frontmatter
- **Skills**: Discoverable by AI based on context

### With Claude Code

Packages compile to `.claude/` directory format:

- Commands: Available via slash commands
- Skills: Loaded automatically when relevant
- Instructions: Applied to matching file patterns

## Repository Structure

```
agent-packages/
├── agents/                 # AI personas (feature-lead, fullstack-engineer, package-manager)
├── skills/                 # Reusable capabilities (tdd-workflow, claude-framework, etc.)
├── instructions/           # Cross-cutting guidelines (apply to all packages)
├── contexts/              # Shared knowledge bases
├── memory/                # Lessons learned and decisions
├── docs/                  # Architecture documentation
└── apm.yml               # Repository manifest
```

### Package Structure

Each package follows APM/Agent Skills standards:

```
package-name/
├── SKILL.md              # Package description (required)
├── apm.yml              # Package manifest (required)
└── .apm/                # Primitives directory (required)
    ├── instructions/    # Guidelines (.instructions.md)
    ├── prompts/        # Workflows (.prompt.md)
    ├── agents/         # Personas (.agent.md)
    └── contexts/       # Knowledge (.context.md)
```

**Critical**: `.apm/` must contain at least one primitive file with content.

## Creating New Packages

### 1. Choose Package Type

- **Agent**: Specialized persona (e.g., "Security Reviewer", "API Designer")
- **Skill**: Reusable capability (e.g., "API Testing", "Performance Optimization")

### 2. Create Structure

```bash
# For a skill
mkdir -p skills/my-skill/.apm/{instructions,prompts,agents}
cd skills/my-skill

# Create SKILL.md
cat > SKILL.md << 'EOF'
---
name: my-skill
description: Clear description of what this skill does and when to use it
---

# My Skill

[Overview and usage guide]
EOF

# Create apm.yml
cat > apm.yml << 'EOF'
name: my-skill
version: 1.0.0
description: Brief description
author: Your Name
type: skill
dependencies:
  apm: []
EOF
```

### 3. Populate Primitives

Add at least one primitive file:

```bash
# Example: Add instruction file
cat > .apm/instructions/standards.instructions.md << 'EOF'
---
applyTo: "**/*.py"
description: Python coding standards
---

# Python Standards

[Your guidelines here]
EOF
```

### 4. Validate Locally

```bash
# Test from another project
cd /path/to/test-project
apm install /absolute/path/to/agent-packages/skills/my-skill

# Should succeed without errors
```

### 5. Commit and Share

```bash
git add .
git commit -m "Add my-skill package"
git push origin main
```

## Validation Requirements

Every package MUST:

1. ✅ Have `SKILL.md` at root (not in `.apm/`)
2. ✅ Have `apm.yml` at root
3. ✅ Have `.apm/` directory with subdirectories
4. ✅ Contain at least one `.md` file in `.apm/` subdirectories
5. ✅ Follow naming conventions (kebab-case)
6. ✅ Have proper YAML frontmatter in primitive files

**Common failure**: Empty `.apm/` directories cause validation errors even if the directory exists.

## VS Code Custom Agent Compliance

Agent files (`.agent.md`) MUST follow VS Code specification:

### Supported YAML Attributes

```yaml
---
name: Agent Name
description: Brief one-line description
tools: ['read', 'edit', 'execute', 'search', 'usages', 'fetch', 'githubRepo']
model: Claude Sonnet 4.5
handoffs:
  - label: Next Step
    agent: other-agent
    prompt: Continue with implementation
    send: false
---
```

### Unsupported Attributes

❌ Move these to markdown body:
- `author`, `version`, `color`
- `boundaries`, `expertise`, `skills`

## Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Skills/Agents | `kebab-case` | `tdd-workflow`, `feature-lead` |
| Instructions | `topic.instructions.md` | `tdd-discipline.instructions.md` |
| Prompts | `action.prompt.md` | `start-tdd.prompt.md` |
| Agents | `role.agent.md` | `tdd-specialist.agent.md` |
| Contexts | `domain.context.md` | `apm-architecture.context.md` |

## Cross-Cutting Concerns

Repository root contains shared resources:

- **instructions/**: Guidelines that apply to all packages
- **contexts/**: Knowledge useful across packages  
- **memory/**: Decisions and lessons learned

These compile into the generated `AGENTS.md` for all projects using this repository.

## Contributing

1. **Follow Package Structure**: Use proper SKILL.md, apm.yml, and .apm/ structure
2. **Test Locally**: Validate with `apm install` before pushing
3. **Document Clearly**: Include usage examples in SKILL.md
4. **Single Responsibility**: One package, one clear purpose
5. **Follow Conventions**: Use proper naming and file extensions

## Example Workflow

```bash
# 1. Install packages in your project
cd my-project
apm install vineethsoma/agent-packages/agents/feature-lead
apm install vineethsoma/agent-packages/skills/tdd-workflow

# 2. Compile to native format
apm compile

# 3. Use in VS Code
# - Chat: @Feature Lead help me build user authentication
# - Prompts: /start-tdd for user registration endpoint
# - Instructions: Auto-applied when editing code

# 4. Verify integration
ls .github/agents/    # Should show feature-lead-apm.agent.md
ls .github/prompts/   # Should show start-tdd-apm.prompt.md
cat AGENTS.md         # Should include compiled instructions
```

## Resources

- **APM Documentation**: https://github.com/danielmeppiel/apm
- **Agent Skills Spec**: https://agentskills.io/specification
- **VS Code Custom Agents**: https://code.visualstudio.com/docs/copilot/customization/custom-agents
- **Architecture Docs**: [docs/feature-lead-architecture.md](docs/feature-lead-architecture.md)

## License

[Specify your license here]

## Author

Vineeth Soma

---

**Built with APM** · [Install APM](https://github.com/danielmeppiel/apm#install)
