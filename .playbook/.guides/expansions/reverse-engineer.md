# Reverse Engineer Expansion
# File: .playbook/.guides/expansions/reverse-engineer.md

The Reverse Engineer expansion maps what an existing codebase actually does
before any architectural decisions are made. It is the foundation for all
adoption and rewrite work on existing projects.

Activate this expansion in `.kit/01-process.md` before starting:

```
Active Expansions:
  reverse-engineer: inventory
```

---

## Why This Exists

You cannot design what should exist without understanding what does exist.
Not what the system was designed to do -- what it actually does right now.

Every assumption about existing code that turns out to be wrong costs time,
money, and stability. This expansion eliminates assumptions before they
become problems.

---

## What It Produces

Three documents in `docs/rewrite/`:

| Document | What It Contains |
|---|---|
| `system-map.md` | What the codebase does, where it lives, what depends on what |
| `decision-record.md` | Classification per component with reasoning |
| `risk-register.md` | What breaks if X changes, dependency chains, migration constraints |

For large projects, any document can split into a sub-folder:

```
docs/rewrite/
  system-map/
    _index.md
    frontend.md
    backend.md
    integrations.md
    data-models.md
```

Same rule as flows and contracts -- `_index.md` references all parts.

---

## Classification System

Every component receives one classification in the decision record:

| Classification | Meaning |
|---|---|
| `Keep` | Works as-is, no changes needed |
| `Align` | Works but needs to conform to new architecture or standards |
| `Extract` | Good logic in the wrong place -- move it out cleanly |
| `Rewrite` | Logic is wrong, unclear, or too coupled -- rebuild from scratch |
| `Replace` | Responsibility stays but the tool changes |
| `Discard` | No longer needed -- remove entirely |
| `Defer` | Decision not yet possible -- needs more information |
| `Evaluate` | Uncertain -- needs investigation before classifying |

---

## Phases

The expansion runs in six sequential phases.
Each phase requires human review before advancing.

### Phase 1 -- Inventory

**Goal:** Map what exists.

Upload all available context -- README, package.json, source files,
any existing docs -- then send:

```
Run an inventory of this codebase.

Map and document:
1. All applications or services and what they do
2. All major modules, components, and services within each app
3. All external integrations and what they provide
4. The data model -- what data exists and how it is structured
5. Entry points -- how the system is accessed (API, UI, CLI, webhooks)
6. Dependency relationships -- what depends on what
7. Tech stack -- frameworks, libraries, tools, versions

Produce a draft system-map.md. Use plain language.
Flag anything that is unclear or needs investigation.
Wait for review before proceeding.
```

**Output:** Draft `docs/rewrite/system-map.md`
**Advance when:** System map reviewed and approved.

---

### Phase 2 -- Audit

**Goal:** Understand the quality and structure of what exists.

```
Audit the codebase against the system map.

For each major component or module, identify:
1. What it actually does vs what it appears designed to do
2. What other parts depend on it
3. Whether it has clear boundaries or mixed responsibilities
4. Known issues, tech debt, or fragility
5. Whether it has tests or documentation

Flag:
- God components -- things doing too many jobs
- Mixed concerns -- business logic in the wrong layer
- Tight coupling -- things that cannot change independently
- Hidden dependencies -- things that rely on undeclared state or globals
- Outdated dependencies -- libraries significantly behind current versions

Update system-map.md with findings.
Wait for review before proceeding.
```

**Output:** Updated `docs/rewrite/system-map.md`
**Advance when:** Audit findings reviewed and approved.

---

### Phase 3 -- Classify

**Goal:** Assign a classification to every major component.

```
Using the audited system map, classify every major component.

Classifications:
- Keep      -- works as-is, no changes needed
- Align     -- works but needs to conform to new standards
- Extract   -- good logic in the wrong place
- Rewrite   -- wrong or too coupled, rebuild from scratch
- Replace   -- responsibility stays, tool changes
- Discard   -- no longer needed
- Defer     -- decision not yet possible
- Evaluate  -- needs investigation

For each component, document:
- Classification
- Reasoning -- why this classification
- Dependencies affected -- what else is impacted by this decision
- Risk level -- low | medium | high | critical

Produce a draft decision-record.md.
Wait for review before proceeding.
```

**Output:** Draft `docs/rewrite/decision-record.md`
**Advance when:** Every component classified, record reviewed and approved.

---

### Phase 4 -- Risk Map

**Goal:** Understand what breaks if X changes.

```
Using the decision record, build a risk register.

For every component classified as Extract, Rewrite, Replace, or Discard:
1. What currently depends on it?
2. What breaks if it changes or disappears?
3. What must change before it can be safely modified?
4. Are there circular dependencies that complicate migration?
5. Is there a safe migration order?

Identify:
- Critical risks -- changes that could break the entire platform
- High risks -- changes that break significant functionality
- Medium risks -- changes that break specific features
- Migration constraints -- ordering rules the rewrite must respect

Produce a draft risk-register.md.
Wait for review before proceeding.
```

**Output:** Draft `docs/rewrite/risk-register.md`
**Advance when:** Risk register reviewed and approved.

---

### Phase 5 -- Gap Report

**Goal:** Identify what is missing, broken, or undocumented.

```
Using the system map, decision record, and risk register, produce a gap report.

Identify:
1. Missing -- functionality needed but not yet existing
2. Broken -- things that do not work correctly right now
3. Undocumented -- things with no documentation, spec, or clear owner
4. Security concerns -- anything that represents a security risk
5. Performance concerns -- anything known to degrade under load

Format as a prioritized list. Flag anything that must be resolved
before the rewrite can begin safely.
Wait for review before proceeding.
```

**Output:** Gap report section appended to `docs/rewrite/system-map.md`
**Advance when:** Gap report reviewed and approved.

---

### Phase 6 -- Handoff

**Goal:** Feed all findings into the Playbook pipeline.

```
The reverse engineering phase is complete.
Package all findings for the Context Kit.

1. From system-map.md:
   Extract open topics for .kit/02-exploration.md
   -- architectural questions not yet decided
   -- things marked Evaluate needing investigation

2. From decision-record.md:
   Extract closed decisions for .kit/02-exploration.md
   -- every classification is a decision with reasoning

3. From risk-register.md:
   Extract known issues for .kit/03-execution.md
   -- every critical and high risk becomes a known issue
   -- include type, severity, and blocked-by for each

4. Update .kit/01-process.md:
   -- Active Expansions: reverse-engineer: handoff (complete)
   -- Last Completed: RE expansion handoff
   -- Next: Context Kit exploration

Generate updated kit files as markdown code blocks for review.
```

**Output:** Updated `.kit/02-exploration.md`, `.kit/03-execution.md`, `.kit/01-process.md`
**Expansion complete when:** All three kit files updated and reviewed.

---

## Rules

- Never advance a phase without human review and approval
- Never begin DDA docs while in inventory, audit, or classify phase
- Every component must be classified before risk-map phase begins
- Handoff is not complete until rewrite docs are referenced in kit files
- system-map.md, decision-record.md, and risk-register.md belong to the project --
  they live in docs/rewrite/, not in .playbook/

---

## After the Expansion

When handoff is complete:

- Update `01-process.md` Active Expansions to reflect completion
- Continue to Context Kit exploration using the fed-in decisions
- PRD can now be drafted with full knowledge of what exists
- Flows and contracts can reference what is being kept vs replaced
- DDA docs reflect the new architecture, not the old one

The rewrite proceeds through the standard pipeline from this point.
