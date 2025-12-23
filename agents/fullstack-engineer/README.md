# Full-Stack Engineer Agent

An expert full-stack engineer AI agent for complete application development with **Spec-Driven Development** methodology. Get specialized guidance across frontend, backend, database, and DevOps layers using structured specifications and intent-driven development.

## Features

✨ **Expert Guidance Across All Layers**
- Backend API design and microservices
- Frontend frameworks and responsive design
- Database architecture and optimization
- DevOps and infrastructure
- Comprehensive testing strategies

📋 **Spec-Driven Development (NEW)**
- Structured specification workflow using GitHub spec-kit
- Constitution-based project principles
- Intent-driven development (what/why before how)
- Multi-step refinement (specify → plan → tasks → implement)
- Technology-independent methodology

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

🔗 **Agent Integration**
- Receives delegated stories from Feature Lead
- Collaborates with TDD Specialist for test coverage
- Gets code reviews from Code Quality Auditor (via claude-framework skill)

**Note**: Handoffs to Code Quality Auditor require the `claude-framework` skill to be installed.

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

- **Spec-Driven Development**: GitHub spec-kit workflow, constitution design, progressive refinement
- **Backend**: RESTful APIs, microservices, database design, caching, security
- **Frontend**: React/Vue/Angular, responsive design, state management, performance
- **Database**: SQL/NoSQL, schema design, optimization, migrations
- **DevOps**: Docker, Kubernetes, CI/CD, monitoring, cloud platforms
- **Testing**: Unit, integration, e2e, performance, security testing (TDD-first)

### Decision-Making Framework

The agent follows a structured approach:
1. **Establish constitution** (if starting new project)
2. **Specify requirements** (what/why before how)
3. **Clarify ambiguities** (optional but recommended)
4. **Plan technical design** (how to implement)
5. **Analyze consistency** (validate spec ↔ plan ↔ tasks)
6. **Break down into tasks** (actionable, ordered list)
7. **Implement with TDD** (Red → Green → Refactor)
8. **Verify constitution compliance** (principles upheld)

### Communication Style

- **Clear**: Explains complex concepts in practical terms
- **Practical**: Focuses on real-world, working solutions
- **Mentoring**: Helps teams learn and improve
- **Well-Documented**: Always explains the "why" behind decisions
- **Spec-First**: Encourages specification before implementation

## Common Use Cases

### 📐 Spec-Driven Feature Development (NEW)
Use structured specifications to build features with clear intent:
- Define project constitution with core principles
- Write specifications focusing on what/why (not how)
- Create technical plans with tech stack choices
- Break down into actionable tasks
- Implement with TDD and constitution compliance

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
Learn full-stack patterns, best practices, and spec-driven methodology.

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
