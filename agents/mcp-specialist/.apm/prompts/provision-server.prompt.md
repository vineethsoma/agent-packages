# Provision MCP Server

Step-by-step workflow for provisioning an MCP server for agent use.

## Inputs Required

- **Use case**: What capability does the agent need?
- **Target agent**: Which agent package will use this server?
- **Deployment preference**: npx (development) or docker (production)?

## Workflow

### Step 1: Identify Server from Catalog

Review [contexts/mcp-catalog.context.md](../../../contexts/mcp-catalog.context.md) and match use case to available servers.

**Questions to answer**:
- Does a server exist for this use case?
- What tools does it provide?
- What are the prerequisites (API keys, Docker, etc.)?

### Step 2: Test Server Connectivity

```bash
# For NPX servers
npx -y @package/mcp-server --version

# For Docker servers
docker pull image:tag
docker run -it --rm image:tag --help

# Test MCP protocol
echo '{"jsonrpc":"2.0","method":"initialize","id":1,"params":{"capabilities":{}}}' | npx -y @package/mcp-server
```

**Validation Gate**: ⏸️ Confirm server responds to MCP initialize

### Step 3: Generate Configuration

Create the appropriate configuration block:

**For VS Code Copilot** (`.vscode/settings.json`):
```json
{
  "github.copilot.chat.codeGeneration.mcp": {
    "mcpServers": {
      "server-name": {
        "command": "npx",
        "args": ["-y", "@package/mcp-server"]
      }
    }
  }
}
```

**For Claude** (`~/.claude/claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@package/mcp-server"]
    }
  }
}
```

### Step 4: Document for Agent Package

Prepare handoff to package-manager with:

1. **Server name**: Identifier for the server
2. **Configuration**: Ready-to-paste JSON
3. **Tools provided**: List of available tools
4. **Prerequisites**: API keys, Docker, etc.
5. **Usage examples**: How agent should invoke tools

### Step 5: Handoff to Package Manager

Use "Add MCP to Agent Package" handoff with all documentation.

## Example Workflow

**Request**: "Provision Playwright for E2E testing agent"

**Step 1**: Found `playwright-mcp` in catalog
- Provider: Microsoft
- Method: npx
- Tools: browser_navigate, browser_screenshot, browser_click, etc.

**Step 2**: Test connectivity
```bash
npx -y @microsoft/playwright-mcp --version
# Output: 1.x.x ✅
```

**Step 3**: Configuration
```json
{
  "playwright": {
    "command": "npx",
    "args": ["-y", "@microsoft/playwright-mcp"]
  }
}
```

**Step 4**: Documentation
- Tools: browser automation suite
- Prerequisites: None (auto-installs browsers)
- Agent: playwright-specialist

**Step 5**: Handoff → package-manager

## Troubleshooting

### Server Not Found in Catalog
1. Search MCP ecosystem for new servers
2. Test server compatibility
3. Add to catalog if validated
4. Proceed with provisioning

### Server Fails to Start
1. Check Node.js version (18+ required)
2. Verify network access (for npx)
3. Check Docker daemon (for docker)
4. Review server-specific prerequisites

### Tools Not Available
1. Verify server implements MCP protocol
2. Check tool names match documentation
3. Test with manual MCP request
