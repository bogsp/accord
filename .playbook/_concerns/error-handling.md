---
id: error-handling
type: concern
status: active
affects: all
provided-by: internal
---
Typed Result pattern used across all Domain and Application layer components.
Returns { ok: true, value } or { ok: false, error } — never throws in business logic.
Errors are caught at Infrastructure and Interface boundaries only.

## Detail
<!-- Error hierarchy, Result type definition, mapping conventions -->
