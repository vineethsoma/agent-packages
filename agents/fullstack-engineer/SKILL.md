---
name: fullstack-engineer
description: Expert full-stack engineer agent for complete application development across frontend, backend, database, and DevOps
---

# Full-Stack Engineer Agent

An expert full-stack engineer agent specializing in complete application development with deep knowledge across the entire technology stack.

## What This Package Provides

A specialized AI agent persona designed to assist with full-stack application development. The agent is trained to:

- **Design Complete Features**: From database schema to user interface
- **Make Architectural Decisions**: Choose technologies that scale and maintain clarity
- **Optimize Performance**: Identify bottlenecks across all layers
- **Implement Security**: Apply best practices from frontend to database
- **Guide Teams**: Mentor developers on full-stack patterns and practices

## Quick Start

```bash
# Install the agent into your project
apm install your-org/fullstack-engineer

# Compile your AGENTS.md with the full-stack engineer
apm compile

# Use the agent in your AI tools (Copilot, Claude, etc.)
```

## When to Use This Agent

Use the full-stack engineer agent when you need help with:

✅ **Feature Implementation**: Building complete features end-to-end  
✅ **Architecture Decisions**: Choosing technologies and patterns  
✅ **Performance Optimization**: Analyzing and improving system performance  
✅ **Security Implementation**: Applying security best practices  
✅ **Code Reviews**: Getting expert feedback on full-stack implementations  
✅ **System Design**: Designing scalable and maintainable systems  

## Agent Expertise

- **Backend**: APIs, microservices, databases, authentication
- **Frontend**: React, Vue, Angular, responsive design, state management
- **Database**: SQL/NoSQL, schema design, optimization, migrations
- **DevOps**: Docker, Kubernetes, CI/CD, infrastructure, monitoring
- **Testing**: Unit, integration, e2e, performance, security testing

## Available Primitives

- **Agents**: Specialized persona in `.apm/agents/`
  - `fullstack-engineer.agent.md` - The full-stack engineer agent
- **Instructions**: Guardrails and standards in `.apm/instructions/` (can be added)
- **Prompts**: Executable workflows in `.apm/prompts/` (can be added)

## Example Usage

### In a Chat with Copilot or Claude:

```
@fullstack-engineer 
Help me design a user authentication system that works across our frontend and backend.
```

### In a Prompt Workflow:

```yaml
---
description: Implement a new feature end-to-end
mode: fullstack-engineer
---

# Implement Feature: ${input:feature_name}

Review the requirements and design a complete implementation from database to UI.
```

## Contributing

To improve this agent, you can:

1. Add new expertise areas to the agent definition
2. Create instructions for specific technologies
3. Add example prompts for common full-stack tasks
4. Share real-world patterns and solutions

## Support

For issues, improvements, or feedback on this agent:
- Open an issue in the repository
- Contribute improvements via pull request
- Share patterns that worked well for your team
