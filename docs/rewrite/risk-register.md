# Risk Register
# File: docs/rewrite/risk-register.md

> Output of the Reverse Engineer expansion -- Phase 4 (Risk Map).
> Documents what breaks if X changes and the safe order for migration.
> For large projects, split into docs/rewrite/risk-register/ with _index.md.

---

## Summary

| Risk Level | Count |
|---|---|
| Critical | 0 |
| High | 0 |
| Medium | 0 |
| **Total** | **0** |

---

## Critical Risks

Changes that could break the entire platform.

### [Risk Name]

**Component:** [what is changing]
**Impact:** What breaks if this changes.
**Depends on it:** [list of components or flows]
**Must change first:** [what needs to happen before this is safe]
**Migration constraint:** [ordering rule this creates]

---

## High Risks

Changes that break significant functionality.

### [Risk Name]

**Component:** [what is changing]
**Impact:** What breaks if this changes.
**Depends on it:** [list]
**Must change first:** [list]
**Migration constraint:** [ordering rule]

---

## Medium Risks

Changes that break specific features.

### [Risk Name]

**Component:** [what is changing]
**Impact:** What breaks if this changes.
**Depends on it:** [list]
**Must change first:** [list]

---

## Migration Constraints

The safe order for the rewrite based on risk dependencies.
Read as: X must be complete before Y can begin.

| Step | Must Complete | Before Starting |
|---|---|---|
| 1 | [component or phase] | [component or phase] |
| 2 | [component or phase] | [component or phase] |

---

## Circular Dependencies

Components that mutually depend on each other -- these require special handling.

| Component A | Component B | Resolution Strategy |
|---|---|---|
| [name] | [name] | [how to break the cycle] |
