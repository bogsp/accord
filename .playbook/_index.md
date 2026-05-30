---
id: [project-name]
type: structure
scope: project
status: draft
entry: []
contains: []
concerns: [logging, error-handling, auth]
integrations: []
---

<!-- TODO: Describe the project purpose in one paragraph -->

## Structure

- [.playbook/](./)
  - [README.md](./README.md) — orientation and journey matrix
  - [pipeline.md](./pipeline.md) — complete pipeline reference
  - [00-rules.md](./00-rules.md) — AI execution layer; read first every session
  - [_index.md](./_index.md) — this file; project manifest and folder tree
  - [_concerns/](./_concerns/)
    - [logging.md](./_concerns/logging.md) — structured JSON logging via DI
    - [error-handling.md](./_concerns/error-handling.md) — typed Result pattern across all layers
    - [auth.md](./_concerns/auth.md) — permission checks at sequencer boundaries
  - [_integrations/](./_integrations/) — (empty — add as integrations are defined)
  - [_shared/](./_shared/) — (empty — add cross-module components here)
  - [.guides/](./.guides/)
    - [new-project.md](./.guides/new-project.md) — new project walkthrough
    - [adopt.md](./.guides/adopt.md) — all adoption paths
    - [context-kit.md](./.guides/context-kit.md) — Context Kit integration guide
    - [expansions/](./.guides/expansions/)
      - [reverse-engineer.md](./.guides/expansions/reverse-engineer.md)
      - [video-insights.md](./.guides/expansions/video-insights.md)
- [docs/](../docs/)
  - [code-standards.md](../docs/code-standards.md) — implementation rules; always paired with 00-rules.md
  - [flows/](../docs/flows/) — runtime path definitions
  - [contracts/](../docs/contracts/) — implementation boundaries
  - [rewrite/](../docs/rewrite/) — RE expansion outputs
  - [rollout/](../docs/rollout/) — generated milestone snapshots; never edit directly
- [.kit/](../.kit/)
  - [00-prompts.md](../.kit/00-prompts.md) — session prompts
  - [01-process.md](../.kit/01-process.md) — session spark
  - [02-exploration.md](../.kit/02-exploration.md) — active decisions
  - [03-execution.md](../.kit/03-execution.md) — guidelines, plan, and tasks
  - [04-progress.md](../.kit/04-progress.md) — append-only progress log
  - [05-kanban.md](../.kit/05-kanban.md) — ticketed work breakdown (optional)
  - [06-qa.md](../.kit/06-qa.md) — test registry and sign-off (optional)
  - [_refs/](../.kit/_refs/) — supporting material
  - [_tests/](../.kit/_tests/) — one file per QA test case
  - [_archives/](../.kit/_archives/) — lean history

## Detail

<!-- Architecture decisions, constraints, known assumptions -->