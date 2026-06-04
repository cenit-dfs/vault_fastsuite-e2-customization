---
type: pattern
status: active
source: agent-generated
e2-package: cenpydownload
e2-area: downloader
created: 2026-06-04
tags:
  - download-api
  - subprogram
  - pattern
  - container-program
related:
  - "[[DULPythonProgram]]"
  - "[[Download Callback Lifecycle]]"
---

# Container Program Detection

When using `OutputSubprogramInSeparateFiles()=True`, the main program may be a
"container" — it holds only CALL statements and no real motion content. Detecting
this is important for conditional logic (e.g., where to emit shared subroutines).

## The Problem

The framework does NOT explicitly expose whether a program is a "container" vs. a
standalone program with real motion content. You need to derive this from available APIs.

## Detection Pattern

```python
def ProgramStart(self, operator, program):
    self.isContainerProgram = (
        program.IsMainProgram() and
        len(program.GetSubprograms()) > 0
    )
```

A container program has:
- `IsMainProgram()` → `True` (it's the top-level entry point)
- `GetSubprograms()` → non-empty (it delegates work to sub-programs)

## Key API Details

| Method | Available on | Returns | Notes |
|--------|-------------|---------|-------|
| `program.IsMainProgram()` | `DULPythonProgram` | `bool` | True for the top-level program in the download |
| `program.GetSubprograms()` | `DULPythonProgram` | `list[DULPythonSubprogram]` | Subprogram references from this program |
| `subprogram.GetCalledProgram()` | `DULPythonSubprogram` | `DULPythonProgram` | The actual program object being called |
| `subprogram.GetName()` | `DULPythonSubprogram` | `str` | Name used in CALL statement |

## Important: `IsMainProgram()` on Subprograms

The `DULPythonSubprogram` object does NOT have `IsMainProgram()`. You must first
call `subprogram.GetCalledProgram()` to get the `DULPythonProgram`, then check
on that object. However, in practice, you rarely need to — any program reached via
`HandleProgram()` from the subprogram loop is by definition NOT the main program.

In `ProgramStart`, when `SeparateFiles=True`:
- First call: `program.IsMainProgram()` → `True` (the container/main)
- Subsequent calls: `program.IsMainProgram()` → `False` (each subprogram)

## Use Case: Conditional Subroutine Emission

```python
def ProgramEnd(self, operator, program):
    self.Source.append('.END')
    # Emit shared subroutines only once — in the main program file
    if self.isContainerProgram and self._treeNeedsSubroutines:
        self._emitHelperSubroutines()
    elif program.IsMainProgram() and self._programNeedsSubroutines:
        # Standalone program (no subs) — emit here too
        self._emitHelperSubroutines()
```

## Evidence

Discovered during Kawasaki downloader implementation (2026-06-04). The framework
provides no `IsContainerProgram()` method — this detection must be done manually.
Verified: `IsMainProgram()` is only True for the first `ProgramStart` call in
a multi-program download.

## See Also

- [[Download Callback Lifecycle]]
- [[DULPythonProgram]]
