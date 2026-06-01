---
type: pattern
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - pattern
  - position
  - units
---

# Position Output Pattern

How to read and convert position data from E2 for robot controller output.

## Unit Conversions

E2 provides positions in **meters** and **degrees**. Most robot controllers
expect **millimeters**:

```python
xyz = position.GetXYZ()             # returns tuple — in METERS
angles = position.GetOrientation()  # returns tuple — in DEGREES (Rx, Ry, Rz)

# Convert to mm for output:
x_mm = xyz[0] * 1000
y_mm = xyz[1] * 1000
z_mm = xyz[2] * 1000
```

## Cartesian vs Joint Target

```python
targetType = position.GetTargetType()

if targetType == TargetType.Joint:
    # Joint-space target — use joint values
    joints = position.GetMainJointValues()
    for joint_obj, value in joints:
        # value: degrees (revolute) or meters (prismatic)
        pass
else:
    # Cartesian target — use XYZ + orientation
    xyz = position.GetXYZ()          # meters
    angles = position.GetOrientation()  # degrees
    config = position.GetConfig()    # e.g., "S2"
    turn = position.GetTurn()        # e.g., "T10"
```

## External Axes

External axes (positioner, track, turntable) are separate from main robot axes:

```python
externals = position.GetExternalJointValues()
externals.sort(key=lambda ext: ext[0].GetJointIndex())

for joint_obj, value in externals:
    if joint_obj.GetJointType() == JointKinematicType.Prismatic:
        value_mm = value * 1000  # meters → mm
    else:
        value_deg = value  # already degrees
```

## Configuration / Turns

Robot configuration strings encode arm posture (elbow up/down, wrist flip, etc.):

```python
config = position.GetConfig()  # e.g., "S2" (KUKA), "cf1" (FANUC)
turn = position.GetTurn()      # e.g., "T10" (KUKA turn bits)
```

These are vendor-specific — pass through to the output as-is.

## Common Mistakes

- **Forgetting m → mm conversion** — most common bug. Always multiply XYZ by 1000.
- **External prismatic axes** — also in meters, also need ×1000.
- **Orientation order** — E2 returns (Rx, Ry, Rz) but some controllers expect
  (Rz, Ry, Rx) or Euler angles in different convention. Check the vendor manual.

## See Also

- [[10_API_Reference/Download/DULPythonPosition|DULPythonPosition]]
- [[10_API_Reference/Download/DULPythonJoint|DULPythonJoint]]
- [[30_Concepts/Unit System|Unit System]]
