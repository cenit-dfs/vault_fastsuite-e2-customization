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
  - state-management
  - common-mistake
related:
  - "[[Callback Lifecycle Pattern]]"
  - "[[Common Downloader Mistakes]]"
  - "[[DULPythonProgram]]"
---

# Per-Program State Reset

When `OutputSubprogramInSeparateFiles()` returns True, `ProgramStart` is called
once per program (main + each subprogram). All per-program state MUST be reset
in `ProgramStart` — not in `__init__` or `OutputHeader`.

## The Trap

A common mistake is initializing per-program state in `__init__` or `OutputHeader`,
assuming they run once per program. In multi-program mode:

| Callback | Calls per download | Correct use |
|----------|--------------------|-------------|
| `__init__()` | 1 (total) | Immutable config, constants |
| `Initialize(operator)` | 1 (total) | Read global settings, set up shared resources |
| `OutputHeader(operator, controller)` | 1 per `HandleProgram` | Controller-level data (output dir, profiles) |
| `ProgramStart(operator, program)` | 1 per `HandleProgram` | **Reset ALL per-program state** |

## What to Reset in ProgramStart

```python
def ProgramStart(self, operator, program):
    self.programName = program.GetName()

    # Output buffers — new file for each program
    self.TransBlock = []
    self.JointsBlock = []
    self.Source = []

    # Motion state
    self.pendingSpeed = None
    self.lastSpeed = None
    self.lastAccuracy = None
    self.lastMotionType = None

    # Technology state
    self.arcOnActive = False
    self.arcOffPending = False
    self.currentWeldCondition = None

    # Feature flags (recalculated per program)
    self.hasTouchSensing = False
    self.hasSignals = False
```

## What Stays in OutputHeader

`OutputHeader` is still called once per `HandleProgram`, so it runs per program
in separate-files mode. But it should only read **controller-level** data that
is constant across programs:

```python
def OutputHeader(self, operator, controller):
    # These don't change between programs:
    self.outputDir = operator.GetOutputDirectory()
    self.ptpSpeed = controller.GetAttribute("FS2_PtpSpeed")
    self.explicitMode = controller.GetBoolAttribute("FS2_ExplicitMode")
    self.resourcePrefix = controller.GetAttribute("FS2_ResourcePrefix")
```

## What Stays in Initialize

`Initialize` is called ONCE per download. Use for:
- Reading operator-level settings that apply to all programs
- Setting up shared data structures (like dictionaries of resources)
- One-time validation

## The Bug This Prevents

Without proper reset, subprogram N inherits state from subprogram N-1:
- Position buffers from previous program leak into current file
- Arc welding flags from one subprogram affect the next
- Speed/accuracy values carry over incorrectly
- Subroutines get emitted in every file instead of once

## Evidence

Discovered during Kawasaki downloader implementation (2026-06-04). When
`SEPARATE_SUBPROGRAM_FILES=True`, the framework calls the full HandleProgram
cycle per subprogram. Without resetting state in ProgramStart, TransBlock
accumulated positions from ALL programs into EVERY output file.

## See Also

- [[Callback Lifecycle Pattern]] — full lifecycle overview
- [[Common Downloader Mistakes]] — related pitfalls
