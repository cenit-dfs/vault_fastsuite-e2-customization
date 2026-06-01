---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - technology
---

# OlpCorePythonTechnology

Represents a technology definition (e.g. ArcWelding, LaserCutting) associated with a program. Provides access to work methods and technology-level events.

**Inherits from:** [[OlpCorePythonItem]]

## Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `GetTechnologyName()` | `str` | Returns the technology name (e.g. "ArcWelding") |
| `GetManufacturer()` | `str` | Returns the technology manufacturer/vendor |
| `GetVersion()` | `str` | Returns the technology version |
| `GetWorkMethods()` | `list[WorkMethod]` | Returns all available work methods |
| `GetEvents()` | `list[Event]` | Returns all technology-level events |
| `GetEventByName(name)` | `Event` | Returns a specific event by name |
| `GetEventByUuid(uuid)` | `Event` | Returns a specific event by UUID |

## Usage

```python
technology = program.GetTechnology()
tech_name = technology.GetTechnologyName()

# Enumerate work methods
for wm in technology.GetWorkMethods():
    print(wm.GetName())

# Look up a specific event
gas_event = technology.GetEventByName("GasOn")
if gas_event:
    print(gas_event.GetEventUuid())
```

## See Also

- [[OlpCorePythonProgram]] — Program referencing this technology
- [[OlpCorePythonEvent]] — Events defined by the technology
- [[OlpCorePythonOperation]] — Operations using work methods
- [[OlpCore Attributes]] — Technology attributes
