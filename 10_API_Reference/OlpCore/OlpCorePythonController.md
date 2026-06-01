---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - controller
---

# OlpCorePythonController

Represents a robot controller in the OLP project tree. Provides access to controller metadata (manufacturer, series, version) and child objects such as robots, external axes, and programs.

**Inherits from:** [[OlpCorePythonItem]]

## Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `GetControllerType()` | `ControllerTypes` | Returns the controller type enumeration value |
| `GetManufacturer()` | `str` | Returns the manufacturer name (e.g. "KUKA", "ABB") |
| `GetSeries()` | `str` | Returns the controller series (e.g. "KRC5", "IRC5") |
| `GetVersion()` | `str` | Returns the controller software version |
| `GetRobots()` | `list[Robot]` | Returns all robots attached to this controller |
| `GetExternalAxes()` | `list[ExternalAxis]` | Returns all external axes configured for this controller |
| `GetPrograms()` | `list[Program]` | Returns all programs under this controller |

## Usage

```python
controller = olp_environment.GetController()
manufacturer = controller.GetManufacturer()
series = controller.GetSeries()

for program in controller.GetPrograms():
    print(program.GetName())
```

## See Also

- [[OlpCorePythonItem]] — Base class
- [[OlpCorePythonProgram]] — Programs returned by `GetPrograms()`
- [[OlpCore Enumerations]] — `ControllerTypes` enum
- [[OlpCore Attributes]] — Attribute access on controller items
