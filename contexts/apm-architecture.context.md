---
name: APM Architecture Knowledge
description: Shared understanding of how APM package system works
tags: [apm, architecture, package-management]
---

# APM Package Architecture

Shared knowledge about the Agent Package Manager (APM) architecture and how packages are structured, discovered, and integrated.

## Package Discovery Process

APM discovers packages through a hierarchical validation process:

1. **Structure Detection**: Checks for `apm.yml` and/or `SKILL.md` at package root
2. **Type Classification**: Determines package type (APM, Claude Skill, Hybrid)
3. **Primitive Scanning**: Searches `.apm/` directory for primitive files
4. **Validation**: Ensures `.apm/` contains actual content (not empty directories)

### Package Types

| Type | Has apm.yml | Has SKILL.md | Has .apm/ | Behavior |
|------|-------------|--------------|-----------|----------|
| **APM Package** | ✅ | ❌ | ✅ Required | Standard APM package |
| **Claude Skill** | ❌ | ✅ | ⚠️ Optional | Auto-generates apm.yml |
| **Hybrid** | ✅ | ✅ | ✅ Required | Best of both worlds |

## Primitive File Patterns

APM searches for primitives using these glob patterns:

```
.apm/instructions/*.instructions.md
.apm/prompts/*.prompt.md
.apm/agents/*.agent.md
.apm/contexts/*.context.md
.apm/memory/*.memory.md
```

**Critical Validation Rule**: At least one primitive type must have actual `.md` files with content. Empty directories fail validation.

## Integration Flow

When you run `apm install vineethsoma/agent-packages/skills/my-skill`:

1. **Download**: Clones from GitHub to `apm_modules/vineethsoma/agent-packages/skills/my-skill/`
2. **Validate**: Checks package structure and primitive content
3. **Integrate Primitives**:
   - Prompts → `.github/prompts/` with `-apm` suffix
   - Agents → `.github/agents/` with `-apm` suffix
   - Instructions → Compiled into `AGENTS.md`
4. **Integrate Skills**: If has `SKILL.md` → `.github/skills/skill-name/`
5. **Update .gitignore**: Excludes generated files

## Dependency Resolution

APM resolves dependencies recursively:

```yaml
# Package A depends on Package B
# Package B depends on Package C
# APM installs: C → B → A (bottom-up)
```

**No circular dependencies allowed** - APM will detect and fail.

## Directory Structure After Install

```
your-project/
├── apm.yml                          # Your dependencies
├── AGENTS.md                        # Compiled instructions
├── apm_modules/                     # Downloaded packages
│   └── owner/repo/package/         # Package source
├── .github/
│   ├── prompts/
│   │   └── workflow-name-apm.prompt.md    # Integrated
│   ├── agents/
│   │   └── agent-name-apm.agent.md        # Integrated
│   └── skills/
│       └── skill-name/                     # Native skill
│           └── SKILL.md
```

## Common Validation Errors

### "Missing required directory: .apm/"
**Cause**: Package has `apm.yml` but no `.apm/` folder
**Fix**: Create `.apm/` and add at least one primitive type with content

### "Subdirectory is not a valid APM package"
**Cause**: Subdirectory package missing `.apm/` or content
**Fix**: Ensure subdirectory has `apm.yml` AND `.apm/` with files

### "No primitive files found in .apm/ directory"
**Cause**: `.apm/` exists but all subdirectories are empty
**Fix**: Add actual `.md` files in at least one primitive type folder

## Compilation Process

`apm compile` generates `AGENTS.md` from instructions:

1. **Discovery**: Finds all `.instructions.md` files
2. **Filtering**: Applies `applyTo` patterns
3. **Optimization**: Uses context optimizer to fit within token limits
4. **Generation**: Creates hierarchical `AGENTS.md` files

## Skill vs Agent vs Instruction

| Primitive | Purpose | Integration Target | Usage |
|-----------|---------|-------------------|-------|
| **Skill** | Complete capability package | `.github/skills/` | Native skill format |
| **Agent** | Specialized persona | `.github/agents/` | Dropdown selection |
| **Instruction** | Domain guidelines | `AGENTS.md` | Always active |
| **Prompt** | Workflow template | `.github/prompts/` | `/` command |

## Best Practices for Package Authors

1. **Always test locally first**: Use local path installation before pushing
2. **Populate .apm/ fully**: Don't leave empty placeholder directories
3. **Write clear SKILL.md**: Explain what, why, and how to use
4. **Use semantic versioning**: Breaking changes = major bump
5. **Test integration**: Verify primitives work after installation
6. **Document dependencies**: Clearly list what skills depend on others

## APM Standards Compliance

This repository follows:
- [AGENTS.md standard](https://agents.md) for instruction compilation
- [Agent Skills standard](https://agentskills.io) for skill format
- [MCP](https://modelcontextprotocol.io) for tool integration

## References

- [APM Documentation](https://github.com/danielmeppiel/apm)
- [Primitives Guide](https://github.com/danielmeppiel/apm/blob/main/docs/primitives.md)
- [Skills Guide](https://github.com/danielmeppiel/apm/blob/main/docs/skills.md)
