---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - speed
  - velocity
---

# DULPythonSpeedProfile

Represents speed/velocity settings for a motion point.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetType()` | `int` | Speed type identifier |
| `GetValue()` | `float` | Speed value (units depend on type) |
| `GetUnit()` | `str` | Speed unit string |
| `IsPercentage()` | `bool` | True if speed is percentage (PTP) |
| `IsAbsolute()` | `bool` | True if speed is absolute value |
| `GetLinearSpeed()` | `float` | Linear speed in **mm/s** |
| `GetAngularSpeed()` | `float` | Angular speed in **deg/s** |
| `GetAcceleration()` | `float` | Acceleration value |

## Usage

```python
def PreMotion(motion):
    speed = motion.GetSpeed()

    if speed.IsPercentage():
        # PTP speed — typically 0-100%
        pctValue = speed.GetValue()
        line = f"  PTP pos SPEED={pctValue:.0f}%"
    else:
        # LIN/CIR speed — absolute value in mm/s
        linSpeed = speed.GetLinearSpeed()
        line = f"  LIN pos VEL={linSpeed:.1f}mm/s"
```

## Notes

- PTP motions typically use percentage speed (joint speed ratio).
- LIN/CIR motions typically use absolute speed in mm/s.
- `GetAcceleration()` may be 0 if not configured in E2.

## See Also

- [[DULPythonMotion]] — parent object (call `motion.GetSpeed()`)
- [[DULPythonAccuracyProfile]] — blending/approximation (related concept)
