---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - accuracy
  - blending
---

# DULPythonAccuracyProfile

Represents accuracy/approximation/blending settings for a motion point.
Controls how closely the robot follows the programmed path at this point.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetType()` | `int` | Accuracy type — see [[#Accuracy Types]] |
| `GetValue()` | `float` | Accuracy value (units depend on type) |
| `IsActive()` | `bool` | Whether accuracy is configured |
| `GetDistance()` | `float` | Distance accuracy in **mm** |
| `GetOrientation()` | `float` | Orientation accuracy in **degrees** |
| `GetVelocity()` | `float` | Velocity accuracy in **%** |

## Accuracy Types

| Constant | Meaning |
|----------|---------|
| `ACCURACY_OFF` | No approximation (exact stop) |
| `ACCURACY_ON` | Approximation enabled |
| `ACCURACY_DISTANCE` | Distance-based blending |
| `ACCURACY_ORIENTATION` | Orientation-based |
| `ACCURACY_VELOCITY` | Velocity-based |

## Usage

```python
def PreMotion(motion):
    acc = motion.GetAccuracyProfile()

    if not acc.IsActive():
        # Exact stop — robot decelerates to zero at this point
        approx = "FINE"
    else:
        # Blending — robot smooths through this point
        dist = acc.GetDistance()
        approx = f"C_DIS={dist:.1f}"
```

## Notes

- "Accuracy" in E2 terminology = "approximation" or "blending" in robot
  language terminology (KUKA `$APO`, FANUC `CNT`, ABB `zone`).
- When `IsActive()` is True, the robot blends through the point without
  stopping. When False, it's an exact stop (fine point).

## See Also

- [[DULPythonMotion]] — parent object (call `motion.GetAccuracyProfile()`)
- [[DULPythonSpeedProfile]] — velocity settings (complementary)
