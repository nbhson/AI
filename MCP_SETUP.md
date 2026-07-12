# MCP Server Setup

## GitHub MCP Server

The GitHub MCP Server has been configured to provide direct GitHub integration capabilities.

### Configuration Details

- **Server Name:** `github.com/github/github-mcp-server`
- **Type:** Remote HTTP server
- **URL:** https://api.githubcopilot.com/mcp/
- **Repository:** https://github.com/github/github-mcp-server

### Setup Location

The MCP server configuration is stored in:
```
/Users/sonnguyen/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json
```

### Configuration

```json
{
  "mcpServers": {
    "github.com/github/github-mcp-server": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

### Authentication

The remote GitHub MCP server uses OAuth authentication. On first use, you'll be prompted to authenticate via your browser.

### Available Capabilities

The GitHub MCP server provides tools for:

- **Repository Management:** Browse code, search files, manage branches, commits, and tags
- **Issues & Pull Requests:** Create, update, comment, review, and manage issues and PRs
- **GitHub Actions:** Monitor workflows, view job logs, trigger runs
- **Code Security:** Access code scanning alerts, Dependabot alerts, secret scanning
- **Projects:** Manage GitHub Projects (V2)
- **Discussions:** Access and manage GitHub Discussions
- **Gists:** Create and manage gists
- **Notifications:** View and manage GitHub notifications
- **Organizations & Users:** Search and access org/user information
- **Stargazers:** Star/unstar repositories

### Usage Examples

Once the server is connected, you can interact with GitHub through natural language:

- "Search for Python repositories about machine learning"
- "List my open pull requests"
- "Get the latest issues in owner/repo"
- "Show me my GitHub notifications"
- "Create an issue in owner/repo with title 'Bug: ...'"
- "Star the repository owner/repo"

### Getting Started

1. Restart VS Code or reload the window to initialize the MCP server
2. Authenticate via GitHub OAuth when prompted
3. Start using GitHub integration through Cline

### Documentation

For more information, see the official documentation:
- GitHub MCP Server: https://github.com/github/github-mcp-server
- MCP Protocol: https://modelcontextprotocol.io
