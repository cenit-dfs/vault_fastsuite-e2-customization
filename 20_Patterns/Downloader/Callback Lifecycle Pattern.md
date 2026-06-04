---
type: pattern
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - pattern
  - lifecycle
---

# Callback Lifecycle Pattern

The E2 download engine calls downloader methods in a fixed order. Understanding
this sequence is essential for correct file output timing.

## Full Lifecycle

```text
__init__()
Initialize(operator)                         ← once per download
HandleProgram(program)
├─ OutputHeader(operator, controller)
├─ LoopProgram(program)
│  ├─ ProgramStart(operator, program)
│  ├─ for each item in program.GetGroupsAndSubprograms():
│  │  ├─ OperationGroupStart(operator, operationGroup)
│  │  │  ├─ OperationStart(operator, operation)
│  │  │  │  ├─ HandleMotion(operator, motion)
│  │  │  │  │  ├─ eventsBefore → HandleEvent(operator, event, motion)
│  │  │  │  │  ├─ OutputPositionData(operator, position)
│  │  │  │  │  ├─ OutputMotionData(operator, motion, ...)
│  │  │  │  │  └─ eventsAfter → HandleEvent(operator, event, motion)
│  │  │  │  └─ ... (more motions)
│  │  │  └─ OperationEnd(operator, operation)
│  │  ├─ OperationGroupEnd(operator, operationGroup)
│  │  └─ or, for a subprogram reference:
│  │     ├─ SubprogramStart(operator, subprogram)
│  │     ├─ [if HandleSubprogramInLoop(): LoopProgram(calledProgram)]
│  │     └─ SubProgramEnd(operator, subprogram)
│  └─ ProgramEnd(operator, program)
├─ [if not separate and not inline: LoopProgram(each calledProgram)]
├─ CreateOutputFile(operator)
├─ WriteOutputFile(operator)
├─ CloseOutputFile(operator)
└─ [if separate files: HandleProgram(each calledProgram)]
```

## Key Rules

- `Initialize` is called **once** — not once per program.
- With separate subprogram files, the full `HandleProgram` cycle runs once for
  the main program and once for each called program.
- Reset all per-program buffers and state in `ProgramStart`.
- `SubprogramStart` and `SubProgramEnd` process a subprogram reference in its
  parent; they do not replace the called program's own lifecycle.
- `CreateOutputFile` is where you build file paths (not where you write).
- `WriteOutputFile` is where you flush buffers to disk.
- `CloseOutputFile` MUST call `operator.AddOutputFilePath(path)` or E2 won't
  show the file in the output dialog.

## Subprogram Strategy Methods

| Method | Base default | Effect |
|--------|--------------|--------|
| `OutputSubprogramInSeparateFiles()` | `True` | Run a complete `HandleProgram` cycle and create a separate file for each called program |
| `HandleSubprogramInLoop()` | `False` | When separate files are disabled, traverse called programs inline at their references |

See [[Subprogram Traversal Semantics]] for the full behavior matrix.

## Callback Signatures

```python
def __init__(self) -> None:
def Initialize(self, operator: DULPythonDownloadOperator):
def OutputHeader(self, operator, controller: DULPythonController):
def ProgramStart(self, operator, program: DULPythonProgram):
def ProgramEnd(self, operator, program: DULPythonProgram):
def OperationGroupStart(self, operator, operationGroup):
def OperationGroupEnd(self, operator, operationGroup):
def OperationStart(self, operator, operation):
def OperationEnd(self, operator, operation):
def SubprogramStart(self, operator, subprogram: DULPythonSubprogram):
def SubProgramEnd(self, operator, subprogram: DULPythonSubprogram):
def HandleMotion(self, operator, motion: DULPythonMotion):
def HandleEvent(self, operator, event: DULPythonEvent, motion):
def CreateOutputFile(self, operator):
def WriteOutputFile(self, operator):
def CloseOutputFile(self, operator):
def OutputSubprogramInSeparateFiles(self) -> bool:
def HandleSubprogramInLoop(self) -> bool:
```

## See Also

- [[10_API_Reference/Download/Download Callback Lifecycle|Download Callback Lifecycle (API)]]
- [[DULPythonSubprogram]]
- [[Per-Program State Reset]]
- [[Subprogram Traversal Semantics]]
- [[File Output Pattern]]
- [[Event Dispatch Pattern]]
