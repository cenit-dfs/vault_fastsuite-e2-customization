---
type: concept
status: active
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - concept
  - events
  - technology
---

# Event Model

Events are technology commands attached to motion points. They represent actions
the robot performs at or near a position: welding start/stop, signal output,
speed changes, dwell times, etc.

## Event Attachment

Every event is attached to a specific motion point and fires either **before**
or **after** that motion is executed:

```text
Motion P001
├── Events Before: [ArcOn, GasPreflow]
└── Events After:  [SpeedChange]
```

## Event Types

Events come from different sources:

| Source | Examples | How They Appear |
|--------|----------|-----------------|
| Technology | ArcOn, ArcOff, GasEvent | Defined by technology scripts |
| Built-in | Speed, Accuracy, Acceleration, Dwell | Always available |
| Signal | SetResourcePort, LogicPort, WaitForResourcePort | I/O commands |
| Text | TextEvent | User comments in output |

## Event Data

Events carry attributes defined by their technology. Access via:

```python
# Generic loop (safest)
for attr in event.GetAttributes():
    name = attr.GetName()
    value = attr.GetValue()

# Typed access (when you know the type)
voltage = event.GetDoubleAttribute("WeldVoltage")
```

## Multi-Signal Events

`SetResourcePort` and `WaitForResourcePort` are **container events** that hold
multiple signals. The attributes repeat in groups — see
[[20_Patterns/Downloader/Event Dispatch Pattern|Event Dispatch Pattern]].

## Insert Position

| Value | Meaning |
|-------|---------|
| `InsertBefore` / `INSERTPOSITION_BEFORE` | Emit code before the motion instruction |
| `InsertAfter` / `INSERTPOSITION_AFTER` | Emit code after the motion instruction |

## See Also

- [[OLP Tree]] — where events sit in the hierarchy
- [[10_API_Reference/Download/DULPythonEvent|DULPythonEvent]] — Download API event object
- [[10_API_Reference/OlpCore/OlpCorePythonEvent|OlpCorePythonEvent]] — OLP Core event object
- [[20_Patterns/Downloader/Event Dispatch Pattern|Event Dispatch Pattern]]
