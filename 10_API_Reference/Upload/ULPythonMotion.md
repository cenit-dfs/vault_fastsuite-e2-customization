---
type: api-class
status: active
e2-package: cenpyupload
e2-area: uploader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - upload-api
  - motion
---

# ULPythonMotion

Represents a motion statement in an uploaded program. Created via [[ULPythonUploadOperator|operator.CreateMotion(name)]].

## Motion Type Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `MOTIONTYPE_PTP` | — | Point-to-point (joint) motion |
| `MOTIONTYPE_LIN` | — | Linear (Cartesian) motion |
| `MOTIONTYPE_CIR` | — | Circular motion |

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Get the motion name |
| `GetMotionType()` | `int` | Get the current motion type |
| `SetName(name)` | — | Set the motion name |
| `SetMotionType(motionType: int)` | — | Set motion type (PTP, LIN, CIR) |
| `SetPosition(pos: ULPythonPosition)` | — | Assign a position to this motion |
| `SetSpeed(speed: float)` | — | Set motion speed |
| `SetAccuracy(accuracy: float)` | — | Set motion accuracy / blending |
| `SetAttribute(name, value)` | — | Set a custom attribute on the motion |

## Usage Example

```python
def PostUpload(operator):
    motion = operator.CreateMotion("LIN_001")
    motion.SetMotionType(MOTIONTYPE_LIN)
    motion.SetSpeed(250.0)
    motion.SetAccuracy(1.0)

    pos = operator.CreatePosition()
    pos.SetX(500.0)
    pos.SetY(100.0)
    pos.SetZ(300.0)
    pos.SetRX(0.0)
    pos.SetRY(0.0)
    pos.SetRZ(90.0)
    motion.SetPosition(pos)
```

## See Also

- [[ULPythonUploadOperator]]
- [[ULPythonPosition]]
- [[Upload Callback Lifecycle]]
