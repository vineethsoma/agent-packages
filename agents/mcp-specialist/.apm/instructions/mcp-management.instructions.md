# MCP Server Management Standards

Guidelines for managing MCP server lifecycle and catalog maintenance.

## Server Selection Criteria

### Must Have
- ✅ Implements MCP protocol correctly
- ✅ Responds to `initialize` RPC call
- ✅ Documented tool list
- ✅ Actively maintained (updated within 6 months)

### Should Have
- ✅ NPX or Docker deployment option
- ✅ Clear prerequisites documented
- ✅ Error messages for common failures
- ✅ Version command support

### Nice to Have
- ✅ Multiple deployment options
- ✅ Health check endpoint
- ✅ Graceful shutdown handling
- ✅ Rate limiting documentation

## Deployment Method Selection

| Scenario | Recommended Method |
|----------|-------------------|
| Development/testing | NPX |
| Production deployment | Docker |
| Air-gapped environment | Local binary |
| CI/CD pipelines | Docker |
| Quick prototyping | NPX |
| Version-locked requirements | Docker with specific tag |

## Configuration Standards

### Naming Convention
```json
{
  "mcpServers": {
    "provider-capability": {  // e.g., "microsoft-playwright"
      "command": "...",
      "args": ["..."]
    }
  }
}
```

### Environment Variables
- Never hardcode secrets in configuration
- Use environment variable references
- Document required env vars in catalog

```json
{
  "server-name": {
    "command": "npx",
    "args": ["-y", "@package/server"],
    "env": {
      "API_KEY": "${SERVER_API_KEY}"
    }
  }
}
```

### Args Format
- Always use array of strings
- Each flag/value as separate element
- Quote paths with spaces

```json
// ✅ Correct
"args": ["-y", "@package/server", "--db-path", "/path/to/db"]

// ❌ Wrong
"args": "-y @package/server --db-path /path/to/db"
```

## Catalog Maintenance

### Adding New Server
1. Test server manually
2. Document all tools provided
3. Note prerequisites
4. Provide ready-to-paste config
5. Map to appropriate agents
6. Add to catalog with date

### Updating Server Entry
1. Test new version
2. Note breaking changes
3. Update tool list if changed
4. Update config if needed
5. Bump catalog version

### Deprecating Server
1. Mark as deprecated in catalog
2. Note replacement server
3. Keep entry for 6 months
4. Remove after migration period

## Testing Protocol

### Basic Connectivity
```bash
# Version check
npx -y @package/server --version

# MCP initialize
echo '{"jsonrpc":"2.0","method":"initialize","id":1,"params":{"capabilities":{}}}' | npx -y @package/server
```

### Tool Verification
```bash
# List tools
echo '{"jsonrpc":"2.0","method":"tools/list","id":2}' | npx -y @package/server

# Call specific tool
echo '{"jsonrpc":"2.0","method":"tools/call","id":3,"params":{"name":"tool_name","arguments":{}}}' | npx -y @package/server
```

### Docker Testing
```bash
# Pull and test
docker pull image:tag
docker run -i --rm image:tag

# Interactive test
docker run -it --rm image:tag /bin/sh
```

## Security Guidelines

### API Keys
- Store in environment variables
- Never commit to version control
- Rotate regularly
- Use separate keys per environment

### Network Access
- Document required network access
- Note if server phones home
- Consider firewall implications

### File System Access
- Limit to required paths only
- Use read-only where possible
- Document all path requirements

### Docker Security
- Use specific version tags (not `latest`)
- Run as non-root user
- Limit container capabilities
- Mount only required volumes

## Troubleshooting Guide

### Common Issues

**"Command not found"**
- Verify npx is in PATH
- Check Node.js version (18+)
- Try `npm install -g` instead

**"Connection refused"**
- Server may need port binding
- Check for port conflicts
- Verify Docker networking

**"Tools not showing"**
- Reload VS Code after config change
- Check JSON syntax
- Verify server name in allowed tools

**"Timeout"**
- Server may be slow to start
- Check network connectivity
- Increase timeout if configurable

### Debug Commands
```bash
# Check npx cache
npx cache ls

# Clear npx cache
npx clear-npx-cache

# Docker logs
docker logs container-id

# Docker inspect
docker inspect container-id
```
