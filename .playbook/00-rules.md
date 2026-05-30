# Playbook Rules
# File: .playbook/00-rules.md

---
version: 2.1
requires: docs/code-standards.md
---

> Always load docs/code-standards.md alongside this file.
> This file defines how to implement. pipeline.md defines what and why.
> Neither is complete without the other.

---

## Core Principle

Progressive Disclosure + Tiering is the default for every structural decision:
- Lead with plain language. Add technical depth below.
- Define a required core. Add sections only when scope or type demands them.
- Never burden simple cases with complex requirements.
- Never let complex cases skip required coverage.

When any format, template, or structure decision needs to be made -- apply this first.

---

## Folder Structure

.playbook/
  README.md              -> orientation and journey matrix
  pipeline.md            -> complete pipeline reference
  00-rules.md            -> this file; AI execution layer
  _index.md              -> project manifest and living folder tree
  _concerns/             -> cross-cutting capabilities
  _integrations/         -> third-party services and libraries
  _shared/               -> components used across multiple modules
  .guides/               -> adoption and setup guides
    adopt.md             -> all adoption paths, decision-tree routed
    new-project.md       -> new project guide
    expansions/          -> expansion-specific guides
      reverse-engineer.md
docs/
  code-standards.md      -> always loaded with this file
  flows/
    _index.md            -> entry point and dependency map
    global.md            -> rules spanning every flow
    flow-N-name.md       -> one file per flow
  contracts/
    _index.md            -> entry point
    global.md            -> system-wide rules; read first always
    flow-N-name.md       -> companion to flows/flow-N-name.md
  rewrite/               -> reverse engineer expansion outputs
    system-map.md
    decision-record.md
    risk-register.md
  rollout/               -> generated snapshots; never edit directly
.kit/
  01-process.md          -> session spark
  02-exploration.md      -> active decisions
  03-execution.md        -> guidelines, plan, tasks
  04-progress.md         -> append-only progress log
  05-kanban.md           -> ticketed work breakdown (optional)
  06-qa.md               -> test registry and sign-off (optional)
  _refs/                 -> supporting material
  _tests/                -> one file per QA test case
  _archives/             -> lean history

---

## AI Load Order

Every dev session loads in this order:

1. .playbook/00-rules.md
2. docs/code-standards.md
3. docs/flows/_index.md
4. docs/contracts/_index.md
5. .playbook/_index.md
6. .playbook/[module]/_index.md   (as needed)
7. Component and sequencer docs   (task-scoped)
8. _concerns/ + _integrations/    (cross-cutting + boundaries)

When Reverse Engineer expansion is active, also load:
  docs/rewrite/system-map.md
  docs/rewrite/decision-record.md
  docs/rewrite/risk-register.md

Scoped packages -- load only what the task needs:

| Task | Load |
|---|---|
| New component | 00-rules.md + code-standards.md + module _index.md + parent doc + flow + contract + _concerns/ |
| Bug fix | 00-rules.md + code-standards.md + component doc + its connects + concerns |
| Write tests | Component doc + code-standards.md testing section |
| New DDA doc | Relevant flow + contract + parent _index.md |
| Rollout doc | Full .playbook/ tree + .kit/04-progress.md |
| RE expansion | system-map.md + decision-record.md + risk-register.md |

---

## Expansions

Expansions are optional pipeline stages declared in .kit/01-process.md.

### Declaring an expansion

Simple (single-phase):
  Active Expansions: video-insights

Multi-phase:
  Active Expansions:
    reverse-engineer: inventory

### Reverse Engineer expansion phases
  inventory | audit | classify | risk-map | gap-report | handoff

Phase advances when its outputs are complete and reviewed.
Expansion is complete when handoff feeds .kit/02-exploration.md and .kit/03-execution.md.

### Expansion behavioral rules
1. Never advance a phase without human review and approval
2. Never begin DDA docs while RE expansion is in inventory, audit, or classify phase
3. Classification must be assigned to every component before risk-map phase begins
4. Handoff is not complete until rewrite docs are referenced in kit files

---

## Document Types

### Universal Frontmatter

id:      # kebab-case -- must match filename
export:  # exported name -- component and sequencer only
file:    # repo path -- component and sequencer only
layer:   # domain | application | infrastructure | interface -- component and sequencer only
type:    # component | sequencer | structure | concern | integration
status:  # draft | active | deprecated

---

### Clean Architecture Layer

Every component and sequencer DDA doc declares its layer. The declared layer
must match the DDA path, implementation file, and import direction.

| Layer | Owns | May Depend On | Must Not Depend On |
|---|---|---|---|
| domain | Entities, value objects, pure rules | nothing project-specific | application, infrastructure, interface |
| application | Use cases, sequencers, ports, orchestration | domain, declared ports | infrastructure implementations, interface |
| infrastructure | Adapter implementations, external I/O | application ports, domain types | interface |
| interface | HTTP, CLI, UI, route composition, framework entrypoints | application APIs | infrastructure internals |

Sequencers usually belong in the application layer. Interface-layer files may
instantiate or call sequencers, but must not hide application sequencing or leaf
component logic. If a sequencer currently points at an interface file, mark the
DDA draft until the application sequencer is extracted or explicitly justified.

DDA files must sit under the matching layer folder:

```text
.playbook/apps/<app>/<layer>/<component-or-sequencer>.md
.playbook/packages/<package>/<layer>/<component-or-sequencer>.md
```

Keep module/package `_index.md` files at the module root. Their links must point
to the layer folders so the DDA tree mirrors the implementation architecture.

---

### structure

Project, app, or module manifest. Always includes Structure section --
a linked annotated tree of everything in scope.

id: billing
type: structure
scope: module
status: active
entry: [billing-sequencer]
contains: [billing-sequencer, invoice-validator, invoice-factory]
integrations: [stripe]
concerns: [logging, auth, error-handling]

## What It Does
Plain language description of this module's purpose.

## Structure
- [_index.md](./_index.md) -- this file; billing module manifest
- [billing-sequencer.md](./billing-sequencer.md) -- coordinates invoice creation
- [invoice-validator.md](./invoice-validator.md) -- validates structure and rules

---

### sequencer

Runtime coordinator. Owns flow and sequencing, not business logic.
Always references docs/flows/ and docs/contracts/ -- never restates them.
Always includes Flow section with full call chain and all branches.

Use a sequencer when a unit receives a trigger and orders other units,
routes work to multiple handlers, or branches by outcome. Sequencers may
validate sequencing preconditions and map outcomes, but they must not hide
leaf responsibilities in their own code. Any meaningful leaf operation
(calculation, validation, storage, rendering, adapter call, parsing, policy
decision) gets its own component DDA doc and implementation file, then appears
in the sequencer's sequence.

id: billing-sequencer
export: BillingSequencer
file: src/billing/application/billing-sequencer.ts
layer: application
type: sequencer
status: active
parent: billing
triggers: [CreateInvoiceCommand]
sequence: [invoice-validator, invoice-factory, invoice-repository]
emits: [InvoiceCreated, InvoiceFailed]
concerns: [logging, auth]

## What It Does
Coordinates the full invoice creation flow. Owns sequence, not logic.

## Flow
References: docs/flows/flow-1-billing.md + docs/contracts/flow-1-billing.md

1. On CreateInvoiceCommand(input):
   a. invoiceValidator.validate(input)
      -> ok: InvoiceValidated     -> continue to b
      -> err: ValidationError     -> emit InvoiceRejected, halt
   b. invoiceFactory.create(validated)
      -> ok: InvoiceDraft         -> continue to c
      -> err: FactoryError        -> emit InvoiceFailed, halt
   c. invoiceRepository.save(draft)
      -> ok: InvoiceCreated       -> emit InvoiceCreated
      -> err: PersistenceError    -> compensate, emit InvoiceFailed

---

### component

Single unit of logic. One responsibility. Returns typed Result.
References contract for behavior definition.

Use a component when a unit performs one bounded responsibility and does not
own a multi-step runtime path. A component may call declared ports or child
components, but it should not coordinate a flow. If it starts receiving
triggers, routing between handlers, or sequencing multiple components, convert
it to a sequencer and extract its leaf responsibilities into components.

id: invoice-validator
export: InvoiceValidator
file: src/billing/domain/invoice-validator.ts
layer: domain
type: component
status: active
parent: billing-sequencer
children: [line-item-checker, tax-calculator]
connects: [invoice-repository]
emits: [InvoiceValidated, InvoiceRejected]
concerns: [logging, error-handling]

## What It Does
Checks whether an invoice is valid before it gets created.
If anything is wrong it stops the process and explains why.

## Interface
Contract: docs/contracts/flow-1-billing/validation.md#failure-modes

input:  CreateInvoiceInput
output: Result<InvoiceValidated, ValidationError>

## Detail
(Edge cases, concurrency constraints, known quirks only.
Omit entirely if nothing fits.)

---

### concern

id: logging
type: concern
status: active
affects: all
provided-by: internal

## What It Does
Structured logging available to all components via DI container.
Never called directly -- always injected.

---

### integration

id: stripe
type: integration
kind: external-service
status: active
used-by: [billing-sequencer]
config: [STRIPE_SECRET_KEY, STRIPE_WEBHOOK_SECRET]
docs: https://stripe.com/docs

## What It Does
Handles payment processing and webhook events.
All interactions go through BillingSequencer only.

---

## Flow Document Format

id: flow-1-billing
type: flow
scope: sequence
trigger: request-response
status: draft
triggers: [CreateInvoiceCommand]
calls: []
emits: [InvoiceCreated, InvoiceFailed]
called-by: []

Core sections -- required for every flow:
- What It Does
- Pre-conditions
- Post-conditions
- Walkthrough (narrative + call chain separated by ---)

Additional sections by scope:

| Scope | Add |
|---|---|
| Journey | Compensation, Child Flows |
| Sequence | Compensation |
| Composite | Child Flows |

Additional sections by trigger:

| Trigger | Add |
|---|---|
| Event-Driven | Event Contract |
| Background/Scheduled | Schedule, Timeout Behavior |
| Reactive | Subscription Contract, Disconnect Behavior |
| Webhook | Payload Contract, Retry Behavior |
| Stream | Subscription Contract, Disconnect Behavior, Reconnect Behavior |

Detail -- always optional.
N/A -- considered, does not apply.
TODO -- not documented; blocks status: active.

---

## Contract Document Format

Flow contract -- required sections:
- Happy Path
- Failure Modes
- Edge Cases
- Concurrency Rules
- Constraints

global.md -- required sections:
- Architecture Rules
- Auth Boundaries
- Error Handling Rules
- Audit Requirements
- Cross-Cutting Constraints

Same N/A / TODO discipline. TODO blocks status: active.

Component docs reference contracts with precision:
  Contract: docs/contracts/flow-1-billing/validation.md#failure-modes

---

## Reverse Engineer Document Format

### system-map.md

What the codebase currently does -- not what it was designed to do.

Required sections:
- Overview -- plain language summary
- Applications -- what apps or services exist
- Data Models -- what data exists and how it is structured
- Integrations -- every external service and what it does
- Dependency Map -- what depends on what
- Entry Points -- how the system is accessed

### decision-record.md

Classification per component with reasoning.

Required sections:
- Summary -- counts per classification
- Decisions -- one entry per component:
  Component, Classification, Reasoning, Dependencies Affected, Risk

### risk-register.md

What breaks if X changes.

Required sections:
- Critical Risks -- things that break everything
- High Risks -- things that break significant functionality
- Medium Risks -- things that break specific features
- Migration Constraints -- ordering rules for the rewrite

---

## Behavioral Rules

1.  Never write a DDA doc without first reading the relevant flow and contract
2.  Never write code for a component without its Playbook doc
3.  Never add a dependency not declared in connects or integrations
4.  Never create a component that duplicates an existing responsibility
5.  Always implement against the declared interface in the doc
6.  Flag any doc with status: draft before coding against it
7.  Suggest _shared/ when a component appears in multiple modules
8.  Run sync mode before closing any task
9.  Confirm all touched Playbook docs are current before every PR
10. Cross-check new components against _shared/ before creating module-level ones
11. Flag missing connects, emits, or children as TODO during generation
12. Update the living folder tree in _index.md when components change
13. Flows and contracts must be approved before any DDA doc is authored
14. Apply Progressive Disclosure + Tiering to every structural decision
15. Prefer native implementations over external libraries
16. External dependencies require explicit justification before adoption
17. Never advance RE expansion phases without human review and approval
18. Never begin DDA docs while RE expansion is in inventory, audit, or classify
19. All arrays passed into or returned from Domain and Application functions must be typed as readonly
20. Never mutate input parameters -- always return new objects
21. Never call new Date() in Domain or Application layer -- inject a clock abstraction
22. Always store and transmit dates in UTC -- never with a timezone offset
23. Never stub implementations -- no hardcoded returns, artificial delays, or placeholder bodies
24. Never leave any code path unhandled -- all branches must be explicit: happy path, errors, edge cases
25. Stop and flag before writing any code when a requirement is unclear, a path is undefined, or a decision is needed
26. Classify trigger-driven coordinators as sequencers, not components
27. Never hide leaf component responsibilities inside sequencer code -- extract each leaf into its own DDA doc and implementation file
28. Every item in a sequencer sequence must resolve to an active component/sequencer DDA doc or be explicitly marked draft/TODO before coding
29. Every component and sequencer DDA doc must declare a Clean Architecture layer
30. DDA file paths must live under the declared layer folder
31. Implementation file paths must match the declared layer; otherwise mark the doc draft and extract or justify before coding

---

## Uncertainty Protocol

When any of the following occur, stop all work and flag before writing any code:

- A requirement is unclear or underspecified
- A flow path is not defined in the DDA doc or contract
- A dependency is needed but not declared in the component doc
- A decision would affect more than the current component
- The correct behavior cannot be inferred without making assumptions

Flag format:
```
[FLAG] Category: description
       Options: (if applicable)
       Blocking: what cannot proceed until this is resolved
```

Categories: underspecified | missing-contract | undeclared-dependency | architectural-decision | ambiguous-behavior

---

## Anti-Patterns (Never Do)

**Architecture**
- Cross-layer imports -- Application → Interface, Domain → Infrastructure
- Direct module-to-module imports -- use ModuleServiceGateway
- DDA docs without an explicit Clean Architecture layer for components/sequencers
- Component/sequencer DDA docs stored outside their declared layer folder
- Implementation paths that contradict the declared DDA layer
- Business logic in sequencers or the Interface layer
- Leaf component logic hidden inside sequencers instead of extracted to documented components
- Anemic domain models -- behavior belongs on the domain object, not in a service

**TypeScript**
- any type anywhere in the codebase
- Non-null assertions (!)
- Type assertions (as) outside adapter boundaries
- Inferred return types on public APIs
- Boolean domain or application state -- use enums, dates, or discriminated unions

**Immutability**
- Mutating input parameters -- always return new objects
- Mutable arrays in Domain or Application signatures -- use ReadonlyArray<T>
- Public setters on domain objects -- no direct property mutation

**Date and Time**
- new Date() in Domain or Application -- inject a clock abstraction
- Storing or transmitting dates with timezone offsets -- always UTC
- Comparing dates as strings -- always compare as Date objects or UTC timestamps
- Display formatting or timezone conversion below the Interface layer

**Error Handling**
- Raw throw in Domain or Application layer -- use Result<T, E>
- Swallowing a caught error silently -- always log or return
- Exposing stack traces or internal error details to clients

**Logging**
- Logging passwords, tokens, secrets, or raw PII at any level
- Plain string logs in production -- always structured JSON
- Missing trace ID on any log entry
- Debug logs in production builds

**Security**
- Hardcoded secrets anywhere in the codebase
- Trusting client-supplied IDs for authorization
- String interpolation in query strings -- always parameterized queries
- Sensitive data in URLs or query strings

**Implementation**
- Stub implementations -- hardcoded returns, artificial delays, placeholder bodies
- Partial implementations -- all paths must be handled explicitly
- Magic strings or numbers -- use named constants
- Commented-out code -- delete it; version control preserves history

**AI-Specific**
- Generating code ahead of a DDA doc
- Adding undeclared dependencies or components
- Silently resolving ambiguity with assumptions
- Duplicating an existing component's responsibilities
- Leaving any code path unhandled or silently ignored
- Making architectural decisions unilaterally -- flag and wait

---

## Session Start

Before any work, load in order:
1. .playbook/00-rules.md
2. docs/code-standards.md
3. docs/flows/_index.md
4. docs/contracts/_index.md
5. .playbook/_index.md

If RE expansion is active, also load rewrite docs.
Confirm understanding. Then wait for instruction.

---

## Session End

Before closing:
1. Run Playbook sync check on all docs touched this session
2. Update .kit/04-progress.md -- completions, decisions, fixes
3. Update Current State in .kit/01-process.md
4. Update living folder tree if components changed

---

## Error Recovery

Something broke. Stop all other work.

1. Identify   -- type error | test failure | Playbook drift | layer violation | other
2. Isolate    -- code, Playbook doc, or both?
3. Fix        -- resolve at source; do not patch symptoms
4. Verify     -- re-run checks before continuing
5. Log        -- Fixed entry in .kit/04-progress.md

Context rot -- if AI ignores uploaded files mid-session:
Stop. Re-upload session files.
Re-read .playbook/00-rules.md and docs/code-standards.md.
If rot persists, start a fresh session with a rollout doc as context.
