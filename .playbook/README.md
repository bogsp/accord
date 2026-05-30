# Playbook
# File: .playbook/README.md

Playbook is a structured development pipeline that keeps humans and AI aligned
from the first idea to deployed code. Everything is defined before it is built.
Everything built is reflected in the docs. Nothing drifts silently.

Playbook is a dev-dependency -- present during development, invisible in production.

---

## Where to Start

| If you are... | Start here |
|---|---|
| New to Playbook | You're in the right place -- read on |
| Starting a new project | `.playbook/.guides/new-project.md` |
| Adopting an existing project | `.playbook/.guides/adopt.md` |
| Resuming a project | `.kit/00-prompts.md` |
| Starting an AI dev session | `.playbook/00-rules.md` |
| Resuming from a rollout doc | `docs/rollout/[milestone].md` then `.playbook/00-rules.md` |
| Learning how the Context Kit works | `.playbook/.guides/context-kit.md` |
| Looking up pipeline reference | `.playbook/pipeline.md` |
| Looking up session prompts | `.kit/00-prompts.md` |

---

## The Pipeline

```
                    Context Kit (always active)
                           |
[RE] -> PRD -> Flows -> Contracts -> DDA -> Code -> Rollout
```

[RE] is the Reverse Engineer expansion -- conditional, existing projects only.

| Stage | What It Is | Where It Lives | Conditional |
|---|---|---|---|
| Reverse Engineer | Maps what exists before building what should | `docs/rewrite/` | Existing projects only |
| Context Kit | Session and decision layer -- always active | `.kit/` | Always |
| PRD | What to build and why | `docs/` | Always |
| Flows | Runtime paths | `docs/flows/` | Always |
| Contracts | Implementation boundaries | `docs/contracts/` | Always |
| DDA | Component structure and relationships | `.playbook/` | Always |
| Code | Implementation | `src/` | Always |
| Rollout | Generated snapshots | `docs/rollout/` | Always |

---

## Expansions

Expansions are optional stages that extend Playbook for specific needs.
Activate in `.kit/01-process.md` under `Active Expansions`.

| Expansion | When to Use | Guide |
|---|---|---|
| Reverse Engineer | Existing or legacy projects | `.playbook/.guides/expansions/reverse-engineer.md` |
| Video Insights | Research-driven ideation from video content | `.playbook/.guides/expansions/video-insights.md` |

---

## Key Rules

- Nothing gets coded without a `.playbook/` doc
- Flows and contracts are approved before any DDA docs are written
- Docs stay in sync with code -- run sync before every PR
- Kit files are updated at the end of every session
- Progress log is append-only -- never edit past entries
- Expansions are declared in `01-process.md` before use

---

The doc is the contract. The code follows.

For the full pipeline reference: `.playbook/pipeline.md`
For the Context Kit guide: `.playbook/.guides/context-kit.md`
