# Code Standards

> Canonical reference for all projects. Copy to `/docs/code-standards.md` in each repo.
> Last updated: 2026-03-03

---

## Table of Contents

1. [Architecture Patterns](#1-architecture-patterns)
2. [TypeScript Strictness](#2-typescript-strictness)
3. [Error Handling](#3-error-handling)
4. [Code Hygiene](#4-code-hygiene)
5. [Naming Conventions](#5-naming-conventions)
6. [Testing Strategy](#6-testing-strategy)
7. [Git Workflow](#7-git-workflow)
8. [Tooling](#8-tooling)
9. [AI-Assisted Development](#9-ai-assisted-development)
10. [Phase Completion Checklist](#10-phase-completion-checklist)
11. [Security](#11-security)

---

## 1. Architecture Patterns

### Core Approach

- **Clean Architecture** — strict layer separation: Domain → Application → Infrastructure → Interface
- **Domain-Driven Design (DDD)** — domain model reflects business language and rules
- **Event-Driven** — state changes communicate via domain events, never direct coupling
- **Dependency Injection** — dependencies are injected, never instantiated inside consumers
- **Factory and Abstraction** — construction logic isolated in factories; consumers depend on interfaces
- **Deep Modules** — rich internal behavior behind minimal, stable interfaces; complexity is hidden, not exposed

### Layer Rules

```
Domain        → pure business logic; no framework, no DB, no network
Application   → use cases; orchestrates domain; depends on interfaces only
Infrastructure→ defines interfaces and abstractions; no concrete SDK imports
Service Adapters → only place concrete technologies appear (DB, Redis, HTTP clients)
Interface     → tRPC routers, WebSocket handlers, CLI; thin translation layer only
```

- No cross-layer imports — Application cannot import Interface; Domain cannot import anything external
- No direct module-to-module imports — all cross-module calls go through `ModuleServiceGateway`
- Replacing a technology requires changes in Service Adapters only — nothing else changes

### Domain Object Rules

- **Value Objects** are immutable — no setters; construction via static factory or constructor
- **Entities** have identity; Value Objects do not
- **Aggregates** enforce invariants before emitting events
- No anemic domain models — behavior lives on the domain object, not in a service class
- Domain events are emitted only after a successful transaction commit
- Events are past-tense and signal-only: `InvoicePosted`, not `updateInvoice`

### Use Case Design (CQRS-lite)

- One class per use case: `CreateInvoiceUseCase`, not `InvoiceService` with 30 methods
- Commands mutate state; queries read state — never mixed in the same use case
- Use cases accept a typed input and return `Result<T, E>` — no raw throws
- Use cases are the only entry point into the Domain layer from above

### Reusability

- Shared logic lives in `/shared` or `/packages` — never copy-pasted, never cross-imported
- If two modules need the same behavior, it belongs in `/shared`
- Interfaces and abstractions are designed before implementations — stable contracts, swappable internals

---

## 2. TypeScript Strictness

### Compiler Config

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "exactOptionalPropertyTypes": true
  }
}
```

`strict: true` enables `noImplicitAny`, `strictNullChecks`, `strictFunctionTypes`, and all strict checks. These are non-negotiable.

### Rules

- **No `any` type** — anywhere in the codebase, ever
- **No type assertions (`as SomeType`)** except at adapter boundaries; must include an inline justification comment
- **Use `satisfies`** over `as` where applicable — catches errors without widening the type
- **Explicit return types** on all functions — no inferred return types on public APIs
- **No non-null assertions (`!`)** — handle nullability explicitly
- **Zod schemas at every external boundary** — API inputs, event payloads, manifest fields, environment variables
- **No boolean domain/application state** — model state with dates, enums, or discriminated unions
- **Zod exception** — booleans are allowed only in Zod schemas and parse/safeParse boundary results; map immediately to domain/application types
- **Predicate exception** — booleans are allowed for pure predicate/equality functions (`is*`, `has*`, `equals`) and `Result` discriminants; never persist or transport booleans as business state
- **No magic strings or numbers** — use `const` enums or literal union types

```typescript
// ❌ Wrong
const status = "active";
function getUser(id: any) { ... }
const data = response as UserData;

// ✅ Correct
const STATUS = { ACTIVE: "active", INACTIVE: "inactive" } as const;
type Status = typeof STATUS[keyof typeof STATUS];
function getUser(id: string): Result<User, UserNotFoundError> { ... }
// Adapter boundary — validated by Zod before this point
const data = response satisfies UserData;
```

### Immutability

- All arrays passed into or returned from Domain and Application functions must be typed as `readonly` — use `ReadonlyArray<T>` or `readonly T[]`
- Never mutate input parameters — always return new objects
- Domain objects must not expose mutable state — no public setters, no direct property mutation
- Use spread for updates in the Application layer: `{ ...existing, status: newStatus }`

```typescript
// ❌ Wrong
function addItem(items: Item[], item: Item): void {
  items.push(item); // mutates input
}

// ✅ Correct
function addItem(items: ReadonlyArray<Item>, item: Item): ReadonlyArray<Item> {
  return [...items, item];
}
```

---

## 3. Error Handling

### Result Type

All Domain and Application layer functions return `Result<T, E>`. No raw `throw` outside Infrastructure and Interface layers.

```typescript
// packages/platform-core/src/types/result.ts

export type Result<T, E> =
  | { readonly ok: true; readonly value: T }
  | { readonly ok: false; readonly error: E };

export const ok = <T>(value: T): Result<T, never> =>
  ({ ok: true, value });

export const err = <E>(error: E): Result<never, E> =>
  ({ ok: false, error });
```

### Error Hierarchy

```
BizKitError (base)
├── DomainError        — invariant violations, business rule failures
├── ApplicationError   — use case failures (not-found, unauthorized, conflict)
└── InfrastructureError— DB failures, network errors, external service failures
```

```typescript
export class BizKitError extends Error {
  constructor(message: string, public readonly code: string) {
    super(message);
    this.name = this.constructor.name;
  }
}

export class InvoiceNotFoundError extends ApplicationError {
  constructor(id: string) {
    super(`Invoice not found: ${id}`, "INVOICE_NOT_FOUND");
  }
}
```

### Layer Responsibilities

| Layer | Behavior |
|---|---|
| Domain | Returns `Result<T, DomainError>` — never throws |
| Application | Returns `Result<T, ApplicationError>` — maps domain errors up |
| Infrastructure | May throw — caught at boundary and wrapped in `InfrastructureError` |
| Interface (tRPC) | Only layer that maps errors to transport codes |

### tRPC Error Mapping

```typescript
// DomainError        → BAD_REQUEST
// Not-found variants → NOT_FOUND
// Auth errors        → UNAUTHORIZED
// InfrastructureError→ INTERNAL_SERVER_ERROR
```

---

## 4. Code Hygiene

### Functions

- One function, one responsibility — max ~20 lines; extract if longer
- No deeply nested conditionals — use early returns (guard clauses)
- No side effects in pure functions — Domain layer functions are always pure
- No commented-out code — delete it; version control preserves history

```typescript
// ❌ Wrong — nested, hard to follow
function process(order: Order) {
  if (order.status === "pending") {
    if (order.items.length > 0) {
      if (order.total > 0) {
        // process...
      }
    }
  }
}

// ✅ Correct — guard clauses, flat structure
function process(order: Order): Result<void, OrderError> {
  if (order.status !== "pending") return err(new InvalidOrderStatusError());
  if (order.items.length === 0) return err(new EmptyOrderError());
  if (order.total <= 0) return err(new InvalidOrderTotalError());
  // process...
  return ok(undefined);
}
```

### Imports and Exports

- **Named exports only** — no default exports; aids refactoring and tree-shaking
- **No barrel `index.ts` re-exports** — import explicitly from the source file
- **No circular imports** — use dependency injection to break cycles
- **Framework exceptions** — default exports are allowed only when a framework contract requires them, such as Docusaurus page modules, Storybook CSF metadata, or tool config entrypoints that require `export default` or `module.exports`

```typescript
// ❌ Wrong
export default function createInvoice() { ... }
import createInvoice from "./invoices";

// ✅ Correct
export function createInvoice() { ... }
import { createInvoice } from "./invoices/create-invoice";
```

### Schema Validation

- All external inputs validated with Zod before entering Application layer
- Zod schemas colocated with their use — not in a separate `schemas/` folder
- Parse, don't validate: use `schema.parse()` to get typed output, not `schema.safeParse()` just for the boolean
- Zod boolean fields are boundary-only; convert to enum/date/union before Domain/Application state modeling

```typescript
const CreateInvoiceInput = z.object({
  tenantId: z.string().uuid(),
  customerId: z.string().uuid(),
  lineItems: z.array(LineItemSchema).min(1),
  dueDate: z.coerce.date(),
});

type CreateInvoiceInput = z.infer<typeof CreateInvoiceInput>;
```

### General

- No `TODO` or `FIXME` in committed code — track in task system instead
- No magic numbers — extract to named constants with units in the name (`MAX_RETRY_COUNT`, `SESSION_TTL_SECONDS`)
- Prefer `const` over `let`; never use `var`
- Async/await over raw Promises — no `.then().catch()` chains

### Date and Time

- Always store and transmit dates in UTC — never store with a timezone offset
- Never call `new Date()` directly in Domain or Application layer — inject a clock abstraction for testability
- Use ISO 8601 for all date serialization; use `z.coerce.date()` at Zod boundaries only
- Display formatting and timezone conversion happen at the Interface layer only — never in Domain or Application
- Never compare dates as strings — always compare as `Date` objects or UTC timestamps

```typescript
// ❌ Wrong
class CreateInvoiceUseCase {
  execute(input: CreateInvoiceInput) {
    const now = new Date(); // untestable, not injected
  }
}

// ✅ Correct
class CreateInvoiceUseCase {
  constructor(private readonly clock: IClock) {}
  execute(input: CreateInvoiceInput) {
    const now = this.clock.now();
  }
}
```

---

## 5. Naming Conventions

### General

| Thing | Convention | Example |
|---|---|---|
| Files | `kebab-case` | `create-invoice.ts` |
| Directories | `kebab-case` | `use-cases/` |
| Classes | `PascalCase` | `InvoiceRepository` |
| Interfaces | `PascalCase` (`I` prefix) | `IInvoiceRepository` |
| Types | `PascalCase` | `CreateInvoiceInput` |
| Functions | `camelCase` | `createInvoice()` |
| Variables | `camelCase` | `lineItemTotal` |
| Constants | `SCREAMING_SNAKE_CASE` | `MAX_LINE_ITEMS` |
| Enums | `PascalCase` members | `InvoiceStatus.POSTED` |
| Booleans | Avoid for state modeling; prefer enums/dates/unions | `status: 'draft' \| 'posted'`, `postedAt?: string` |

### Domain and Events

- Events: past-tense, aggregate-scoped — `InvoicePosted`, `PaymentReceived`
- Use cases: verb + noun — `CreateInvoiceUseCase`, `PostInvoiceUseCase`
- Repositories: noun + `Repository` — `InvoiceRepository`
- Factories: noun + `Factory` — `InvoiceFactory`
- Errors: noun + description + `Error` — `InvoiceNotFoundError`

### Module-Scoped (replace `[module]` with actual module id)

| Resource | Pattern | Example |
|---|---|---|
| tRPC routes | `module.resource.action` | `accounting.invoices.create` |
| Events | `[Module][Entity][Action]` | `AccountingInvoicePosted` |
| DB tables | `[module]_[table]` | `accounting_invoices` |
| Permissions | `[module]:[action]` | `accounting:create-invoice` |
| Settings | `[module].[setting]` | `accounting.default-currency` |
| Routes | `/[module]/[resource]` | `/accounting/invoices` |

---

## 6. Testing Strategy

### Tools

- **Vitest** — unit and integration tests
- **Playwright** — E2E tests (web and Electron; one tool for both)
- **MSW** — mock HTTP and WebSocket in integration tests; never mock at the network level in unit tests

### Coverage Gates (enforced in CI)

| Layer | Target | Method |
|---|---|---|
| Domain | 100% | Unit tests — pure functions, no dependencies |
| Application (use cases) | 90%+ | Integration tests with mocked adapters via MSW |
| Infrastructure | Integration tests | Test against real DB/Redis in CI; no unit mocks |
| Interface (tRPC) | Key paths + error mapping | Not exhaustive |
| UI | Critical workflows | Playwright E2E |

Build fails in CI if Domain < 100% or Application < 90%.

### Rules

- Domain tests are pure — no DB, no gateway, no infrastructure, no MSW
- One test file per unit: `create-invoice.use-case.test.ts` mirrors `create-invoice.use-case.ts`
- Test behavior, not implementation — test what a function does, not how
- Arrange / Act / Assert structure — one assertion concept per test
- All adapters must have a mock implementation for use in Application-layer tests
- No `describe` nesting deeper than 2 levels

```typescript
// ✅ Good — tests behavior, not internals
describe("CreateInvoiceUseCase", () => {
  describe("when line items are empty", () => {
    it("returns EmptyOrderError", async () => {
      const result = await useCase.execute({ ...validInput, lineItems: [] });
      expect(result.ok).toBe(false);
      expect(result.error).toBeInstanceOf(EmptyLineItemsError);
    });
  });
});
```

---

## 7. Git Workflow

### Branches

| Type | Pattern | Example |
|---|---|---|
| Feature | `feature/[scope]/[description]` | `feature/accounting/add-invoice-search` |
| Fix | `fix/[scope]/[description]` | `fix/accounting/invoice-date-validation` |
| Milestone | `milestone/[number]-[name]` | `milestone/1-platform-core` |
| Chore | `chore/[description]` | `chore/update-dependencies` |

- `main` is always deployable — no broken builds on main, ever
- Branch from `main`; merge via PR only
- Delete branches after merge

### Commits

Format: `[scope]: short imperative description`

```
accounting: add invoice line item validation
platform-core: implement version adapter registry
fix(auth): handle expired WorkOS session token
chore: update Biome to 1.9
```

- Imperative mood — "add", "fix", "implement", not "added", "fixes", "implementing"
- Scope is the module or package name
- Under 72 characters for the first line
- Body (optional) explains *why*, not *what*

### PR Rules

- Every PR requires at least one review before merge
- PR description includes: what changed, why, and how to test
- CI must pass (lint, format, type check, tests, coverage gates)
- Checklist before requesting review:
  - [ ] DDA sync check — all touched docs reflect current implementation
  - [ ] No new dependencies added without a DDA doc
  - [ ] No new components coded without a DDA doc
  - [ ] No cross-layer violations
  - [ ] No direct module-to-module imports
  - [ ] Events follow naming conventions
  - [ ] Typed errors — no raw `throw` in Domain/Application
  - [ ] Tests included for new behavior
  - [ ] Biome check and format pass
  - [ ] No `TODO`/`FIXME` in committed code

---

## 8. Tooling

### Biome

Single tool for linting and formatting. Replaces ESLint + Prettier.

```json
// biome.json — minimum config
{
  "linter": { "enabled": true, "rules": { "recommended": true } },
  "formatter": { "enabled": true, "indentStyle": "space", "indentWidth": 2 },
  "organizeImports": { "enabled": true }
}
```

- All code must pass `biome check` and `biome format` before commit
- Configure as pre-commit hook — never commit code that fails Biome
- CI fails on Biome errors — no exceptions

### Vitest

```typescript
// vitest.config.ts
export default defineConfig({
  test: {
    coverage: {
      provider: "v8",
      thresholds: { lines: 90, functions: 90, branches: 90 },
      include: ["src/domain/**", "src/application/**"],
    },
  },
});
```

### Playwright

```typescript
// playwright.config.ts
export default defineConfig({
  testDir: "./e2e",
  use: { baseURL: "http://localhost:3000" },
  projects: [
    { name: "chromium", use: { ...devices["Desktop Chrome"] } },
    // Add Electron project for desktop app tests
  ],
});
```

### Zod

- All schemas use `z.object().strict()` — rejects unknown fields by default at external boundaries
- Coerce only where explicitly needed (`z.coerce.date()`) — never `z.any()`
- Infer types from schemas: `type Input = z.infer<typeof InputSchema>`

### Logging

- All logs must be structured JSON with a trace ID — no plain string logs
- Trace ID originates at the Interface layer and flows through all downstream calls via DI
- Log levels:
  - `error` — unrecoverable failures; always include full context and original cause
  - `warn` — recoverable issues or degraded behavior
  - `info` — significant state transitions (`InvoicePosted`, `PaymentReceived`)
  - `debug` — development only; must not appear in production builds
- Always log: request entry and exit, unhandled errors, external service calls, significant state changes
- Never log: passwords, tokens, secrets, raw PII, full request bodies at `info` level or above
- Infrastructure errors must be logged before being wrapped — log the original cause, not just the wrapper class

### Environment Variables

- Validate all env vars at startup with Zod — crash fast on missing config
- Never access `process.env` directly outside the env validation module
- Pin build/runtime toolchains to an explicitly supported Node LTS version for framework-owned tooling such as Docusaurus and Storybook

```typescript
// src/env.ts
const EnvSchema = z.object({
  DATABASE_URL: z.string().url(),
  REDIS_URL: z.string().url(),
  PORT: z.coerce.number().default(3000),
});

export const env = EnvSchema.parse(process.env);
```

---

## 9. AI-Assisted Development

This project uses Document-Driven Architecture (DDA). Always load `.playbook/00-rules.md`
alongside this file at the start of every AI session. DDA defines structure;
this file defines implementation. Neither is complete without the other.

### DDA Integration

- All components are defined in `.playbook/` before any code is written
- AI generates code against DDA component docs — not ahead of them
- Every module has a `_index.md` in `.playbook/[module]/` describing: purpose,
  entry points, contained components, integrations, and concerns
- Keep DDA docs updated when implementations change — run sync mode before every PR

### AI Session Boot Order

```
1. .playbook/00-rules.md
2. docs/code-standards.md        ← this file
3. docs/flows/_index.md
4. docs/contracts/_index.md
5. .playbook/_index.md
6. .playbook/[module]/_index.md
7. Relevant component and sequencer docs
8. _concerns/ + _integrations/
```

### Deep Modules for AI Clarity

- Minimal, explicit public interfaces — AI reads interfaces to understand module behavior
- Rich internal implementation — complexity is hidden, not scattered
- Document the *why* in JSDoc, not just the *what* — AI uses this as reasoning context

```typescript
/**
 * Posts a draft invoice, making it legally binding and triggering payment tracking.
 * Invariants: invoice must be in DRAFT status; line items must balance to total.
 * Emits: InvoicePosted event after successful transaction commit.
 */
export class PostInvoiceUseCase { ... }
```

### Prompting Standards

- Always provide: the relevant DDA component doc, the interface the code must implement,
  and the error types it must handle
- Never accept AI-generated code that uses `any`, raw throws, or skips Zod validation
- Never accept stub implementations — no hardcoded return values, no artificial delays or intervals standing in for real logic, no placeholder function bodies
- Never accept partial implementations — every code path must be fully handled: happy path, all error branches, and all edge cases; silent skips are not acceptable
- All branches must be explicit and transparent — if a path is not handled, it must surface as a typed error, not disappear silently
- AI-generated code must pass Biome check before commit — no exceptions
- Flag any AI output that adds undeclared dependencies or duplicates existing responsibilities

### Implementation Accuracy

- Always implement against official documentation, not AI assumptions
- For any third-party SDK: verify method signatures against current docs before using AI-generated calls
- Flag any AI output that uses deprecated APIs or patterns inconsistent with current library versions

### Behavioral Constraints

- Never add functionality, files, or components beyond the current task scope
- Never introduce a dependency not declared in the component's `connects` or `integrations` fields
- Never make architectural decisions unilaterally — flag and wait for instruction
- Never rename, move, restructure, or refactor anything outside the current task scope
- Never resolve ambiguity silently — stop and flag before writing any code

### Uncertainty Protocol

When any of the following occur, stop all work and flag before writing any code:

- A requirement is unclear or underspecified
- A flow path is not defined in the DDA doc or contract
- A dependency is needed but not declared in the component doc
- A decision would affect more than the current component
- The correct behavior cannot be inferred without making assumptions

Flag format:
```
[FLAG] Category: description
       Options: (if applicable)
       Blocking: what cannot proceed until this is resolved
```

Categories: `underspecified` | `missing-contract` | `undeclared-dependency` | `architectural-decision` | `ambiguous-behavior`

### Anti-Patterns (Quick Reference)

Never do any of the following:

**Architecture**
- Cross-layer imports — Application → Interface, Domain → Infrastructure
- Direct module-to-module imports — use `ModuleServiceGateway`
- Business logic in sequencers or the Interface layer
- Anemic domain models — behavior belongs on the domain object, not in a service

**TypeScript**
- `any` type anywhere in the codebase
- Non-null assertions (`!`)
- Type assertions (`as`) outside adapter boundaries
- Inferred return types on public APIs
- Boolean domain or application state — use enums, dates, or discriminated unions

**Implementation**
- Stub implementations — hardcoded returns, artificial delays, placeholder bodies
- Partial implementations — all paths must be handled explicitly
- Raw `throw` in Domain or Application layer — use `Result<T, E>`
- Magic strings or numbers — use named constants
- Commented-out code — delete it; version control preserves history
- `new Date()` in Domain or Application — inject a clock abstraction
- Mutating input parameters — return new objects

**AI-Specific**
- Generating code ahead of a DDA doc
- Adding undeclared dependencies or components
- Silently resolving ambiguity with assumptions
- Duplicating an existing component's responsibilities
- Leaving any code path unhandled or silently ignored

---

## 10. Phase Completion Checklist

Run this checklist at the end of every phase and before every PR.
No phase is complete until all applicable items are checked.

### Code Quality

- [ ] `biome check` passes with zero errors
- [ ] `biome format` applied — no unformatted files
- [ ] Type check passes — `tsc --noEmit` exits clean
- [ ] All tests pass — `vitest run` exits clean
- [ ] Coverage gates met — Domain 100%, Application 90%+
- [ ] No stub implementations — no hardcoded returns, artificial delays, or placeholder bodies
- [ ] No partial implementations — all branches handled: happy path, errors, and edge cases

### DDA Sync

- [ ] All touched components have up-to-date DDA docs
- [ ] No new dependencies added without a DDA doc
- [ ] No new components coded without a DDA doc
- [ ] Emergent components traced back — parent and `_index.md` updated
- [ ] All modified docs are `status: active` (not `draft`)

### Git

- [ ] Commits are grouped logically — one concern per commit
- [ ] Commit messages follow format: `[scope]: short imperative description`
- [ ] No `TODO` or `FIXME` in committed code
- [ ] No `console.log` left in code
- [ ] Branch is up to date with `main`
- [ ] All changes pushed to remote

### Context Kit

- [ ] `04-progress.md` updated with completions and decisions
- [ ] `01-process.md` Current State reflects where the project is now
- [ ] Active tasks in `03-execution.md` checked off
- [ ] Kanban tickets moved to Done if `05-kanban.md` is active
- [ ] QA test cases updated if `06-qa.md` is active

### Milestone Only (at major releases)

- [ ] Rollout doc generated — `docs/rollout/[milestone].md`
- [ ] All DDA docs audited — code matches docs across all touched modules
- [ ] QA sign-off complete if `06-qa.md` is active

---

## 11. Security

### Secrets and Configuration

- All secrets via environment variables validated at startup — never hardcoded anywhere in the codebase
- Never log secrets, tokens, API keys, or credentials at any log level
- Never expose secrets in URLs, query strings, or client-facing responses

### Input Trust

- Never trust client-supplied IDs for authorization — always verify against session context
- All external input passes through Zod validation before entering the Application layer — no exceptions
- Use parameterized queries only — never interpolate values directly into query strings

### Error Exposure

- Never expose internal error details, stack traces, or infrastructure messages to clients
- Map all errors to safe transport codes at the Interface layer — internal detail stays server-side
- `InfrastructureError` messages are never forwarded to the client

### Transport

- All inter-service communication over HTTPS — no plaintext internal calls in production
- Sensitive data is never placed in URLs or query strings — use request body or headers

---

## Error Mitigation

### Prevention

- Biome configured as pre-commit hook — broken code never reaches the repo
- CI enforces: lint, format, type check, tests, coverage gates — all must pass
- DDA sync check is mandatory before every PR — no exceptions
- All `status: draft` DDA docs must be resolved before coding begins

### Detection

- Biome and TypeScript surface issues at write time in the editor
- CI gate catches anything that slips through locally
- DDA audit at each milestone catches structural drift
- QA plan (`06-qa.md`) catches behavioral regressions

### Recovery

- **Build failure** — fix Biome/type errors before any other work; do not stack changes on broken builds
- **Test failure** — revert to last passing commit if cause is unclear; fix forward only if root cause is known
- **DDA drift** — run sync mode immediately; update the doc before writing any new code on that component
- **Context rot** — re-upload kit files and DDA docs; use the anchoring prompt in `.kit/00-prompts.md`
- **Bad merge** — revert the merge commit; re-apply changes manually with proper review

---

*This document is the source of truth for code standards. When in doubt, refer here first.*
*Always load alongside `.playbook/00-rules.md`.*
