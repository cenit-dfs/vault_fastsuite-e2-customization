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
  - motion
  - architecture
---

# Buffered Motion Data Pattern

Motion settings (SPEED, ACCURACY, CP, ACCEL) should be **buffered** from events
and **flushed** once before each motion instruction. This pattern decouples event
arrival order from output order and enables conditional suppression.

## Problem

Events fire in arbitrary order (`Speed` before `Accuracy`, or vice versa), but
the controller expects a fixed output order. Additionally, certain states
(welding, touch sensing) require suppressing some settings entirely.

## Solution

```python
def __init__(self):
    # Pending values (set by event handlers)
    self.pendingSpeed = None
    self.pendingAccel = None
    self.pendingAccuracy = None
    self.pendingCp = None
    # Last-output tracking (for deduplication)
    self.lastSpeed = None
    self.lastAccel = None
    self.lastAccuracy = None
    self.lastCp = None
```

### Event Handlers Set Pending Values

```python
def _handleSpeed(self, event):
    pathtype = ''
    speed = 0.0
    for attr in event.GetAttributes():
        if attr.GetName() == 'Value':
            speed = float(attr.GetValue())
        elif attr.GetName() == 'PathType':
            pathtype = attr.GetValue()
    if pathtype == 'Contour':
        self.pendingSpeed = (str(speed * 1000), 'MM/S')
    else:
        self.pendingSpeed = (str(speed), None)
```

### Flush Before Each Motion

```python
def _flushPendingMotionData(self):
    # Fixed output order: SPEED → ACCEL → CP → ACCURACY
    if self.pendingSpeed is not None:
        if not self.suppressSpeed:  # e.g., during welding
            if self.pendingSpeed != self.lastSpeed:
                val, suffix = self.pendingSpeed
                line = 'SPEED ' + val
                if suffix:
                    line += ' ' + suffix
                line += ' ALWAYS'
                self.Source.append(line)
                self.lastSpeed = self.pendingSpeed
        self.pendingSpeed = None

    if self.pendingAccuracy is not None:
        if not self.suppressAccuracy:
            if self.pendingAccuracy != self.lastAccuracy:
                self.Source.append('ACCURACY ' + self.pendingAccuracy + ' ALWAYS')
                self.lastAccuracy = self.pendingAccuracy
        self.pendingAccuracy = None
    # ... same for ACCEL, CP
```

### Call in HandleMotion

```python
def HandleMotion(self, operator, motion):
    # Process eventsBefore first (they set pending values)
    for event in motion.GetEventsBefore():
        self.HandleEvent(operator, event, motion)
    # Flush all pending settings as a block, before the motion line
    self._flushPendingMotionData()
    # Emit motion instruction
    self.Source.append('LMOVE ' + posTarget)
```

## Benefits

1. **Deduplication** — Only emits when value actually changes (`!= last*`)
2. **Fixed output order** — Regardless of which event fires first
3. **Conditional suppression** — Welding, touch sensing, or explicit mode can
   suppress any setting by checking a flag in the flush method
4. **Clean separation** — Event handlers are simple (just set pending), flush
   logic is centralized

## When to Use

- Any downloader where motion settings must appear as separate lines before
  the motion instruction (Kawasaki, KUKA, FANUC, etc.)
- Especially important when technology events (weld, touch) need to suppress
  settings during certain phases

## Related

- [[Weld State Machine Pattern]] — drives the suppression flags
- [[Event Dispatch Pattern]] — event handlers that feed pending values
- [[Read-Ahead Pattern]] — complements buffered motion by pre-scanning eventsAfter
