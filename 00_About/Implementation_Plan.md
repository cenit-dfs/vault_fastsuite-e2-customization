^
# FASTSUITE E2 Customization Knowledge Vault

## Implementation Plan — MCP-Served Knowledge for Agentic Workflows

> **Status (2026-06-02):** Phase 1 seed complete. Patterns folder expanded.
> **Key finding from production session:** Agent uses `typings/` (95%) > vault (5%)
> for downloaders. Vault's highest value = Patterns + Technology APIs + Lifecycles.
> Per-class API signature docs are redundant with stubs — deprioritized.
>
> **Revised priority:** Patterns > Technology APIs > Lifecycles > Docstrings > API class docs.
>
> **Vault name:** `vault_fastsuite-e2-customization`
> **Repository:** public on GitHub.com (CENIT DFS org)
> **Audience:** CENIT internal, customers, partners — anyone using VS Code agent tooling with FASTSUITE E2.

---

# 1. Problem Statement

The [fastsuite-copilot-starter](https://github.com/cenit-dfs/fastsuite-copilot-starter) repository already provides excellent agent context for E2 customization:

- 5 API docs (Download, Upload, OLP/Technology, OlpCore, ArcWelding Report)
- 3 skills (Downloader comprehensive, Technology and Uploader stubs)
- AGENTS.md with 3 agent modes (E2Downloader, E2Technology, E2Uploader)
- Copilot instructions, coding rules, reference code

**Limitation:** That knowledge is locked inside one VS Code workspace. It requires the copilot-starter repo to be open, and it only works with GitHub Copilot. Customers using Cursor, Claude Code, Cline, Windsurf, or other MCP clients cannot access it.

**Solution:** Extract, restructure, and extend this knowledge into an Obsidian vault served via MCP Connector. Any MCP-compatible client can then query the full E2 customization knowledge base — API reference, patterns, examples, docstrings — without depending on a specific repo being open.

```text
fastsuite-copilot-starter (skills, API docs, AGENTS.md)
    ↓  extract + restructure
Obsidian Vault (vault_fastsuite-e2-customization)
    ↓  MCP Connector plugin (HTTP on 127.0.0.1:27200)
    ↓  semantic search (native MiniLM, on-device)
Any MCP Client (VS Code, Cursor, Claude Code, Cline, Windsurf, …)
```

---

# 2. Core Principles

## 2.1 AI-Assisted, Human-Governed

Agents suggest, synthesize, organize, and retrieve.
Humans approve, govern ontology, maintain standards, and own decisions.

All agent-generated content lands in `90_Inbox_AI/` for review before promotion.

## 2.2 Small Safe Changes

Agents patch instead of rewrite. Preserve frontmatter, links, and author voice.

Preferred workflow: `Search → Inspect → Plan → Preview → Patch → Verify`

## 2.3 Git Branch Workflow

```text
main             = stable, reviewed, team-approved
ai-experiments   = agent-generated content awaiting review
feature/*        = human-driven feature branches
```

All changes to `main` go through Pull Requests on GitHub.com.
Agent-generated batches are committed to `ai-experiments` with attributable messages
(e.g., `[agent/librarian] classify 5 inbox notes`) and merged via PR after human review.

## 2.4 Single Vault, Single MCP Instance

One vault open at a time. One MCP Connector on `127.0.0.1:27200`.
No multi-vault port allocation — keeps the setup simple and avoids contention.

---

# 3. Relationship to fastsuite-copilot-starter

The two repositories serve different roles:

| | fastsuite-copilot-starter | This vault |
|---|---|---|
| **Purpose** | Scaffold new E2 customization repos | Serve E2 knowledge to any MCP client |
| **Contains** | Working code, templates, submodules | Knowledge notes, API reference, patterns |
| **Consumed by** | VS Code + Copilot (workspace context) | Any MCP client (tool calls) |
| **Who clones it** | Every developer starting a project | Anyone wanting E2 knowledge via MCP |
| **Knowledge flow** | Reads its own `skills/` and `docs/` | Reads via `search_vault_smart`, `get_vault_file_partial` |

The copilot-starter's `skills/`, `docs/API_Python/`, and `AGENTS.md` are the **source material**.
This vault is the **canonical, searchable, MCP-queryable knowledge base** that evolves beyond what a starter template can hold.

### What stays in copilot-starter

- Code templates (`skills/downloader/templates/`)
- `.instructions.md` files (scoped to code editing)
- Community submodule references
- VS Code workspace settings, launch configs
- AGENTS.md (VS Code agent modes for code work)

### What moves to / lives in this vault

- API reference (restructured from the 5 `E2_API_*.md` files)
- Pattern libraries (extracted from SKILL.md files, expanded)
- Enhanced docstrings (per the docstring-guide.md roadmap)
- Knowledge graphs (backlinks, MOCs, cross-cutting concepts)
- Troubleshooting guides, FAQs, onboarding material
- Prompt library (MCP-native slash commands)

### Sync strategy

Knowledge that exists in both places (e.g., API method signatures) should have
**one canonical source** — this vault — with copilot-starter referencing it or
containing a deliberately simplified subset. The exact sync mechanism (manual,
CI-driven, or linked) is a Phase 3+ decision.

---

# 4. Architecture

## 4.1 High-Level Topology

```text
┌──────────────────────────────────────────────┐
│ Obsidian (vault_fastsuite-e2-customization)  │
│   ├─ MCP Connector plugin                    │
│   │    ├─ 30 MCP tools (read/write/search)   │
│   │    ├─ Native MiniLM semantic search      │
│   │    └─ HTTP on 127.0.0.1:27200            │
│   └─ Templater (prompt rendering)            │
└──────────────────┬───────────────────────────┘
                   │ Bearer-token auth
    ┌──────────────┼──────────────┐
    │              │              │
 VS Code      Cursor/Cline   Claude Code
 (Copilot)    (streamable)   (HTTP)
```

## 4.2 MCP Connection Config

All clients use the same endpoint. Example for VS Code (`mcp.json`):

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

For Claude Desktop (stdio-only), use the `npx mcp-remote` bridge — the plugin
generates the config via "Copy config for Claude Desktop."

---

# 5. Vault Folder Structure

```text
vault_fastsuite-e2-customization/
├── 00_About/                    # Vault purpose, onboarding, this plan
├── 10_API_Reference/            # Restructured from E2_API_*.md
│   ├── Download/                #   DULPython* classes, callbacks
│   ├── Upload/                  #   ULPython* classes, callbacks
│   ├── OlpCore/                 #   Core object model
│   ├── Technology/              #   IOlpTechnology, IOlpWorkMethod, etc.
│   └── Reporting/               #   ReportUtility, PDF generation
├── 20_Patterns/                 # Extracted from SKILL.md files
│   ├── Downloader/              #   Lifecycle, event dispatch, file I/O
│   ├── Uploader/                #   (expand from stub)
│   └── Technology/              #   (expand from stub)
├── 30_Concepts/                 # Evergreen notes (OLP tree, controllers, etc.)
├── 40_Docstrings/               # Enhanced docstrings per docstring-guide.md
│   ├── cenpydownload/
│   ├── cenpyupload/
│   ├── cenpyolpcore/
│   └── cenpylib/
├── 50_MOCs/                     # Maps of Content (auto-generated + curated)
├── 60_Troubleshooting/          # Common errors, gotchas, FAQ
├── 70_Archive/                  # Superseded or deprecated content
├── 80_Standards/                # Coding rules, docstring guide, conventions
├── 90_Inbox_AI/                 # Agent-generated content awaiting review
├── 99_Templates/                # Obsidian note templates
├── Prompts/                     # MCP prompt library (Templater-rendered)
├── AGENTS.md                    # Vault-level agent rules
├── VAULT_RULES.md               # Governance baseline
└── .obsidian/                   # Plugin config (MCP Connector, Templater, Git)
```

---

# 6. Knowledge Types and Frontmatter

## 6.1 Note Types

| Type | Purpose | Folder |
|------|---------|--------|
| api-class | Single API class/interface reference | `10_API_Reference/` |
| api-callback | Callback lifecycle documentation | `10_API_Reference/` |
| pattern | Reusable code pattern with rationale | `20_Patterns/` |
| concept | Evergreen knowledge (OLP tree, units, etc.) | `30_Concepts/` |
| docstring | Enhanced docstring for a Python symbol | `40_Docstrings/` |
| moc | Map of content | `50_MOCs/` |
| troubleshooting | Problem → diagnosis → fix | `60_Troubleshooting/` |
| standard | Coding rule or convention | `80_Standards/` |

## 6.2 Frontmatter Schema

```yaml
---
type: api-class
status: draft | active | deprecated | archived
e2-package: cenpydownload | cenpyupload | cenpyolpcore | cenpylib | cenpymath | cenpyunits
e2-area: downloader | uploader | technology | reporting | core
source: fastsuite-copilot-starter | manual | agent-generated
created: 2026-05-27
tags:
  - download-api
  - callback
---
```

## 6.3 Lifecycle States

| State | Meaning |
|-------|---------|
| draft | Work in progress or agent-generated, not yet reviewed |
| active | Current, reviewed, authoritative |
| deprecated | Superseded — kept for history, not for use |
| archived | Historical only, moved to `70_Archive/` |

---

# 7. Agent Governance

## 7.1 VAULT_RULES.md (baseline)

```markdown
# Vault Rules — FASTSUITE E2 Customization Knowledge

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
```

## 7.2 AGENTS.md (vault-level)

```markdown
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
```

## 7.3 MCP Tool Usage Hierarchy

### Discovery (read-only, always safe)

1. `search_vault_smart` — semantic search across the vault
2. `get_vault_file_partial` — read a section/field without loading full note
3. `get_backlinks` / `get_outgoing_links` — graph navigation
4. `get_recent_files` — what changed recently
5. `list_tags` / `get_files_by_tag` — taxonomy queries
6. `list_vault_files` — directory listing

### Editing (requires review workflow)

1. `patch_vault_file` — preferred for targeted changes
2. `create_vault_file` — new notes (→ `90_Inbox_AI/` by default)
3. `rename_heading` — preserves link integrity across vault
4. `rename_vault_file` — preserves link integrity across vault

### Command Execution (Phase 3+, opt-in)

Disabled by default. When enabled, use the allowlist + confirmation modal.
Useful later for triggering Templater renders, Dataview refreshes, graph view.

## 7.4 Agent Permissions Progression

| Phase | Permissions | Trust gate |
|-------|-------------|------------|
| Phase 1 | Read-only (search, get, list) | Initial setup |
| Phase 2 | Write to `90_Inbox_AI/` only | Team reviews first batch |
| Phase 3 | Patch existing notes via PR | Inbox outputs pass quality bar |
| Phase 4 | Graph maintenance (backlinks, MOCs) | Patching outputs are reliable |
| Phase 5 | Scheduled workflows | Team trusts unattended outputs |

---

# 8. Agent Role Maturity Path

## Maturity Level 1 — MCP Prompts (start here)

Build repeatable workflows as MCP prompt files in `Prompts/`.
These surface as slash commands in any connected MCP client.

See [§9 Prompt Library](#9-prompt-library) for the initial set.

**Why first:** Low risk, immediately useful, works across all MCP clients,
validates the knowledge structure before granting write access.

## Maturity Level 2 — VS Code Agent Modes (.agent.md)

Once prompt workflows stabilize and roles become clear, codify them as
`.agent.md` files in VS Code with tool restrictions and scoped instructions.

Candidate roles (to be refined based on Level 1 experience):

| Role | Responsibility | Scoped to |
|------|---------------|-----------|
| E2-Librarian | Classify, tag, deduplicate, inbox triage | `90_Inbox_AI/`, `30_Concepts/` |
| E2-Docstring | Generate/improve docstrings per guide | `40_Docstrings/` |
| E2-Graph | Backlinks, orphan detection, MOC generation | `50_MOCs/`, cross-vault links |
| E2-Reviewer | Weekly review, stale note detection, changelog | `70_Archive/`, `50_MOCs/` |

## Maturity Level 3 — Scheduled Workflows

Automated runs (e.g., weekly orphan scan, monthly docstring batch) only
after Level 2 outputs are consistently trusted by the team.

## Maturity Level 4 — Conversational Exploration

Keep unstructured, exploratory agent conversations (no formal role) for:
- ad-hoc research
- architecture brainstorming
- one-off queries

---

# 9. Prompt Library

Store in `Prompts/` at vault root. Each file uses Templater syntax
and the `#mcp-tools-prompt` tag to register as an MCP prompt.

## Initial Prompt Set

| Prompt file | Purpose | Parameters |
|-------------|---------|------------|
| `api-lookup.md` | Find API class/method docs | `query` |
| `pattern-lookup.md` | Find relevant coding patterns | `area` (downloader/uploader/technology), `topic` |
| `docstring-draft.md` | Generate a docstring for a Python symbol | `package`, `symbol_name` |
| `troubleshoot.md` | Diagnose an E2 customization issue | `error_message`, `area` |
| `create-concept-note.md` | Turn raw info into a structured concept note | `title`, `content` |
| `inbox-triage.md` | Review and classify notes in `90_Inbox_AI/` | (none) |
| `weekly-review.md` | Summarize recent vault changes | `days` |
| `backlink-review.md` | Find notes missing cross-references | `folder` |
| `generate-moc.md` | Create/update a Map of Content | `area` |

### Example prompt file: `Prompts/api-lookup.md`

```markdown
---
tags:
  - mcp-tools-prompt
description: Search the E2 API reference for a class, method, or callback
---

Search the vault for API documentation matching:
"<% tp.mcpTools.prompt("query", "What API class, method, or callback to look up — e.g. 'DULPythonMotion' or 'PreDownload callback'") %>"

Use search_vault_smart first, then get_vault_file_partial to read the relevant
section. Return: class name, key methods, parameters, return types, and any
linked pattern notes. Include wikilinks to related API notes.
```

---

# 10. Knowledge Migration Plan

> **Revised (2026-06-02):** Based on production session results, §10.1 API Reference
> Migration is deprioritized for Download/Upload/OlpCore classes (stubs cover them).
> Technology APIs remain high priority (no stubs exist). Patterns (§10.2) are the
> highest-value vault content. Docstrings (Phase 3) deferred until scenarios are mature.

## Lessons Learned (2026-06-02 Kawasaki Production Session)

| Finding | Implication |
|---------|-------------|
| Agent used `typings/` for 95% of API needs | Per-class vault API docs are redundant for download/upload |
| Patterns (`20_Patterns/Downloader/`) were critical when discovered | Patterns are highest-value vault content |
| Technology has no stubs at all | Technology APIs are the vault's unique contribution |
| Golden files + reference dumps = ground truth | More scenarios > more docs |
| BoolAttribute trap was a silent bug | Gotcha notes prevent repeat mistakes across teams |
| MCP vault was barely queried | Vault value is for NEW domains (tech) and patterns, not repeating stubs |

### Revised Priority Order

1. **Patterns** (`20_Patterns/`) — behavioral knowledge not expressible in types ✅ Downloader done
2. **Technology APIs** (`10_API_Reference/Technology/`) — only source, no stubs
3. **Lifecycle docs** (callback order, timing, state) — keep and expand
4. **Troubleshooting/Gotchas** (`60_Troubleshooting/`) — prevent silent bugs
5. **Docstrings** (`40_Docstrings/`) — deferred until scenario coverage is broader
6. ~~Per-class API method docs~~ — **deprioritized**, stubs are sufficient

## Phase 1 — Seed from fastsuite-copilot-starter (Week 1–2)

### 10.1 API Reference Migration

Source files → Vault notes (one note per class/interface):

| Source file | Target folder | Notes to create |
|-------------|--------------|-----------------|
| `E2_API_Download.md` | `10_API_Reference/Download/` | ~10 notes (DULPythonDownloadOperator, DULPythonMotion, DULPythonPosition, …) + 1 callback lifecycle note |
| `E2_API_Upload.md` | `10_API_Reference/Upload/` | ~8 notes (ULPythonUploadOperator, ULPythonMotion, …) + 1 callback lifecycle note |
| `E2_API_OlpCore.md` | `10_API_Reference/OlpCore/` | ~12 notes (OlpCorePythonOperation, OlpCorePythonProgram, …) |
| `E2_API_Offline_Programming.md` | `10_API_Reference/Technology/` | ~8 notes (IOlpTechnology, IOlpWorkMethod, IOlpEvent, …) |
| `E2_API_ArcWelding_Report.md` | `10_API_Reference/Reporting/` | ~3 notes (ReportUtility, columns, language config) |

Estimated: **~40 notes** from the 5 API docs.

### 10.2 Pattern Migration

Source → restructured pattern notes:

| Source | Target | Notes |
|--------|--------|-------|
| `skills/downloader/SKILL.md` (~500 lines) | `20_Patterns/Downloader/` | ~8–10 pattern notes (callback lifecycle, event dispatch, unit conversion, file I/O buffering, …) |
| `skills/technology/SKILL.md` (~60 lines) | `20_Patterns/Technology/` | ~2 notes (stub — expand later) |
| `skills/uploader/SKILL.md` (~50 lines) | `20_Patterns/Uploader/` | ~2 notes (stub — expand later) |

### 10.3 Standards Migration

| Source | Target |
|--------|--------|
| `docs/standards/docstring-guide.md` | `80_Standards/docstring-guide.md` |
| `OLPTranslators/.instructions.md` | `80_Standards/olp-translator-coding-rules.md` |
| `.github/copilot-instructions.md` (coding rules subset) | `80_Standards/general-coding-rules.md` |

### 10.4 Concepts to Create

Cross-cutting concepts that don't fit in API reference or patterns:

- OLP Tree structure (what programs, operations, motions are)
- Controller model (virtual vs. physical, multi-controller)
- Unit system (E2 internal meters → robot mm/deg)
- Event model (technology events, signal events, container events)
- Download vs. Upload lifecycle comparison
- Technology callback chain

Estimated: **~10 concept notes**.

---

## Phase 2 — Enhance and Connect (Week 3–4)

- Generate first MOCs: `50_MOCs/Download_API.md`, `50_MOCs/Upload_API.md`, `50_MOCs/Technology_API.md`
- Add backlinks between API notes and pattern notes
- Write initial troubleshooting notes from known team pain points
- Build and test the MCP prompt library (all 9 prompts from §9)
- Enable `search_vault_smart` and verify semantic retrieval quality
- First PR from `ai-experiments` → `main` for team review

## Phase 3 — Docstrings (Week 5–8)

Follow the docstring-guide.md priority batches:

1. **Batch 1:** cenpydownload — DULPython* classes (highest impact, most users)
2. **Batch 2:** cenpyolpcore — OlpCorePython* classes (shared foundation)
3. **Batch 3:** cenpyupload — ULPython* classes
4. **Batch 4:** cenpylib, cenpymath, cenpyunits — utilities

Agent generates drafts → `90_Inbox_AI/` → human review → promote to `40_Docstrings/`.

## Phase 4 — Expand Coverage (Week 9+)

- Technology and Uploader pattern expansion (currently stubs)
- Community translator corpus analysis → extract additional patterns
- Customer/partner FAQ → `60_Troubleshooting/`
- Knowledge graph refinement (orphan scan, MOC updates)
- Evaluate: sync mechanism between this vault and copilot-starter docs

---

# 11. Required Software

## Required

| Software | Purpose |
|----------|---------|
| Obsidian (v1.7.2+) | Knowledge vault host |
| MCP Connector plugin | Serves vault via MCP HTTP |
| VS Code | Primary editor + Copilot agent runtime |
| GitHub Copilot (Pro+ or Enterprise) | AI agent access |
| Git | Version control |
| Node.js LTS | Required for Claude Desktop bridge (if used) |

## Strongly Recommended

| Software | Purpose |
|----------|---------|
| Templater plugin | MCP prompt rendering, note templates |
| Obsidian Git plugin | Auto-commit, branch management from within Obsidian |
| Claude model access in Copilot | Best results for knowledge work |

## Optional (Phase 3+)

| Software | Purpose |
|----------|---------|
| Dataview plugin | Structured queries across vault |
| Local REST API plugin | Enables `search_vault` DQL/JsonLogic tool |
| Cursor / Claude Code / Cline | Alternative MCP clients for team members |

---

# 12. Security and Governance

## MCP Security

- MCP Connector binds to `127.0.0.1` only (loopback) — no network exposure.
- Bearer token required on every request. Rotate via plugin settings.
- Token is per-vault, stored in `.obsidian/plugins/mcp-tools-istefox/data.json`.
- `.gitignore` must exclude `.obsidian/plugins/*/data.json` to avoid leaking tokens.

## Content Governance

| Action | Approval required? |
|--------|--------------------|
| Read/search | No |
| Create note in `90_Inbox_AI/` | No (agent sandbox) |
| Promote from inbox to permanent folder | Yes — PR review |
| Patch existing active note | Yes — PR review |
| Delete any note | Yes — explicit human approval |
| Rename folders | Yes — explicit human approval |
| Modify `80_Standards/` | Yes — team consensus |
| Modify `AGENTS.md` or `VAULT_RULES.md` | Yes — team consensus |

## Public Repo Considerations

- No customer-specific data in the vault. Patterns and API docs are product-level.
- No credentials, tokens, or internal URLs.
- License choice: TBD (MIT? Apache 2.0? Same as copilot-starter?)
- CODEOWNERS file to enforce review requirements per folder.

---

# 13. Rollout Plan

## Week 1 — Foundation

- [ ] Rename vault to `vault_fastsuite-e2-customization`
- [ ] Initialize Git repo, push to GitHub.com (public)
- [ ] Set up `.gitignore` (exclude `.obsidian/plugins/*/data.json`, `.obsidian/workspace.json`)
- [ ] Install MCP Connector, Templater, Obsidian Git plugins
- [ ] Create folder structure (§5)
- [ ] Write `AGENTS.md`, `VAULT_RULES.md`
- [ ] Create `00_About/README.md` with vault purpose and onboarding
- [ ] Configure MCP connection from VS Code, verify `get_server_info`
- [ ] Set up branch protection on `main` (require PR, at least 1 reviewer)

## Week 2 — Seed Knowledge

- [ ] Migrate API reference (§10.1) — ~40 notes from 5 source files
- [ ] Migrate patterns (§10.2) — ~12 notes from 3 SKILL.md files
- [ ] Migrate standards (§10.3) — 3 notes
- [ ] Create initial concept notes (§10.4) — ~10 notes
- [ ] First PR from `ai-experiments` → `main`

## Week 3 — Connect and Prompt

- [ ] Generate first MOCs
- [ ] Add backlinks between API ↔ pattern ↔ concept notes
- [ ] Build MCP prompt library (§9) — 9 prompt files
- [ ] Test semantic search quality (does `search_vault_smart` find the right API note for a natural language question?)
- [ ] Write initial troubleshooting notes
- [ ] Team demo: show MCP-served knowledge in action

## Week 4 — Stabilize

- [ ] Team feedback round → refine note structure, frontmatter, prompts
- [ ] Fix semantic search gaps (add synonyms, improve note titles)
- [ ] Establish PR review cadence for `ai-experiments` merges
- [ ] Decide CODEOWNERS per folder
- [ ] Begin docstring batch 1 (cenpydownload) if team is ready

## Week 5–8 — Docstrings + Expand

- [ ] Docstring batches 1–4 (§10 Phase 3)
- [ ] Expand Technology and Uploader patterns
- [ ] Grow troubleshooting section from real team issues
- [ ] Introduce `.agent.md` roles if prompt workflows are stable

## Week 9+ — Mature

- [ ] Scheduled workflows (weekly orphan scan, monthly MOC refresh)
- [ ] Remote serving strategy (see §17)
- [ ] Community contribution guide for partners/customers
- [ ] Knowledge graph analytics (coverage heat map by E2 area)

---

# 14. Open Questions for Team Discussion

1. **License for the public repo** — MIT (like copilot-starter) or something else?
2. **Granularity of API notes** — one note per class, or one note per class + its methods as sub-headings? (Affects semantic search precision vs. note count.)
3. ~~**Docstring canonical location**~~ — **DECIDED:** Vault is canonical. Docstrings live here. Copilot-starter `docs/API_Python/` will be removed after migration; `typings/` stubs get enriched locally for IDE hover, and later contributed upstream to C++ headers (see §18).
4. **CODEOWNERS** — who reviews which folders? Proposal: API reference needs domain expert, patterns need customization engineer, docstrings can be lighter review.
5. **Customer/partner contributions** — do we accept PRs from external contributors, or is this CENIT-maintained only?
6. **Vault name** — `vault_fastsuite-e2-customization` or shorter like `e2-knowledge`?
7. **Note language** — English only, or bilingual (EN/DE) with a convention?

---

# 15. Success Criteria

| Metric | Target | When |
|--------|--------|------|
| Semantic search finds correct API note | >80% of test queries | Week 3 |
| All 5 API docs migrated and interlinked | 100% | Week 2 |
| MCP prompts working in VS Code + 1 other client | 9 prompts | Week 3 |
| Team can answer E2 customization questions via MCP without opening copilot-starter | Demonstrated | Week 4 |
| Docstring coverage for cenpydownload | 100% of public symbols | Week 8 |
| First external user (customer/partner) successfully uses the vault | 1 confirmed | Week 12 |

---

# 16. Long-Term Vision

```text
FASTSUITE E2 customization knowledge
    +
MCP-served semantic retrieval
    +
Agent-assisted documentation
    +
Cross-client compatibility
    +
Community-contributed patterns
    =
The canonical, queryable, AI-ready knowledge base
for everyone building on FASTSUITE E2.
```

---

# 17. Remote Serving Strategy (Post-Demo)

Local Obsidian + MCP Connector on `127.0.0.1:27200` is sufficient for the demo
and internal team use. For external users (customers, partners) who should not
need to run Obsidian locally, two remote-serving paths have been evaluated:

## Option A — pgvector Sync (inspired by Open Brain OB1)

The [Open Brain](https://github.com/NateBJones-Projects/OB1) project has a
community-built Obsidian Vault Import recipe that ingests Markdown notes
(with frontmatter) into a Supabase PostgreSQL + pgvector database. An MCP
server Edge Function then serves recall to any remote AI client.

Applied to this vault:

```text
Obsidian vault (authoring — human-friendly, Git-versioned)
    ↓  periodic sync (CI action or manual trigger)
Supabase / Azure PostgreSQL + pgvector (cloud, embeddings)
    ↓  MCP server (Edge Function or Azure Container App)
Any MCP client (no local Obsidian required)
```

**Pros:** True remote access, no Obsidian dependency for consumers, scales to
many concurrent users, leverages existing open-source tooling.

**Cons:** Adds infrastructure (database, Edge Function, embedding pipeline),
potential cost, one-way sync (vault → DB) unless bidirectional editing is built.

**Decision point:** Evaluate after Week 8 when vault content is mature enough
to serve externally.

## Option B — Azure AI Search (from api-availability-for-agents.md, Milestone 3)

Serve the vault content (or a subset) via Azure AI Search index, accessed
through `@azure/mcp` or a custom MCP server. Already planned in the
copilot-starter roadmap.

**Pros:** Integrates with existing Azure stack, enterprise-grade, no new
database to manage.

**Cons:** Requires Azure AI Search resource + indexer pipeline, potentially
higher cost, less community tooling.

**Recommendation:** Option A is faster to prototype (free Supabase tier,
open-source import recipe). Option B is better for production/enterprise.
They're not mutually exclusive — could use A for early adopters, B for production.

---

# 18. Stub Generator Contribution Path (Post-Demo)

**Finding:** The `.pyi` stubs in `typings/` are auto-generated from C++ headers
via `Cenit.API.PyLibrary/IntelliSense/StubsGenerator.py`. The generator uses
`clang.cindex` to parse C++ headers and Mako templates (`pyi.template`) to emit
Python stubs. The templates already have full docstring support — `f.comment`,
`f.paramDescriptions`, `f.returnDescription` render directly from C++ XML doc
comments (`/// <summary>`, `/// <param>`, `/// <returns>`).

**Implication:** The stubs are sparse not because the tooling can't handle
docstrings, but because the source C++ headers lack detailed `///` comments.

**Contribution architecture:**

```text
1. Write rich docstrings in vault (40_Docstrings/) — human review
2. Inject into local typings/ stubs for immediate IDE benefit
3. Translate approved docstrings → C++ XML doc format
4. PR to Cenit.FastSuite C++ headers (coordinated with E2 dev team)
5. Regenerate stubs via StubsGenerator.py → ships with next E2 release
6. All customers benefit automatically
```

**Blocking dependency:** Coordination with E2 development team (Shabas / stub
maintainer). Defer to after demo; use vault docstrings + local typings
enrichment in the meantime.

---

# 19. Skills as Portable Prompt Packs

Inspired by OB1's `/skills` folder pattern: plain-text skill packs that work
across Claude Code, Cursor, Copilot, Codex, and any MCP-compatible client.

The vault's `Prompts/` folder (§9) already implements this pattern. Key insight
from OB1: these work best when they are:
- Self-contained (no external dependencies beyond the MCP connection)
- Documented with prerequisites and expected outcomes
- Versioned alongside the knowledge they reference
- Discoverable via the MCP prompt listing protocol

**Action (Week 3–4):** Validate that the `Prompts/` files work identically in
VS Code Copilot, Cursor, and Claude Code. Document any client-specific quirks
in `80_Standards/mcp-client-compatibility.md`.
