---
description: 🧪 Comprehensive Test Audit, Mocking & Test Data Engine. Inventory test coverage gaps, generate automated test suites, build MSW API mocks, and create relational seed fixtures.
argument-hint: "[scope] [--mock] [--seed] [--write-tests] [--run] [--fe|--be|--full]"
---

# /test - Comprehensive Testing, Mocking & Fixture Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **UNIFIED TESTING & QUALITY MODE** to discover test coverage gaps, generate automated test cases, simulate backend APIs via MSW mocks, and generate relational test data.

> **IRON INVARIANTS:**
> 1. **BEHAVIOR OVER RAW LINE COVERAGE:** Prioritize edge cases, validation bounds, and state mutations over superficial line coverage.
> 2. **CONTRACT FIDELITY:** Mock handlers and test fixtures must strictly conform to actual TypeScript types, Zod schemas, or DB models.
> 3. **RELATIONAL INTEGRITY & IDEMPOTENCY:** Seed scripts must maintain strict foreign-key order (parents before children) and support idempotent execution (`upsert` or `ON CONFLICT`).
> 4. **ZERO PRODUCTION CONTAMINATION:** Mock handlers, synthetic seeders, and fixtures must be strictly isolated from production build bundles (`process.env.NODE_ENV !== 'production'`).
> 5. **STRICTLY READ-ONLY AUDIT BY DEFAULT:** Never generate or modify test files during an audit unless `--write-tests` is explicitly provided.

---

## ⚙️ Argument Parsing & Usage Modes

```text
# 1. Test Coverage Audit (Discover gaps across FE & BE):
/test [--fe|--be|--full] [--changed] [--write-tests] [--run]

# 2. Flaky Test Stress Discovery (Repeated execution to expose non-determinism):
/test --flaky [test-file] [--count 5]

# 3. Mock API & Network Simulation:
/test --mock <endpoint or feature> (e.g. /test --mock POST /api/checkout)

# 4. Relational Test Data & Seeding:
/test --seed <model or domain> [--count <N>] [--locale <vi|en>]
```

### Flags:
- `--flaky`: Executes targeted tests repeatedly (default: 5 runs) under concurrent load to isolate flaky tests and shared state leaks.
- `--write-tests`: Generates real automated test files (Vitest, Jest, Playwright) for identified coverage gaps.
- `--mock`: Creates contract-faithful MSW (Mock Service Worker) handlers or fixture responses.
- `--seed`: Generates relational DB seed scripts or JSON fixtures with foreign-key integrity.
- `--run`: Executes the relevant test runner and reports pass/fail counts.
- `--changed`: Restricts testing/audit scope strictly to git-modified files and their regression blast radius.
- `--fe` / `--be` / `--full`: Targets Frontend surfaces only, Backend surfaces only, or fullstack (default).

---

## 🔬 Mode A: Surface Inventory & Test Audit (Default)

Exhaustively maps actual application surfaces and generates test scenarios:

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Surface Inventory   │ ──► │ 2. Scenario Matrix     │ ──► │ 3. Gap Analysis        │
│    (Routes, DTOs, UI)  │     │    (Happy, Auth, Bounds│     │    (Missing tests)     │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
                                                                          ▼
                                                              ┌────────────────────────┐
                                                              │ 4. Optional Execution  │
                                                              │    (--write-tests/--run│
                                                              └────────────────────────┘
```

### 1. Surface Checklist:
- **Frontend Surfaces (`--fe`):** Interactive inputs, loading/error states, empty states, optimistic rollback, auth redirects, boundary fallbacks, debounce/throttling, SSR hydration.
- **Backend Surfaces (`--be`):** Input schema validation, auth/RBAC guards, business rule invariants, atomic DB transactions, 4xx/5xx status codes, rate limits, webhooks.

### 2. Scenario Generation Matrix:

| Dimension | Frontend Verification Scope | Backend Verification Scope |
| :--- | :--- | :--- |
| **1. Happy Path** | Valid input submission, optimistic update, state sync | Valid JWT, valid payload, 201/200 response, DB record written |
| **2. Auth & Access** | Protected route redirects, hidden UI controls | 401 unauthenticated, 403 forbidden, tenant boundary violations |
| **3. Validation & Bounds** | Empty fields, min/max length, regex, invalid formats | Missing required DTO keys, invalid types, payload size limits |
| **4. Failures & Errors** | API errors (4xx, 5xx), offline retry, network drop | DB connection drop, 3rd-party API timeout, transaction rollback |
| **5. Concurrency & Race**| Rapid double-clicks, debounced search cancels | Concurrent balance deduction, optimistic lock collisions |
| **6. State & Lifecycle** | Unmount cleanup, cache invalidation, SSR hydration | Background cron failure, queue consumer retry & dead-lettering |

---

## 🧪 Mode B: API Mocking & Network Simulation (`--mock`)

Generates contract-faithful MSW (Mock Service Worker) handlers or isolated HTTP test servers:

```typescript
import { http, HttpResponse, delay } from 'msw';

export const handlers = [
  http.post('/api/v1/orders', async ({ request }) => {
    // 1. Simulate realistic latency
    await delay(300);

    // 2. Parse & validate request payload
    const body = await request.json() as { items: Array<{ id: string; qty: number }> };

    // 3. Negative scenario simulation
    if (!body.items?.length) {
      return HttpResponse.json({ error: 'Cart is empty' }, { status: 400 });
    }

    // 4. Positive scenario response
    return HttpResponse.json({
      id: 'ord_mock_123',
      status: 'confirmed',
      totalAmount: 450000,
    }, { status: 201 });
  }),
];
```

### Mocking Best Practices:
- **Stateful Mocking:** Use in-memory stores (`@mswjs/data` or module-scoped maps) to simulate realistic CRUD persistence across multi-step UI flows.
- **Network Chaos Testing:** Add configurable latency (`delay(800)`), status overrides (`HttpResponse.json(..., { status: 401 })`), or network drops (`HttpResponse.error()`).
- **Production Safety:** Wrap worker registration inside `if (process.env.NODE_ENV === 'development')` to ensure zero production bundle bloat.

---

## 🎲 Mode C: Relational Synthetic Seed Data (`--seed`)

Generates typed, relational fixtures or idempotent DB seeders (Prisma, Drizzle, TypeORM, raw SQL):

### 5 Seeding Best Practices:
1. **Relational Top-Down Order:** Seed parents before children (`User` -> `Organization` -> `Order` -> `OrderItem`).
2. **Reverse Cleanup Order:** When resetting data, delete children before parents to avoid foreign-key constraint violations.
3. **Idempotent Upserts:** Always use `upsert` or `ON CONFLICT (id) DO UPDATE/NOTHING` instead of raw `insert` to ensure scripts can be re-run safely.
4. **Deterministic Sequences:** Use incrementing counters or seeded random generation for foreign keys and unique constraints.
5. **Batching for High Volumes:** When generating >1000 records, batch database inserts in chunks of 500 records to prevent memory exhaustion and timeout.
6. **Environment Guards:** Abort immediately if `process.env.NODE_ENV === 'production'`.

---

## 🔁 Mode D: Flaky Test Isolation & Non-Determinism Defense (`--flaky`)

Runs targeted tests in repeated tight loops (e.g. 5–10 iterations) to expose hidden race conditions and state leakage.

### 4 Root Causes of Flaky Tests & Defenses:
1. **Unmocked Time & Clocks:** Direct calls to `Date.now()` or `new Date()` causing failures across midnight, leap years, or timezones.
   - *Fix:* Freeze clock with fake timers (`vi.useFakeTimers()`); advance explicitly via `vi.advanceTimersByTime(ms)`.
2. **Asynchronous Polling & Arbitrary Sleep:** Using `await sleep(100)` hoping background operations finish in time.
   - *Fix:* Replace arbitrary sleep with deterministic waiting: `await waitFor(() => expect(...))` or Playwright auto-retrying locators.
3. **Leaky Test State & Shared Fixtures:** Mutation of shared module singletons or DB rows without cleanup.
   - *Fix:* Enforce test isolation via `beforeEach` / `afterEach` with `vi.clearAllMocks()`, `vi.resetModules()`, and transactional DB rollbacks.
4. **Unseeded Randomness:** Using `Math.random()` or unseeded faker libraries producing payloads that occasionally fail validation.
   - *Fix:* Seed pseudo-random generators (e.g. `faker.seed(1234)`) so failures can be replayed identically.

---

## 📋 Standard Testing Output Template

```markdown
## 🧪 Test & Quality Report: [Target Scope]

### 1. Surface Coverage & Gap Audit

| Layer | Surface / Endpoint | Existing Test | Identified Coverage Gap | Risk |
| :--- | :--- | :--- | :--- | :--- |
| **BE Route** | `POST /api/orders` | `orders.spec.ts` | Missing test for concurrency race condition | High |
| **FE Page** | `CheckoutForm.tsx` | None | Missing validation for expired coupon codes | Med |
| **Domain** | `DiscountEngine.ts`| Partial | Missing edge case for 100% discount limit | High |

---

### 2. Actions Taken
- `[MOCK GENERATED]` `src/mocks/handlers/orderMock.ts` (MSW 2.0 handler with latency & error simulation)
- `[SEED SCRIPT]` `prisma/seeds/orderSeed.ts` (10 relational orders with foreign keys & idempotent upsert)
- `[TESTS WRITTEN]` `tests/unit/discountEngine.test.ts` (4 new edge case tests covering bounds)

---

### 3. Verification & Test Execution
- **Command:** `npx vitest run tests/unit/discountEngine.test.ts`
- **Result:** **All 4 tests PASSING (100% green)**
```
