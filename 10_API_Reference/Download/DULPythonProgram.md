---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - program
---

# DULPythonProgram

Represents a program being downloaded. Contains metadata and aggregate
statistics about operations, motions, and events.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Program name |
| `GetProgramType()` | `int` | Program type identifier |
| `GetOperationCount()` | `int` | Number of operations |
| `GetMotionCount()` | `int` | Total motion points |
| `GetEventCount()` | `int` | Total events |
| `GetCycleTime()` | `float` | Total cycle time in **seconds** |
| `GetPathLength()` | `float` | Total path length in **mm** |
| `GetAttribute(name)` | `object` | Custom program attribute |
| `IsMainProgram()` | `bool` | True for the top-level program in the download |
| `GetSubprograms()` | `list[DULPythonSubprogram]` | Subprogram references from this program |
| `GetGroupsAndSubprograms()` | `list` | Interleaved operation groups and subprogram references in workcell order |
| `GetOperationGroups()` | `list[DULPythonOperationGroup]` | Operation groups only |

## Usage

```python
def PreProgram(program):
    name = program.GetName()
    motionCount = program.GetMotionCount()

    # Open output file
    self.currentFile = open(f"{name}.src", "w")
    self.currentFile.write(f"; Program: {name}\n")
    self.currentFile.write(f"; Points: {motionCount}\n")
```

## Notes

- Program names come from the E2 workcell. They may contain spaces — sanitize
  for file systems if needed.
- `GetCycleTime()` and `GetPathLength()` are estimates from E2 simulation.
- Items from `GetGroupsAndSubprograms()` must be distinguished with
  `CastToOperationGroup()` or `CastToSubprogram()`.

## See Also

- [[DULPythonDownloadOperator]] — `operator.GetProgram()`
- [[DULPythonSubprogram]] — subprogram reference and called-program access
- [[Download Callback Lifecycle]] — PreProgram/PostProgram callbacks
- [[Subprogram Traversal Semantics]] — traversal and file-output strategies
