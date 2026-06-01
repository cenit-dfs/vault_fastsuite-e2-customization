---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - attributes
---

# OlpCore Attributes

The attribute system provides typed access to parameters on OLP items (controllers, programs, operations, teach points, events, etc.). Attributes are accessed via setter classes (for creating/modifying) and getter classes (for reading values).

## Setter Classes

Used when defining or modifying attributes on items.

| Class | Value Type | Type-Specific Methods |
|-------|-----------|----------------------|
| `OlpCorePythonSetBoolAttribute` | `bool` | `SetValue(bool)` |
| `OlpCorePythonSetIntegerAttribute` | `int` | `SetValue(int)`, `SetMin(int)`, `SetMax(int)` |
| `OlpCorePythonSetDoubleAttribute` | `float` | `SetValue(float)`, `SetMin(float)`, `SetMax(float)`, `SetUnit(AttributeValueUnitType)` |
| `OlpCorePythonSetStringAttribute` | `str` | `SetValue(str)` |
| `OlpCorePythonSetEnumAttribute` | `int` | `SetValue(int)`, `AddOption(label, value)`, `SetOptions(list)` |

### Common Setter Methods

All setter classes share:

| Method | Description |
|--------|-------------|
| `SetName(name)` | Sets the attribute name |
| `SetProperties(AttributeProperties)` | Sets attribute properties (READONLY, HIDDEN, etc.) |
| `SetGroup(group)` | Sets the display group |
| `SetDescription(desc)` | Sets the tooltip/description |

## Getter Classes

Used when reading attribute values from items.

| Class | Value Type | Type-Specific Methods |
|-------|-----------|----------------------|
| `OlpCorePythonBoolAttribute` | `bool` | `GetValue() → bool` |
| `OlpCorePythonIntegerAttribute` | `int` | `GetValue() → int`, `GetMin() → int`, `GetMax() → int` |
| `OlpCorePythonDoubleAttribute` | `float` | `GetValue() → float`, `GetMin() → float`, `GetMax() → float`, `GetUnit() → AttributeValueUnitType` |
| `OlpCorePythonStringAttribute` | `str` | `GetValue() → str` |
| `OlpCorePythonEnumAttribute` | `int` | `GetValue() → int`, `GetOptions() → list`, `GetSelectedLabel() → str` |

### Common Getter Methods

All getter classes share:

| Method | Description |
|--------|-------------|
| `GetName() → str` | Returns the attribute name |
| `GetProperties() → AttributeProperties` | Returns the attribute properties |
| `GetGroup() → str` | Returns the display group |
| `GetDescription() → str` | Returns the tooltip/description |

## Usage

### Reading Attributes

```python
# Get a string attribute from a teach point
attr = tp_element.GetAttribute("Speed")
if attr:
    speed = attr.GetValue()
    unit = attr.GetUnit()
```

### Setting Attributes (Technology Scripts)

```python
# Define a new double attribute
attr = OlpCorePythonSetDoubleAttribute()
attr.SetName("WeldSpeed")
attr.SetValue(10.0)
attr.SetMin(1.0)
attr.SetMax(100.0)
attr.SetUnit(AttributeValueUnitType.UNIT_MM_S)
attr.SetProperties(AttributeProperties.DOWNLOADABLE)
```

## See Also

- [[OlpCore Enumerations]] — `AttributeProperties`, `AttributeValueUnitType` enums
- [[OlpCorePythonItem]] — Base class providing attribute access
- [[OlpCorePythonTechnology]] — Technology defining attributes
- [[OlpCorePythonOperation]] — Operations carrying attributes
