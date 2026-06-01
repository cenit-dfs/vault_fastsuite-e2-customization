---
type: api-callback
status: active
e2-package: cenpyupload
e2-area: uploader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - upload-api
  - callback-lifecycle
---

# Upload Callback Lifecycle

The upload process is the **reverse of download** — it reads robot program files and creates E2 programs, operations, and motions in the OLP tree.

## Callback Order

| # | Callback | Parameter | Description |
|---|----------|-----------|-------------|
| 1 | `PreUpload` | controller | Called once before upload begins; initialize state |
| 2 | `PreParseFile` | — | Called before each robot file is parsed |
| 3 | `PostParseFile` | — | Called after each robot file is parsed |
| 4 | `PreCreateProgram` | program | Called before a program node is created in E2 |
| 5 | `PostCreateProgram` | program | Called after a program node is created in E2 |
| 6 | `PreCreateOperation` | — | Called before an operation is created |
| 7 | `PostCreateOperation` | — | Called after an operation is created |
| 8 | `PostUpload` | operator | Called once after all files are processed; finalize |

## Usage Example

```python
def PreUpload(controller):
    """Initialize upload state, read config files."""
    pass

def PostUpload(operator):
    """Finalize upload, set program attributes."""
    prog = operator.GetProgram()
    prog.SetAttribute("UploadSource", "CustomUploader")

def PreParseFile():
    """Prepare for file parsing."""
    pass

def PostParseFile():
    """Post-process parsed data."""
    pass

def PreCreateProgram(program):
    """Configure program before creation."""
    program.SetName("Uploaded_Program")

def PostCreateProgram(program):
    """Adjust program after creation."""
    pass

def PreCreateOperation():
    """Prepare operation creation."""
    pass

def PostCreateOperation():
    """Finalize operation after creation."""
    pass
```

## See Also

- [[ULPythonUploadOperator]]
- [[ULPythonController]]
- [[ULPythonProgram]]
- [[Download Callback Lifecycle]]
