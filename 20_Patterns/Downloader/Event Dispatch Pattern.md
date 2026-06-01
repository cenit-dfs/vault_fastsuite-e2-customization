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
  - event
  - signal
---

# Event Dispatch Pattern

Events are dispatched in two layers: first by `GetEventType()` enum, then by
event-specific attribute values.

## Level 1 — Dispatch by EventType

Always dispatch on `event.GetEventType()`, **not** `event.GetName()`:

```python
def HandleEvent(self, operator, event, motion):
    eventType = event.GetEventType()

    if eventType == EventType.Speed:
        self.SetSpeed(operator, event)
    elif eventType == EventType.Accuracy:
        self.SetAccuracy(operator, event)
    elif eventType == EventType.Acceleration:
        self.SetAcceleration(operator, event)
    elif eventType == EventType.Dwell:
        self.DwellEvent(operator, event)
    elif eventType == EventType.LogicPort:
        self.LogicPortEvent(operator, event)
    elif eventType == EventType.SetResourcePort:
        self.ResourcePortEvent(operator, event)
    elif eventType == EventType.WaitForResourcePort:
        self.WaitResourcePortEvent(operator, event)
```

> **Warning:** `event.GetName()` returns UI text like "BuiltInEvent" which is
> NOT unique. `GetEventType()` is the reliable dispatch key.

## Level 2 — Signal Events

### LogicPort (Single Signal)

LogicPort events contain exactly one signal. Read via generic attribute loop:

```python
def LogicPortEvent(self, operator, event):
    eventSubType = ""
    signalName = ""
    signalNumber = ""
    signalValue = ""

    for attr in event.GetAttributes():
        name = attr.GetName()
        if name == "EventType":
            eventSubType = attr.GetValue()
        elif name == "SignalName":
            signalName = attr.GetValue()
        elif name == "SignalNumber":
            signalNumber = str(attr.GetValue())
        elif name == "SignalValue":
            signalValue = str(attr.GetValue())
```

### SetResourcePort / WaitForResourcePort (Multi-Signal Container)

These events contain **multiple signals** in repeating attribute groups.
Each new `SignalName` starts a new signal:

```python
def ResourcePortEvent(self, operator, event):
    signals = []
    currentSignal = {}

    for attr in event.GetAttributes():
        name = attr.GetName()
        if name == "SignalName":
            if currentSignal.get("SignalName"):
                signals.append(currentSignal)
            currentSignal = {
                "SignalName": attr.GetValue(),
                "SignalAddress": "",
                "SignalNumber": "",
                "SignalValue": ""
            }
        elif name == "SignalAddress":
            currentSignal["SignalAddress"] = attr.GetValue()
        elif name == "SignalNumber":
            currentSignal["SignalNumber"] = str(attr.GetValue())
        elif name == "SignalValue":
            currentSignal["SignalValue"] = str(attr.GetValue())

    if currentSignal.get("SignalName"):
        signals.append(currentSignal)

    # Output one command per signal
    for sig in signals:
        self.OutputSignal(sig)
```

## Common Event Attributes

| Event | Key Attributes |
|-------|---------------|
| Speed | `Value` (float), `PathType` ("Contour"/"PointToPoint") |
| Accuracy | `Value` (float), `PathType`, `Criteria` ("On"/"Off"/"Distance") |
| Dwell | `Value` (float — seconds) |
| TextEvent | `Text` (str), `IsComment` (bool) |
| LogicPort | `EventType` (subtype), `SignalName`, `SignalNumber`, `SignalValue` |
| SetResourcePort | `ResourceName`, repeating: `SignalName/Address/Number/Value` |

## See Also

- [[10_API_Reference/Download/DULPythonEvent|DULPythonEvent]]
- [[10_API_Reference/Download/Download Enums and Constants|Download Enums]]
- [[Callback Lifecycle Pattern]]
