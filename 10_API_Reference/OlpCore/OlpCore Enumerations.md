---
type: api-class
status: active
e2-package: cenpyolpcore
e2-area: core
source: fastsuite-copilot-starter
created: 2026-06-01
tags:
  - olpcore-api
  - enumerations
---

# OlpCore Enumerations

Enumerations used throughout the OLP Core API for typed constants.

## AttributeProperties

Flags controlling attribute visibility and behavior. Can be combined with bitwise OR.

| Value | Description |
|-------|-------------|
| `NONE` | No special properties |
| `READONLY` | Attribute cannot be edited by the user |
| `HIDDEN` | Attribute is not displayed in the UI |
| `DOWNLOADABLE` | Attribute is included in the download output |
| `REQUIRED` | Attribute must have a value |

## AttributeValueUnitType

Unit types for numeric (double) attributes.

| Value | Description |
|-------|-------------|
| `UNIT_NONE` | No unit |
| `UNIT_MM` | Millimeters |
| `UNIT_CM` | Centimeters |
| `UNIT_M` | Meters |
| `UNIT_INCH` | Inches |
| `UNIT_DEG` | Degrees |
| `UNIT_RAD` | Radians |
| `UNIT_MM_S` | Millimeters per second |

## ControllerTypes

Identifies the controller type/brand.

| Value | Description |
|-------|-------------|
| Values are vendor-specific | Returned by `GetControllerType()` on [[OlpCorePythonController]] |

## OperationType

Classifies the type of operation.

| Value | Description |
|-------|-------------|
| Values define motion/process type | Returned by `GetOperationType()` on [[OlpCorePythonOperation]] |

## InsertPosition

Defines where an event is placed relative to a teach point.

| Value | Description |
|-------|-------------|
| `BEFORE` | Event fires before reaching the teach point |
| `AT` | Event fires at the teach point |
| `AFTER` | Event fires after leaving the teach point |

Used by [[OlpCorePythonEvent]] — `GetInsertPosition()` / `SetInsertPosition()`.

## ItemType

High-level classification of OLP tree items.

| Value | Description |
|-------|-------------|
| Identifies major item categories | Controllers, programs, operations, resources, etc. |

## ItemSubtype

Fine-grained classification within an `ItemType`.

| Value | Description |
|-------|-------------|
| Identifies specific item variants | E.g. linear vs. circular motion within operations |

## See Also

- [[OlpCore Attributes]] — Uses `AttributeProperties` and `AttributeValueUnitType`
- [[OlpCorePythonController]] — Uses `ControllerTypes`
- [[OlpCorePythonOperation]] — Uses `OperationType`
- [[OlpCorePythonEvent]] — Uses `InsertPosition`
