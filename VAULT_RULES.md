# Vault Rules — FASTSUITE E2 Customization Knowledge

## General Editing Rules

- Never mass-edit without approval.
- Preserve YAML frontmatter on every edit.
- Preserve wikilinks and markdown links.
- Prefer patching (`patch_vault_file`) over full rewrites.
- Search (`search_vault_smart`) before creating new notes.
- Avoid duplicate concepts — link to existing notes instead.
- Use wikilinks for internal references.
- Agent-generated content goes into `90_Inbox_AI/` unless instructed otherwise.
- Ask before deleting anything.
- Never rename folders automatically.
- Include `source: agent-generated` in frontmatter when creating notes.
- Respect the E2 package boundaries (don't merge cenpydownload and cenpyupload concepts).

## Agent Write-Back from External Workspaces

Coding agents (e.g., those in `fastsuite-copilot-starter` repos) may write back
platform discoveries to this vault via the `obsidian-e2` MCP connection.

### Allowed writes: `90_Inbox_AI/` only

- **Filename convention:** `YYYY-MM-DD-<slug>.md` (e.g., `2026-06-04-boolattribute-trap.md`)
- **Required frontmatter:**
  ```yaml
  ---
  type: troubleshooting | pattern | concept
  status: draft
  source: agent-generated
  e2-area: downloader | uploader | technology | reporting | core
  created: <date>
  tags: []
  ---
  ```
- Must search vault first (`search_vault_smart`) to avoid duplicates.
- Must include evidence (code snippet, error message, or test result).
- Should include `related:` wikilinks to existing vault notes when known.

### Good candidates for write-back

- Undocumented callback behavior discovered during debugging
- Attribute type traps (e.g., BoolAttribute returns unexpected type)
- Framework traversal rules not documented in official API notes
- Error messages with confirmed root causes and fixes
- Timing/ordering dependencies between callbacks

### NOT candidates (keep in the coding repo instead)

- Vendor-specific output format decisions
- Project-specific state machine designs
- Golden file expectations
- Coding conventions specific to one translator
- Implementation patterns that only apply to one vendor

### Never write to

- `80_Standards/` — human-maintained governance, requires explicit approval
- `10_API_Reference/` directly — must go through inbox triage first
- `00_About/` — vault governance and meta-documentation
- Any folder outside `90_Inbox_AI/` without explicit human instruction

## Source of Truth Boundaries

| Knowledge domain | Source of truth | Rationale |
|---|---|---|
| E2 platform API (class methods, callbacks, attribute semantics) | **This vault** (`10_API_Reference/`) | Authoritative, shared across all projects |
| Platform behavior traps & gotchas | **This vault** (`60_Troubleshooting/`) | Affects all teams, not project-specific |
| Framework lifecycle & traversal rules | **This vault** (`10_API_Reference/`, `20_Patterns/`) | Platform documentation |
| Cross-vendor coding patterns | **Coding repo** (`skills/downloader/SKILL.md`) | Curated subset, works without MCP |
| Vendor output format specs | **Coding repo** (`docs/ctrl-specific/<VENDOR>/spec/`) | Project decision, may diverge |
| Project state & progress | **Coding repo** (`docs/ctrl-specific/<VENDOR>/current.md`) | Git-tracked, PR-reviewable |

The vault is the **upstream reference** (comprehensive, authoritative).
The coding repo skill is the **downstream curated extract** (task-optimized, works offline).
