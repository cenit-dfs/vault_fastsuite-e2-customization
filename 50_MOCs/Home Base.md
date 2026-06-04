---
type: moc
status: draft
e2-area: core
source: agent-generated
created: 2026-06-03
tags:
  - moc
  - home-base
  - navigation
---

# Home Base

Start here when navigating FASTSUITE E2 OLP customization knowledge.

## Quick Paths

| Need | Start With |
|------|------------|
| Understand the vault | [[README]] |
| Learn E2 customization basics | [[Beginner_Guide]] |
| Compare downloader and uploader flows | [[Download vs Upload]] |
| Understand the OLP object model | [[OLP Tree]] |
| Find callback order and lifecycle behavior | [[Download Callback Lifecycle]], [[Upload Callback Lifecycle]], [[Technology Callback Lifecycle]] |
| Look up API classes | [[DULPythonDownloadOperator]], [[ULPythonUploadOperator]], [[OlpCorePythonProgram]], [[OlpCorePythonTechnology]] |
| Reuse implementation patterns | [[Callback Lifecycle Pattern]], [[Event Dispatch Pattern]], [[File Output Pattern]] |
| Diagnose common problems | [[Troubleshooting Index]] |
| Follow coding conventions | [[olp-translator-coding-rules]] |

## By Work Area

### Downloader

- API: [[DULPythonDownloadOperator]], [[DULPythonProgram]], [[DULPythonController]], [[DULPythonMotion]], [[DULPythonPosition]], [[DULPythonEvent]]
- Lifecycle: [[Download Callback Lifecycle]]
- Patterns: [[Buffered Motion Pattern]], [[Read-Ahead Pattern]], [[Position Output Pattern]], [[Event Dispatch Pattern]], [[Weld State Machine Pattern]]
- Common traps: [[BoolAttribute Trap]], [[Common Downloader Mistakes]], [[Euler Conversion Pattern]], [[Multi-Signal Container Pattern]]

### Uploader

- API: [[ULPythonUploadOperator]], [[ULPythonProgram]], [[ULPythonController]], [[ULPythonMotion]], [[ULPythonPosition]], [[ULPythonEvent]]
- Lifecycle: [[Upload Callback Lifecycle]]
- Concept bridge: [[Download vs Upload]]

### Technology

- Core API: [[OlpCorePythonTechnology]], [[OlpCorePythonEvent]], [[OlpCorePythonOperation]], [[OlpCorePythonProgram]]
- Callback references: [[Technology Callback Lifecycle]], [[Workmethod Callback Lifecycle]], [[Series Callbacks]], [[Event Callbacks]], [[Event Rules]]
- Related concepts: [[Event Model]], [[OLP Tree]]

### Core OLP Model

- Classes: [[OlpCorePythonItem]], [[OlpCorePythonProgram]], [[OlpCorePythonProgramComponent]], [[OlpCorePythonController]], [[OlpCorePythonResource]], [[OlpCorePythonOperation]], [[OlpCorePythonTechnology]]
- Reference tables: [[OlpCore Attributes]], [[OlpCore Enumerations]]
- Concepts: [[Controller Model]], [[Event Model]], [[Unit System]]

### Reporting

- API: [[ReportUtility]]

## By Task

### Create or Modify a Downloader

1. Read [[Download Callback Lifecycle]].
2. Review [[Callback Lifecycle Pattern]] and [[Buffered Motion Pattern]].
3. Use [[DULPythonDownloadOperator]] as the main API entry point.
4. Check [[Common Downloader Mistakes]] before testing.

### Work With Events

1. Start with [[Event Model]].
2. For downloader output, use [[Event Dispatch Pattern]] and [[DULPythonEvent]].
3. For technology-level behavior, use [[OlpCorePythonTechnology]], [[OlpCorePythonEvent]], [[Event Callbacks]], and [[Event Rules]].

### Trace Program Structure

1. Start with [[OLP Tree]].
2. Use [[OlpCorePythonProgram]] to navigate the program.
3. Use [[OlpCorePythonOperation]], [[OlpCorePythonProgramComponent]], and [[OlpCorePythonResource]] for child objects.
4. Use [[OlpCorePythonTechnology]] for technology metadata, work methods, and technology-level events.

### Troubleshoot

1. Start with [[Troubleshooting Index]].
2. Check [[Common Downloader Mistakes]] for downloader failures.
3. Check [[BoolAttribute Trap]] for attribute truthiness problems.
4. Check [[Euler Conversion Pattern]] for orientation conversion issues.

## Prompt Library

- [[api-lookup]]
- [[pattern-lookup]]
- [[troubleshoot]]
- [[docstring-draft]]
- [[inbox-triage]]

## Other Navigation Options

- **Area MOCs:** Create focused landing pages such as `Download API`, `Upload API`, `Technology API`, and `OlpCore API` in `50_MOCs/`.
- **Task MOCs:** Create workflow pages such as `Create a Downloader`, `Handle Events`, or `Troubleshoot Output`.
- **Index Notes:** Add compact alphabetical indexes for API classes, callbacks, patterns, and concepts.
- **Dataview Dashboards:** If Dataview is enabled in Obsidian, generate live tables from frontmatter such as `type`, `e2-area`, `status`, and `tags`.
- **Canvas Map:** Build an Obsidian Canvas for a visual graph of downloader, uploader, technology, and core relationships.
- **Folder Notes:** Add one overview note inside each major folder for local navigation without leaving the folder.

## Maintenance

- Keep this note curated and short.
- Add new agent-generated notes under `90_Inbox_AI/` until reviewed.
- Prefer adding focused MOCs in `50_MOCs/` when this page becomes too dense.
- Do not duplicate full API details here; link to the source notes.
