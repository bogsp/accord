# Playbook Pipeline
# File: .playbook/pipeline.md

> The complete reference for the Playbook pipeline.
> This document explains what each stage is and why it exists.
> For how AI implements it, read `.playbook/00-rules.md`.

---

## Table of Contents

1. [The Pipeline](#1-the-pipeline)
2. [Core Principle](#2-core-principle)
3. [Expansions](#3-expansions)
4. [Stage 0 — Reverse Engineer](#4-stage-0--reverse-engineer)
5. [Stage 1 — Context Kit](#5-stage-1--context-kit)
6. [Stage 2 — PRD and Specs](#6-stage-2--prd-and-specs)
7. [Stage 3 — Flows](#7-stage-3--flows)
8. [Stage 4 — Contracts](#8-stage-4--contracts)
9. [Stage 5 — DDA](#9-stage-5--dda)
10. [Stage 6 — Code](#10-stage-6--code)
11. [Stage 7 — Rollout](#11-stage-7--rollout)
12. [Adopting an Existing Project](#12-adopting-an-existing-project)
13. [Phase Completion Checklist](#13-phase-completion-checklist)
14. [Quick Reference](#14-quick-reference)

---

## 1. The Pipeline

```
[Reverse Engineer] -> Context Kit -> PRD -> Flows -> Contracts -> DDA -> Code -> Rollout
```

[Reverse Engineer] is conditional -- only for existing projects.
New projects start at Context Kit.

| Stage | What It Does | Where It Lives | Conditional |
|---|---|---|---|
| Reverse Engineer | Maps what exists before building what should | docs/rewrite/ | Existing projects only |
| Context Kit | Captures thinking, decisions, and progress | .kit/ | Always |
| PRD | Defines what to build and why | docs/ | Always |
| Flows | Defines runtime paths | docs/flows/ | Always |
| Contracts | Defines implementation boundaries | docs/contracts/ | Always |
| DDA | Defines structure and relationships | .playbook/ | Always |
| Code | Implementation against contracts | src/ | Always |
| Rollout | Generated milestone snapshots | docs/rollout/ | Always |

Playbook is a dev-dependency. .playbook/ and .kit/ are Playbook's world.
Everything else belongs to the project.

---

## 2. Core Principle

### Progressive Disclosure + Tiering

Every format, template, and structure in Playbook follows this principle:

Progressive Disclosure -- surface the simplest, most human-readable version
first. Add technical depth below it. Never bury the plain language version.
The same document serves two audiences without sacrificing either.

Tiering -- define a required core, then add sections only when the scope
or type demands it. Never burden simple cases with complex requirements.
Never let complex cases skip required coverage.

This applies to everything: document formats, flow templates, contract sections,
ticket types, QA test cases, kit file sections, and adoption guides.

When any structural decision needs to be made -- apply this principle first.

---

## 3. Expansions

Expansions are optional pipeline stages that extend Playbook for specific needs.
They plug in around the core pipeline without changing it.

| Expansion | Plugs In | When to Use |
|---|---|---|
| Reverse Engineer | Before Context Kit | Existing or legacy projects |
| Video Insights | During Context Kit exploration | Research-driven ideation |

Activating an expansion:
Declare it in .kit/01-process.md under Active Expansions before use.
Each expansion has its own guide in .playbook/.guides/expansions/.

---

## 4. Stage 0 -- Reverse Engineer

Only active when the Reverse Engineer expansion is declared in 01-process.md.
New projects skip this stage entirely.

### Why This Stage Exists

Existing codebases cannot be adopted or rewritten without first understanding
what they actually do. Not what they were designed to do -- what they do right now.

Skipping this stage means making architectural decisions based on assumptions.
Every assumption that turns out wrong costs time, money, and stability.

### What It Produces

Three documents in docs/rewrite/:

| Document | What It Contains |
|---|---|
| system-map.md | What the codebase does, where it lives, what depends on what |
| decision-record.md | Classification per component with reasoning |
| risk-register.md | What breaks if X changes, dependency chains, migration constraints |

For large projects, any of these can split into sub-folders:

docs/rewrite/
  system-map.md              -- small project
  system-map/                -- large project
    _index.md
    frontend.md
    backend.md
    integrations.md
    data-models.md

### Classification System

Every component receives one classification:

| Classification | Meaning |
|---|---|
| Keep | Works as-is, no changes needed |
| Align | Works but needs to conform to new architecture or standards |
| Extract | Good logic in the wrong place -- move it out cleanly |
| Rewrite | Logic is wrong, unclear, or too coupled -- rebuild from scratch |
| Replace | Responsibility stays but the tool changes |
| Discard | No longer needed -- remove entirely |
| Defer | Decision not yet possible -- needs more information |
| Evaluate | Uncertain -- needs investigation before classifying |

### Internal Phases

1. Inventory    Scan and map what exists
2. Audit        Identify what each piece does and what depends on it
3. Classify     Assign a classification to every major component
4. Risk Map     What breaks if X changes
5. Gap Report   What is missing, broken, or undocumented
6. Handoff      Package outputs for Context Kit

### Handoff to Context Kit

Not complete until all three documents feed the kit files:

docs/rewrite/system-map.md       -> .kit/02-exploration.md open topics
docs/rewrite/decision-record.md  -> .kit/02-exploration.md closed decisions
docs/rewrite/risk-register.md    -> .kit/03-execution.md known issues

Full guide: .playbook/.guides/expansions/reverse-engineer.md

---

## 5. Stage 1 -- Context Kit

The Context Kit is the project management layer. It captures thinking, locks
decisions, and tracks progress across sessions.

### Files

| File | Job | Updated |
|---|---|---|
| .kit/01-process.md | Session spark | Every session |
| .kit/02-exploration.md | Active decisions | When decisions change |
| .kit/03-execution.md | Guidelines, plan, tasks, known issues | When work changes |
| .kit/04-progress.md | Append-only log | Every session |
| .kit/05-kanban.md | Ticket breakdown (optional) | When tickets change |
| .kit/06-qa.md | Test registry and sign-off (optional) | When tests change |

### Supporting Folders

| Folder | Contains |
|---|---|
| .kit/_refs/ | Supporting material for 02-exploration.md |
| .kit/_tests/ | One file per QA test case |
| .kit/_archives/ | Lean history |

### Session Rhythm

Start   -> upload kit files (2 messages)
Work    -> focus on Current State only
End     -> update 01-process.md + 04-progress.md before closing

---

## 6. Stage 2 -- PRD and Specs

Defines what to build and why. Upstream input for flows, contracts, and DDA.

What belongs here: goals, outcomes, features, acceptance criteria, constraints.
What does not: runtime paths, implementation rules, component relationships, code.

---

## 7. Stage 3 -- Flows

Defines every major runtime path before any component is designed.
Every hour in flows prevents hours of debugging.

### Flow Taxonomy

By scope: Journey | Sequence | Operation | Composite | Compensation
By trigger: Request-Response | Event-Driven | Background | Reactive | Webhook | Stream

Core sections required for every flow:
- What It Does -- plain language
- Pre-conditions
- Post-conditions
- Walkthrough -- narrative above divider, call chain below

Tier additions by scope and trigger -- see .playbook/00-rules.md for full table.

N/A -- considered, does not apply.
TODO -- not documented; blocks status: active.

---

## 8. Stage 4 -- Contracts

Extracts implementation rules from flows. All behavior lives here.
Component docs point to contracts -- never contain behavior.

### Required Sections -- Flow Contracts

- Happy Path
- Failure Modes
- Edge Cases
- Concurrency Rules
- Constraints

### Required Sections -- global.md

- Architecture Rules
- Auth Boundaries
- Error Handling Rules
- Audit Requirements
- Cross-Cutting Constraints

Read global.md before any flow-specific contract. Always.

---

## 9. Stage 5 -- DDA

Defines every component before any code is written.
The document is the contract. Code follows it.

### Document Types

| Type | Owns |
|---|---|
| structure | Shape and manifest |
| sequencer | Runtime flow coordination |
| component | Logic unit |
| concern | Cross-cutting capability |
| integration | External contract |

Sequencer vs component rule:
- Sequencer: receives a trigger, orders other units, routes work, or branches by outcome
- Component: performs one bounded leaf responsibility
- Sequencers own sequence, not leaf logic
- Leaf work inside a sequencer must be extracted into a component with its own DDA doc and implementation file

### Clean Architecture Layers

Every component and sequencer DDA doc declares `layer` and lives under the
matching layer folder:

| Layer | DDA Scope |
|---|---|
| domain | Pure business rules, entities, value objects |
| application | Use cases, sequencers, orchestration, port definitions |
| infrastructure | Adapter implementations and external I/O |
| interface | HTTP, CLI, UI, route composition, framework entrypoints |

```text
.playbook/apps/<app>/<layer>/<component-or-sequencer>.md
.playbook/packages/<package>/<layer>/<component-or-sequencer>.md
```

The implementation file must match the declared layer. If it does not, the DDA
stays draft until the code is extracted or the exception is explicitly approved.

### Document Structure

Every DDA doc follows progressive disclosure:

What It Does    -- plain language, any audience
Flow            -- required for sequencers; trigger, call chain, branches
Interface       -- required for components; contract reference + signature
Detail          -- edge cases, constraints -- omit if nothing fits

---

## 10. Stage 6 -- Code

Code is generated against Playbook contracts. The doc is the source of truth.

### What Compliant Code Means

Code that passes the Phase Completion Checklist:

- All Biome checks pass -- lint, format, imports
- TypeScript strict mode clean -- no any, no !, no inferred public return types
- All arrays in Domain and Application typed as readonly
- No input parameters mutated -- new objects returned
- No new Date() in Domain or Application -- clock abstraction injected
- All dates stored and transmitted in UTC
- No raw throw in Domain or Application -- Result<T, E> used throughout
- No stub implementations -- no hardcoded returns, artificial delays, placeholders
- No partial implementations -- every path handled: happy, error, and edge cases
- No secrets hardcoded -- all via validated environment variables
- No client-supplied IDs trusted for authorization without session verification
- Domain coverage 100%, Application coverage 90%+

### AI Boot Sequence

1. .playbook/00-rules.md
2. docs/code-standards.md
3. docs/flows/_index.md
4. docs/contracts/_index.md
5. .playbook/_index.md
6. Module and component docs as needed

---

## 11. Stage 7 -- Rollout

Generated artifacts -- never authored manually.

Two purposes:
1. Human-facing -- deployment guide, release notes, migration steps
2. AI-facing -- full project context snapshot for a fresh session

---

## 12. Adopting an Existing Project

For all adoption paths -- new, simple, standard, or legacy:

.playbook/.guides/adopt.md

The guide uses a short decision tree to route you to the right process
based on your project's actual state. No assumptions. No rigid tiers.

---

## 13. Phase Completion Checklist

### Development Phase

- [ ] biome check -- zero errors
- [ ] biome format --write -- zero diffs
- [ ] vitest run -- all tests pass, coverage gates met (Domain 100%, Application 90%+)
- [ ] No stub implementations -- no hardcoded returns, artificial delays, or placeholder bodies
- [ ] No partial implementations -- all branches handled: happy path, errors, and edge cases
- [ ] All arrays in Domain and Application typed as readonly
- [ ] No input parameters mutated -- new objects returned throughout
- [ ] No new Date() in Domain or Application -- clock abstraction injected
- [ ] All dates stored and transmitted in UTC
- [ ] No raw throw in Domain or Application -- Result<T, E> used throughout
- [ ] No secrets hardcoded -- all via validated environment variables
- [ ] Playbook sync -- all touched .playbook/ docs reflect current code
- [ ] Folder trees updated -- all _index.md Structure sections current
- [ ] Git commits -- grouped by scope, all changes pushed
- [ ] Kit files updated -- 01-process.md and 04-progress.md
- [ ] Rollout doc generated -- if a milestone was completed

### Any Phase

- [ ] Decisions logged in 02-exploration.md
- [ ] Progress logged in 04-progress.md
- [ ] Current State updated in 01-process.md
- [ ] Active modules and expansions declared in 01-process.md

---

## 14. Quick Reference

### Naming Conventions

| Item | Convention | Example |
|---|---|---|
| Files | kebab-case.md | invoice-validator.md |
| IDs | match filename | id: invoice-validator |
| Folders | kebab-case | billing/ |
| Reserved folders | leading underscore | _concerns/ |
| Structure docs | always _index.md | billing/_index.md |
| Events | PascalCase | InvoicePosted |
| Commits | scoped imperative | playbook: add billing module |

### Classification System (Reverse Engineer expansion)

| Classification | Meaning |
|---|---|
| Keep | Works as-is, no changes needed |
| Align | Works but needs to conform to new standards |
| Extract | Good logic in the wrong place -- move it out |
| Rewrite | Logic is wrong or too coupled -- rebuild |
| Replace | Responsibility stays, tool changes |
| Discard | No longer needed -- remove |
| Defer | Decision not yet possible |
| Evaluate | Needs investigation before classifying |

### Cross-Cutting Standards (always enforced)

| Area | Rule |
|---|---|
| Immutability | ReadonlyArray in Domain/Application; no input mutation; no public setters |
| Date and Time | UTC only; inject clock; no new Date() in Domain/Application; Interface layer formats |
| Error Handling | Result<T, E> in Domain/Application; no raw throw; no silent swallowing |
| Logging | Structured JSON; trace ID always present; no secrets or PII logged |
| Security | No hardcoded secrets; parameterized queries; no client ID trust for auth |
| Implementation | No stubs; no partial paths; all branches explicit |
| Sequencing | Coordinators are sequencers; leaf work is extracted to documented components |
| Layers | Component/sequencer DDA declares layer; DDA path and implementation path match layer |

---

The doc is the contract. The code follows.
