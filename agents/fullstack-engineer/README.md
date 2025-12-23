# Full-Stack Engineer Agent

An expert full-stack engineer AI agent for complete application development. Get specialized guidance across frontend, backend, database, and DevOps layers.

## Features

✨ **Expert Guidance Across All Layers**
- Backend API design and microservices
- Frontend frameworks and responsive design
- Database architecture and optimization
- DevOps and infrastructure
- Comprehensive testing strategies

🎯 **Specialized Decision-Making**
- Architectural trade-off analysis
- Technology selection guidance
- Performance optimization strategies
- Security best practices
- Scalability planning

🚀 **Production-Ready Solutions**
- Battle-tested patterns and practices
- Real-world implementation guidance
- Team mentoring and knowledge sharing
- Clear documentation of decisions

## Installation

```bash
# Install into your project
apm install your-org/fullstack-engineer

# Compile AGENTS.md with the agent
apm compile
```

## Usage

### With Copilot or Claude

Simply mention the agent in your chat:

```
@fullstack-engineer
I need to implement user authentication. What's the best approach?
```

### In APM Workflows

Create a workflow that uses this agent:

```yaml
---
description: Implement a feature end-to-end
mode: fullstack-engineer
input: [feature_name, requirements]
---

# Implement ${input:feature_name}

Review these requirements and design a complete implementation:
${input:requirements}

Provide:
1. Database schema design
2. Backend API specification
3. Frontend component structure
4. Testing strategy
5. Deployment considerations
```

## What You Get

### The Agent Persona

A full-stack engineer with expertise in:

- **Backend**: RESTful APIs, microservices, database design, caching, security
- **Frontend**: React/Vue/Angular, responsive design, state management, performance
- **Database**: SQL/NoSQL, schema design, optimization, migrations
- **DevOps**: Docker, Kubernetes, CI/CD, monitoring, cloud platforms
- **Testing**: Unit, integration, e2e, performance, security testing

### Decision-Making Framework

The agent follows a structured approach:
1. Understand the problem and constraints
2. Explain trade-offs between options
3. Recommend production-ready solutions
4. Justify architectural decisions
5. Guide implementation strategy

### Communication Style

- **Clear**: Explains complex concepts in practical terms
- **Practical**: Focuses on real-world, working solutions
- **Mentoring**: Helps teams learn and improve
- **Well-Documented**: Always explains the "why" behind decisions

## Common Use Cases

### 🏗️ System Design
Get expert help designing scalable systems that work end-to-end.

### 🔧 Feature Development
Build complete features with confidence across all layers.

### 📊 Performance Optimization
Identify bottlenecks and optimize across the entire stack.

### 🔐 Security Implementation
Apply security best practices from frontend to database.

### 👥 Code Reviews
Get expert feedback on full-stack implementations.

### 🎓 Team Mentoring
Learn full-stack patterns and best practices.

## Project Structure

```
fullstack-engineer/
├── apm.yml                    # Package manifest
├── SKILL.md                   # This file (package guide)
├── README.md                  # Full documentation
└── .apm/
    ├── agents/
    │   └── fullstack-engineer.agent.md    # Agent definition
    ├── instructions/          # Can add coding standards
    └── prompts/              # Can add workflow examples
```

## Extending the Agent

You can customize this agent for your team:

### Add Instructions

Create `.apm/instructions/` files for your tech stack:

```yaml
---
description: Full-stack TypeScript standards
applyTo: "**/*.ts"
---

# TypeScript Standards

- Use strict mode
- Define interfaces for all types
- ...
```

### Add Prompts

Create `.apm/prompts/` workflows:

```yaml
---
description: Design and implement a feature
mode: fullstack-engineer
input: [feature_description, tech_stack]
---

# Feature Implementation Template

Design and implement: ${input:feature_description}
Using: ${input:tech_stack}
```

## Requirements

- **APM**: Version 0.6.0 or higher
- **Compatibility**: Works with GitHub Copilot, Claude Code, Cursor, and other AI tools

## Contributing

Improvements welcome! You can:

- Add new expertise areas to the agent
- Create technology-specific instructions
- Share common workflows and prompts
- Provide feedback on agent effectiveness

## License

This agent package is part of the APM ecosystem and follows the same license as your APM installation.

---

**Built with APM** - The package manager for AI-native development
