# MCP Servers - Docker Deployment

Docker-based MCP server deployment for production environments.

## Available Servers

### Playwright MCP
Browser automation and E2E testing server.

**Tools Provided**:
- `browser_navigate`, `browser_screenshot`, `browser_click`
- `browser_fill`, `browser_select`, `browser_hover`
- `browser_evaluate`

## Quick Start

### Pull and Run

```bash
# Pull the official Microsoft image
docker-compose pull playwright-mcp

# Start Playwright MCP server
docker-compose up -d playwright-mcp

# View logs
docker-compose logs -f playwright-mcp

# Stop services
docker-compose down
```

### Test MCP Server

```bash
# Test server responds to MCP protocol
echo '{"jsonrpc":"2.0","method":"initialize","id":1,"params":{"capabilities":{}}}' | \
  docker-compose exec -T playwright-mcp playwright-mcp

# Expected: JSON response with server capabilities
```

## Configuration

### VS Code Copilot

Add to `.vscode/settings.json`:

```json
{
  "github.copilot.chat.codeGeneration.mcp": {
    "mcpServers": {
      "playwright": {
        "command": "docker",
        "args": [
          "run",
          "-i",
          "--rm",
          "--init",
          "mcr.microsoft.com/playwright/mcp"
        ]
      }
    }
  }
}
```

### Claude Desktop

Add to `~/.claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "playwright": {
      "command": "docker",
      "arrun",
        "-i",
        "--rm",
        "--init",
        "mcr.microsoft.com/playwright/", "-i",
        "playwright-mcp"
      ]
    }
  }
}
```

## Management

### Update Server

```bash
# Pull latest version
docker-compose pull playwright-mcp

# Restart service with new image
docker-compose up -d playwright-mcp
```

### View Resource Usage

```bash
docker stats playwright-mcp-server
```

### Clean Up

```bash
# Stop and remove containers
docker-compose down

# Remove volumes (cached browsers)
docker-compose down -v

# Remove images
docker rmi mcr.microsoft.com/playwright/mcp:latest
```

## Troubleshooting

### Server Not Responding

```bash
# Check if container is running
docker-compose ps

# View logs
docker-compose logs playwright-mcp

# Restart service
docker-compose restart playwright-mcp
```

### Browser Installation Issues

```bash
# Rebuild without cache
docker-compose build --no-cache playwright-mcp

# Check browser installation
docker-compose exec playwright-mcp npx playwright install --dry-run
```

### Memory Issues

If browsers crash, increase memory limits in `docker-compose.yml`:

```yaml
deploy:
  resources:
    limits:
      memory: 4G  # Increase from 2G
```

## Benefits of Docker Deployment

✅ **Isolated environment** - No conflicts with host system
✅ **Reproducible** - Same environment everywhere
✅ **Version-locked** - Specific Playwright version
✅ **Resource limits** - CPU/memory constraints
✅ **Easy cleanup** - Remove containers cleanly

## When to Use

- **Production deployments**
- **CI/CD pipelines**
- **Team consistency** (everyone uses same setup)
- **Multiple MCP server versions**

## When NOT to Use

- **Quick prototyping** (use npx instead)
- **Development with frequent updates**
- **Limited Docker experience** (start with npx)

---

**Note**: MCP servers communicate via stdio (stdin/stdout), not HTTP ports.
