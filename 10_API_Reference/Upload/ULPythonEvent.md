---
type: api-class
status: active
e2-package: cenpyupload
e2-area: uploader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - upload-api
  - event
  - technology
---

# ULPythonEvent

Represents a technology event (e.g., arc-on, arc-off, seam start) in an uploaded program. Created via [[ULPythonUploadOperator|operator.CreateEvent(name)]].

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Get the event name |
| `GetType()` | `str` | Get the event type |
| `SetName(name)` | — | Set the event name |
| `SetType(eventType)` | — | Set the event type identifier |
| `SetUuid(uuid)` | — | Set a UUID for the event |
| `SetInsertPosition(pos)` | — | Set where the event is inserted in the program |
| `SetAttribute(name, value)` | — | Set a generic attribute |
| `SetBoolAttribute(name, value)` | — | Set a boolean attribute |
| `SetIntegerAttribute(name, value)` | — | Set an integer attribute |
| `SetDoubleAttribute(name, value)` | — | Set a double/float attribute |
| `SetStringAttribute(name, value)` | — | Set a string attribute |
| `SetEnumAttribute(name, value)` | — | Set an enum attribute by value |
| `SetEnumAttributeByName(name, valueName)` | — | Set an enum attribute by name |

## Usage Example

```python
def PostUpload(operator):
    # Create an arc-on event
    arc_on = operator.CreateEvent("ArcOn_1")
    arc_on.SetType("ArcOn")
    arc_on.SetDoubleAttribute("Current", 180.0)
    arc_on.SetDoubleAttribute("Voltage", 22.5)
    arc_on.SetStringAttribute("WeldSchedule", "Schedule_01")
    arc_on.SetEnumAttributeByName("GasType", "ArgonCO2")
    arc_on.SetBoolAttribute("WeavingEnabled", True)

    # Create an arc-off event
    arc_off = operator.CreateEvent("ArcOff_1")
    arc_off.SetType("ArcOff")
    arc_off.SetDoubleAttribute("CraterFillTime", 0.5)
```

## See Also

- [[ULPythonUploadOperator]]
- [[ULPythonMotion]]
- [[Upload Callback Lifecycle]]
