---
tags:
  - mcp-tools-prompt
description: Search the E2 API reference for a class, method, or callback
---

Search the vault for API documentation matching: "{{query}}"

Use `search_vault_smart` first, then `get_vault_file_partial` to read the
relevant section. Return: class name, key methods with parameters and return
types, units, and any linked pattern notes. Include wikilinks to related API
notes.

If the query is about a callback lifecycle, check `10_API_Reference/Download/`
or `10_API_Reference/Technology/` for the lifecycle notes.
