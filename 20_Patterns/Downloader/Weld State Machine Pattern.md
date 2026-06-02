---
type: pattern
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-02
tags:
  - download-api
  - pattern
  - arc-welding
  - state-machine
  - technology
---

# Weld State Machine Pattern

Arc welding downloaders need a state machine to track the current weld phase.
This pattern defines the states, transitions, and suppression rules.

## States

```
┌─────────────┐    ArcOnEvent     ┌──────────────┐
│  IDLE       │ ─────────────────→ │  WELD ACTIVE │
│ inWeldSection=F │                │ arcOnActive=T │
│ arcOnActive=F   │                │ inWeldSection=T│
└─────────────┘    ArcOffEvent     └──────────────┘
       ↑          (after LWE)              │
       └───────────────────────────────────┘
```

## State Variables

```python
def __init__(self):
    self.arcOnActive = False         # True between LWS and LWE output
    self.arcOnIsFirstMotion = False  # First motion after ArcOn → becomes LWS
    self.arcOnLastProcessCurve = False  # Last process motion → becomes LWE
    self.inWeldSection = False       # Broader flag (ArcOn → ArcOff)
    self.hasCrater = False           # Crater fill enabled
    self.craterCondNumber = 0        # Crater condition number for LWE
    self.hasSensing = False          # Seam sensing active → SSENSING OFF at LWE
    self.hasRtpm = False             # RTPM active → RTPM OFF at LWE
    self.weldConditionNumber = 1     # Current weld condition for LWC/LWE
```

## Transitions

### ArcOn Fires (InsertAfter on approach motion)

```python
def _handleArcOn(self, event):
    # ... extract weld parameters, emit SETCOND lines ...
    self.inWeldSection = True
    self.arcOnActive = True
    self.arcOnIsFirstMotion = True
```

### HandleMotion — First Weld Motion

```python
if self.arcOnIsFirstMotion:
    self.Source.append('LWS ' + posTarget)
    self.arcOnIsFirstMotion = False
```

### ArcOff Fires (InsertAfter on last process motion)

Uses read-ahead pattern — detected BEFORE outputting the motion:

```python
# In HandleMotion read-ahead:
if hasArcOff:
    self.arcOnLastProcessCurve = True
```

### HandleMotion — Last Weld Motion (LWE)

```python
if self.arcOnLastProcessCurve:
    lwe = 'LWE ' + posTarget + ' ,' + str(self.weldConditionNumber)
    if self.hasCrater:
        lwe += ',' + str(self.craterCondNumber)
    self.Source.append(lwe)
    self.arcOnLastProcessCurve = False
    self.arcOnActive = False  # ← CRITICAL: before eventsAfter fire
    # Cleanup
    if self.hasSensing:
        self.Source.append('SSENSING OFF')
    if self.hasRtpm:
        self.Source.append('RTPM OFF')
```

### ArcOff Handler (eventsAfter)

```python
def _handleArcOff(self, event, motion):
    self.inWeldSection = False
    # If arcOnActive is still True here, something went wrong
    if not self.arcOnActive:
        return
    self.arcOnLastProcessCurve = True  # fallback
```

## Suppression Rules

The state machine drives suppression in `_flushPendingMotionData()`:

| Setting | Suppressed When | Reason |
|---------|----------------|--------|
| SPEED | `arcOnActive == True` | Welder controls speed |
| ACCURACY | `arcOnActive == True` | Continuous path assumed during weld |
| ACCEL | `hasTouchSensing == True` | Touch sensing has own speed control |
| CP | `hasTouchSensing or explicitMode` | Not applicable in these modes |

## Critical Timing: arcOnActive Reset

`arcOnActive` must be set to `False` AFTER outputting LWE but BEFORE
`eventsAfter` fire on that motion. This ensures:

- Speed/Accuracy events with `InsertAfter` on the LWE motion are NOT suppressed
- They affect the retract motion, not the last weld motion
- The flush on the NEXT motion will emit them normally

## Mid-Weld Parameter Changes

`ArcWeldConditionEvent` fires mid-weld to change parameters without
stopping/restarting the arc:

```python
def _handleArcWeldCondition(self, event):
    # Update weld parameters (SETCONDW1, etc.)
    # Update self.weldConditionNumber
    # Does NOT change arcOnActive state
```

## When to Use

- Any arc welding downloader (Kawasaki, KUKA, ABB, FANUC, Motoman)
- Laser welding with similar start/end semantics
- Any technology with "process on" / "process off" bracketing

## Related

- [[Read-Ahead Pattern]] — detects ArcOff before motion output
- [[Buffered Motion Pattern]] — suppression flags checked during flush
- [[Event Dispatch Pattern]] — ArcOn/ArcOff arrive as technology events
