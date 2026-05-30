# Starting a New Project with Playbook

# File: playbook/guides/new-project.md

This guide walks through every stage of the Playbook pipeline from scratch.
Follow the stages in order. Do not skip ahead.

---

## Stage 1 — Context Kit (Exploration)

Get your thinking straight before touching any tools.

### What to do

1. Copy the blank kit templates from `.kit/` into a working folder
2. Upload `.kit/01-process.md` + `.kit/03-execution.md` to a new AI session
3. Describe your project in 1–2 sentences and ask AI to populate the files
4. Upload `.kit/02-exploration.md` + `.kit/04-progress.md`
5. Work through open questions until key decisions are locked
6. Review closed decisions — are you ready to define what to build?

Use prompt: **Context Kit — Fresh Project** from `.kit/00-prompts.md`

### Done when

- Project goals are clear
- Key technical decisions are in Closed Decisions
- Current State phase is set to PRD

---

## Stage 2 — PRD and Specs

Define what you are building and why. This is the upstream input for flows,
contracts, and DDA.

### What to do

1. Use closed decisions from `02-exploration.md` as input
2. Ask AI to draft a PRD from your decisions
3. Review and approve the PRD
4. Store it in `docs/` (e.g. `docs/PRD.md`)
5. Log completion in `04-progress.md`

### Done when

- PRD is approved and saved to `docs/`
- Acceptance criteria are clear and verifiable

---

## Stage 3 — Flows + Contracts

Define runtime paths and lock implementation boundaries before any DDA docs
are written. This stage prevents bugs that would otherwise live in the
connections between components.

### What to do

1. Write flow files as small, focused files under `docs/flows/`
   (entry point: `docs/flows/_index.md`)
2. Write a companion contract file under `docs/contracts/` for each flow
   (entry point: `docs/contracts/_index.md`)
3. Always write `docs/contracts/global.md` first — rules that apply everywhere
4. Walk each flow step by step including failure paths — find gaps and race conditions
5. Review flows + contracts together for completeness and consistency
6. Lock flows and contracts in `.kit/01-process.md` before authoring any DDA doc

### Flows vs contracts

- **Flows** answer: what happens at runtime? What are the steps? What happens on failure?
- **Contracts** answer: what rules must every implementation honour?

### File structure

```
docs/flows/
  _index.md          — entry point; table of all flow files
  global.md          — optional: rules that span every flow
  flow-0-boot.md     — one file per flow
  flow-1-task.md
  ...

docs/contracts/
  _index.md          — entry point; table of all contract files
  global.md          — rules that apply to every component and layer
  flow-1-task.md     — companion contract to flows/flow-1-task.md
  ...
```

### Done when

- Every runtime path the system must support has a flow file
- Every flow has a companion contract file
- `global.md` in contracts covers audit, auth, and architecture rules
- Flows and contracts are reviewed and marked locked in `.kit/01-process.md`

---

## Stage 4 — DDA (Playbook Scaffold)

Define every component before any code is written. Read the relevant flow
and contract files before authoring each DDA doc.

### What to do

1. Load Playbook context (use prompt: **Playbook — Start Dev Session**)
2. Bootstrap the scaffold from your PRD (use prompt: **Playbook — Bootstrap Scaffold from PRD**)
3. Review all generated stubs — does the structure make sense?
4. Author each doc top-down: project `_index.md` → app/module `_index.md` → sequencers → components
5. For each sequencer doc, read the relevant flow and contract files first
6. Activate docs one by one: change `status: draft` to `status: active` as each is approved
7. Confirm the living folder tree in every `_index.md` is accurate

### Authoring order

```
1. .playbook/_index.md            — project manifest
2. .playbook/[app]/_index.md      — app or module manifest
3. Sequencers                     — flow coordination docs
4. Components                     — logic units
5. _concerns/                     — cross-cutting capabilities
6. _integrations/                 — external contracts
```

### Done when

- All docs are `status: active`
- Living folder trees are accurate
- No TODO flags remain in any doc
- Every sequencer doc references its flow and contract files

---

## Stage 5 — Code

Generate and write code against Playbook contracts.

### What to do

1. Start every dev session with the Playbook boot prompt
2. Load only the docs needed for the current task (scoped context)
3. Work ticket by ticket if using Kanban (`.kit/05-kanban.md`)
4. After each component: run Playbook sync check
5. Before every PR: run Phase Completion Checklist from `.kit/01-process.md`
6. Keep Playbook docs updated as code evolves — never let them drift

### Rules

- Nothing gets coded without a Playbook doc
- No new dependency without a doc in `_integrations/` or `_shared/`
- All code passes `biome check` and `biome format` before commit
- Commits are grouped logically by scope

### Done when

- All acceptance criteria from `.kit/03-execution.md` are met
- All QA test cases pass (if `.kit/06-qa.md` is active)
- Phase Completion Checklist passes fully

---

## Stage 6 — Rollout

Generate a snapshot for the completed milestone.

### What to do

1. Use prompt: **Playbook — Generate Rollout Doc**
2. Attach all `.playbook/` docs + `.kit/04-progress.md`
3. Review the generated `docs/rollout/[milestone].md`
4. Use it as context for the next session or new team member
5. Never edit rollout docs directly — edit the source Playbook doc and regenerate

### Done when

- Rollout doc is generated and saved to `docs/rollout/`
- Kit files are updated with final state
- All changes are pushed to the repo

---

## Quick Reference

```
Stage 1   Context Kit        .kit/ files + AI session
Stage 2   PRD / Specs        docs/PRD.md
Stage 3   Flows + Contracts  docs/flows/ + docs/contracts/
Stage 4   DDA                .playbook/ scaffold + docs
Stage 5   Code               src/ — built against .playbook/ contracts
Stage 6   Rollout            docs/rollout/[milestone].md
```
