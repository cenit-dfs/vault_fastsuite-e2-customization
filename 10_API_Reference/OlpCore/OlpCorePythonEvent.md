---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - event
---

# OlpCorePythonEvent

Represents an event in the OLP Core model. Events are technology-defined actions attached to operations at specific insert positions relative to teach points.

> [!important]
> This is the **OLP Core** event class used in technology scripts. For the **Download API** event class used in downloaders, see [[10_API_Reference/Download/DULPythonEvent|DULPythonEvent]].

**Inherits from:** [[OlpCorePythonItem]]

## Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `GetEventName()` | `str` | Returns the event name |
| `GetEventType()` | `str` | Returns the event type identifier |
| `GetEventUuid()` | `str` | Returns the unique event UUID |
| `GetInsertPosition()` | `InsertPosition` | Returns where the event is inserted relative to a TP |
| `SetInsertPosition(pos)` | — | Sets the insert position |
| `GetReferenceTpElement()` | `TpElement` | Returns the teach point this event is attached to |
| `GetOperation()` | `OlpCorePythonOperation` | Returns the parent operation |

## Usage

```python
for event in operation.GetEvents():
    name = event.GetEventName()
    event_type = event.GetEventType()
    insert_pos = event.GetInsertPosition()
    ref_tp = event.GetReferenceTpElement()
    
    print(f"Event '{name}' ({event_type}) at {insert_pos}")
```

## See Also

- [[OlpCorePythonOperation]] — Parent operation containing events
- [[OlpCorePythonTechnology]] — Technology defining available events
- [[OlpCore Enumerations]] — `InsertPosition` enum
- [[10_API_Reference/Download/DULPythonEvent|DULPythonEvent]] — Download API event (different class)
