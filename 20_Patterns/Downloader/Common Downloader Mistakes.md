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
  - mistakes
  - troubleshooting
---

# Common Downloader Mistakes

Pitfalls that frequently catch new downloader authors.

## 1. Multi-Signal Container Not Handled

**Problem:** `SetResourcePort` / `WaitForResourcePort` events contain MULTIPLE
signals. A simple scalar loop only captures the last one.

**Fix:** Collect into a list by detecting each new `SignalName`. See
[[Event Dispatch Pattern#SetResourcePort / WaitForResourcePort (Multi-Signal Container)]].

## 2. Using Typed Getters on Wrong Type

**Problem:** `event.GetBoolAttribute("SignalValue", True)` crashes if the signal
is actually an integer.

**Fix:** Use `event.GetAttributes()` generic loop first. Only use typed getters
for LogicPort subtypes where you know the exact type from `EventType`.

## 3. Forgetting AddOutputFilePath

**Problem:** Output files don't appear in E2's download dialog.

**Fix:** Always call `operator.AddOutputFilePath(path)` in `CloseOutputFile` for
every file produced.

## 4. Wrong Unit Conversion

**Problem:** Positions are 1000× too small or too large.

**Fix:** E2 positions are in **meters**. Multiply by 1000 for mm. External
prismatic axes are also in meters.

## 5. Modifying E2 Installation Files

**Problem:** Changes to `downloadStarter.py` or `downloader.py` break the
download engine or get overwritten on E2 updates.

**Fix:** Never touch these files. Your code goes in a **separate** `.py` file
that inherits from `Downloader`.

## 6. Logger Scope

**Problem:** `operator.GetLogOperator()` called outside a callback crashes.

**Fix:** Store logger as local variable within each callback that needs it.
It requires the `operator` parameter.

## 7. GetIndex() Large Values

**Problem:** Tool/Base profile `GetIndex()` returns values > 128.

**Fix:** Values > 128 mean "not mapped" on the controller. Check for this and
log a warning instead of outputting an invalid frame number.

## 8. Dispatching on event.GetName()

**Problem:** Multiple event types share the same `GetName()` value (e.g.,
"BuiltInEvent").

**Fix:** Always dispatch on `event.GetEventType()` enum, never on `GetName()`.

## See Also

- [[Event Dispatch Pattern]]
- [[Position Output Pattern]]
- [[File Output Pattern]]
- [[60_Troubleshooting/.gitkeep|Troubleshooting]]
