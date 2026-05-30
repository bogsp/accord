# The Context Kit
# File: .playbook/.guides/context-kit.md

The Context Kit is the session and decision layer of the Playbook pipeline.
It keeps humans and AI aligned across every session, every stage, and every
decision from the first idea to deployed code.

It is also a standalone system that works with any AI tool, for any kind of
project. You do not need Playbook to use the Context Kit.

But inside Playbook, the two are inseparable. The kit drives the pipeline.
The pipeline gives the kit its full power.

> For the Context Kit as a standalone system beyond Playbook:
> _The Context Kit_ by Bogs — the complete reference.

---

## What the Context Kit Is

AI has no memory between sessions. Every new chat starts from zero.
Without a system to carry context forward, every session wastes time
re-explaining what was already decided, re-discovering what was already built,
and re-correcting what the AI already got wrong.

The Context Kit solves this with four files, each with one job:

| File | Job |
|---|---|
| `01-process.md` | Session spark — where you are, what comes next |
| `02-exploration.md` | Decision log — what is open, what is closed |
| `03-execution.md` | Work definition — guidelines, plan, tasks, issues |
| `04-progress.md` | Project history — append-only record of everything done |

Two optional modules activate when the work demands them:

| File | Job | When |
|---|---|---|
| `05-kanban.md` | Ticket breakdown | Complex execution with discrete deliverables |
| `06-qa.md` | Test registry and sign-off | Any deliverable needing verification |

---

## How It Fits in the Pipeline

The Context Kit is not a stage that completes and hands off.
It is the persistent layer running beneath every stage.

```
                    Context Kit (always active)
                           |
[RE] -> PRD -> Flows -> Contracts -> DDA -> Code -> Rollout
```

Every stage has a kit touchpoint:

| Stage | Kit Touchpoint |
|---|---|
| Reverse Engineer | RE findings feed `02-exploration.md` and `03-execution.md` on handoff |
| PRD | Goals and acceptance criteria live in `03-execution.md` |
| Flows | Flow decisions close in `02-exploration.md` with Flow: references |
| Contracts | Contract decisions close in `02-exploration.md` |
| DDA | Component decisions close in `02-exploration.md` |
| Code | Progress logs in `04-progress.md`; issues tracked in `03-execution.md` |
| Rollout | Milestone logged in `04-progress.md`; Current State updated in `01-process.md` |

The kit does not contain the pipeline work. It tracks the decisions and
progress that shape it.

---

## The Four Files in Detail

### `01-process.md` — Session Spark

The simplest file. Updated at the end of every session. Read at the start
of the next. Four fields only:

```
Phase:              Exploration | Execution | Progress
Last Completed:     Most recent milestone
Next:               Active goal
Active Modules:     05-kanban | 06-qa | none
Active Expansions:  reverse-engineer: [phase] | none
```

This is the baton pass. When you open a new session, this file tells the
AI exactly where to pick up. Nothing else needed to orient the session.

**Rule:** If it does not change session to session, it does not live here.

---

### `02-exploration.md` — Decision Log

Two states. Nothing else:

**Open Topics** — questions still being explored, options being weighed.
Gets resolved or dropped. Never stays open indefinitely.

**Closed Decisions** — dated, reasoned, permanent. Once closed, never
edited. Only reopened explicitly with a new dated entry explaining why.

```
## Open Topics

**Authentication Approach**
- Evaluating Better Auth vs Clerk vs custom
- Constraint: must work with existing PostgreSQL schema
- Ref: .kit/_refs/research/auth-comparison.md

---

## Closed Decisions

**2026-05-23 -- Database ORM**
- What: Prisma over Drizzle
- Why: Team familiarity, stronger ecosystem for our stack
- Alternatives considered: Drizzle (lighter, faster), raw SQL (too verbose)
- Flow: docs/flows/flow-1-data-access.md
```

**Reference fields:**
- `Ref:` — points to supporting material in `.kit/_refs/`
- `Flow:` — points to the flow that implements or resulted from this decision

**Rule:** If a decision produces a flow, it must reference it.
If a flow exists, it must be traceable to a closed decision.

---

### `03-execution.md` — Work Definition

Three sections:

**Guidelines** — how the work is done. Style, constraints, quality bar.
Declared once, followed every session. Includes:
- Preference for native implementations over external libraries
- External dependency justification requirement
- QA tier declaration
- Custom ticket types if needed

**Plan** — what is being built. Goals, outcomes, scope, acceptance criteria.
Acceptance criteria seed both `05-kanban.md` and `06-qa.md`.

**Action** — what is left to do. Current focus and task list.

Plus a **Known Issues** table — typed, severity-rated, dependency-aware:

```
| # | Issue | Type | Severity | Blocked By | Resolution |
```

Type: `bug | dependency | design | performance | security`
Severity: `critical | high | low`

**Rule:** A known issue without type and severity cannot be assessed.
A known issue without blocked-by cannot be prioritized.

---

### `04-progress.md` — Project History

Append-only. Entries never edited or deleted. Ever.

Three entry types:
- **Completed** — a deliverable or task finished
- **Decided** — a decision made and closed
- **Fixed** — a known issue resolved

One special entry:
- **In Progress** — always the last entry, overwritten each session

```
## 2026-05-23
- **Decided:** Authentication approach -- Better Auth selected
- **Completed:** Flow docs for auth and session management
- **Fixed:** Token expiry not handled in refresh flow
- **In Progress:** Drafting DDA docs for auth module
```

**Rule:** If it happened, it gets logged.
If it did not happen, it does not get added retroactively.

---

## The Supporting Folders

Three folders extend the kit without bulking up the files:

### `.kit/_refs/`

Supporting material referenced by `02-exploration.md`.
Never contains content inline — always pointed to by a `Ref:` field.

```
.kit/_refs/
  video-insights/    -- extracted insights from research videos
  templates/         -- reusable templates
  examples/          -- reference examples
  snippets/          -- code or content snippets
  research/          -- investigation notes
```

### `.kit/_tests/`

One file per QA test case. Referenced by `06-qa.md`.
`06-qa.md` is the registry. `_tests/` owns the detail.

### `.kit/_archives/`

Lean history. Nothing gets deleted — it gets moved.

```
.kit/_archives/
  exploration/       -- deprecated closed decisions
  progress/          -- old phase progress logs
  kanban/            -- done tickets no longer referenced
```

Archive when a file gets unwieldy. Keep main files current and actionable.

---

## The Two-Message Pattern

Every session starts the same way. Two messages. Always.

**Message 1** — upload `01-process.md` + `03-execution.md`

How to work and what is in focus right now.

```
I'm resuming a Playbook project. I'll share files in two messages.

This message: Process + Execution
Next message: Exploration + Progress

Focus only on Current State. Do not reopen closed decisions unless I ask.
Just provide a brief confirmation.
```

**Message 2** — upload `02-exploration.md` + `04-progress.md`

What has been decided and what has been done.

```
Here is the context from earlier work. Just provide a brief confirmation.
```

Add `05-kanban.md` and `06-qa.md` when active.

**Why this order matters:**
Message 1 tells the AI how to work and where to focus.
Message 2 gives it the history and context.
Orientation before context. Always.

---

## Session Rhythm

Every session follows the same rhythm:

```
Start    Upload kit files (2 messages, ~2 minutes)
         AI confirms Current State and waits for instruction

Work     Focus only on what is in Current State
         Log decisions and issues as they arise
         Do not update files mid-session for routine work

End      Update 01-process.md -- Last Completed, Next
         Append to 04-progress.md -- Completed, Decided, Fixed, In Progress
         Update 03-execution.md if tasks or issues changed
         Archive kit files if they are getting long
```

Two minutes of filing at session end prevents twenty minutes of
re-explanation at session start.

**Rule:** Never close a session without updating `01-process.md`
and `04-progress.md`. Everything else is optional. These two never are.

---

## Kit Files and Pipeline Stages

How the kit files connect to each Playbook stage:

### During Reverse Engineer expansion

```
RE inventory findings  -> 02-exploration.md open topics
RE decision record     -> 02-exploration.md closed decisions
RE risk register       -> 03-execution.md known issues
Phase advances         -> 01-process.md Active Expansions updated
```

### During PRD

```
Goals and outcomes     -> 03-execution.md Plan
Acceptance criteria    -> 03-execution.md Plan
PRD approved           -> 04-progress.md Completed entry
                       -> 02-exploration.md closed decision
```

### During Flows and Contracts

```
Flow decisions         -> 02-exploration.md closed decisions with Flow: ref
Contract decisions     -> 02-exploration.md closed decisions
Flows approved         -> 04-progress.md Completed entries
```

### During DDA

```
Component decisions    -> 02-exploration.md closed decisions
Stub docs created      -> 04-progress.md Completed entries
Docs activated         -> 04-progress.md Completed entries
```

### During Code

```
Task progress          -> 03-execution.md Action tasks checked off
Issues found           -> 03-execution.md Known Issues table
Issues resolved        -> 04-progress.md Fixed entries
Tickets               -> 05-kanban.md (if active)
Tests                 -> 06-qa.md + .kit/_tests/ (if active)
```

### During Rollout

```
Milestone complete     -> 04-progress.md Completed entry
Current state updated  -> 01-process.md
```

---

## Declaring Expansions

When an expansion is active, declare it in `01-process.md`:

**Single-phase expansion:**
```
Active Expansions: video-insights
```

**Multi-phase expansion:**
```
Active Expansions:
  reverse-engineer: classify
```

Update the phase as it advances. Mark complete when handoff is done:
```
Active Expansions: reverse-engineer: complete
```

Then clear it next session:
```
Active Expansions: none
```

---

## Keeping Files Lean

Files grow over time. That is normal. The rule is simple:

**Active files are current and actionable.**
**Archives own the history.**

When to archive:

| File | Archive when |
|---|---|
| `02-exploration.md` | Closed decision no longer gates any active task, flow, or component |
| `04-progress.md` | Entries older than the current phase |
| `05-kanban.md` | Done tickets no longer referenced by any active test, flow, or issue |

Every archived file gets a header:

```
# Archive: Exploration Decisions
Date Range: 2026-01-01 to 2026-01-31
Phase: Execution -- auth module
Archived: 2026-02-01
Reason: Decisions no longer active in current phase
```

---

## Context Rot

AI performance degrades in long sessions. Signs of context rot:

- Ignoring Current State in `01-process.md`
- Suggesting ideas already in Closed Decisions
- Breaking formatting or ignoring guidelines
- Favoring its own assumptions over uploaded files

**Recovery:**

```
Stop. Your only source of truth is the files uploaded at session start.
Ignore all prior conversation context.
Re-read .playbook/00-rules.md and docs/code-standards.md before continuing.
```

If rot persists, start a fresh session with a rollout doc as context.

**Prevention:**
- Keep files under 300 lines where possible
- Archive regularly
- Start a new session at major milestones rather than continuing indefinitely

---

## Quick Reference

### File Responsibilities

| File | Owns | Does Not Own |
|---|---|---|
| `01-process.md` | Session state | Rules, conventions, history |
| `02-exploration.md` | Active decisions | Deprecated decisions (archive) |
| `03-execution.md` | Active work definition | Pipeline conventions |
| `04-progress.md` | Project history | Anything not yet done |
| `05-kanban.md` | Active tickets | Task definitions (03-execution.md) |
| `06-qa.md` | Test registry, sign-off | Test detail (_tests/) |

### Entry Types

| Type | File | Rule |
|---|---|---|
| Completed | `04-progress.md` | Something finished |
| Decided | `04-progress.md` | A decision made |
| Fixed | `04-progress.md` | An issue resolved |
| In Progress | `04-progress.md` | Always last, always overwritten |
| Open Topic | `02-exploration.md` | Not yet decided |
| Closed Decision | `02-exploration.md` | Decided, dated, reasoned |
| Known Issue | `03-execution.md` | Typed, severity-rated, blocked-by declared |

### The Non-Negotiables

- `01-process.md` updated every session — no exceptions
- `04-progress.md` append-only — entries never edited or deleted
- Closed decisions stay closed unless explicitly reopened with a new dated entry
- Known issues have type, severity, and blocked-by — or they are not actionable
- Files are current and lean — history lives in `_archives/`

---

_The kit is the memory. The pipeline is the work. Together they are one system._
