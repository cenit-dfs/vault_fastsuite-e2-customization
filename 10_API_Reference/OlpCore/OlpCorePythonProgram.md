---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - program
---

# OlpCorePythonProgram

Represents a robot program in the OLP project tree. A program belongs to a controller and contains operations, operation groups, and subprograms. Supports computation for cycle time and path length.

**Inherits from:** [[OlpCorePythonItem]]

## Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `GetController()` | `OlpCorePythonController` | Returns the parent controller |
| `GetTechnology()` | `OlpCorePythonTechnology` | Returns the associated technology |
| `GetChildComponents()` | `list[ProgramComponent]` | Returns all direct child components |
| `GetOperations()` | `list[Operation]` | Returns all operations in this program |
| `GetOperationGroups()` | `list[OperationGroup]` | Returns all operation groups |
| `GetSubprograms()` | `list[Subprogram]` | Returns all subprograms |
| `Compute()` | `bool` | Computes the program (trajectory planning); returns success |
| `IsComputed()` | `bool` | Returns whether the program is currently computed |
| `GetCycleTime()` | `float` | Returns total cycle time in seconds |
| `GetPathLength()` | `float` | Returns total path length in mm |

## Usage

```python
program = controller.GetPrograms()[0]

if not program.IsComputed():
    program.Compute()

cycle_time = program.GetCycleTime()
path_length = program.GetPathLength()

for operation in program.GetOperations():
    print(operation.GetName(), operation.GetCycleTime())
```

## See Also

- [[OlpCorePythonController]] — Parent controller
- [[OlpCorePythonOperation]] — Operations within the program
- [[OlpCorePythonTechnology]] — Associated technology
- [[OlpCorePythonItem]] — Base class
