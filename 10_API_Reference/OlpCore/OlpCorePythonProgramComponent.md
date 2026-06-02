---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - base-class
  - program-component
---

# OlpCorePythonProgramComponent

Intermediate base class for elements that belong to a program hierarchy
(operation groups, operations, and related program-level components).

**Inherits from:** [[OlpCorePythonItem]]

## Typical Responsibilities

| Responsibility | Description |
|---------------|-------------|
| Program context | Belongs to a program tree and participates in hierarchy traversal |
| Shared metadata | Common naming and identity behavior for program components |
| Attribute support | Inherited typed attribute access via the base item model |

## Why It Matters

When class docs mention shared behavior between program node types, that behavior
is often defined at this intermediate level.

## See Also

- [[OlpCorePythonItem]]
- [[OlpCorePythonProgram]]
- [[OlpCorePythonOperation]]
