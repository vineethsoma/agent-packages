# MCP Server Catalog

Curated catalog of MCP servers for agent tool provisioning.

## Quick Reference

| Server | Provider | Method | Primary Use Case |
|--------|----------|--------|------------------|
| playwright-mcp | Microsoft | npx | Browser automation, E2E testing |
| filesystem | Anthropic | npx | File operations, directory traversal |
| github | GitHub | npx | Repository operations, issues, PRs |
| postgres | Anthropic | docker | Database queries, schema inspection |
| sqlite | Anthropic | npx | Local database operations |
| fetch | Anthropic | npx | HTTP requests, web scraping |
| memory | Anthropic | npx | Persistent key-value storage |
| puppeteer | Anthropic | npx | Browser automation (alternative) |
| brave-search | Brave | npx | Web search with API |
| slack | Anthropic | npx | Slack messaging, channels |
| google-drive | Anthropic | npx | Google Drive file operations |
| google-maps | Anthropic | npx | Location, directions, places |
| sentry | Sentry | npx | Error tracking, issue management |
| linear | Linear | npx | Issue tracking, project management |
| aws-kb-retrieval | AWS | npx | AWS Knowledge Base queries |
| everart | EverArt | npx | Image generation |
| sequential-thinking | Anthropic | npx | Step-by-step reasoning |

---

## Browser & Testing

### Microsoft Playwright MCP
**Best for**: E2E testing, browser automation, screenshot capture

```json
{
  "playwright": {
    "command": "npx",
    "args": ["-y", "@microsoft/playwright-mcp"]
  }
}
```

**Tools Provided**:
- `browser_navigate` - Navigate to URL
- `browser_screenshot` - Capture page screenshot
- `browser_click` - Click element
- `browser_fill` - Fill form field
- `browser_select` - Select dropdown option
- `browser_hover` - Hover over element
- `browser_evaluate` - Execute JavaScript

**Prerequisites**: None (Playwright installs browsers on first run)

**Agent Mapping**: playwright-specialist

---

### Puppeteer MCP
**Best for**: Lightweight browser automation, PDF generation

```json
{
  "puppeteer": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-puppeteer"]
  }
}
```

**Tools Provided**:
- `puppeteer_navigate`
- `puppeteer_screenshot`
- `puppeteer_click`
- `puppeteer_fill`
- `puppeteer_select`
- `puppeteer_evaluate`

**Prerequisites**: Chrome/Chromium

---

## Filesystem & Storage

### Filesystem MCP
**Best for**: File operations outside workspace, system file access

```json
{
  "filesystem": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-filesystem", "/allowed/path"]
  }
}
```

**Tools Provided**:
- `read_file` - Read file contents
- `write_file` - Write file contents
- `list_directory` - List directory contents
- `create_directory` - Create directory
- `move_file` - Move/rename file
- `search_files` - Search by pattern
- `get_file_info` - File metadata

**Security Note**: Must specify allowed paths as arguments

---

### Memory MCP
**Best for**: Persistent storage across sessions, key-value data

```json
{
  "memory": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-memory"]
  }
}
```

**Tools Provided**:
- `store` - Store key-value pair
- `retrieve` - Get value by key
- `delete` - Remove key
- `list_keys` - List all keys

---

## Database

### PostgreSQL MCP
**Best for**: Production database queries, schema inspection

```json
{
  "postgres": {
    "command": "docker",
    "args": [
      "run", "-i", "--rm",
      "-e", "DATABASE_URL=postgresql://user:pass@host:5432/db",
      "mcp/postgres"
    ]
  }
}
```

**Tools Provided**:
- `query` - Execute SQL query
- `describe_table` - Get table schema
- `list_tables` - List all tables

**Prerequisites**: Docker, database connection string

---

### SQLite MCP
**Best for**: Local databases, embedded storage

```json
{
  "sqlite": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-sqlite", "--db-path", "./database.db"]
  }
}
```

**Tools Provided**:
- `read_query` - Execute SELECT
- `write_query` - Execute INSERT/UPDATE/DELETE
- `create_table` - Create table
- `describe_table` - Get schema
- `list_tables` - List tables

---

## Web & API

### Fetch MCP
**Best for**: HTTP requests, API calls, web content retrieval

```json
{
  "fetch": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-fetch"]
  }
}
```

**Tools Provided**:
- `fetch` - HTTP GET/POST/PUT/DELETE
- Supports headers, body, auth

---

### Brave Search MCP
**Best for**: Web search with structured results

```json
{
  "brave-search": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-brave-search"],
    "env": {
      "BRAVE_API_KEY": "your-api-key"
    }
  }
}
```

**Tools Provided**:
- `brave_web_search` - Web search
- `brave_local_search` - Local business search

**Prerequisites**: Brave Search API key

---

## Source Control

### GitHub MCP
**Best for**: Repository management, issues, PRs, actions

```json
{
  "github": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-github"],
    "env": {
      "GITHUB_TOKEN": "your-token"
    }
  }
}
```

**Tools Provided**:
- `create_repository` - Create repo
- `search_repositories` - Search repos
- `create_issue` - Create issue
- `create_pull_request` - Create PR
- `push_files` - Push file changes
- `list_commits` - List commits
- `get_file_contents` - Read file from repo

**Prerequisites**: GitHub Personal Access Token

---

## Productivity

### Slack MCP
**Best for**: Team communication, channel management

```json
{
  "slack": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-slack"],
    "env": {
      "SLACK_TOKEN": "xoxb-your-token"
    }
  }
}
```

**Tools Provided**:
- `send_message` - Send to channel/DM
- `list_channels` - List channels
- `get_channel_history` - Read messages
- `add_reaction` - React to message

**Prerequisites**: Slack Bot Token

---

### Linear MCP
**Best for**: Issue tracking, sprint management

```json
{
  "linear": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-linear"],
    "env": {
      "LINEAR_API_KEY": "your-key"
    }
  }
}
```

**Tools Provided**:
- `create_issue` - Create issue
- `update_issue` - Update issue
- `list_issues` - Query issues
- `get_issue` - Get issue details

**Prerequisites**: Linear API Key

---

## Cloud & Infrastructure

### AWS Knowledge Base MCP
**Best for**: RAG queries against AWS Knowledge Bases

```json
{
  "aws-kb": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-aws-kb-retrieval"],
    "env": {
      "AWS_ACCESS_KEY_ID": "...",
      "AWS_SECRET_ACCESS_KEY": "...",
      "AWS_REGION": "us-east-1"
    }
  }
}
```

**Tools Provided**:
- `retrieve` - Query knowledge base

**Prerequisites**: AWS credentials, Knowledge Base ID

---

### Sentry MCP
**Best for**: Error tracking, issue investigation

```json
{
  "sentry": {
    "command": "npx",
    "args": ["-y", "@sentry/mcp-server"],
    "env": {
      "SENTRY_AUTH_TOKEN": "your-token"
    }
  }
}
```

**Tools Provided**:
- `list_issues` - List recent issues
- `get_issue` - Issue details
- `list_projects` - List projects

**Prerequisites**: Sentry Auth Token

---

## AI & Generation

### Sequential Thinking MCP
**Best for**: Complex reasoning, step-by-step problem solving

```json
{
  "sequential-thinking": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-sequential-thinking"]
  }
}
```

**Tools Provided**:
- `think` - Process thought step
- `reflect` - Review reasoning
- `conclude` - Final answer

---

### EverArt MCP
**Best for**: Image generation

```json
{
  "everart": {
    "command": "npx",
    "args": ["-y", "@anthropic/mcp-server-everart"],
    "env": {
      "EVERART_API_KEY": "your-key"
    }
  }
}
```

**Tools Provided**:
- `generate_image` - Create image from prompt

**Prerequisites**: EverArt API Key

---

## Server Selection Guide

| Use Case | Recommended Server |
|----------|-------------------|
| E2E browser testing | playwright-mcp |
| Quick browser automation | puppeteer |
| Production database | postgres |
| Local database | sqlite |
| File operations | filesystem |
| Web API calls | fetch |
| Web search | brave-search |
| GitHub operations | github |
| Team notifications | slack |
| Issue tracking | linear |
| Error monitoring | sentry |
| Complex reasoning | sequential-thinking |

## Adding New Servers

When adding a server to this catalog:

1. **Test the server** - Verify it works with current MCP protocol
2. **Document tools** - List all tools provided
3. **Note prerequisites** - API keys, Docker, etc.
4. **Provide config** - Ready-to-paste JSON
5. **Map to agents** - Which agents benefit from this server

---

**Last Updated**: 2024-12-24
**Catalog Version**: 1.0.0
