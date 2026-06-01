---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - position
  - coordinates
---

# DULPythonPosition

Represents a Cartesian position (translation + rotation) in the robot's
coordinate system. Positions are always in **millimeters** and **degrees**.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetX()` | `float` | X coordinate in **mm** |
| `GetY()` | `float` | Y coordinate in **mm** |
| `GetZ()` | `float` | Z coordinate in **mm** |
| `GetRX()` | `float` | Rotation around X in **degrees** |
| `GetRY()` | `float` | Rotation around Y in **degrees** |
| `GetRZ()` | `float` | Rotation around Z in **degrees** |
| `GetMatrix()` | `list[list[float]]` | 4×4 homogeneous transformation matrix |
| `GetFrameName()` | `str` | Reference frame name (empty = world) |
| `IsValid()` | `bool` | Whether position data is valid |

## Units

- Translation: **millimeters** (mm)
- Rotation: **degrees** (°)
- Matrix: standard 4×4 homogeneous (rotation 3×3 + translation column, row-major)

> **Note:** E2 internally stores positions in meters. The Download API converts
> to millimeters before exposing to the downloader. No manual conversion needed.

## Usage

```python
def PreMotion(motion):
    pos = motion.GetPosition()
    if not pos.IsValid():
        return True  # skip invalid positions

    x = pos.GetX()
    y = pos.GetY()
    z = pos.GetZ()
    rx = pos.GetRX()
    ry = pos.GetRY()
    rz = pos.GetRZ()

    # Most robot languages expect mm and degrees — output directly
    line = f"  LIN {{X {x:.3f}, Y {y:.3f}, Z {z:.3f}, A {rz:.3f}, B {ry:.3f}, C {rx:.3f}}}"
```

## See Also

- [[DULPythonMotion]] — parent object (call `motion.GetPosition()`)
- [[DULPythonJoint]] — joint-space representation of the same point
