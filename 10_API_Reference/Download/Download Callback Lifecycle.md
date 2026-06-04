---
type: api-callback
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - callback
  - lifecycle
---

# Download Callback Lifecycle

The download process invokes callbacks in a fixed order. Implement these in your
downloader script to customize code generation at each stage.

## Callback Order

```text
PreDownload(controller)
│
├─ PreProgram(program)
│  │
│  ├─ PreOperation(operation)
│  │  │
│  │  ├─ PreMotion(motion)
│  │  │  ├─ PreEvent(event)     ← events attached to this motion
│  │  │  └─ PostEvent(event)
│  │  └─ PostMotion(motion)
│  │
│  └─ PostOperation(operation)
│
└─ PostProgram(program)
│
PostDownload(operator)
```

## Callbacks

### PreDownload

Called once before any program download begins. Use for: opening global output
files, reading controller settings, initializing state.

**Parameter:** `controller` — [[DULPythonController]]

### PostDownload

Called once after all programs are downloaded. Use for: closing files, writing
summary, cleanup.

**Parameter:** `operator` — [[DULPythonDownloadOperator]]

### PreProgram

Called before each program. Use for: opening program file, writing program header.

**Parameter:** `program` — [[DULPythonProgram]]

### PostProgram

Called after each program. Use for: writing program footer, closing program file.

**Parameter:** `program` — [[DULPythonProgram]]

### PreOperation

Called before each operation within a program.

**Parameter:** `operation` — operation object

### PostOperation

Called after each operation.

**Parameter:** `operation` — operation object

### PreMotion

Called before each motion point. This is where most position output happens.

**Parameter:** `motion` — [[DULPythonMotion]]

### PostMotion

Called after each motion point.

**Parameter:** `motion` — [[DULPythonMotion]]

### PreEvent

Called for each technology event attached to a motion. Events include arc
welding start/stop, signals, tool changes, etc.

**Parameter:** `event` — [[DULPythonEvent]]

### PostEvent

Called after event processing.

**Parameter:** `event` — [[DULPythonEvent]]

## Starter Framework Lifecycle

Downloader implementations based on `downloadStarter.py` use a more detailed
callback layer around program traversal:

```text
HandleProgram(program)
├─ OutputHeader(operator, controller)
├─ LoopProgram(program)
│  ├─ ProgramStart(operator, program)
│  ├─ operation groups → operations → motions and events
│  ├─ subprogram reference
│  │  ├─ SubprogramStart(operator, subprogram)
│  │  ├─ [optional inline LoopProgram(calledProgram)]
│  │  └─ SubProgramEnd(operator, subprogram)
│  └─ ProgramEnd(operator, program)
├─ [optional appended LoopProgram for each called program]
├─ CreateOutputFile(operator)
├─ WriteOutputFile(operator)
├─ CloseOutputFile(operator)
└─ [separate-file mode: HandleProgram(each called program)]
```

### SubprogramStart

Called when parent-program traversal encounters a [[DULPythonSubprogram]]
reference. Typically used to emit a `CALL` statement.

### SubProgramEnd

Called after processing a subprogram reference. The capital `P` in
`SubProgramEnd` is part of the framework method name.

These callbacks process references in the parent program. When a called program
is traversed, it receives its own `ProgramStart` through `ProgramEnd` lifecycle.

## Subprogram Strategy Methods

These overridable methods are queried by the starter framework:

| Method | Base default | Effect |
|--------|--------------|--------|
| `OutputSubprogramInSeparateFiles()` | `True` | Create a separate output file for each called program |
| `HandleSubprogramInLoop()` | `False` | Inline called-program traversal at the reference when separate files are disabled |

## See Also

- [[DULPythonDownloadOperator]] — main operator available in all callbacks
- [[DULPythonProgram]] — program traversal methods
- [[DULPythonSubprogram]] — subprogram reference API
- [[20_Patterns/Downloader/Callback Lifecycle Pattern|Callback Lifecycle Pattern]]
- [[Subprogram Traversal Semantics]] — detailed behavior matrix
- [[Per-Program State Reset]] — state handling across separate files
