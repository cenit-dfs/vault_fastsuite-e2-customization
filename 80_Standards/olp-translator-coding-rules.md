---
type: standard
status: active
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - standard
  - coding-rules
  - downloader
---

# OLP Translator Coding Rules

Rules that apply when editing files under `OLPTranslators/`.

## Architecture

- Separate orchestration from syntax emission:
  - Baseline downloader handles E2 lifecycle/traversal, generic motion, file structure
  - Technology plugins handle tech events and tech-specific syntax
- Keep the customization surface small:
  - Prefer simple mapping tables / helper functions over deep inheritance
  - Avoid changing traversal logic to support customer-specific syntax

## Naming

- `DOWNLOAD_CLASS_NAME` must be defined at module level
- Class name matches the translator folder name where possible
- Use `snake_case` for helper methods, `CamelCase` for callbacks

## Safety

- Never modify `downloadStarter.py`, `downloader.py` (E2 installation files)
- Never modify E2 site-packages (`cenpydownload`, `cenpyolpcore`, `cenpylib`)
- Do not install new Python packages unless explicitly approved
- Do not add debugging prints; use `operator.GetLogOperator()` when needed

## Determinism

- Generated output should be deterministic for tests
- Avoid embedding timestamps/usernames unless guarded by a test flag
- Prefer golden-file comparisons for validation

## File Output

- Always call `operator.AddOutputFilePath(path)` in `CloseOutputFile`
- Buffer all output into arrays, write at the end
- Use `FileUtility.AppendTextArrayToFile()` for writing
