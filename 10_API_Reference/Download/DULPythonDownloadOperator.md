---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - operator
---

# DULPythonDownloadOperator

The main operator object providing download infrastructure: file I/O, logging,
controller/program access. Available in `PostDownload` and accessible via the
global download context.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetController()` | [[DULPythonController]] | Controller object |
| `GetProgram()` | [[DULPythonProgram]] | Current program |
| `WriteFile(filename, content)` | — | Write file to output directory |
| `WriteLine(line)` | — | Write line to current open file |
| `GetLogger()` | `Logger` | Logger instance |
| `LogInfo(message)` | — | Log info message |
| `LogWarning(message)` | — | Log warning message |
| `LogError(message)` | — | Log error message |
| `GetOutputPath()` | `str` | Output directory path |
| `CreateDirectory(path)` | — | Create subdirectory in output |
| `FileExists(filename)` | `bool` | Check file existence in output |
| `ReadFile(filename)` | `str` | Read file from output directory |
| `GetAttribute(name)` | `object` | Download setting attribute |

## File I/O

Two patterns for writing output:

### Pattern 1: WriteFile (complete content)

```python
def PostProgram(program):
    content = "\n".join(self.lines)
    self.operator.WriteFile(f"{program.GetName()}.src", content)
```

### Pattern 2: WriteLine (streaming)

```python
def PreMotion(motion):
    self.operator.WriteLine(f"  LIN {{X {pos.GetX():.3f}}}")
```

## Logging

Use the operator's logging methods to report progress and errors. Messages
appear in the E2 download log window.

```python
self.operator.LogInfo(f"Processing program: {name}")
self.operator.LogWarning(f"Unreachable point: {motion.GetName()}")
self.operator.LogError(f"Failed to write file: {filename}")
```

## See Also

- [[DULPythonController]] — controller info
- [[DULPythonProgram]] — program info
- [[Download Callback Lifecycle]] — operator available in PostDownload
