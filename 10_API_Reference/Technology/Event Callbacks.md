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

# Event Callbacks

The `IOlpEvent` interface defines callbacks for custom technology events. Each event is a separate Python file registered via [[Technology Callback Lifecycle#PostTechInitEvents|PostTechInitEvents]] or [[Workmethod Callback Lifecycle#PostWmInitEvents|PostWmInitEvents]].

## Callback Order

| # | Callback | Purpose |
|---|----------|---------|
| 1 | [[#PostInitAttributes]] | Initialize event attributes |
| 2 | [[#PostProcessAttributes]] | Process after creation |
| 3 | [[#PostProcessAttributesUpload]] | Process uploaded event |
| 4 | [[#PostCompute]] | Compute event behavior during program compute |
| 5 | [[#PostOnAttribChanged]] | Attribute change handler |

---

## PostInitAttributes

Initializes event-specific attributes when the event is first created.

```python
def PostInitAttributes(Operator):
    attribCreator = Operator.GetAttribCreator()
    attribCreator.AddInteger("GasChannel", 1)
    attribCreator.AddBool("GasOn", True)
    attribCreator.AddDouble("FlowRate", 15.0)
    return True
```

**Operator type:** `CENPyOlpEvent_InitAtributeOperator`

---

## PostProcessAttributes

Processes event attributes after the event is created. Useful for loading defaults from external files.

```python
def PostProcessAttributes(Operator):
    from cenpylib.csvParser import CsvParser
    csv = CsvParser.LoadCsvFile("params.csv")

    attribSetter = Operator.GetAttribSetter()
    value = csv.GetCell(0, 0)
    attribSetter.SetString("Parameter", value)

    return True
```

**Operator type:** `CENPyOlpEvent_ProcessAttribOperator`

---

## PostProcessAttributesUpload

Processes attributes for uploaded events (imported from robot programs via uploader).

```python
def PostProcessAttributesUpload(Operator):
    attribGetter = Operator.GetAttribGetter()
    attribSetter = Operator.GetAttribSetter()
    # Map uploaded values to internal attributes
    return True
```

**Operator type:** `CENPyOlpEvent_ProcessAttribOperator`

---

## PostCompute

Computes event behavior during program compute. This is the most powerful event callback — it can add motions, skip path segments, insert circular moves, and modify speeds.

```python
def PostCompute(Operator):
    logger = Operator.GetLoggerOperator()
    attribGetter = Operator.GetAttribGetter()
    controller = Operator.GetController()
    actors = Operator.GetActors()
    sensors = Operator.GetSensors()

    # Get initial path matrix at a specific length
    pathMatrix = Operator.GetInitialPathMatrixByLength(0.0)

    # Get reference TPE
    refTpe = Operator.GetRefTpElement()

    # Event operations
    eventOp = Operator.GetEventOperator()

    # Add a speed change
    eventOp.AddSpeed(500)

    # Skip the current path segment
    eventOp.SkipPath()

    # Insert a circular motion
    eventOp.MoveCir(centerMatrix, targetMatrix)

    # Matrix operations
    matrix = Operator.CreateMatrix()

    return True
```

**Operator type:** `CENPyOlpEvent_ComputeOperator`

### Key EventOperator Methods

| Method | Description |
|--------|-------------|
| `AddSpeed(value)` | Insert a speed override |
| `SkipPath()` | Skip the current path segment |
| `MoveCir(center, target)` | Insert a circular motion |

---

## PostOnAttribChanged

Called when event attributes are modified by the user.

```python
def PostOnAttribChanged(Operator):
    changedAttr = Operator.GetChangedAttributeName()
    if changedAttr == "GasOn":
        gasOn = Operator.GetAttribGetter().GetBool("GasOn")
        # Update related attributes
    return True
```

**Operator type:** `CENPyOlpEvent_OnAttribChangedOperator`

---

## See Also

- [[Event Rules]]
- [[Technology Callback Lifecycle]]
- [[Workmethod Callback Lifecycle]]
