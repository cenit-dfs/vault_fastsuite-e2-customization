---
type: api-class
status: active
source: agent-generated
e2-package: cenpydownload
e2-area: downloader
created: 2026-06-04
tags:
  - download-api
  - subprogram
  - api-gap
related:
  - "[[DULPythonProgram]]"
  - "[[Download Callback Lifecycle]]"
---

# DULPythonSubprogram

Represents a subprogram reference within a parent program. Obtained from
`program.GetSubprograms()` or by casting from `GetGroupsAndSubprograms()`.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Subprogram name (used in CALL statements) |
| `GetCalledProgram()` | `DULPythonProgram` | The actual program object being called |

## Usage

```python
def SubprogramStart(self, operator, subprogram):
    """Called during parent program traversal when a subprogram CALL is encountered."""
    self.Source.append('CALL ' + subprogram.GetName())
```

## Accessing the Called Program

```python
# To inspect the subprogram's contents (e.g., pre-scanning for features):
calledProg = subprogram.GetCalledProgram()
name = calledProg.GetName()
isMain = calledProg.IsMainProgram()  # Always False for subprograms
opGroups = calledProg.GetOperationGroups()
```

## Callback Context

`SubprogramStart(operator, subprogram)` is called:
- During `GetGroupsAndSubprograms()` traversal of the PARENT program
- BEFORE the subprogram's own `HandleProgram` call (if `SeparateFiles=True`)
- The subprogram object here is a REFERENCE, not the program itself

## Notes

- `DULPythonSubprogram` does NOT have `IsMainProgram()` — that's on `DULPythonProgram`.
- The name from `GetName()` matches the name from `GetCalledProgram().GetName()`.
- In `GetGroupsAndSubprograms()`, use `CastToSubprogram()` to distinguish from operation groups.

## See Also

- [[DULPythonProgram]] — the program object (has `IsMainProgram()`, `GetSubprograms()`)
- [[Download Callback Lifecycle]] — when SubprogramStart is called
- [[Subprogram Traversal Semantics]] — multi-program traversal strategies
