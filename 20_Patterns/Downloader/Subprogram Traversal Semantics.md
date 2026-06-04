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
  - traversal
  - framework-behavior
related:
  - "[[Download Callback Lifecycle]]"
  - "[[Callback Lifecycle Pattern]]"
  - "[[DULPythonProgram]]"
---

# Subprogram Traversal Semantics

The E2 download starter (`downloadStarter.py`) supports three subprogram strategies
controlled by two overridable methods on the `Downloader` subclass. This determines
how the framework traverses and outputs multi-program robot jobs.

## The Two Control Methods

```python
def OutputSubprogramInSeparateFiles(self):
    """Return True to generate each subprogram as its own file."""
    return True  # base class default

def HandleSubprogramInLoop(self):
    """Return True to inline subprograms during main traversal."""
    return False  # base class default
```

## Behavior Matrix

| `OutputSubprogramInSeparateFiles()` | `HandleSubprogramInLoop()` | Behavior |
|-------------------------------------|---------------------------|----------|
| `True` (base default) | — (ignored) | Each subprogram gets its own full `HandleProgram()` call → separate output file |
| `False` | `False` | After main program loop, subprograms are appended via `LoopProgram()` in the same file |
| `False` | `True` | Subprograms are traversed inline during `GetGroupsAndSubprograms()` iteration |

## Framework Traversal Flow

Extracted from `downloadStarter.py` — this is the actual call sequence:

```text
DownloadActiveProgram()
└─ HandleProgram(mainProgram)
   ├─ OutputHeader()
   ├─ LoopProgram(mainProgram)
   │  ├─ ProgramStart()
   │  ├─ for each in program.GetGroupsAndSubprograms():
   │  │  ├─ if OperationGroup → OperationGroupStart → Operations → OperationGroupEnd
   │  │  └─ if Subprogram → SubprogramStart() / [inline LoopProgram if HandleInLoop] / SubProgramEnd()
   │  └─ ProgramEnd()
   ├─ [if !SeparateFiles && !HandleInLoop: loop program.GetSubprograms() → LoopProgram(each)]
   ├─ CreateOutputFile → WriteOutputFile → CloseOutputFile
   └─ [if SeparateFiles: loop program.GetSubprograms() → HandleProgram(each)]
```

## Critical Implications

1. **When `SeparateFiles=True`:** The full cycle (`OutputHeader → LoopProgram → Create/Write/Close`) runs once per program. `Initialize()` is still called only ONCE for the entire download.

2. **When `SeparateFiles=True`:** `SubprogramStart` is called during the MAIN program's traversal (for the CALL statement), but the sub's own content is handled in a separate `HandleProgram` call later.

3. **`GetGroupsAndSubprograms()`** returns interleaved operation groups AND subprogram references in the order they appear in the E2 workcell. Use `CastToOperationGroup()` / `CastToSubprogram()` to distinguish them.

4. **`GetSubprograms()`** returns only the subprogram references (no operation groups). Use this for iteration when you don't need ordering context.

## Evidence

Confirmed via production session implementing Kawasaki multi-program downloader
(2026-06-04). Verified behavior by examining `downloadStarter.py` source and
testing with E2's HandleProgram cycle per subprogram.

## See Also

- [[Download Callback Lifecycle]] — single-program callback order
- [[Callback Lifecycle Pattern]] — lifecycle usage patterns
- [[DULPythonProgram]] — program object API
