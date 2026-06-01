---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - event
  - technology
---

# DULPythonEvent

Represents a technology event attached to a motion point. Events carry
technology-specific data: arc welding start/stop, signal set/reset, tool
changes, laser power commands, touch sensing, etc.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Event name (e.g., "ArcOn", "SetSignal") |
| `GetType()` | `str` | Event type identifier |
| `GetUuid()` | `str` | Unique event ID |
| `GetInsertPosition()` | `int` | `INSERTPOSITION_BEFORE` or `INSERTPOSITION_AFTER` |
| `GetAttribute(name)` | `object` | Generic attribute access |
| `GetBoolAttribute(name)` | `bool` | Boolean attribute |
| `GetIntegerAttribute(name)` | `int` | Integer attribute |
| `GetDoubleAttribute(name)` | `float` | Float attribute |
| `GetStringAttribute(name)` | `str` | String attribute |
| `GetEnumAttribute(name)` | `int` | Enum attribute (index) |
| `GetEnumAttributeName(name)` | `str` | Enum attribute (option name) |

## Insert Position

Events fire either **before** or **after** the motion they're attached to:

| Constant | Meaning |
|----------|---------|
| `INSERTPOSITION_BEFORE` | Emit event code before the motion instruction |
| `INSERTPOSITION_AFTER` | Emit event code after the motion instruction |

## Attribute Access

Events carry technology-specific attributes defined by the technology scripts.
Use the typed getters to retrieve values:

```python
def PreEvent(event):
    eventType = event.GetType()

    if eventType == "ArcOn":
        current = event.GetDoubleAttribute("WeldCurrent")
        voltage = event.GetDoubleAttribute("WeldVoltage")
        feedRate = event.GetDoubleAttribute("WireFeedRate")
        schedule = event.GetIntegerAttribute("ScheduleNumber")
    elif eventType == "SetSignal":
        signalNum = event.GetIntegerAttribute("SignalNumber")
        signalVal = event.GetBoolAttribute("SignalValue")
```

## Notes

- Event attribute names depend on the technology configuration. Check the
  reference tree dumps (`.txt` files from Simple_Python_Translator) to see
  which attributes are available for each event type.
- `GetAttribute()` returns a generic object — prefer typed getters for clarity.
- Multiple events can attach to the same motion. They are delivered in order.

## See Also

- [[DULPythonMotion]] — parent motion object
- [[Download Callback Lifecycle]] — when `PreEvent`/`PostEvent` fire
- [[30_Concepts/Event Model|Event Model]] — conceptual overview of events
