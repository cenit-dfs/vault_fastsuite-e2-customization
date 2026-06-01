---
type: api-callback
status: active
e2-package: cenpyolpcore
e2-area: technology
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - technology-api
  - callback
---

# Series Callbacks

The `IOlpSeries` interface defines callbacks scoped to a specific controller series. These follow the same patterns as [[Technology Callback Lifecycle|technology-level callbacks]] but apply only when a particular robot controller series is active.

---

## PostSeriesInitAttributes

Creates series-level attributes. Same pattern as [[Technology Callback Lifecycle#PostTechInitAttributes|PostTechInitAttributes]].

```python
def PostSeriesInitAttributes(Operator):
    attribCreator = Operator.GetAttribCreator()
    attribCreator.AddInteger("MaxPayload", 6)
    attribCreator.AddEnum("ControllerVersion", ["V1", "V2", "V3"], 2)
    return True
```

**Operator type:** `CENPyOlpSeries_InitAtributeOperator`

---

## PostSeriesInitEvents

Registers series-specific events.

```python
def PostSeriesInitEvents(Operator):
    Operator.RegisterPyTechnologyEvent("SeriesSpecificEvent.py")
    return True
```

**Operator type:** `CENPyOlpSeries_InitEventOperator`

See also: [[Event Callbacks]]

---

## PostSeriesOnAttribChanged

Called when series attributes change.

```python
def PostSeriesOnAttribChanged(Operator):
    changedAttr = Operator.GetChangedAttributeName()
    if changedAttr == "ControllerVersion":
        version = Operator.GetAttribGetter().GetEnum("ControllerVersion")
        # Adjust series-specific behavior
    return True
```

**Operator type:** `CENPyOlpSeries_OnAttribChangedOperator`

---

## See Also

- [[Technology Callback Lifecycle]]
- [[Workmethod Callback Lifecycle]]
- [[Event Callbacks]]
