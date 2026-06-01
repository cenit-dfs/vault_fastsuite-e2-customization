---
type: api-class
status: active
e2-package: cenpylib
e2-area: reporting
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - reporting-api
---

# ReportUtility

The `ReportUtility` class generates PDF reports for offline programs. Located in `cenpylib.report`, it uses the `fpdf2` library under the hood.

---

## Import

```python
from cenpylib.report import ReportUtility
```

---

## Key Methods

| Method | Description |
|--------|-------------|
| `setLanguage(lang)` | Set report language (`"EN"`, `"DE"`, `"FR"`, `"CN"`, `"JP"`) |
| `createAutoExecutePDFReport(Operator, appPath)` | Generate report from AutoExecute (after download) |
| `createAuxCommandsPDFReport(Operator, appPath)` | Generate report from Auxiliary Commands |
| `createPDFReport(Operator, appPath, repFilePath)` | Custom report to a specific output path |
| `setHeaderLogo(filename)` | Set custom header logo (jpg/png) |
| `setFooterLogo(filename)` | Set custom footer logo (jpg/png) |
| `setCosts(value)` | Set cost per meter (float) |
| `setCurrency(currency)` | Set currency: `"E"` (Euro), `"D"` (Dollar), `"P"` (Pound), `"Y"` (Yen) |

---

## Available Report Columns

| Column | Content |
|--------|---------|
| `"Name"` | TPE name |
| `"MotionType"` | Motion type (PTP/LIN/CIR) |
| `"Length"` | Path length (mm) |
| `"Speed"` | Motion speed |
| `"Time"` | Cycle time |
| `"Costs"` | Calculated costs |
| `"CollReach"` | Collision/reachability status |
| `"Events"` | Events on TPE |
| `"Various"` | Custom column |

---

## Usage Example

### Basic Report (PostProgramDownload)

```python
def PostProgramDownload(Operator):
    pdf = ReportUtility()
    pdf.setLanguage("EN")
    pdf.createAutoExecutePDFReport(Operator, "")
```

### Custom Column Selection

```python
def ModifyActiveProgram(Operator):
    pdf = ReportUtility()
    pdf.setLanguage("DE")
    pdf.setCosts(2.56)
    pdf.setCurrency("E")

    columns = ["Name", "MotionType", "Length", "Time", "Costs"]
    pdf.createAuxCommandsPDFReport(Operator, "", columns)
```

### Custom Branding

```python
def PostProgramDownload(Operator):
    logoPath = "C:\\Technologies\\TechnologyCommon\\Standard\\AuxiliaryCommands\\Logos\\"
    pdf = ReportUtility()
    pdf.setLanguage("EN")
    pdf.setHeaderLogo("CustomerHeader.jpg")
    pdf.setFooterLogo("CustomerFooter.jpg")
    pdf.createAutoExecutePDFReport(Operator, logoPath)
```

---

## Language File Configuration

Language files are located at `\Lib\site-packages\cenpylib\languages\`. Key settings:

| Setting | Description |
|---------|-------------|
| `defaultreportname` | Default PDF filename (`##` = program name) |
| `setfontname` | Font type (not for CN/JP) |
| `setfontsize` | Font size |
| `commandname` | Menu command name |
| `operationheader` | Header style: 0=divider, 1=name, 2=group+name, 3=none |
| `operationsummary` | Summary row (0=disabled) |

---

## See Also

- [[Technology Callback Lifecycle]]
- [[Workmethod Callback Lifecycle]]
