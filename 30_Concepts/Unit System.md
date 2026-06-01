---
type: concept
status: active
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - concept
  - units
  - conversion
---

# Unit System

E2 uses SI units internally. Robot controllers use various units. This note
documents the conversions needed.

## E2 Internal Units

| Quantity | E2 Unit | Common Robot Unit | Conversion |
|----------|---------|-------------------|------------|
| Position (X, Y, Z) | **meters** | millimeters | × 1000 |
| Orientation (Rx, Ry, Rz) | **degrees** | degrees | none |
| Joint angles (revolute) | **degrees** | degrees | none |
| Joint values (prismatic) | **meters** | millimeters | × 1000 |
| Speed (linear) | mm/s | mm/s | none (already mm/s in API) |
| Speed (joint) | % | % | none |
| Time | seconds | seconds | none |

## Critical Rule

> **Positions from `GetXYZ()` are in METERS. Always multiply by 1000 for mm.**

This is the single most common bug in new downloaders.

## Where Each Unit Appears

```python
# Meters — needs conversion
position.GetXYZ()           # → (x, y, z) in meters
position.GetExternalJointValues()  # prismatic: meters

# Degrees — no conversion needed
position.GetOrientation()   # → (rx, ry, rz) in degrees
position.GetMainJointValues()  # revolute: degrees

# Already in robot units
speed.GetLinearSpeed()      # → mm/s
speed.GetValue()            # → % (for PTP)
```

## Euler Angle Conventions

E2 returns orientation as (Rx, Ry, Rz) — but robots use different conventions:

| Robot | Convention | E2 Mapping |
|-------|-----------|------------|
| KUKA | A, B, C (Z-Y'-X'') | Rz → A, Ry → B, Rx → C |
| ABB | q1-q4 (quaternion) | Convert from Euler |
| FANUC | W, P, R | Direct mapping varies |
| Kawasaki | O, A, T | Rz → O, Ry → A, Rx → T |

Check the vendor controller manual for the exact Euler decomposition.

## See Also

- [[20_Patterns/Downloader/Position Output Pattern|Position Output Pattern]]
- [[10_API_Reference/Download/DULPythonPosition|DULPythonPosition]]
