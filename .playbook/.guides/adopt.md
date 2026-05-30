# Adopting Playbook
# File: .playbook/.guides/adopt.md

This guide covers all adoption paths. Answer four questions to find your starting point.
No assumptions. No rigid categories. Just honest routing.

---

## Where to Start

Answer these questions in order. Each answer points to your next step.

---

**Question 1 — Does the project have `.kit/` files?**

```
Yes -> skip to Question 2
No  -> generate kit files first
      Upload .playbook/00-rules.md + docs/code-standards.md
      Use prompt: Adopt Existing Project -- Message 1 (from .kit/00-prompts.md)
      Then continue to Question 2
```

---

**Question 2 — Is the codebase organized enough to map without reverse engineering?**

Ask yourself: can you describe what every major part of the system does
and how they connect, without reading the code?

```
Yes -> skip to Question 3
No  -> activate the Reverse Engineer expansion first
      Add to .kit/01-process.md:
        Active Expansions:
          reverse-engineer: inventory
      Read .playbook/.guides/expansions/reverse-engineer.md
      Complete all RE phases before continuing
      Then return to Question 3
```

---

**Question 3 — Is there existing documentation, architecture notes, or specs?**

```
Yes -> attach them to your audit prompt as additional context
No  -> audit from source files only; AI will surface what it finds
```

---

**Question 4 — Is this a partial update or a full rewrite?**

```
Partial update  -> strangler fig approach
                   Adopt incrementally, keep platform live
                   Priority: _concerns/ -> _integrations/ -> _shared/ -> sequencers -> components

Full rewrite    -> greenfield with RE expansion
                   RE expansion must be complete before any new code is written
                   New architecture defined in DDA before old code is touched
```

---

## Running the Audit

Once you have answered the four questions, run the structured audit.

Upload all available context -- kit files, docs, source files -- then send:

```
Run a structured audit against the Playbook pipeline.

1. Inventory
   - Map all modules, services, components, dependencies, tech stack
   - Note all third-party integrations
   - Identify entry points and data flows

2. Audit
   - What exists in .playbook/ already?
   - Do docs/flows/ and docs/contracts/ exist? Are they complete?
   - Which components have no Playbook doc?
   - Are there layer violations or direct module-to-module imports?
   - Are there undocumented concerns or integrations?
   - What drifts from Playbook conventions?

3. Gap Report
   - Missing: what does not exist yet
   - Drifting: what exists but does not match Playbook
   - Compliant: what already follows Playbook

Wait for review before proceeding.
```

---

## Scaffolding

After reviewing and approving the gap report:

```
Proceed with scaffold:

1. Scaffold flows and contracts if missing
   - Create docs/flows/_index.md and docs/contracts/_index.md
   - Draft stub flow files from existing codebase
   - Draft docs/contracts/global.md

2. Scaffold .playbook/ around existing code
   - Create _index.md with living folder tree
   - Create stub docs (status: draft) for all unmapped components
   - Do not modify any existing code

3. Reconcile -- for each stub, note:
   - What the code currently does vs what the doc says
   - Any naming that needs to align with code-standards.md
   - Any structural changes recommended (not required yet)

4. Update kit files -- Current State and Progress log

Generate all files as markdown code blocks for review.
```

---

## Incremental Adoption

After scaffolding is reviewed and approved:

```
Begin incremental adoption.

Work through the gap report in priority order:
1. _concerns/      -- cross-cutting capabilities first
2. _integrations/  -- external contracts next
3. _shared/        -- cross-module components
4. Sequencers      -- flows before component code
5. Components      -- one at a time, test after each

For each item:
1. Author or update the Playbook doc to status: active
2. Identify code changes needed -- flag but do not make yet
3. Once doc is approved, make corresponding code changes
4. Run Phase Completion Checklist after each item
5. Log completion in .kit/04-progress.md
```

---

## Adoption Rules

- Nothing is deleted -- adoption is additive only
- Existing code stays intact until its Playbook doc is active
- Closed decisions in existing kit files stay closed
- Progress log entries are never edited -- new entries only
- Flag risky structural changes -- defer them; compliance is incremental
- Full rewrite: no new code until RE expansion handoff is complete

---

## Related

- Reverse Engineer expansion: `.playbook/.guides/expansions/reverse-engineer.md`
- New project: `.playbook/.guides/new-project.md`
- Session prompts: `.kit/00-prompts.md`
