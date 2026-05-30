---
id: auth
type: concern
status: active
affects: all
provided-by: internal
---
Permission checks applied at orchestrator boundaries.
Components do not handle auth directly.
All auth context flows from the request — never queried mid-flow.

## Detail
<!-- Auth provider, permission model, session handling notes -->
