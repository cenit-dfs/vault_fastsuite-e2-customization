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
1. __init__()              — construct, initialize arrays/variables
2. Initialize(operator)    — called ONCE per download (even with sub-programs)
3. OutputHeader(operator, controller) — controller name, date, etc.
4. CreateOutputFile(operator)  — define output file paths
5. ProgramStart(operator, program)
   ├─ OperationGroupStart(operator, operationGroup)
   │  ├─ OperationStart(operator, operation)
   │  │  ├─ HandleMotion(operator, motion)
   │  │  │  ├─ eventsBefore → HandleEvent(operator, event, motion)
   │  │  │  ├─ OutputPositionData(operator, position)
   │  │  │  ├─ OutputMotionData(operator, motion, ...)
   │  │  │  └─ eventsAfter → HandleEvent(operator, event, motion)
   │  │  └─ ... (more motions)
   │  ├─ OperationEnd(operator, operation)
   │  └─ ... (more operations)
   ├─ OperationGroupEnd(operator, operationGroup)
   └─ ... (more groups)
6. ProgramEnd(operator, program)
7. WriteOutputFile(operator)  — write all buffered content to files
8. CloseOutputFile(operator)  — register output files with E2
```

## Key Rules

- `Initialize` is called **once** — not once per program.
- `CreateOutputFile` is where you build file paths (not where you write).
- `WriteOutputFile` is where you flush buffers to disk.
- `CloseOutputFile` MUST call `operator.AddOutputFilePath(path)` or E2 won't
  show the file in the output dialog.

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
def HandleMotion(self, operator, motion: DULPythonMotion):
def HandleEvent(self, operator, event: DULPythonEvent, motion):
def CreateOutputFile(self, operator):
def WriteOutputFile(self, operator):
def CloseOutputFile(self, operator):
```

## See Also

- [[10_API_Reference/Download/Download Callback Lifecycle|Download Callback Lifecycle (API)]]
- [[File Output Pattern]]
- [[Event Dispatch Pattern]]
