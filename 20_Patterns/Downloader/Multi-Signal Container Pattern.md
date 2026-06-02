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
  - signal
  - event
---

# Multi-Signal Container Pattern

`SetResourcePort` and `WaitForResourcePort` events are **containers** that hold
one or more signals. Their attributes repeat in groups. This pattern shows how
to correctly parse them.

## Problem

A single `SetResourcePort` event can contain multiple signals:

```
Attributes in order:
  ResourceName = "Rampf_MSC+Halter_neu"
  ExactStop = False
  TriggerRelatedToStart = True
  TriggerDistance = 0.0
  TriggerTime = 0.0
  SignalName = "Frei"              ← Signal 1 starts
  SignalAddress = "$OUT3113"
  SignalNumber = 3113
  SignalValue = True
  SignalName = "GunNumberForState1" ← Signal 2 starts
  SignalAddress = "$OUT2857"
  SignalNumber = 2857
  SignalValue = 2                   ← Note: int, not bool!
```

A naive scalar loop only captures the LAST signal's values.

## Solution: Collect by SignalName Boundary

```python
def _handleSetResourcePort(self, event):
    names = []
    addresses = []
    values = []

    for attr in event.GetAttributes():
        n = attr.GetName()
        if n == 'SignalName':
            names.append(attr.GetValue())
        elif n == 'SignalAddress':
            addresses.append(attr.GetValue())
        elif n == 'SignalValue':
            values.append(attr.GetValue())

    # Output one command per signal
    for i in range(len(names)):
        sign = '' if str(values[i]) == 'True' else '-'
        onOff = 'On' if str(values[i]) == 'True' else 'Off'
        self.Source.append('SIGNAL ' + sign + addresses[i] + ' ; ' + names[i] + '=' + onOff)
```

## Why Parallel Lists Work

E2 guarantees that within a `SetResourcePort` or `WaitForResourcePort` event,
the attributes always appear in repeating groups:

```
[header attrs...]
SignalName → SignalAddress → SignalNumber → SignalValue  ← group 1
SignalName → SignalAddress → SignalNumber → SignalValue  ← group 2
...
```

By appending to parallel lists whenever we see `SignalName`, `SignalAddress`, or
`SignalValue`, the indices naturally align: `names[i]` corresponds to
`addresses[i]` and `values[i]`.

## Mixed Value Types

Signal values can be different types within the same event:
- `bool` — digital signal (True/False)
- `int` — register value
- `float` — analog value

Always use `str(values[i])` when comparing, and handle vendor-specific output
format per type if needed.

## Contrast with LogicPort (Single Signal)

`LogicPort` events always contain exactly ONE signal. A simple scalar loop is
sufficient:

```python
def _handleLogicPort(self, event):
    eventType = ''
    sigName = ''
    sigAddress = ''
    sigValue = True

    for attr in event.GetAttributes():
        name = attr.GetName()
        if name == 'EventType':
            eventType = attr.GetValue()
        elif name == 'SignalName':
            sigName = attr.GetValue()
        elif name == 'SignalAddress':
            sigAddress = attr.GetValue()
        elif name == 'SignalValue':
            sigValue = attr.GetValue()
    # Single signal — use scalar values directly
```

## WaitForResourcePort Variant

Same multi-signal structure, different output syntax:

```python
def _handleWaitResourcePort(self, event):
    # Same collection logic as SetResourcePort
    for i in range(len(names)):
        sign = '' if str(values[i]) == 'True' else '-'
        onOff = 'On' if str(values[i]) == 'True' else 'Off'
        # Different output format:
        self.Source.append('WAIT SIG(' + sign + addresses[i] + ') ; ' + names[i] + '=' + onOff)
```

## Related

- [[Event Dispatch Pattern]] — dispatching signal events by EventType
- [[BoolAttribute Trap]] — `str()` needed for bool signal values
- [[Common Downloader Mistakes#1. Multi-Signal Container Not Handled]]
