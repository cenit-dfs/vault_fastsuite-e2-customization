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

# Workmethod Callback Lifecycle

The `IOlpWorkMethod` interface defines callbacks at the workmethod level. Workmethods are nested within a technology and define specific manufacturing process variants.

## Callback Order

| # | Callback | Purpose |
|---|----------|---------|
| 1 | [[#PostWmInitAttributes]] | Create workmethod attributes |
| 2 | [[#PostWmInitEvents]] | Register workmethod events |
| 3 | [[#PostWmInitRules]] | Workmethod event rules |
| 4 | [[#PostWmOnAttribChanged]] | Attribute change handler |
| 5 | [[#PostWmSyncPgAttributes]] | Sync process geometry attributes (compute only) |
| 6 | [[#PostProcessOperationAttributes]] | CSV parsing, geometry operations |
| 7 | [[#PostWmOnFrameChanged]] | Frame change handler |

---

## PostWmInitAttributes

Creates workmethod attributes. Identical pattern to [[Technology Callback Lifecycle#PostTechInitAttributes|PostTechInitAttributes]].

```python
def PostWmInitAttributes(Operator):
    attribCreator = Operator.GetAttribCreator()

    # Integer attribute with default value
    attribCreator.AddInteger("WireSpeed", 300)

    # Double attribute
    attribCreator.AddDouble("GasFlow", 15.0)

    # String attribute
    attribCreator.AddString("WireType", "ER70S-6")

    # Boolean attribute
    attribCreator.AddBool("PulseMode", False)

    # Enum attribute (list of options, default index)
    attribCreator.AddEnum("TransferMode", ["Short Arc", "Spray", "Pulse"], 0)

    return True
```

**Operator type:** `CENPyOlpWm_InitAtributeOperator`

### Attribute Access Pattern

```python
# Reading attributes
attribGetter = Operator.GetAttribGetter()
speed = attribGetter.GetInteger("WireSpeed")
flow = attribGetter.GetDouble("GasFlow")
wire = attribGetter.GetString("WireType")
pulse = attribGetter.GetBool("PulseMode")

# Writing attributes
attribSetter = Operator.GetAttribSetter()
attribSetter.SetInteger("WireSpeed", 400)
attribSetter.SetDouble("GasFlow", 18.5)
```

---

## PostWmInitEvents

Registers workmethod-specific events.

```python
def PostWmInitEvents(Operator):
    Operator.RegisterPyTechnologyEvent("ZAxisEvent.py")
    return True
```

**Operator type:** `CENPyOlpWm_InitEventOperator`

See also: [[Event Callbacks]]

---

## PostWmInitRules

Initializes workmethod event rules for automatic event placement.

**Operator type:** `CENPyOlpWm_InitRuleOperator`

See also: [[Event Rules]]

---

## PostWmOnAttribChanged

Called when workmethod attributes change.

```python
def PostWmOnAttribChanged(Operator):
    changedAttr = Operator.GetChangedAttributeName()
    if changedAttr == "TransferMode":
        mode = Operator.GetAttribGetter().GetEnum("TransferMode")
        # Adjust other attributes based on mode
    return True
```

**Operator type:** `CENPyOlpWm_OnAttribChangedOperator`

---

## PostWmSyncPgAttributes

Synchronizes process geometry attributes. **Called only on compute, not recompute.** Use this to read geometry properties and set event attributes accordingly.

```python
def PostWmSyncPgAttributes(Operator):
    pgOperator = Operator.GetCurrentProcessGeometryOperator()

    pgName = pgOperator.GetProcessGeometryName()
    isRegshape = pgOperator.IsRegshape()

    if isRegshape:
        center = pgOperator.GetRegshapeCenter()
        length = pgOperator.GetRegshapeLength()
        height = pgOperator.GetRegshapeHeight()
        radius = pgOperator.GetRegshapeRadius()
        cornerRadius = pgOperator.GetRegshapeCornerRadius()
        geoType = pgOperator.GetGeoType()
        shapeType = pgOperator.GetRegshapeType()

    # Access event attributes for specific events
    eventAttribGetter = pgOperator.GetEventAttribGetter("EventName")
    eventAttribSetter = pgOperator.GetEventAttribSetter("EventName")

    return True
```

**Operator type:** `CENPyOlpWm_SyncPgAttribOperator`

---

## PostProcessOperationAttributes

Processes operation attributes. Supports CSV parsing and geometry vector operations.

```python
def PostProcessOperationAttributes(Operator):
    # Geometry operations
    geoOp = Operator.GetGeometryOperator()
    vec1 = geoOp.CreateVector(0, 0, 1)
    vec2 = geoOp.CreateVector(1, 0, 0)
    angle = geoOp.GetIncludedAngle(vec1, vec2)
    radians = geoOp.ToRadian(90.0)
    degrees = geoOp.ToDegrees(1.5708)

    # CSV parsing
    from cenpylib.csvParser import CsvParser
    csv = CsvParser.LoadCsvFile("data.csv")
    rows = csv.GetNumberOfRows()
    cols = csv.GetNumberOfColumns()
    cell = csv.GetCell(row=0, col=0)

    return True
```

**Operator type:** `CENPyOlpWm_ProcessOperationAttribOperator`

---

## PostWmOnFrameChanged

Called when frames change at the workmethod level. Same pattern as [[Technology Callback Lifecycle#PostTechOnFrameChanged|PostTechOnFrameChanged]].

**Operator type:** `CENPyOlpWm_OnFrameChangedOperator`

---

## See Also

- [[Technology Callback Lifecycle]]
- [[Event Callbacks]]
- [[Series Callbacks]]
