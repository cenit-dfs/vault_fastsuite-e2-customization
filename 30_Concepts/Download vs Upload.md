---
type: concept
status: active
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - concept
  - download
  - upload
  - lifecycle
---

# Download vs Upload

Download and Upload are inverse operations. Understanding both helps when
writing translators that need to round-trip.

## Comparison

| Aspect | Download | Upload |
|--------|----------|--------|
| Direction | E2 → Robot files | Robot files → E2 |
| Input | OLP tree (programs, motions, events) | Text files (.src, .mod, .ls, .as) |
| Output | Vendor-specific robot code | E2 programs with motions/events |
| API package | `cenpydownload` | `cenpyupload` |
| Main object | `DULPythonDownloadOperator` | `ULPythonUploadOperator` |
| Position access | Read (`GetXYZ()`, `GetOrientation()`) | Write (`SetX()`, `SetY()`, ...) |
| Event access | Read (`GetAttributes()`) | Write (`SetAttribute()`, `CreateEvent()`) |
| File I/O | Write files (`WriteOutputFile`) | Read files (`ReadFile`, `GetFileList`) |

## Lifecycle Comparison

```text
DOWNLOAD                          UPLOAD
─────────                         ──────
Initialize                        PreUpload
CreateOutputFile                  PreParseFile
ProgramStart                      ← Parse robot file
  OperationStart                  PreCreateProgram
    HandleMotion                    CreateMotion + SetPosition
    HandleEvent                     CreateEvent + SetAttributes
  OperationEnd                    PostCreateProgram
ProgramEnd                        PostParseFile
WriteOutputFile                   PostUpload
CloseOutputFile
```

## Key Difference: Creator vs. Consumer

- **Download** = you CONSUME E2 data and FORMAT it as text
- **Upload** = you PARSE text and CREATE E2 objects

Upload requires creating objects explicitly:
```python
motion = operator.CreateMotion("P001")
pos = operator.CreatePosition()
pos.SetX(100.0)  # mm
motion.SetPosition(pos)
```

## See Also

- [[10_API_Reference/Download/Download Callback Lifecycle|Download Callbacks]]
- [[10_API_Reference/Upload/Upload Callback Lifecycle|Upload Callbacks]]
