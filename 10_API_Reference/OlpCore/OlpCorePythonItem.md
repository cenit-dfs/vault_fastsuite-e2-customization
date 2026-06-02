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
---

# OlpCorePythonItem

Base class for most OLP Core objects (controller, program, operation, event,
resource, technology). It provides shared functionality such as typed attribute
access and common metadata methods.

## Typical Capabilities

| Capability | Description |
|-----------|-------------|
| Attribute access | Read/write typed attributes on the item |
| Metadata | Name, type, and identity helpers |
| Hierarchy hooks | Access parent/child context where supported |

## Why It Matters

When API docs reference methods or behavior inherited from a base object, that
base is usually `OlpCorePythonItem`. Concrete classes extend it and add
class-specific methods.

## See Also

- [[OlpCore Attributes]]
- [[OlpCore Enumerations]]
- [[OlpCorePythonController]]
- [[OlpCorePythonProgram]]
- [[OlpCorePythonOperation]]
- [[OlpCorePythonEvent]]
- [[OlpCorePythonResource]]
- [[OlpCorePythonTechnology]]
