---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - joint
  - axes
---

# DULPythonJoint

Represents joint-space configuration at a motion point. Contains axis values
for robot axes and external axes.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetNumberOfAxes()` | `int` | Number of robot axes |
| `GetJointValue(index)` | `float` | Single joint value in **degrees** |
| `GetJointValues()` | `list[float]` | All robot joint values in **degrees** |
| `IsValid()` | `bool` | Whether joint data is valid |
| `GetExternalAxisValues()` | `list[float]` | External axis values |
| `GetTurns()` | `list[int]` | Axis turn indicators |

## Units

- Robot axes: **degrees** (°)
- External axes: **mm** (linear) or **degrees** (rotary) — depends on axis type

## Usage

```python
def PreMotion(motion):
    joint = motion.GetJoint()
    if not joint.IsValid():
        return True  # no joint solution available

    numAxes = joint.GetNumberOfAxes()
    values = joint.GetJointValues()

    # Format for KUKA: A1=..., A2=..., etc.
    parts = [f"A{i+1} {v:.3f}" for i, v in enumerate(values)]

    # External axes (positioner, track, etc.)
    extAxes = joint.GetExternalAxisValues()
    for i, v in enumerate(extAxes):
        parts.append(f"E{i+1} {v:.3f}")
```

## Notes

- `IsValid()` may return `False` if E2 couldn't solve inverse kinematics.
  Handle this gracefully (skip point or emit warning).
- `GetTurns()` provides turn/configuration indicators needed by some robot
  languages (KUKA S/T bits, Fanuc FLIP/NOFLIP, etc.).
- Axis indices are 0-based.

## See Also

- [[DULPythonMotion]] — parent object (call `motion.GetJoint()`)
- [[DULPythonPosition]] — Cartesian alternative
