---
id: [kebab-case — matches filename]
export: [ExportedName]
file: [src/module/component.ts]
layer: application
type: component
status: draft
parent: [parent-sequencer-or-module]
children: []
connects: []
emits: []
concerns: []
---

## What It Does
Plain language description of this component's single responsibility.
Any reader understands this without technical knowledge.

Clean Architecture: set `layer` to domain, application, infrastructure, or
interface. Store this DDA file under the matching layer folder and keep the
implementation file in that layer.

Use this doc type only for one bounded leaf responsibility: validation,
calculation, persistence, rendering, parsing, policy decision, or adapter call.
If this unit receives triggers, routes between handlers, or sequences multiple
components, use `type: sequencer` instead.

## Interface
Contract: `docs/contracts/[flow]/[file].md#[section]`

input:  [InputType]
output: Result<[SuccessType], [ErrorType]>

```[language]
// language-specific signature
function [name](input: [InputType]): Result<[SuccessType], [ErrorType]>
```

## Detail
_(Only present when edge cases, concurrency constraints, or known quirks
exist that would cause bugs if missed. Omit entirely if nothing fits.)_
