---
applyTo: "**/compose.yaml,**/compose.yml,**/docker-compose.yaml,**/docker-compose.yml"
description: Docker Compose file standards for MCP server deployment
---

# Docker Compose Standards for MCP Servers

Apply these standards when creating or modifying Docker Compose configurations for MCP servers.

## File Naming

**Preferred**: `compose.yaml` (official Docker recommendation)
**Acceptable**: `docker-compose.yaml`, `compose.yml`, `docker-compose.yml`

## MCP Server Requirements

### Stdio Protocol Support

**MANDATORY for all MCP servers**:
```yaml
services:
  mcp-server:
    stdin_open: true  # REQUIRED
    tty: true         # REQUIRED
```

**Rationale**: MCP servers communicate via stdin/stdout, not HTTP. Without these settings, the MCP protocol won't function.

### No Port Mapping Needed

MCP stdio-based servers don't need `ports:` section:
```yaml
services:
  playwright-mcp:
    image: mcr.microsoft.com/playwright/mcp:latest
    stdin_open: true
    tty: true
    # No ports: section needed
```

## Service Configuration Best Practices

### Image Pinning

**Production**:
```yaml
image: postgres:16.1  # Pin to specific version
```

**Development**:
```yaml
image: postgres:16    # Major version OK
image: postgres:latest  # Acceptable for local dev only
```

### Restart Policies

```yaml
restart: unless-stopped  # RECOMMENDED for MCP servers
restart: always          # Alternative (restarts even after manual stop)
restart: on-failure      # Only restart on error exit codes
restart: "no"            # Default (no restart)
```

**Rationale**: `unless-stopped` provides automatic recovery while respecting manual stops.

### Resource Limits

**Always set limits** to prevent resource exhaustion:
```yaml
deploy:
  resources:
    limits:
      cpus: '1.0'
      memory: 1G
    reservations:
      cpus: '0.5'
      memory: 512M
```

**Typical MCP server resources**:
- CPU: 0.5-1.0 cores
- Memory: 512M-1G

### Volume Mounts

**Read-only when possible**:
```yaml
volumes:
  - ./workspace:/workspace:ro  # Read-only access
  - ./data:/data               # Read-write for output
```

**Use named volumes for persistence**:
```yaml
volumes:
  - mcp-data:/data

volumes:
  mcp-data:
    # Docker manages storage location
```

## Health Checks

**Include health checks for dependent services**:
```yaml
services:
  database:
    image: postgres:16
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "user", "-d", "db"]
      interval: 2s
      timeout: 2s
      retries: 3
      start_period: 2s

  app:
    depends_on:
      database:
        condition: service_healthy  # Wait for health check
```

**Health check components**:
- `test`: Command to check health
- `interval`: How often to check
- `timeout`: Max time for single check
- `retries`: Failures before marking unhealthy
- `start_period`: Grace period on startup

## Environment Variables

**Use `.env` files for configuration**:
```yaml
services:
  database:
    env_file:
      - .env
      - .env.local  # Override with local settings
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}  # Reference from .env
      POSTGRES_USER: ${DB_USER}
```

**Never commit secrets** to version control:
```bash
# .gitignore
.env.local
.env.override
*.secret
```

## Networks

**Explicit networks for isolation**:
```yaml
services:
  frontend:
    networks:
      - public

  backend:
    networks:
      - public
      - private

  database:
    networks:
      - private  # Only accessible to backend

networks:
  public:
  private:
```

**Default network**: Docker creates default network for all services automatically.

**Service DNS**: Containers can reach each other by service name:
```yaml
services:
  app:
    # Can connect to database at: postgresql://database:5432
  database:
    image: postgres:16
```

## Service Dependencies

**Basic dependency**:
```yaml
services:
  app:
    depends_on:
      - database  # Start database first
```

**Conditional dependency** (recommended):
```yaml
services:
  app:
    depends_on:
      database:
        condition: service_healthy  # Wait for health check
        restart: true               # Restart if database restarts
```

## File Organization

**Standard structure**:
```
mcp-servers/
├── compose.yaml          # Main configuration
├── .env                  # Environment variables (gitignored)
├── .env.example          # Template (committed)
├── README.md             # Setup instructions
└── data/                 # Persistent data (gitignored)
    ├── playwright/
    └── filesystem/
```

## Validation

**Check syntax**:
```bash
docker compose config
```

**Validate and view merged config**:
```bash
docker compose config --resolve-image-digests
```

## Anti-Patterns

❌ **Missing stdio flags for MCP servers**:
```yaml
services:
  playwright:
    image: mcr.microsoft.com/playwright/mcp:latest
    # Missing stdin_open and tty - MCP won't work!
```

❌ **Using :latest in production**:
```yaml
image: postgres:latest  # Unpredictable updates
```

❌ **No resource limits**:
```yaml
services:
  app:
    # No deploy.resources - can exhaust host resources
```

❌ **Hardcoded secrets**:
```yaml
environment:
  DB_PASSWORD: "mysecretpassword"  # NEVER do this
```

❌ **Incorrect volume syntax**:
```yaml
volumes:
  - /data  # Anonymous volume, data lost on removal
```

✅ **Correct volume syntax**:
```yaml
volumes:
  - mcp-data:/data  # Named volume, data persists
```

## Example: Complete MCP Server Configuration

```yaml
version: '3'

services:
  playwright:
    image: mcr.microsoft.com/playwright/mcp:1.0.0  # Pinned version
    container_name: playwright-mcp
    stdin_open: true  # REQUIRED for MCP
    tty: true         # REQUIRED for MCP
    restart: unless-stopped
    volumes:
      - playwright-data:/data
      - ./workspace:/workspace:ro
    networks:
      - mcp-network
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 1G
        reservations:
          cpus: '0.5'
          memory: 512M
    environment:
      - LOG_LEVEL=${LOG_LEVEL:-info}

networks:
  mcp-network:
    driver: bridge

volumes:
  playwright-data:
```

---

**Remember**: MCP servers need `stdin_open: true` and `tty: true`. Always set resource limits. Pin versions in production.