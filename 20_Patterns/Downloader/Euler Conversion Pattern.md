---
type: pattern
status: active
e2-package: cenpymath
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-02
tags:
  - download-api
  - pattern
  - position
  - euler
  - orientation
---

# Euler Conversion Pattern

E2 stores orientations in `Euler_XYZs` (static/fixed XYZ convention). Each
robot vendor uses a different Euler convention. This pattern shows how to convert
using the `cenpymath` package.

## E2 Internal Convention

All orientation values from `position.GetOrientation()` are in **degrees** using
the **Euler_XYZs** (static XYZ, also called "fixed angles") convention:

```python
rx, ry, rz = position.GetOrientation()  # degrees, Euler_XYZs
```

## Conversion Using cenpymath

```python
from cenpymath.Euler.Converter import Converter
from cenpymath.Euler.Notations import Notations

class MyDownloader(Downloader):
    def __init__(self):
        super().__init__()
        self.EulerConverter = Converter()

    def _convertOrientation(self, rx, ry, rz):
        # Convert from E2 internal (XYZs) to vendor convention
        a, b, c = self.EulerConverter.ConvertEuler(
            rx, ry, rz,
            Notations.Euler_ZYZr,  # TARGET convention
            Notations.Euler_XYZs   # SOURCE convention (E2)
        )
        return a, b, c
```

> **Note:** The parameter order is `(rx, ry, rz, TARGET, SOURCE)` — target
> convention comes BEFORE source.

## Vendor Conventions

| Vendor | Convention | Notation | Angle Names |
|--------|-----------|----------|-------------|
| E2 internal | Static XYZ | `Euler_XYZs` | Rx, Ry, Rz |
| KUKA | Rotating ZYZ | `Euler_ZYZr` | A, B, C |
| Kawasaki | Rotating ZYZ | `Euler_ZYZr` | O, A, T |
| FANUC | Rotating XYZ | `Euler_XYZr` | W, P, R |
| ABB | Quaternion | *(separate conversion)* | q1, q2, q3, q4 |
| Motoman | Rotating ZYZ | `Euler_ZYZr` | Rx, Ry, Rz |
| Stäubli | Rotating XYZ | `Euler_XYZr` | Rx, Ry, Rz |

## Available Notations in cenpymath

From `cenpymath.Euler.Notations`:

```
Euler_XYZs  — Static (fixed) XYZ
Euler_XYZr  — Rotating (intrinsic) XYZ
Euler_ZYZs  — Static ZYZ
Euler_ZYZr  — Rotating (intrinsic) ZYZ
Euler_ZYXs  — Static ZYX
Euler_ZYXr  — Rotating (intrinsic) ZYX
... (other combinations available)
```

## Common Mistakes

### 1. Swapping Source and Target

The API puts target first: `ConvertEuler(rx, ry, rz, TARGET, SOURCE)`.
Swapping them gives wrong results without any error.

### 2. Forgetting Degrees vs Radians

E2 `GetOrientation()` returns **degrees**. The converter also works in degrees.
No conversion needed. But if you manually compute Euler angles, ensure they're
in degrees before calling the converter.

### 3. Quaternion Conversion (ABB)

ABB uses quaternions, not Euler angles. The `cenpymath` Euler converter cannot
produce quaternions directly. Use a separate quaternion conversion (e.g., via
rotation matrix intermediate).

## Full Example (Kawasaki)

```python
def _buildPosition(self, position):
    xyz = position.GetXYZ()  # meters
    angles = position.GetOrientation()  # degrees, Euler_XYZs

    # Convert orientation to Kawasaki OAT (Euler_ZYZr)
    o, a, t = self.EulerConverter.ConvertEuler(
        angles[0], angles[1], angles[2],
        Notations.Euler_ZYZr,
        Notations.Euler_XYZs
    )

    # Convert position to mm
    x_mm = xyz[0] * 1000
    y_mm = xyz[1] * 1000
    z_mm = xyz[2] * 1000

    return x_mm, y_mm, z_mm, o, a, t
```

## Related

- [[Position Output Pattern]] — unit conversions and target types
- [[10_API_Reference/OlpCore/OlpCorePythonPosition]] — position API
