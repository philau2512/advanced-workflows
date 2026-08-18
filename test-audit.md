---
name: test-audit
description: Exhaustively inventory frontend and backend behavior, generate detailed test cases for every discovered interaction/API/business path, identify coverage gaps, and optionally hand off to test implementation/execution skills.
argument-hint: "[scope] [--fe|--be|--full] [--changed] [--saturation] [--write-tests] [--run]"
---

# /test-audit — Exhaustive FE + BE Test Coverage Audit

$ARGUMENTS

## Purpose & Core Contract

Discover and inventory all actual application surfaces (Frontend UI/interactions & Backend APIs/domain logic), generate concrete test scenarios, and map coverage gaps.

> **BEHAVIOR COVERAGE > LINE COVERAGE. INVENTORY BEFORE SCENARIO GENERATION.**

- **Ground in evidence:** Every test case must trace back to real code symbols, routes, or schema definitions.
- **Strictly read-only by default:** Do NOT generate/write test files unless `--write-tests` is explicitly provided.
- **Zero invented behavior:** Mark unclear behavior as `UNKNOWN / NEEDS SPEC` rather than guessing.

---

## Flags & Modes

| Flag | Description |
| :--- | :--- |
| `--fe` | Audit Frontend surfaces only (UI, interactions, state, client effects). |
| `--be` | Audit Backend surfaces only (Endpoints, business logic, DB, integrations). |
| `--full` | *(Default)* Exhaustively audit both Frontend and Backend. |
| `--changed` | Restrict audit to changed code and affected regression blast radius. |
| `--saturation` | Iteratively expand edge cases until 2 consecutive passes discover no new scenarios. |
| `--write-tests`| Implement automated tests for identified gaps using existing project frameworks. |
| `--run` | Execute existing and newly generated tests and attach empirical results. |

---

## Surface Inventory Dimensions

### 1. Frontend Surfaces (`--fe`)
- **Routes & Screens:** Layouts, protected routes, redirects, query-param modal states, 404/error boundaries, SSR/SSG hydration boundaries.
- **Interactive Controls:** Forms, inputs, buttons, dropdowns, uploads, modals, pagination, drag-and-drop, keyboard actions.
- **UI States:** Initial, loading, empty, validation error, server error, disabled, offline, stale data, optimistic rollback.
- **Client Data & Effects:** API hooks/queries, global state/cache, localStorage, debouncing, WebSocket/SSE subscriptions, file upload/stream interruptions.

### 2. Backend Surfaces (`--be`)
- **External Contracts:** REST, GraphQL, RPC, Webhooks, WebSockets (methods, auth, headers, payload schema, status codes, reconnection/heartbeat).
- **Domain Logic:** Business rules, validators, calculators, policies/permissions, state machines, serializers.
- **Persistence & DB:** Transactions, unique constraints, foreign keys, concurrent writes, optimistic locks, cache invalidation.
- **Async & Background:** Cron jobs, queues/workers, retries, dead-letter queues, 3rd-party integrations (payments, emails).

---

## Scenario Generation Matrix (Test Perspectives)

| Dimension | Frontend Scope | Backend Scope |
| :--- | :--- | :--- |
| **Happy Path** | Valid input, state updates, expected navigation | Valid auth, valid payload, 2xx status, expected DB mutations |
| **Auth & Access** | Protected route redirects, hidden UI vs access gates | 401 unauthenticated, 403 forbidden, tenant boundary violations |
| **Validation & Bounds**| Empty/missing fields, boundary limits, regex, format | Missing keys, bad types, min/max limits, oversized payloads |
| **Failures & Errors** | API errors (4xx, 5xx), timeouts, offline, retries, SSR hydration mismatch | DB connection loss, 3rd-party API failures, transaction rollbacks |
| **Concurrency/Timing**| Double clicks, rapid toggles, unmount during fetch, multi-tab storage conflict | Duplicate requests, idempotency keys, race conditions, locking |
| **Edge & Environment** | Keyboard focus, responsive views, locale/timezone, upload abort, cache serialization roundtrip | Out-of-order events, expired tokens, partial batch failures, WS reconnect |

---

## Priority Classification

- **P0 (Critical):** Auth bypass, data corruption/loss, payment/money operations, security boundaries, irreversible actions.
- **P1 (High):** Core business workflows, critical API contracts, primary revenue/user flows.
- **P2 (Medium):** Secondary features, recoverable error states, UI polish, edge-case validations.
- **P3 (Low):** Minor cosmetic issues, rare non-critical edge paths.

---

## Execution Protocol

### Phase 1: Context & Framework Detection
- Detect frameworks, test runners (Jest, Vitest, Playwright, Pytest, Go test), and existing test directories.

### Phase 2: Surface Inventory Discovery
- Scan codebase to build explicit inventories of FE elements and BE handlers.

### Phase 3: Matrix Generation & Gap Analysis
- Map existing tests against discovered surfaces. Mark each item: `COVERED`, `PARTIAL`, `MISSING`, `MANUAL`, or `UNKNOWN`.

### Phase 4: Saturation Sweep (Optional `--saturation`)
- Expand edge cases across auth, validation, concurrency, and error handling until no new meaningful tests emerge.

### Phase 5: Implementation & Execution (Optional `--write-tests` / `--run`)
- If `--write-tests`: Implement tests prioritizing P0/P1 gaps using repo conventions.
- If `--run`: Execute tests, log results, and flag failures. *(Never weaken test assertions to force green)*.

---

## Output Formats

### Standard Audit Report

```markdown
## 🧪 Test Audit Report: [Scope]

### 1. Inventory & Coverage Summary
- **Frontend:** [Routes: N] | [Interactive Controls: N] | [State Transitions: N]
- **Backend:** [Endpoints: N] | [Services/Domain Rules: N] | [Async/Jobs: N]
- **Coverage Status:** [Covered: N] | [Partial: N] | [Missing: N] | [Unknown: N]

### 2. High-Priority Coverage Gaps (P0 / P1)
| ID | Layer | Target / Contract | Scenario | Priority | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `GAP-01` | BE | `POST /orders` | Duplicate request with same idempotency key | P0 | Missing |
| `GAP-02` | FE | `CheckoutForm` | Network failure during payment submission | P1 | Partial |

### 3. Saturation & Unresolved Behavior
- **Saturation:** [Completed after N passes / Not requested]
- **Unknowns (`NEEDS SPEC`):** [Any business rule where expected outcome cannot be verified from code]

### 4. Recommended Next Action
- `--write-tests` for gaps `GAP-01` through `GAP-N`
- `/debug` if existing test runs revealed broken assertions
```

---

## Definition of Done

1. All FE/BE surfaces in scope discovered and inventoried.
2. Discovered behaviors mapped to existing tests with explicit gap status (`COVERED` / `MISSING` / `PARTIAL`).
3. Concrete, prioritized test scenarios generated for all gaps.
4. If `--write-tests` or `--run` was set, test execution and file creation verified.
