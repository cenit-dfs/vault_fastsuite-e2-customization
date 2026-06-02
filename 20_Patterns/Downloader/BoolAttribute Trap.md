---
type: pattern
status: active
e2-package: cenpyolpcore
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-02
tags:
  - download-api
  - pattern
  - mistakes
  - attribute
  - troubleshooting
---

# BoolAttribute Trap

The #1 silent bug in E2 downloaders. `GetValue()` on a BoolAttribute returns a
Python `bool` object (`True` / `False`), **NOT** the string `"True"` / `"False"`.

## The Bug

```python
# WRONG — this condition is ALWAYS False:
attr = event.GetBoolAttribute("IsComment", True)
if attr.GetValue() == 'True':
    ...  # never executes!

# Why: Python bool True compared to string 'True' → False
#      bool is not str, no implicit conversion
```

## The Fix

Always convert to string before comparing:

```python
# CORRECT:
if str(attr.GetValue()) == 'True':
    ...
```

Or use boolean directly (but be careful with truthy/falsy edge cases):

```python
# ALSO CORRECT (when you know it's always a bool):
if attr.GetValue():
    ...
```

The `str()` approach is safer because it works regardless of whether the
attribute returns a Python `bool`, `int`, or `str` — making the comparison
explicit and predictable.

## Where This Applies

Every bool attribute from E2, including:

| Source | Attribute Examples |
|--------|-------------------|
| `event.GetAttributes()` loop | `IsComment`, `ExactStop`, `TriggerRelatedToStart` |
| Technology attributes | `DLLTOff`, `DLSSDown`, `DLRTPM`, `DLWCStartGain` |
| Signal values | `SignalValue` (when bool type) |
| Operation/program attrs | Any custom bool attribute |

## Why It's Silent

- No exception is thrown
- No type error
- Code runs fine — it just always takes the `else` branch
- Output looks plausible (missing a comment, missing an `OFF` command)
- Only caught by careful golden-file comparison

## Pattern for Generic Attribute Loop

When iterating `event.GetAttributes()`, the value type varies per attribute.
Use `str()` defensively:

```python
for attr in event.GetAttributes():
    name = attr.GetName()
    if name == 'IsComment':
        isComment = str(attr.GetValue()) == 'True'
    elif name == 'SignalValue':
        sigValue = str(attr.GetValue()) == 'True'
    elif name == 'DLLTOff':
        ltOff = str(attr.GetValue()) == 'True'
```

## Prevention

- **Code review checkpoint:** Search for `== 'True'` or `== 'False'` without
  `str()` wrapper — this is always a potential bug
- **Vendor-scoped instructions:** Add this rule to `.instructions.md` for each
  vendor folder
- **Golden-file tests:** The only reliable detection method since the bug is
  silent

## Related

- [[Common Downloader Mistakes]] — listed as mistake #7
- [[Event Dispatch Pattern]] — attribute reading in event handlers
