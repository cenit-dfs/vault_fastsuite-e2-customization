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
  - event
  - technology
---

# Read-Ahead Pattern

Technology events attached to a motion via `eventsAfter` can change how that
motion should be output. The read-ahead pattern scans `eventsAfter` BEFORE
emitting the motion to make informed decisions.

## Problem

The download lifecycle fires events in this order for each motion:

```
eventsBefore → HandleMotion body → eventsAfter
```

But the motion instruction itself often depends on what comes AFTER it:
- Is this the last weld motion (has ArcOff)? → emit `LWE` instead of `LWC`
- Does an Accuracy(Off) follow? → suppress accuracy on this motion
- Is ArcOn coming? → the motion IS the weld start point

Without read-ahead, you'd need to output the motion first and then retroactively
fix it — which is fragile or impossible with buffered output.

## Solution

Scan `motion.GetEventsAfter()` at the START of `HandleMotion`, before any output:

```python
def HandleMotion(self, operator, motion):
    eventsAfter = motion.GetEventsAfter()
    readAheadIndices = set()

    # Read-ahead scan
    hasArcOn = False
    hasArcOff = False
    hasAccuracyOff = False

    for i, event in enumerate(eventsAfter):
        name = event.GetName()
        if name == 'ArcOnEvent':
            hasArcOn = True
            readAheadIndices.add(i)  # handle inline, not in post-loop
        elif name == 'ArcOffEvent':
            hasArcOff = True
            readAheadIndices.add(i)
        elif name == 'Accuracy':
            for attr in event.GetAttributes():
                if attr.GetName() == 'Criteria' and attr.GetValue() == 'Off':
                    hasAccuracyOff = True
                    break

    # Process eventsBefore (may set pending motion data)
    for event in motion.GetEventsBefore():
        self.HandleEvent(operator, event, motion)

    # Handle read-ahead ArcOn BEFORE the motion (it sets state for LWS)
    if hasArcOn:
        for i in readAheadIndices:
            if eventsAfter[i].GetName() == 'ArcOnEvent':
                self._handleArcOn(eventsAfter[i])

    # Now output motion with full knowledge
    if hasArcOff:
        self.arcOnLastProcessCurve = True  # triggers LWE output

    self._flushPendingMotionData()
    self._emitMotionLine(motion)

    # Fire remaining eventsAfter (skip already-handled ones)
    for i, event in enumerate(eventsAfter):
        if i not in readAheadIndices:
            self.HandleEvent(operator, event, motion)
```

## Key Insight: Event Timing

The `InsertAfter` events on a motion logically belong to the transition
FROM that motion TO the next. For welding:

- **ArcOnEvent** (InsertAfter) on motion N means: "start welding AT motion N"
  → motion N becomes `LWS` (Linear Weld Start)
- **ArcOffEvent** (InsertAfter) on motion N means: "end welding AT motion N"
  → motion N becomes `LWE` (Linear Weld End)

This is why read-ahead is essential — you need to know the motion's role before
outputting it.

## When State Resets Matter

After outputting the motion but before firing eventsAfter, reset state flags
that affect eventsAfter processing:

```python
# After emitting LWE:
self.arcOnActive = False  # IMPORTANT: eventsAfter (Speed, Accuracy) can now fire normally
```

This ensures that SPEED/ACCURACY events attached InsertAfter on the LWE motion
are NOT suppressed — they affect the retract, not the last weld motion.

## When to Use

- Arc welding downloaders (ArcOn/ArcOff determine motion type)
- Any downloader where motion format depends on subsequent events
- Touch sensing (collision events change motion meaning)

## Related

- [[Buffered Motion Pattern]] — flush happens after read-ahead decisions
- [[Weld State Machine Pattern]] — state transitions driven by read-ahead results
- [[Event Dispatch Pattern]] — normal event handling for non-read-ahead events
