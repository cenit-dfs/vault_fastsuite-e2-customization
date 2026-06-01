---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - operation
---

# OlpCorePythonOperation

Represents a single operation (motion sequence) within a program. Contains teach points (TpElements), events, and references to process geometry and work methods.

**Inherits from:** [[OlpCorePythonProgramComponent]]

## Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `GetOperationType()` | `OperationType` | Returns the operation type enumeration value |
| `GetTpElements()` | `list[TpElement]` | Returns all teach point elements in order |
| `GetEvents()` | `list[Event]` | Returns all events attached to this operation |
| `GetProcessGeometry()` | `ProcessGeometry` | Returns the associated process geometry |
| `GetWorkMethod()` | `WorkMethod` | Returns the work method used by this operation |
| `Compute()` | `bool` | Computes the operation; returns success |
| `IsComputed()` | `bool` | Returns whether the operation is currently computed |
| `GetCycleTime()` | `float` | Returns cycle time in seconds |
| `GetPathLength()` | `float` | Returns path length in mm |

## Usage

```python
for operation in program.GetOperations():
    op_type = operation.GetOperationType()
    tp_elements = operation.GetTpElements()
    
    for tp in tp_elements:
        # Access teach point positions
        pass
    
    for event in operation.GetEvents():
        print(event.GetEventName(), event.GetInsertPosition())
```

## See Also

- [[OlpCorePythonProgram]] — Parent program
- [[OlpCorePythonEvent]] — Events within the operation
- [[OlpCore Enumerations]] — `OperationType`, `InsertPosition` enums
- [[OlpCorePythonTechnology]] — Technology providing work methods
