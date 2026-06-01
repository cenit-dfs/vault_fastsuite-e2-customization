---
type: concept
status: active
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - concept
  - controller
  - robot
---

# Controller Model

In E2, a Controller represents a physical robot controller that manages one or
more robots (axes groups) plus external axes.

## Virtual vs. Physical

| Aspect | Virtual (OLP) | Physical (Onsite) |
|--------|---------------|-------------------|
| Context | Offline programming in E2 | Connected to real controller |
| Data source | Simulation results | Live robot state |
| Output | Generated program files | Transferred programs |

During download, you work with the virtual controller — all data comes from the
E2 simulation.

## Controller Properties

```python
controller.GetName()           # "KRC5_Robot1"
controller.GetManufacturer()   # "KUKA"
controller.GetSeries()         # "KRC5"
controller.GetControllerType() # ControllerTypes enum
controller.GetOutputDirectory() # where files go
```

## Multi-Controller

A workcell can contain multiple controllers. Each controller downloads
independently. The download engine calls your downloader once per controller's
active program.

## Profiles (Frames)

Controllers carry frame definitions that map to physical frame numbers:

| Profile | What It Is | Example |
|---------|-----------|---------|
| Tool Profile | TCP (Tool Center Point) definition | Tool #1 = welding torch |
| Base Profile | Work object / base frame | Base #2 = positioner |
| Accuracy Profile | Blending/approximation settings | Zone z10, z50 |
| Motion Profile | Speed/acceleration defaults | Speed 100%, 500mm/s |

```python
tools = controller.GetToolProfiles()
for tool in tools:
    index = tool.GetIndex()  # controller slot number
    xyz = tool.GetXYZ()      # TCP offset in meters
```

> **Note:** `GetIndex()` > 128 means "not mapped" — skip it.

## See Also

- [[10_API_Reference/Download/DULPythonController|DULPythonController]]
- [[10_API_Reference/OlpCore/OlpCorePythonController|OlpCorePythonController]]
- [[OLP Tree]] — controller sits at the top
