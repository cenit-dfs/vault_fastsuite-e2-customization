---
type: api-class
status: active
e2-package: cenpyupload
e2-area: uploader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - upload-api
  - controller
---

# ULPythonController

Represents the target robot controller for the upload. Provides identifying information about the controller hardware. Accessed via [[ULPythonUploadOperator|operator.GetController()]] or as parameter in `PreUpload`.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Get the controller name |
| `GetManufacturer()` | `str` | Get the robot manufacturer (e.g., "KUKA", "ABB") |
| `GetSeries()` | `str` | Get the controller series (e.g., "KRC5", "IRC5") |
| `GetVersion()` | `str` | Get the controller software version |
| `GetSourcePath()` | `str` | Get the source path for robot files |
| `SetAttribute(name, value)` | — | Set a custom attribute on the controller |

## Usage Example

```python
def PreUpload(controller):
    manufacturer = controller.GetManufacturer()
    series = controller.GetSeries()
    version = controller.GetVersion()

    print(f"Uploading for: {manufacturer} {series} v{version}")
    print(f"Source path: {controller.GetSourcePath()}")

    # Store metadata
    controller.SetAttribute("UploadTimestamp", "2026-06-01")
```

## See Also

- [[ULPythonUploadOperator]]
- [[Upload Callback Lifecycle]]
- [[ULPythonProgram]]
