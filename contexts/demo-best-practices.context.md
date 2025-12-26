# Demo Best Practices

Context for running live feature demos with Playwright MCP browser automation.

## Playwright MCP Live Demo Workflow

### Pre-Demo Setup

1. **Start Backend**:
   ```bash
   cd backend && npm run dev
   ```
   - Verify backend logs show correct CORS origins
   - Confirm port (typically 3001)
   - Check `/health` endpoint responds

2. **Start Frontend**:
   ```bash
   cd frontend && npm run dev
   ```
   - Note actual frontend port (may not be default 5173)
   - Verify Vite dev server message
   - Update backend CORS if port differs

3. **Verify Integration**:
   ```bash
   # Test CORS preflight
   curl -I -X OPTIONS http://localhost:3001/api/v1/search \
     -H "Origin: http://localhost:5175" \
     -H "Access-Control-Request-Method: POST"
   
   # Test API endpoint
   curl -X POST http://localhost:3001/api/v1/search \
     -H "Content-Type: application/json" \
     -d '{"query": "test"}' | jq '.'
   ```

### Demo Script Template

```bash
# 1. Navigate to application
mcp_microsoft_pla_browser_navigate(url="http://localhost:5175")

# 2. Take initial screenshot
mcp_microsoft_pla_browser_screenshot(description="Initial landing page")

# 3. Execute primary user flow
mcp_microsoft_pla_browser_type(selector="input[placeholder='Search...']", text="red bird")
mcp_microsoft_pla_browser_click(selector="button:has-text('Search')")

# 4. Wait for results
mcp_microsoft_pla_browser_wait_for(selector="[data-testid='search-results']", state="visible")

# 5. Capture results
mcp_microsoft_pla_browser_screenshot(description="Search results for: red bird")

# 6. Verify success criteria
# - Results displayed
# - No console errors
# - Network requests successful
```

### Debugging Mid-Demo

**Check Console Errors**:
```bash
mcp_microsoft_pla_browser_console_messages(level='error')
# Look for: CORS errors, type errors, undefined errors
```

**Inspect Network Traffic**:
```bash
mcp_microsoft_pla_browser_network_requests()
# Filter: status=failed or method=OPTIONS
# Check: Response headers include Access-Control-Allow-Origin
```

**Capture Current State**:
```bash
mcp_microsoft_pla_browser_snapshot()
# Returns: HTML structure, visible text, interactive elements
```

**Inspect Specific Element**:
```bash
mcp_microsoft_pla_browser_inspect(selector="[data-testid='result-card']")
# Returns: Attributes, text content, style
```

### Common Demo Failures

| Issue | Symptom | Quick Fix | Prevention |
|-------|---------|-----------|------------|
| CORS error | `ERR_FAILED` network request | Update backend `.env` CORS_ORIGIN, restart backend | Use port range (5173-5175) |
| Type error | `Cannot read property 'x' of undefined` | Check API response structure vs frontend types | Validate API contract before demo |
| Stale UI | Old code showing after changes | Remove `.js` files from `src/`, hard refresh browser (`Cmd+Shift+R`) | Add `.js` to `.gitignore` |
| Port conflict | `EADDRINUSE` error | `lsof -ti:3001 \| xargs kill -9` | Use different port or kill previous instance |
| Cache issues | Old data showing | Clear browser cache, restart dev servers | Use cache-busting in dev mode |

### Demo Scenarios to Test

**Happy Path**:
- Primary user flow (most common use case)
- Expected inputs with expected outputs
- Smooth transitions and loading states

**Edge Cases**:
- Empty state (no results found)
- Error state (API failure)
- Boundary conditions (very long input, special characters)

**Integration Points**:
- Cross-origin requests (CORS)
- API contract adherence (response structure)
- State persistence (URL params, localStorage)

### Post-Demo Validation

**Evidence Collection**:
- [ ] Screenshots of working feature
- [ ] Console log showing zero errors
- [ ] Network tab showing successful requests
- [ ] API response sample saved to specs/

**Integration Verification**:
- [ ] Both services ran on expected ports
- [ ] CORS headers present in responses
- [ ] Type transformations working correctly
- [ ] No stale build artifacts

## When to Use Playwright MCP for Demos

**Good Use Cases**:
- Visual feature demonstrations
- Integration testing with live services
- Debugging cross-origin issues
- Capturing evidence for retrospectives

**Alternatives**:
- **curl**: For API-only validation
- **Manual testing**: For exploratory flows
- **Unit tests**: For logic validation
- **Playwright E2E**: For automated regression testing

## Related Skills

- [integration-testing](../skills/integration-testing/SKILL.md) - Integration validation patterns
- [playwright-testing](../skills/playwright-testing/SKILL.md) - E2E test standards
- [fullstack-expertise](../skills/fullstack-expertise/SKILL.md) - Full-stack best practices

---

**Use this workflow for**: Feature acceptance demos, integration debugging, stakeholder presentations, retrospective evidence collection.
