---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - motion
  - trajectory
---

# DULPythonMotion

Represents a single robot motion point in the download stream. Contains the
target position, joint configuration, speed profile, accuracy profile, and
attached events.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Motion name |
| `GetMotionType()` | `int` | Motion type — see [[#Motion Types]] |
| `GetPosition()` | [[DULPythonPosition]] | Target Cartesian position |
| `GetJoint()` | [[DULPythonJoint]] | Joint configuration at target |
| `GetSpeed()` | [[DULPythonSpeedProfile]] | Speed/velocity profile |
| `GetAccuracyProfile()` | [[DULPythonAccuracyProfile]] | Approximation/accuracy |
| `IsReachable()` | `bool` | Whether the robot can reach this point |
| `HasCollision()` | `bool` | Whether collision was detected |
| `GetPathLength()` | `float` | Path length from previous point in **mm** |
| `GetCycleTime()` | `float` | Estimated cycle time in **seconds** |
| `GetAttribute(name)` | `object` | Custom motion attribute by name |

## Motion Types

| Constant | Value | Description |
|----------|-------|-------------|
| `MOTIONTYPE_PTP` | 0 | Point-to-point (joint interpolation) |
| `MOTIONTYPE_LIN` | 1 | Linear (Cartesian interpolation) |
| `MOTIONTYPE_CIR` | 2 | Circular (arc interpolation) |
| `MOTIONTYPE_SPLINE` | 3 | Spline motion |

## Usage

```python
def PreMotion(motion):
    motionType = motion.GetMotionType()
    pos = motion.GetPosition()
    speed = motion.GetSpeed()

    if motionType == MOTIONTYPE_PTP:
        # Joint move — may prefer joint values
        joint = motion.GetJoint()
        if joint.IsValid():
            values = joint.GetJointValues()
    elif motionType == MOTIONTYPE_LIN:
        # Linear move — use Cartesian position
        x, y, z = pos.GetX(), pos.GetY(), pos.GetZ()
    elif motionType == MOTIONTYPE_CIR:
        # Circular — this is the via-point or endpoint
        pass
```

## Notes

- Circular motions come in pairs: the first is the via-point, the second is the
  endpoint. Handle them together in your downloader state.
- `IsReachable()` and `HasCollision()` reflect the E2 simulation result.
  Most downloaders skip unreachable points or emit a warning comment.

## See Also

- [[DULPythonPosition]] — Cartesian coordinates
- [[DULPythonJoint]] — joint values
- [[DULPythonSpeedProfile]] — velocity data
- [[DULPythonAccuracyProfile]] — approximation/blending
- [[Download Callback Lifecycle]] — when `PreMotion` is called
