---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - enums
  - constants
---

# Download Enums and Constants

Constants used throughout the Download API.

## Motion Type

| Constant | Value | Description |
|----------|-------|-------------|
| `MOTIONTYPE_PTP` | 0 | Point-to-point (joint interpolation) |
| `MOTIONTYPE_LIN` | 1 | Linear (Cartesian interpolation) |
| `MOTIONTYPE_CIR` | 2 | Circular (arc interpolation) |
| `MOTIONTYPE_SPLINE` | 3 | Spline motion |

## Insert Position

| Constant | Value | Description |
|----------|-------|-------------|
| `INSERTPOSITION_BEFORE` | 0 | Event fires before the motion |
| `INSERTPOSITION_AFTER` | 1 | Event fires after the motion |

## Accuracy Type

| Constant | Value | Description |
|----------|-------|-------------|
| `ACCURACY_OFF` | 0 | Exact stop |
| `ACCURACY_ON` | 1 | Approximation enabled |
| `ACCURACY_DISTANCE` | 2 | Distance-based blending |
| `ACCURACY_ORIENTATION` | 3 | Orientation-based |
| `ACCURACY_VELOCITY` | 4 | Velocity-based |

## See Also

- [[DULPythonMotion]] — uses Motion Type constants
- [[DULPythonEvent]] — uses Insert Position constants
- [[DULPythonAccuracyProfile]] — uses Accuracy Type constants
