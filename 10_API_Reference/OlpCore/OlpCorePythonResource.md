---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - resource
---

# OlpCorePythonResource

Represents a resource (robot, tool, fixture, workpiece, etc.) in the OLP project tree. Resources have spatial frames and can form parent-child hierarchies.

**Inherits from:** [[OlpCorePythonItem]]

## Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `GetResourceType()` | `str` | Returns the resource type identifier |
| `GetFrame()` | `Matrix` | Returns the resource frame (4×4 transformation matrix) |
| `SetFrame(frame)` | — | Sets the resource frame |
| `GetParentResource()` | `Resource` | Returns the parent resource (or None if root) |
| `GetChildResources()` | `list[Resource]` | Returns all child resources |

## Usage

```python
resource = olp_environment.GetResource()
res_type = resource.GetResourceType()
frame = resource.GetFrame()

# Traverse resource hierarchy
for child in resource.GetChildResources():
    print(child.GetName(), child.GetResourceType())
```

## See Also

- [[OlpCorePythonItem]] — Base class
- [[OlpCorePythonController]] — Controller resource
- [[OlpCore Attributes]] — Attributes on resources
