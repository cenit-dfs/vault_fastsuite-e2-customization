---
type: api-class
status: active
e2-package: cenpyupload
e2-area: uploader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - upload-api
  - operator
---

# ULPythonUploadOperator

The central orchestration object for the upload process. Provides access to the controller, programs, file I/O, and logging. Received as parameter in `PostUpload`.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetController()` | [[ULPythonController]] | Get the target controller object |
| `GetProgram()` | [[ULPythonProgram]] | Get the current program being created |
| `CreateProgram(name)` | [[ULPythonProgram]] | Create a new program node with the given name |
| `CreateOperation(name)` | Operation | Create a new operation in the current program |
| `CreateMotion(name)` | [[ULPythonMotion]] | Create a new motion statement |
| `CreatePosition()` | [[ULPythonPosition]] | Create a new position object |
| `CreateEvent(name)` | [[ULPythonEvent]] | Create a new event (technology command) |
| `ReadFile(filename)` | `str` | Read a robot file and return its contents |
| `FileExists(filename)` | `bool` | Check if a file exists in the source path |
| `GetFileList(pattern)` | `list[str]` | List files matching a glob pattern |
| `GetLogger()` | Logger | Get the E2 logger instance |
| `LogInfo(message)` | — | Log an informational message |
| `LogWarning(message)` | — | Log a warning message |
| `LogError(message)` | — | Log an error message |
| `GetSourcePath()` | `str` | Get the source directory for robot files |
| `GetAttribute(name)` | `object` | Get a named attribute from the upload context |

## Usage Example

```python
def PostUpload(operator):
    source = operator.GetSourcePath()
    files = operator.GetFileList("*.src")

    for f in files:
        if operator.FileExists(f):
            content = operator.ReadFile(f)
            prog = operator.CreateProgram(f.replace(".src", ""))
            op = operator.CreateOperation("MainOp")

            # Parse content and create motions
            motion = operator.CreateMotion("M1")
            pos = operator.CreatePosition()
            pos.SetX(100.0)
            pos.SetY(200.0)
            pos.SetZ(300.0)
            motion.SetPosition(pos)

    operator.LogInfo("Upload complete")
```

## See Also

- [[Upload Callback Lifecycle]]
- [[ULPythonController]]
- [[ULPythonProgram]]
- [[ULPythonMotion]]
- [[ULPythonPosition]]
- [[ULPythonEvent]]
