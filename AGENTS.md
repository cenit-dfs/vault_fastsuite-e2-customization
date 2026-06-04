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

## Write-Back from Coding Sessions

Coding agents working in external repositories (e.g., `fastsuite-copilot-starter`
projects) may write platform discoveries to this vault after completing milestones.

### Trigger

The coding repo's Session Debrief checklist (step 5) gates write-back:
> "Platform discovery? → If `obsidian-e2` MCP is connected, write to `90_Inbox_AI/`"

### Required Steps

1. **Search first** — use `search_vault_smart` to confirm no existing note covers this.
2. **Create in inbox** — use `create_vault_file` with path `90_Inbox_AI/YYYY-MM-DD-<slug>.md`.
3. **Include frontmatter** — `status: draft`, `source: agent-generated`, `type:`, `e2-area:`.
4. **Include evidence** — code snippet, error output, or behavioral observation.
5. **Link if possible** — add `related:` wikilinks to known vault notes.

### What Belongs Here vs. the Coding Repo

| Write to vault (`90_Inbox_AI/`) | Keep in coding repo |
|---|---|
| Undocumented E2 API behavior | Vendor output format specs |
| Attribute type traps | Project state machines |
| Callback timing/ordering discoveries | Golden file expectations |
| Framework traversal semantics | Cross-vendor coding patterns (→ skill) |
| Error root cause + fix | Vendor-specific implementation decisions |

### What Happens After Write-Back

A human (or the E2-Librarian agent role) triages `90_Inbox_AI/` periodically:
- Verified API behavior → promoted to `10_API_Reference/`
- Verified pattern → promoted to `20_Patterns/`
- Confirmed trap/gotcha → promoted to `60_Troubleshooting/`
- Low quality or duplicate → revised or rejected

See `Prompts/inbox-triage.md` for the triage workflow.
