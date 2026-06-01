# AGENTS.md — FASTSUITE E2 Knowledge Vault

You are working with a knowledge vault for FASTSUITE E2 OLP customization.
This vault serves API reference, patterns, docstrings, and troubleshooting
knowledge via MCP to any connected AI client.

## Context

- E2 is an offline programming (OLP) system for industrial robots.
- Customization areas: Downloaders, Uploaders, Technologies, Reporting.
- Python 3.12 (bundled with E2). Key packages: cenpydownload, cenpyupload,
  cenpyolpcore, cenpylib, cenpymath, cenpyunits.

## Rules

- Read before editing. Use `get_vault_file_partial` for large notes.
- Preserve YAML frontmatter and links.
- Use wikilinks for cross-references between notes.
- Prefer `patch_vault_file` over full rewrites.
- Search (`search_vault_smart`) before creating notes to avoid duplicates.
- New agent content → `90_Inbox_AI/` with `status: draft`.
- Never modify `80_Standards/` without explicit approval.

## Workflow

1. Search for relevant existing notes.
2. Read with `get_vault_file_partial` (section or frontmatter only).
3. Inspect backlinks and outgoing links for context.
4. Plan the change.
5. Apply minimal patch.
6. Verify links, summarize what changed.
