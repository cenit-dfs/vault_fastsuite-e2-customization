---
type: api-class
status: active
e2-package: cenpyupload
e2-area: uploader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - upload-api
  - program
---

# ULPythonProgram

Represents a program node in the E2 OLP tree created during upload. Created via [[ULPythonUploadOperator|operator.CreateProgram(name)]] or received as parameter in `PreCreateProgram` / `PostCreateProgram`.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Get the program name |
| `SetName(name)` | — | Set the program name |
| `SetProgramType(progType: int)` | — | Set the program type |
| `SetAttribute(name, value)` | — | Set a custom attribute on the program |
| `AddOperation(operation)` | — | Add an operation to the program |
| `GetOperationCount()` | `int` | Get the number of operations in this program |

## Usage Example

```python
def PostUpload(operator):
    # Create a new program
    prog = operator.CreateProgram("WeldProgram_01")
    prog.SetProgramType(0)
    prog.SetAttribute("Author", "UploadScript")

    # Add operations
    op1 = operator.CreateOperation("Seam_1")
    prog.AddOperation(op1)

    op2 = operator.CreateOperation("Seam_2")
    prog.AddOperation(op2)

    count = prog.GetOperationCount()
    operator.LogInfo(f"Program has {count} operations")


def PreCreateProgram(program):
    """Modify program name before creation."""
    original = program.GetName()
    program.SetName(f"UL_{original}")
```

## See Also

- [[ULPythonUploadOperator]]
- [[ULPythonController]]
- [[Upload Callback Lifecycle]]
