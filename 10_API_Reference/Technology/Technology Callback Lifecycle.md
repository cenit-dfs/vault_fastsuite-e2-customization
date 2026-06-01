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

# Technology Callback Lifecycle

The `IOlpTechnology` interface defines callbacks invoked at the technology level. These are registered in the technology's main Python script and called by E2 during the technology lifecycle.

## Callback Order

| # | Callback | Purpose |
|---|----------|---------|
| 1 | [[#PostTechInitAttributes]] | Create technology-level attributes |
| 2 | [[#PostTechInitEvents]] | Register custom technology events |
| 3 | [[#PostTechInitRules]] | Set automatic event placement rules |
| 4 | [[#PostInitManufacturingGeometry]] | After geometry initialization |
| 5 | [[#PrevExecuteRecipe]] | Before recipe execution (return `True` to override) |
| 6 | [[#PostTechOnAttribChanged]] | React to attribute changes |
| 7 | [[#PostProcessOperationGroupAttributes]] | Process operation group attributes |
| 8 | [[#PostTechOnFrameChanged]] | React to frame changes |
| 9 | [[#PostTechUpdate]] | Handle technology version migrations |

---

## PostTechInitAttributes

Creates technology attributes using the [[OlpCore Attribute System|attribute creator API]].

```python
def PostTechInitAttributes(Operator):
    attribCreator = Operator.GetAttribCreator()
    attribCreator.AddInteger("WeldSpeed", 500)
    attribCreator.AddDouble("Voltage", 25.0)
    attribCreator.AddString("ProcessName", "ArcWelding")
    attribCreator.AddBool("UseShielding", True)
    attribCreator.AddEnum("WeldType", ["MIG", "TIG", "Laser"], 0)
    return True
```

**Operator type:** `CENPyOlpTech_InitAtributeOperator`

---

## PostTechInitEvents

Registers custom technology events. Each event is a separate Python file.

```python
def PostTechInitEvents(Operator):
    Operator.RegisterPyTechnologyEvent("GasEvent.py")
    return True
```

**Operator type:** `CENPyOlpTech_InitEventOperator`

See also: [[Event Callbacks]]

---

## PostTechInitRules

Sets automatic event placement rules that determine where events are inserted in the path.

```python
def PostTechInitRules(Operator):
    Operator.AddPyEvent("GasEvent.py", EVENTPROCESS_RETRACT, TPINSERTPOS_INSERTAFTER)
    return True
```

**Operator type:** `CENPyOlpTech_InitRuleOperator`

See also: [[Event Rules]]

---

## PostInitManufacturingGeometry

Called after manufacturing geometry is initialized. Provides access to the process geometry operator.

```python
def PostInitManufacturingGeometry(Operator):
    pgOperator = Operator.GetProcessGeometryOperator()
    if pgOperator.IsRegshape():
        centerMatrix = pgOperator.GetRegshapeCenter()
    return True
```

**Operator type:** `CENPyOlpTech_InitMfGeoOperator`

---

## PrevExecuteRecipe

Called before recipe execution. Return `True` to override default recipe behavior, `False` to use it.

```python
def PrevExecuteRecipe(Operator):
    pgList = Operator.GetSelectedProcessGeometries()
    for pg in pgList:
        operation = Operator.CreateOperation("CustomOp")
    return True  # Override default recipe
```

**Operator type:** `CENPyOlpTech_ExecuteRecipeOperator`

---

## PostTechOnAttribChanged

React to attribute changes at the technology level.

```python
def PostTechOnAttribChanged(Operator):
    changedAttr = Operator.GetChangedAttributeName()
    if changedAttr == "WeldSpeed":
        speed = Operator.GetAttribGetter().GetInteger("WeldSpeed")
    return True
```

**Operator type:** `CENPyOlpTech_OnAttribChangedOperator`

---

## PostProcessOperationGroupAttributes

Processes operation group attributes. Called when operation groups are created or modified.

**Operator type:** `CENPyOlpTech_ProcessOperationGroupAttribOperator`

---

## PostTechOnFrameChanged

Called when frames are modified. Return `True` to request recompute.

```python
def PostTechOnFrameChanged(Operator):
    frameName = Operator.GetChangedFrameName()
    frameIndex = Operator.GetChangedFrameIndex()
    frameMatrix = Operator.GetChangedFrameMatrix()
    worldMatrix = Operator.GetChangedFrameWorldMatrix()
    return True
```

**Operator type:** `CENPyOlpTech_OnFrameChangedOperator`

---

## PostTechUpdate

Handles technology version migrations when a saved technology is opened with a newer version.

```python
def PostTechUpdate(Operator):
    currentVersion = Operator.GetPythonTechnologyVersion()
    lastSavedVersion = Operator.GetLastSavedPythonTechnologyVersion()
    if lastSavedVersion < currentVersion:
        program = Operator.GetOlpProgram()
        techRuleOp = Operator.GetTechEventRuleUpdateOperator()
        wmRuleOp = Operator.GetWmEventRuleUpdateOperator()
    return True
```

**Operator type:** `CENPyOlpTech_UpdateOperator`

---

## See Also

- [[Workmethod Callback Lifecycle]]
- [[Event Callbacks]]
- [[Event Rules]]
- [[Series Callbacks]]
