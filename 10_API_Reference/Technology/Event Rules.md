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

# Event Rules

The `IOlpEventRule` interface defines callbacks for automatic event placement. Rules determine where events are inserted in the path during compute.

Rules are registered in [[Technology Callback Lifecycle#PostTechInitRules|PostTechInitRules]] or [[Workmethod Callback Lifecycle#PostWmInitRules|PostWmInitRules]].

---

## PostExecute

Executes the rule logic to determine where events should be placed. Use `FindTpElementsByType` to locate path elements and `AddTpe` to mark them for event insertion.

```python
def PostExecute(Operator):
    # Find all linear TPEs
    tpeList = Operator.FindTpElementsByType(TP_TYPE_LIN)

    # Add each matching TPE to the rule
    for tpe in tpeList:
        Operator.AddTpe(tpe)

    return True
```

**Operator type:** `CENPyOlpEventRule_ExecuteOperator`

---

## Key Methods

| Method | Description |
|--------|-------------|
| `FindTpElementsByType(type)` | Find path elements by motion type |
| `AddTpe(tpe)` | Mark a TPE for event insertion |

---

## Placement Constants

Used in [[Technology Callback Lifecycle#PostTechInitRules|PostTechInitRules]] when registering rules:

| Constant | Description |
|----------|-------------|
| `EVENTPROCESS_RETRACT` | Event placed at retract motion |
| `EVENTPROCESS_APPROACH` | Event placed at approach motion |
| `TPINSERTPOS_INSERTAFTER` | Insert event after the target TPE |
| `TPINSERTPOS_INSERTBEFORE` | Insert event before the target TPE |

### Registration Example

```python
def PostTechInitRules(Operator):
    # Place GasEvent after retract
    Operator.AddPyEvent("GasEvent.py", EVENTPROCESS_RETRACT, TPINSERTPOS_INSERTAFTER)

    # Place WeldStart before approach
    Operator.AddPyEvent("WeldStart.py", EVENTPROCESS_APPROACH, TPINSERTPOS_INSERTBEFORE)

    return True
```

---

## See Also

- [[Event Callbacks]]
- [[Technology Callback Lifecycle#PostTechInitRules]]
- [[Workmethod Callback Lifecycle#PostWmInitRules]]
