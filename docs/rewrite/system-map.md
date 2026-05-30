# System Map
# File: docs/rewrite/system-map.md

> Output of the Reverse Engineer expansion -- Phase 1 (Inventory) and Phase 2 (Audit).
> Plain language throughout. Technical detail in sub-sections where needed.
> For large projects, split into docs/rewrite/system-map/ with _index.md.

---

## Overview

Plain language summary of what this system does and who it serves.
Any reader understands this without technical knowledge.

---

## Applications

One section per application or service.

### [Application Name]

**What it does:**
Plain language description.

**Tech stack:**
- Framework: [name + version]
- Language: [name + version]
- Key libraries: [list]

**Entry points:**
How this application is accessed -- UI, API, CLI, webhooks, etc.

**Known issues:**
Anything fragile, outdated, or problematic discovered during audit.

---

## Data Models

What data exists in the system and how it is structured.

### [Model Name]

**What it represents:** [plain language]
**Where it lives:** [database, service, or application]
**Key fields:** [list the important ones]
**Relationships:** [what it connects to]

---

## Integrations

Every external service the system depends on.

| Integration | What It Does | Used By | Status |
|---|---|---|---|
| [Name] | [purpose] | [app or service] | Keep / Replace / Evaluate |

---

## Dependency Map

What depends on what. Read top-down -- left side depends on right side.

| Component | Depends On | Type |
|---|---|---|
| [name] | [name] | direct / indirect |

---

## Entry Points

How the system is accessed from outside.

| Entry Point | Type | Handled By |
|---|---|---|
| [path or event] | REST / GraphQL / Webhook / UI / CLI | [component] |

---

## Gap Report

_(Added during Phase 5 -- Gap Report)_

### Missing
Things needed but not yet existing.

### Broken
Things that do not work correctly right now.

### Undocumented
Things with no documentation, spec, or clear owner.

### Security Concerns
Anything representing a security risk.

### Performance Concerns
Anything known to degrade under load.
