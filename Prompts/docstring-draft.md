---
tags:
  - mcp-tools-prompt
description: Generate a docstring for an E2 Python API symbol following the style guide
---

Generate a Google-style docstring for: package="{{package}}", symbol="{{symbol_name}}"

Steps:
1. Search `10_API_Reference/` and `40_Docstrings/` for existing documentation
2. Read `80_Standards/docstring-guide.md` for the format rules
3. Search `20_Patterns/` for usage examples of this symbol
4. Generate a complete docstring following the guide:
   - One-line summary
   - Extended description with units and coordinate frames
   - Args section (with units for every numeric param)
   - Returns section (with units and structure)
   - Note section for gotchas
   - Example section with real usage

Output the docstring ready to paste into a `.pyi` stub file.
Always state units explicitly. Always document sentinel values.
