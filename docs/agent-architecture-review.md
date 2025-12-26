# Agent Architecture Review & Recommendations

Based on US-001 retrospective learnings and agent feedback analysis.

## 1. Retro Specialist Subagent Invocation ✅

**Status**: Already working! Successfully demonstrated in US-001 retrospective.

**What Happened**:
- Retro Specialist invoked all 5 agents as subagents
- Each agent provided structured YAML feedback
- Feedback synthesized into comprehensive retrospective

**What Needs Documentation**:

Update `retro-specialist.agent.md` to explicitly document subagent consultation workflow:

```markdown
### 2. Gather Feedback via Subagent Consultation

**Use `runSubagent` tool to consult each contributor**:

```bash
runSubagent(
  agentName="tdd-specialist",
  prompt="[Story context + request for YAML feedback]"
)
```

**Agents to consult**:
- TDD Specialist (test discipline feedback)
- Fullstack Engineer (implementation feedback)
- Playwright Specialist (E2E testing feedback)
- Code Quality Auditor (quality gates feedback)
- Feature Lead (process and DoD feedback)

**Benefits**:
- Structured, consistent feedback format
- Each agent reflects from their domain expertise
- Captures domain-specific learnings
- Prevents forgetting to gather input
```

**Action**: Update retro-specialist agent instructions to include subagent consultation as mandatory step.

---

## 2. Playwright Tools Access for Manual Integration Testing

**Current State**:
- Playwright Specialist has `playwright/*` tools
- Fullstack Engineer has standard tools only
- Feature Lead has read-only tools

**Problem**: Integration testing requires Playwright tools for manual verification (browser navigation, screenshots, console inspection).

### Recommendation: Add Playwright Tools to Fullstack Engineer

**Rationale**:
1. Fullstack Engineer implements features end-to-end
2. Manual integration verification is part of DoD (new mandate)
3. Engineer needs to capture screenshot evidence before PR
4. Engineer troubleshoots integration issues (CORS, type mismatches)

**Updated fullstack-engineer tools**:
```yaml
tools: ['execute', 'read', 'edit', 'search', 'todo', 'playwright/*']
```

**Why not give to everyone?**:
- Feature Lead: Coordination role, doesn't implement → no need
- TDD Specialist: Reviews tests, doesn't run integration → no need
- Code Quality Auditor: Reviews code, doesn't execute → no need
- Retro Specialist: Facilitates reflection, doesn't test → no need

**Why Playwright Specialist keeps full access**:
- Writes E2E tests (needs all Playwright capabilities)
- Debugs E2E test failures
- Designs test automation strategies

**Implementation**:
- Update `fullstack-engineer.agent.md` tools list
- Add "Integration Verification" section to fullstack engineer workflow
- Document Playwright tool usage for manual validation

---

## 3. CI/CD Specialist - Do We Need One?

**Context**: Agents identified need for:
- GitHub Actions E2E workflow
- Integration test pipeline
- Pre-merge quality gates
- Automated evidence collection

### Option A: Create DevOps Specialist ⭐ **RECOMMENDED**

**Rationale**:
- CI/CD setup is specialized knowledge (GitHub Actions, Docker, deployment)
- Fullstack Engineer should focus on feature implementation
- Centralized CI/CD expertise prevents inconsistencies
- Can handle: pipelines, monitoring, infrastructure-as-code

**DevOps Specialist Responsibilities**:
- Create/maintain GitHub Actions workflows
- Set up integration test pipelines
- Configure MCP servers in CI environment
- Design deployment automation
- Monitor pipeline health
- Optimize build times

**Skills Required**:
- Docker & containerization
- GitHub Actions YAML
- CI/CD best practices
- Infrastructure as code (Terraform, CloudFormation)
- Monitoring and observability

**Handoffs**:
- FROM Feature Lead: "Set up CI pipeline for integration tests"
- TO Fullstack Engineer: "Pipeline ready, integrate your tests"

### Option B: Extend Fullstack Engineer (NOT RECOMMENDED)

**Why not**: Context rot. Fullstack Engineer already has:
- Backend development
- Frontend development
- Database design
- API contracts
- Integration testing
- Refactoring discipline

Adding CI/CD would overload the agent's context.

### Recommendation: Create `devops-specialist` Agent

**Priority**: HIGH (integration testing mandate requires CI enforcement)

---

## 4. Fullstack Engineer Bloat - Split into Specialists?

**Current Fullstack Engineer Responsibilities**:
- Backend development (Node.js, Express, TypeScript)
- Frontend development (React, TypeScript, TanStack Query)
- Database design (PostgreSQL, SQLite, migrations)
- API contracts (OpenAPI, REST, type safety)
- Integration testing (manual verification)
- TDD workflow (unit + integration tests)
- Refactoring patterns
- Git worktree management

### Problem: Context Rot Risk

**Symptoms**:
- Agent instructions are 192 lines (already substantial)
- Multiple domains to track simultaneously
- Risk of shallow expertise in each area

### Recommendation: Gradual Specialization ⭐

**Phase 1: Keep Unified (Current) - For Simple Projects**

Keep fullstack-engineer as-is for:
- Small projects (< 10K LOC)
- Solo developer workflows
- Rapid prototyping
- Balanced frontend/backend work

**Phase 2: Introduce Specialists - For Complex Projects**

Create specialized agents:

#### Backend Specialist
**Focus**: API development, database, business logic
- Tools: `execute`, `read`, `edit`, `search`, `todo`
- Skills: `claude-framework`, `tdd-workflow`, `backend-expertise`
- Scope: Everything backend (routes, services, DB)

#### Frontend Specialist
**Focus**: React components, state management, UI/UX
- Tools: `execute`, `read`, `edit`, `search`, `todo`, `playwright/*`
- Skills: `claude-framework`, `tdd-workflow`, `frontend-expertise`
- Scope: Everything frontend (components, hooks, styles)

#### Integration Specialist (NEW)
**Focus**: Connecting frontend ↔ backend
- Tools: `execute`, `read`, `edit`, `search`, `playwright/*`
- Skills: `integration-testing`, `tdd-workflow`, `fullstack-expertise`
- Scope: API contracts, CORS, type alignment, E2E tests

**When to Use Specialists**:
- Project size > 10K LOC
- Team has multiple developers
- Clear frontend/backend separation
- Complex integration requirements

**When to Use Fullstack**:
- Project size < 10K LOC
- Solo developer
- Rapid iteration
- Balanced frontend/backend work (like birdmate MVP)

### Decision Matrix

| Project Characteristic | Use Fullstack | Use Specialists |
|------------------------|---------------|-----------------|
| Size | < 10K LOC | > 10K LOC |
| Team | Solo/pair | 3+ developers |
| Architecture | Monolith | Microservices |
| Iteration speed | Fast | Stable |
| Domain complexity | Low-medium | High |

### Recommendation for Birdmate (Current State)

**Keep fullstack-engineer** because:
- Project is < 5K LOC (MVP phase)
- Solo development (one story at a time)
- Simple architecture (Express + React)
- Rapid iteration required

**Prepare for specialization** when:
- Project reaches 10K LOC
- Multiple stories run in parallel
- Team expands beyond 2 agents
- Backend or frontend becomes complex

---

## Implementation Plan

### Immediate (High Priority)

1. **Document subagent consultation in retro-specialist** ✅ (already working, just document)
   - Update retro-specialist.agent.md
   - Add "Subagent Consultation" section
   - Version bump: v1.0.0 → v1.1.0

2. **Add Playwright tools to fullstack-engineer** (for integration testing)
   - Update tools: add `playwright/*`
   - Add "Manual Integration Verification" workflow section
   - Document Playwright tool usage for DoD compliance
   - Version bump: v1.0.0 → v1.1.0

3. **Create devops-specialist agent** (for CI/CD)
   - New agent: `agents/devops-specialist/`
   - Skills: Docker, GitHub Actions, monitoring
   - Handoffs to/from feature-lead and fullstack-engineer
   - Version: v1.0.0

### Future (Medium Priority)

4. **Split fullstack-engineer when project grows**
   - Monitor birdmate LOC (currently ~3K)
   - Prepare backend-specialist, frontend-specialist, integration-specialist
   - Trigger: 10K LOC or parallel story development

---

## Summary

| Question | Answer | Priority | Action |
|----------|--------|----------|--------|
| 1. Retro subagent invocation? | ✅ Already working, needs documentation | HIGH | Update retro-specialist docs |
| 2. Playwright tools for integration? | ✅ Add to fullstack-engineer | HIGH | Update fullstack-engineer tools |
| 3. Need CI/CD specialist? | ✅ YES - Create devops-specialist | HIGH | New agent |
| 4. Fullstack engineer bloat? | ⚠️ Monitor - specialize at 10K LOC | MEDIUM | Deferred until needed |

**Next Steps**:
1. Update retro-specialist agent with subagent consultation workflow
2. Update fullstack-engineer with Playwright tools and integration verification
3. Create devops-specialist agent for CI/CD automation
4. Monitor birdmate project size for specialization trigger
