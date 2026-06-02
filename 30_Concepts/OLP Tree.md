---
type: concept
status: active
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - concept
  - olp-tree
  - hierarchy
---

# OLP Tree

The OLP (Offline Programming) tree is the hierarchical structure that represents
a robot program in FASTSUITE E2. Understanding this tree is essential for writing
downloaders and technology scripts.

## Hierarchy

```text
Controller
└── Program
    └── OperationGroup (optional nesting)
        └── Operation
            └── Motion (TP Element)
                ├── Position (Cartesian + Joint)
                ├── Events Before (technology events fired before motion)
                └── Events After (technology events fired after motion)
```

## Key Objects

| Level | Object | Purpose |
|-------|--------|---------|
| Controller | [[10_API_Reference/Download/DULPythonController|DULPythonController]] | Robot identity, output path, profiles |
| Program | [[10_API_Reference/Download/DULPythonProgram|DULPythonProgram]] | Named program, may contain sub-programs |
| Operation | `DULPythonOperation` | Logical grouping of motions (one weld seam, one path) |
| Motion | [[10_API_Reference/Download/DULPythonMotion|DULPythonMotion]] | Single teach point with position + speed + accuracy |
| Position | [[10_API_Reference/Download/DULPythonPosition|DULPythonPosition]] | Cartesian coordinates or joint values |
| Event | [[10_API_Reference/Download/DULPythonEvent|DULPythonEvent]] | Technology command (weld on, signal, speed change) |

## How to Inspect the Tree

Use `Simple_Python_Translator.py` in E2 to generate a `.txt` dump that shows
every level with all attributes. This is the fastest way to understand what data
is available for your downloader.

## Download vs. OLP Core Views

The same tree is accessible via two APIs:

| API | When | Objects |
|-----|------|---------|
| **Download API** (`cenpydownload`) | During download execution | `DULPython*` — read-only, optimized for output |
| **OLP Core** (`cenpyolpcore`) | In technology scripts, any time | `OlpCorePython*` — read/write, full model access |

## See Also

- [[Event Model]] — how events attach to motions
- [[Unit System]] — coordinate units in the tree
- [[20_Patterns/Downloader/Callback Lifecycle Pattern|Callback Lifecycle Pattern]] — traversal order
