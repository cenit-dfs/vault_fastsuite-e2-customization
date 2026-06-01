---
type: api-class
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - controller
---

# DULPythonController

Provides controller (robot) information during download. Available in the
`PreDownload` callback and via `operator.GetController()`.

## Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `GetName()` | `str` | Controller name in E2 workcell |
| `GetManufacturer()` | `str` | Robot manufacturer (e.g., "KUKA", "ABB") |
| `GetSeries()` | `str` | Controller series (e.g., "KRC5", "IRC5") |
| `GetVersion()` | `str` | Controller/firmware version |
| `GetIP()` | `str` | IP address (if configured) |
| `GetPort()` | `int` | Communication port |
| `GetOutputPath()` | `str` | Download output directory path |
| `GetAttribute(name)` | `object` | Custom controller attribute |

## Usage

```python
def PreDownload(controller):
    name = controller.GetName()
    manufacturer = controller.GetManufacturer()
    series = controller.GetSeries()
    outputPath = controller.GetOutputPath()

    # Use series to select syntax variant
    if series == "KRC5":
        # KUKA KRC5 specific setup
        pass
```

## Notes

- `GetOutputPath()` returns the directory where the downloader should write
  output files. Same as `operator.GetOutputPath()`.
- Controller attributes are set in the E2 workcell configuration by the user.

## See Also

- [[DULPythonDownloadOperator]] — main operator (also provides controller access)
- [[Download Callback Lifecycle]] — `PreDownload` receives the controller
