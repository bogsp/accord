---
id: [flow-N-kebab-name]
type: flow
scope: [journey | sequence | operation | composite | compensation]
trigger: [request-response | event-driven | background | reactive | webhook | stream]
status: draft
triggers: []
calls: []
emits: []
called-by: []
---

## What It Does
Plain language description of this flow's purpose and outcome.
Any reader understands this without technical knowledge.

## Pre-conditions
What must be true before this flow can start.

## Post-conditions
What is guaranteed to be true when this flow completes successfully.

## Walkthrough

Narrative description of what happens at each stage — what decisions are made,
what can go wrong, what the system guarantees. Written for any reader but
precise enough for a developer to follow.

---

1. On [TriggerEvent](input):
   a. [componentName].[method](input)
      → ok: [ResultType]     → continue to b
      → err: [ErrorType]     → emit [FailureEvent], halt
   b. [componentName].[method](result)
      → ok: [ResultType]     → continue to c
      → err: [ErrorType]     → compensate, emit [FailureEvent]

---
_(Add sections below based on scope and trigger type)_

## Compensation
_(Required for Journey and Sequence scope)_
What happens when the flow fails mid-way and state needs unwinding.

## Child Flows
_(Required for Journey and Composite scope)_
- `[flow-id]` — [what it handles]

## Event Contract
_(Required for Event-Driven trigger)_
Event schema and delivery guarantees.

## Schedule
_(Required for Background/Scheduled trigger)_
When and how often this flow runs.

## Timeout Behavior
_(Required for Background/Scheduled trigger)_
What happens when the flow exceeds its time budget.

## Subscription Contract
_(Required for Reactive and Stream triggers)_
What is subscribed to and what constitutes a triggering change.

## Disconnect Behavior
_(Required for Reactive and Stream triggers)_
What happens when the connection drops mid-flow.

## Reconnect Behavior
_(Required for Stream trigger)_
How and when reconnection is attempted.

## Payload Contract
_(Required for Webhook trigger)_
Expected payload schema and validation rules.

## Retry Behavior
_(Required for Webhook trigger)_
Retry strategy, backoff, and failure handling.

## Detail
_(Optional — present only for concurrency rules, known constraints, or quirks
that cannot be inferred from the walkthrough)_

---
_N/A — section considered and does not apply_
_TODO — section not yet documented; blocks status: active_
