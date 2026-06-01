---
type: api-class
status: active
e2-package: cenpyupload
e2-area: uploader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - upload-api
  - position
---

# ULPythonPosition

Represents a Cartesian position (translation + rotation) for an uploaded motion. Units are **mm** (translation) and **degrees** (rotation). Created via [[ULPythonUploadOperator|operator.CreatePosition()]].

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetX()` | `float` | Get X translation (mm) |
| `GetY()` | `float` | Get Y translation (mm) |
| `GetZ()` | `float` | Get Z translation (mm) |
| `GetRX()` | `float` | Get rotation around X axis (degrees) |
| `GetRY()` | `float` | Get rotation around Y axis (degrees) |
| `GetRZ()` | `float` | Get rotation around Z axis (degrees) |
| `SetX(value: float)` | — | Set X translation (mm) |
| `SetY(value: float)` | — | Set Y translation (mm) |
| `SetZ(value: float)` | — | Set Z translation (mm) |
| `SetRX(value: float)` | — | Set rotation around X (degrees) |
| `SetRY(value: float)` | — | Set rotation around Y (degrees) |
| `SetRZ(value: float)` | — | Set rotation around Z (degrees) |
| `SetFromMatrix(matrix)` | — | Set position from a 4×4 transformation matrix |
| `SetFrameName(name)` | — | Assign a named reference frame |

## Usage Example

```python
pos = operator.CreatePosition()

# Set translation
pos.SetX(1250.5)
pos.SetY(-300.0)
pos.SetZ(850.0)

# Set orientation
pos.SetRX(180.0)
pos.SetRY(0.0)
pos.SetRZ(90.0)

# Assign to a motion
motion.SetPosition(pos)

# Alternative: set from a 4x4 matrix
pos2 = operator.CreatePosition()
pos2.SetFromMatrix(homogeneous_matrix)
pos2.SetFrameName("Base")
```

## See Also

- [[ULPythonMotion]]
- [[ULPythonUploadOperator]]
- [[Upload Callback Lifecycle]]
