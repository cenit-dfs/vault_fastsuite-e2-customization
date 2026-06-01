---
type: pattern
status: active
e2-package: cenpydownload
e2-area: downloader
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - download-api
  - pattern
  - file-io
---

# File Output Pattern

Downloaders buffer all output into arrays, then write everything at the end.

## Buffer-Then-Write

```python
def __init__(self):
    self.Header = []
    self.Source = []
    self.Data = []
    self.Footer = []
    self.outputPath = ""

def CreateOutputFile(self, operator):
    controller = operator.GetController()
    program = controller.GetActiveProgram()
    outputDir = controller.GetOutputDirectory()
    self.outputPath = os.path.join(outputDir, f"{program.GetName()}.src")

def WriteOutputFile(self, operator):
    fileUtil = FileUtility()
    fileUtil.AppendTextArrayToFile(self.outputPath, self.Header)
    fileUtil.AppendTextArrayToFile(self.outputPath, self.Source)
    fileUtil.AppendTextArrayToFile(self.outputPath, self.Data)
    fileUtil.AppendTextArrayToFile(self.outputPath, self.Footer)

def CloseOutputFile(self, operator):
    operator.AddOutputFilePath(self.outputPath)  # REQUIRED!
```

## Critical: AddOutputFilePath

`operator.AddOutputFilePath(path)` **must** be called in `CloseOutputFile` for
every file you produce. Without it, E2 won't show the file in the download
output dialog and won't copy it to the controller.

## Multiple Output Files

Some translators produce multiple files (e.g., KUKA: `.src` + `.dat`):

```python
def __init__(self):
    self.srcPath = ""
    self.datPath = ""

def CreateOutputFile(self, operator):
    outputDir = controller.GetOutputDirectory()
    name = program.GetName()
    self.srcPath = os.path.join(outputDir, f"{name}.src")
    self.datPath = os.path.join(outputDir, f"{name}.dat")

def CloseOutputFile(self, operator):
    operator.AddOutputFilePath(self.srcPath)
    operator.AddOutputFilePath(self.datPath)
```

## Required Imports

```python
import sys, inspect, os
sys.dont_write_bytecode = True
sys.path.append(str(os.path.dirname(os.path.abspath(
    inspect.getfile(inspect.currentframe())))))

from cenpylib import FileUtility
from cenpydownload import *
from cenpyolpcore import *
from centypes import *
```

## Class Registration

Every downloader must define `DOWNLOAD_CLASS_NAME` at module level:

```python
DOWNLOAD_CLASS_NAME = "MyDownloader"

class MyDownloader(Downloader):
    def __init__(self):
        ...
```

## See Also

- [[Callback Lifecycle Pattern]] — when each method is called
- [[10_API_Reference/Download/DULPythonDownloadOperator|DULPythonDownloadOperator]]
