---
title: "E2 Python API Docstring Style Guide"
version: 1.0
date_created: 2026-05-19
status: "Draft — pending E2 team review"
tags: [standards, docstrings, api, python]
---

# E2 Python API Docstring Style Guide

This guide defines the docstring standard for all E2 Python API stub files (`typings/**/*.pyi`). Docstrings written to this standard are contributed back to the E2 product source so every future E2 release ships with them.

---

## Style: Google Docstring Format

All docstrings use [Google style](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings). This renders correctly in Pylance hover tooltips, is supported by Sphinx and MkDocs, and is familiar to Python engineers.

```python
def method_name(self, param: type) -> ReturnType:
    """One-line summary — what this method returns or does.

    Extended description if needed. Mention units, coordinate frames,
    and any non-obvious behavior here.

    Args:
        param: Description. Always include units if numeric
            (e.g. "in meters", "in degrees").

    Returns:
        Description of the return value. Always include units and
        type structure for tuples/lists.

    Raises:
        SomeError: When this occurs (only if it can actually raise).

    Note:
        Critical gotchas go here — wrong units, sort requirements,
        multi-value containers, index sentinel values, etc.

    Example:
        x_mm = position.GetXYZ()[0] * 1000
    """
    ...
```

---

## Required Sections by Symbol Type

### Class

```python
class DULPythonPosition:
    """Represents a motion target — either a Cartesian pose or joint configuration.

    Returned by DULPythonMotion.GetPosition() and GetViaPosition().
    Use GetTargetType() to determine which data accessors are valid.
    """
```

- One-line summary + one paragraph of context
- Always say where the object comes from (which method returns it)
- Reference related classes or methods that must be used together

### Method — no parameters

```python
def GetXYZ(self) -> tuple[float, float, float]:
    """Return the Cartesian target position in meters.

    Returns:
        tuple[float, float, float]: (x, y, z) in **meters**.
            Multiply each component by 1000 for millimeters.

    Note:
        Only valid when GetTargetType() == TargetType.Cartesian.
        Values are in the world coordinate frame, not the tool or base frame.

    Example:
        x_mm, y_mm, z_mm = [v * 1000 for v in position.GetXYZ()]
    """
    ...
```

### Method — with parameters

```python
def GetBoolAttribute(self, name: str, default: bool) -> bool:
    """Return a named boolean attribute value from this event.

    Args:
        name: Attribute name (e.g. ``"SignalValue"``).
        default: Value returned if the attribute is absent.

    Returns:
        bool: The attribute value, or ``default`` if not found.

    Note:
        Use only inside LogicPort event handlers where the attribute
        type is known. For unknown types, iterate GetAttributes() instead
        to avoid a type mismatch crash.
    """
    ...
```

### Enum

```python
class EventType:
    """Enum identifying the high-level category of a motion event.

    Returned by DULPythonEvent.GetEventType(). Always dispatch on this
    enum rather than event.GetName() — GetName() returns UI display
    strings that may differ from enum names.
    """

    SetResourcePort: int
    """Multi-signal output event. A single event may contain multiple
    (SignalName, SignalAddress, SignalNumber, SignalValue) groups.
    Collect into a list — do NOT use a scalar loop.

    Observed when: output signals are set at a motion point.
    """

    LogicPort: int
    """Single-signal controller logic port event. Read the 'EventType'
    attribute to determine the signal subtype (bool/int/float set or wait).

    Observed when: a logic port command is placed on a motion.
    """
```

---

## Mandatory Conventions

### Units — always explicit

Never write "a float" for numeric values. Always state the unit:

| ✗ Wrong | ✓ Correct |
|---------|-----------|
| `Returns: float` | `Returns: float: value in **meters**` |
| `x, y, z coordinates` | `(x, y, z) in **meters** — multiply by 1000 for mm` |
| `joint angles` | `joint values in degrees (Revolute) or meters (Prismatic)` |

### Gotchas — always in a `Note:` block

If a caller can make a common mistake, document it:

```python
Note:
    GetExternalJointValues() returns joints in undefined order.
    Always sort by joint.GetJointIndex() before output.
```

```python
Note:
    GetIndex() returns values > 128 when the profile is not mapped
    to a controller slot. Check for this before using the index.
```

### Paired methods — always cross-reference

When two methods must be called together:

```python
def WriteOutputFile(self, operator: DULPythonDownloadOperator) -> None:
    """Write all buffered output to the output file.

    See Also:
        CloseOutputFile: Must be called after WriteOutputFile to register
            the file with E2. Skipping this means the file will not appear
            in the E2 download result.
    """
    ...
```

### Sentinel values — always documented

```python
def GetIndex(self) -> int:
    """Return the controller slot index of this tool profile.

    Returns:
        int: Slot index (0-based). Values > 128 indicate the profile
            is not mapped to a controller slot — log an error and skip.
    """
    ...
```

---

## What NOT to Document

- **Obvious getters:** `GetName() -> str` with body `...` — one-line summary is enough: `"""Return the name of this program."""`
- **Standard Python errors:** Do not document `AttributeError` or `TypeError` that Pylance already catches
- **Internal implementation details:** These are stubs — do not explain how the C++ backend works
- **Redundant type info:** The return type annotation already says `tuple[float, float, float]` — the docstring adds the *unit*, not the type

---

## Review Checklist (before committing a docstringed stub)

- [ ] One-line summary is a complete sentence ending without a period (PEP-257)
- [ ] All numeric returns state their unit explicitly
- [ ] All gotchas are in `Note:` blocks
- [ ] Multi-signal container methods have the collection pattern in the Note
- [ ] Paired methods cross-reference each other
- [ ] Sentinel values (> 128, None returns) are documented
- [ ] At least one `Example:` for methods called in every downloader
- [ ] Enum members each have a one-liner + "Observed when:"
- [ ] No wall-of-text — if a description exceeds 6 lines, consider splitting

---

## Priority Order for Docstring Generation

**Batch 1 — every downloader uses these:**
`DULPythonPosition`, `DULPythonMotion`, `DULPythonEvent`, `DULPythonDownloadOperator`

**Batch 2 — common downloader + core:**
`DULPythonController`, `DULPythonJoint`, `DULPythonProgram`, `DULPythonToolProfile`, `DULPythonBaseProfile`, `EventType` (cenpyolpcore), `OlpCorePythonResource`, `OlpCorePythonTechnology`

**Batch 3 — upload domain:**
`Uploader` + all `cenpyupload` stubs

**Batch 4 — utilities:**
`cenpylib`: `FileUtility`, `attribute`, `arcreport`, `report`
