# FASTSUITE E2 Customization Knowledge Vault

The canonical, MCP-queryable knowledge base for FASTSUITE E2 OLP customization.

## Purpose

Serve structured E2 API reference, coding patterns, enhanced docstrings, and
troubleshooting knowledge to any MCP-compatible AI client — VS Code Copilot,
Cursor, Claude Code, Cline, Windsurf.

## Quick Start

1. Open this vault in Obsidian (v1.7.2+)
2. Enable the MCP Connector plugin (Community plugins → MCP Connector)
3. Add to your editor's MCP config:

```json
{
  "servers": {
    "obsidian-e2": {
      "type": "http",
      "url": "http://127.0.0.1:27200/mcp",
      "headers": {
        "Authorization": "Bearer <token-from-plugin-settings>"
      }
    }
  }
}
```

4. Test: ask your AI agent "What callbacks does a downloader implement?"

## Folder Structure

| Folder | Content |
|--------|---------|
| `00_About/` | Vault purpose, onboarding, implementation plan |
| `10_API_Reference/` | One note per API class/callback (from E2_API_*.md) |
| `20_Patterns/` | Reusable coding patterns with rationale |
| `30_Concepts/` | Evergreen notes (OLP tree, controllers, units, events) |
| `40_Docstrings/` | Enhanced docstrings per package/class |
| `50_MOCs/` | Maps of Content (auto-generated + curated) |
| `60_Troubleshooting/` | Common errors, gotchas, FAQ |
| `70_Archive/` | Superseded or deprecated content |
| `80_Standards/` | Coding rules, docstring guide, conventions |
| `90_Inbox_AI/` | Agent-generated content awaiting review |
| `Prompts/` | MCP prompt library |

## Related Repositories

- [fastsuite-copilot-starter](https://github.com/cenit-dfs/fastsuite-copilot-starter) — VS Code workspace scaffold for E2 customization projects
- [fastsuite-e2-community](https://github.com/cenit-dfs/fastsuite-e2-community) — Community translators, technology scripts, scenarios
