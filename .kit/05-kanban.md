# KANBAN
# File: .kit/05-kanban.md

## Rules
- Tickets seeded from Acceptance Criteria in `03-execution.md`
- One outcome per ticket — no bundling
- Priority within each column is top-down (top = next)
- Blocked tickets stay in their column — blocking is explicit
- When a ticket moves to Done, log it in `04-progress.md`

## Ticket Types
**Core** _(always available)_: `feature` | `bug` | `task`
**Pipeline** _(when .playbook/ exists)_: `doc` | `sync`
**Project** _(declared in `03-execution.md` guidelines)_: [custom types]

---

## Backlog

### [K-01] Ticket Title
**Type:** feature | bug | task | doc | sync | [custom]
**Description:** What needs to happen and why.
**Ref:** [optional — flow, contract, DDA doc, or _refs/ file]
**Blocked by:** none

---

## In Progress

### [K-02] Ticket Title
**Type:** feature | bug | task | doc | sync | [custom]
**Description:** What needs to happen and why.
**Ref:** [optional]
**Blocked by:** none

---

## Done

### [K-03] Ticket Title
**Completed:** YYYY-MM-DD
