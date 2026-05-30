---
id: [kebab-case — matches filename]
export: [ExportedName]
file: [src/module/sequencer.ts]
layer: application
type: sequencer
status: draft
parent: [module]
triggers: []
sequence: []
emits: []
concerns: []
---

## What It Does
Plain language description of what this sequencer coordinates.
Owns sequence, not logic. Any reader understands this without technical knowledge.

Use this doc type when this unit receives a trigger, orders other units,
routes work to multiple handlers, or branches by outcome.

Clean Architecture: sequencers usually live in the application layer. Store
this DDA file under the matching layer folder. If the implementation file is in
interface/server/router code, keep the doc draft until the application
sequencer is extracted or the exception is approved.

Rule: sequencer code must not contain leaf component logic. Extract each
meaningful leaf operation into its own component DDA doc and implementation
file, then list it in `sequence`.

## Flow
References: `docs/flows/[flow-file].md` + `docs/contracts/[contract-file].md`

1. On [TriggerEvent](input):
   a. [componentName].[method](input)
      → ok: [ResultType]     → continue to b
      → err: [ErrorType]     → emit [FailureEvent], halt
   b. [componentName].[method](result)
      → ok: [ResultType]     → continue to c
      → err: [ErrorType]     → compensate, emit [FailureEvent]
   c. [componentName].[method](result)
      → ok: [ResultType]     → emit [SuccessEvent]
      → err: [ErrorType]     → compensate, emit [FailureEvent]

## Detail
_(Only present when concurrency rules, timeouts, or constraints exist
that cannot be inferred from the flow. Omit entirely if not needed.)_

## Leaf Components
_(Optional checklist for generation/review. Every item must have its own DDA
doc and implementation before this sequencer is active.)_
- [ ] [component-id] -- [leaf responsibility]
