---
tags:
  - mcp-tools-prompt
description: Review and classify notes in the AI inbox for promotion
---

Review all notes in `90_Inbox_AI/`:

For each note:
1. Read the note content and frontmatter
2. Assess quality: Is it accurate? Complete? Well-structured?
3. Determine target folder based on note type:
   - api-class/api-callback → `10_API_Reference/<area>/`
   - pattern → `20_Patterns/<area>/`
   - concept → `30_Concepts/`
   - docstring → `40_Docstrings/<package>/`
   - troubleshooting → `60_Troubleshooting/`
4. Check for duplicates in the target folder
5. Suggest: PROMOTE (with target path), REVISE (with feedback), or REJECT (with reason)

Output a table: | Note | Quality | Action | Target | Reason |
