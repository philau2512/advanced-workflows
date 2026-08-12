---
name: test-audit
description: Exhaustively inventory frontend and backend behavior, generate detailed test cases for every discovered interaction/API/business path, identify coverage gaps, and optionally hand off to test implementation/execution skills.
argument-hint: "[scope] [--fe|--be|--full] [--changed] [--saturation] [--write-tests] [--run]"
---

# /test-audit — Exhaustive FE + BE Test Coverage Audit

Generate a source-grounded, feature-by-feature test inventory for the requested scope.

This workflow is designed for **coverage discovery and test-case generation first**. It must not claim coverage merely because a test suite passes or code coverage is high.

The core question is:

> For every user-visible interaction and every backend behavior in scope, what can happen, what should happen, and which concrete test proves it?

---

## Core Contract

1. **Inventory before scenarios.** Do not generate generic tests before discovering the actual application surfaces.
2. **Evidence before claims.** Every discovered surface and important behavior should be traceable to repository evidence when source is available.
3. **Behavior coverage, not line coverage.** Code coverage is supporting evidence only; it does not prove feature completeness.
4. **FE means interactions, state, and effects.** A button is not covered by merely rendering it; test its events, state transitions, network effects, navigation, errors, and relevant accessibility behavior.
5. **BE means contracts and business behavior.** An endpoint is not covered by only a 200 response; test authentication, authorization, inputs, business rules, persistence, failure paths, concurrency, and response contracts where relevant.
6. **No silent omissions.** Any skipped route, control, endpoint, function, state, role, environment, or integration must be listed with a reason.
7. **No invented behavior.** If expected behavior cannot be determined from code, tests, docs, schemas, or user requirements, mark it `UNKNOWN / NEEDS SPEC` instead of guessing.
8. **Read-only by default.** Do not modify application or test code unless `--write-tests` is explicitly requested.
9. **Do not weaken tests to make them pass.** When execution is requested, treat failures as evidence requiring investigation.
10. **Preserve project conventions.** Use the repository's current testing stack and architecture unless the user explicitly requests a migration.

---

## Default Behavior

When invoked without arguments:

- Scope: entire repository.
- Layers: frontend + backend when both exist.
- Depth: exhaustive behavior inventory.
- Output: test audit and test-case matrix only.
- Code changes: none.
- Test execution: none unless needed only to inspect existing behavior and safe to run.

If the repository contains only FE or only BE, audit the available layer and state that the other layer was not found.

---

## Flags

| Flag | Purpose |
|------|---------|
| `--fe` | Audit frontend only |
| `--be` | Audit backend only |
| `--full` | Audit both FE and BE; this is the default when both exist |
| `--changed` | Focus on changed code plus directly affected behavior and regression surfaces |
| `--saturation` | Continue edge-case generation until two consecutive passes find no meaningful new scenarios |
| `--write-tests` | After the audit is complete, implement missing automated tests using the project's existing stack |
| `--run` | Run relevant existing/generated tests and attach actual results to the report |

A path, feature name, route, module, API prefix, or symbol may be supplied as the scope.

Examples:

```text
/test-audit
/test-audit src/features/checkout
/test-audit "login and session refresh"
/test-audit --fe
/test-audit --be src/api/orders
/test-audit --changed --saturation
/test-audit src/features/payment --saturation --write-tests --run
```

---

# Workflow

## Phase 0 — Resolve Scope and Testing Context

Before generating cases:

1. Identify repository root and project type(s).
2. Identify FE framework(s), BE framework(s), language(s), package managers, and test runners.
3. Locate existing test configuration and conventions.
4. Resolve requested scope.
5. Identify whether the target is:
   - whole application,
   - feature/module,
   - route/page,
   - API group,
   - changed code,
   - specific symbol.
6. Record exclusions explicitly.

Prefer `ak:scout` for broad repository discovery if available. Use native repository search/read tools when the target is already known or the skill is unavailable.

### Scope Output

```text
Scope: [target]
Mode: FE | BE | FULL
Repository areas included: [...]
Repository areas excluded: [... + reason]
Testing stack detected: [...]
Existing test locations: [...]
Known runtime constraints: [...]
```

Do not proceed with an assumed architecture if repository evidence can resolve it.

---

## Phase 1 — Build the Application Surface Inventory

The inventory is the source of truth for completeness. Scenario generation must map back to inventory items.

### 1A. Frontend Inventory

Discover and enumerate applicable FE surfaces.

#### Routes and Screens

- routes
- nested routes
- protected routes
- layouts
- pages/screens
- dialogs routed by state/query parameters
- redirects
- deep links
- error/not-found pages

#### Interactive Elements

Inventory actual user-interactive controls, including when custom components implement them indirectly:

- buttons
- links
- text inputs
- textareas
- selects/comboboxes
- checkboxes
- radio buttons
- switches/toggles
- forms
- file uploads
- file downloads
- menus
- context menus
- dropdowns
- tabs
- accordions
- modals/dialogs/drawers
- tooltips/popovers when behavior matters
- pagination
- search controls
- filters
- sort controls
- date/time pickers
- steppers
- sliders
- tables with actions
- cards with click actions
- drag/drop targets
- infinite scroll/load-more controls
- copy/share controls
- retry/cancel controls
- keyboard-only actions
- touch/mobile gestures when supported

#### Events

Map events that can alter behavior:

- click
- double click when relevant
- submit
- input/change
- focus
- blur
- keydown/keyup
- Enter/Escape/Tab behavior
- hover
- drag/drop
- scroll
- touch/swipe
- resize/orientation when behavior changes
- route navigation
- browser back/forward
- refresh/reload
- visibility/focus restoration

#### UI States

For each meaningful feature/control, identify applicable states:

- initial
- loading
- success
- empty
- validation error
- server error
- offline/network failure
- timeout
- retrying
- disabled
- read-only
- unauthorized
- forbidden
- stale data
- optimistic update
- rollback
- partially loaded
- already completed
- duplicate action
- session expired

#### Client-Side Data and Effects

Map relevant:

- API calls
- query/mutation hooks
- local component state
- shared/global state
- cache behavior
- optimistic updates
- debouncing/throttling
- localStorage/sessionStorage/cookies
- URL/query/search params
- form state and validation
- event subscriptions
- timers
- websocket/SSE behavior
- analytics or side-effect events when product-critical

### FE Inventory Row

Each discovered surface should be representable as:

| Field | Meaning |
|------|---------|
| ID | Stable audit ID, e.g. `FE-INV-001` |
| Feature | User/business feature |
| Route/Screen | Where the surface exists |
| Source | File/symbol evidence |
| Element/Surface | Button/input/form/menu/etc. |
| Events | Relevant trigger events |
| States | Meaningful states |
| Effects | API/navigation/state/storage/etc. |
| Roles | User roles/personas that change behavior |
| Existing Tests | Test references if found |

Do not collapse multiple controls into one row when they have independently testable behavior.

---

### 1B. Backend Inventory

Discover and enumerate applicable BE surfaces.

#### External Contracts

- REST endpoints
- GraphQL queries/mutations/subscriptions
- RPC handlers
- websocket messages
- webhook receivers
- public SDK/service interfaces
- message consumers/producers

For each endpoint/handler map:

- method/operation
- path/topic/name
- authentication
- authorization/role rules
- path parameters
- query parameters
- headers
- request body/schema
- validation
- response status/code
- response schema
- side effects
- idempotency behavior
- rate/abuse controls when present

#### Application and Domain Logic

Inventory business-significant code paths:

- controllers/handlers
- services/use cases
- commands/queries
- exported/public business functions
- domain functions
- validators
- policy/permission functions
- state machines
- calculation/pricing functions
- serializers/mappers when contract-critical

Do not create noise by treating trivial generated accessors or obvious constant-only helpers as independent business features. Include them only when they contain behavior worth testing.

#### Persistence

Map:

- repositories/DAOs
- database queries
- transactions
- unique constraints
- foreign-key behavior
- migrations relevant to current behavior
- locking/concurrency behavior
- cache read/write/invalidation
- rollback paths

#### Background and Integration Behavior

Map:

- cron jobs
- scheduled tasks
- queues
- workers
- retries
- dead-letter handling
- webhooks
- third-party APIs
- email/SMS/push delivery
- object/file storage
- payment providers
- search/indexing
- event bus integrations

### BE Inventory Row

| Field | Meaning |
|------|---------|
| ID | Stable audit ID, e.g. `BE-INV-001` |
| Feature | Business capability |
| Contract/Symbol | Endpoint, handler, service, function, job, etc. |
| Source | File/symbol evidence |
| Inputs | Request/function inputs |
| Auth | Authentication/authorization requirements |
| Business Rules | Important invariants/branches |
| Side Effects | DB/cache/queue/external effects |
| Failure Modes | Known error paths |
| Existing Tests | Test references if found |

---

## Phase 2 — Trace FE ↔ BE Behavior

When both layers exist, connect user actions to backend behavior.

For each relevant FE action, trace when possible:

```text
User action
  -> component/event handler
  -> client validation/state transition
  -> API/query/mutation
  -> route/controller
  -> service/use case
  -> repository/integration
  -> response/event
  -> client state update
  -> rendered user outcome
```

Record contract mismatches or uncertain links.

Important examples:

- button triggers wrong endpoint
- UI assumes a response field not guaranteed by schema
- backend allows state the UI cannot represent
- UI hides action but BE authorization does not enforce it
- retry can create duplicate writes
- FE optimistic update cannot recover from BE rejection

Do not assume FE restrictions are backend authorization.

---

## Phase 3 — Inventory Existing Tests

Locate existing automated tests and map them to the behavior inventory.

Classify each relevant test as:

- unit
- component
- integration
- API
- contract
- E2E
- visual regression
- accessibility
- security
- load/performance

For every inventory item, record one of:

- `COVERED` — concrete existing test proves the behavior
- `PARTIAL` — some behavior is tested, meaningful cases are missing
- `MISSING` — no relevant automated test found
- `MANUAL` — intentionally manual with reason
- `UNKNOWN` — mapping cannot be proven

### Important Rule

Never mark an inventory item `COVERED` only because:

- the source file has high line coverage,
- a broad E2E test passes through the screen,
- a snapshot exists,
- the endpoint is indirectly called by another test.

Coverage requires an assertion that proves the behavior being claimed.

---

# Phase 4 — Generate Detailed Test Cases

Generate cases from actual discovered behavior, not a generic checklist.

Every test case must include enough information that another engineer can implement it without guessing the intended behavior.

## Common Test Case Fields

| Field | Required Content |
|------|------------------|
| ID | Stable ID: `FE-TC-*`, `BE-TC-*`, `X-TC-*` |
| Inventory ID | Surface being covered |
| Feature | Human-readable capability |
| Level | unit/component/integration/API/E2E/etc. |
| Priority | P0/P1/P2/P3 |
| Scenario | One concrete behavior |
| Preconditions | Required state/user/data |
| Test Data | Specific values/classes of values |
| Trigger / Steps | Exact action or invocation |
| Expected Result | Observable result/assertion |
| Side Effects | Expected DB/cache/network/navigation/events |
| Negative Assertions | What must NOT happen when relevant |
| Existing Test | Existing file/test name or `MISSING` |
| Evidence | Source references supporting the case |
| Notes | Unknowns, setup, cleanup, flakiness concerns |

---

## 4A. Frontend Test Generation Rules

For every interactive FE surface, evaluate all relevant categories below.

### A. Rendering and Availability

- renders when allowed
- hidden when not allowed
- disabled/enabled rules
- correct initial value/state
- conditional rendering
- loading placeholder/skeleton
- empty state
- error state

### B. Direct Interaction

For the events actually supported by the surface:

- click
- repeated click/double submission
- keyboard activation
- input/change
- focus/blur validation
- Enter submit
- Escape close/cancel
- Tab navigation
- hover behavior
- drag/drop
- scroll/load-more
- touch/gesture

Do not generate irrelevant event tests merely to inflate count.

### C. Input Classes

When user input exists, cover applicable:

- valid typical value
- empty
- null/undefined where programmatically possible
- whitespace-only
- leading/trailing whitespace
- minimum boundary
- maximum boundary
- below minimum
- above maximum
- wrong type/format
- special characters
- unicode
- emoji when relevant
- very large input
- duplicate value
- stale value
- pasted value
- autofill behavior when relevant

### D. State Transitions

Test applicable transitions such as:

```text
initial -> editing
editing -> validating
valid -> submitting
submitting -> success
submitting -> error
error -> retrying
retrying -> success
optimistic -> confirmed
optimistic -> rollback
active session -> expired session
```

### E. Network Outcomes

For FE behavior that depends on network calls, cover applicable:

- success
- validation response
- unauthenticated response
- forbidden response
- not found
- conflict
- rate limited
- server error
- timeout
- offline/network disconnect
- malformed/unexpected response when robustness matters
- retry behavior
- canceled request
- out-of-order responses/race when possible

### F. Navigation

When the feature navigates:

- correct destination
- query/path params
- deep link
- browser back
- browser forward
- refresh on destination
- unsaved-state handling
- protected-route redirect
- redirect-back after login when supported

### G. Repeated / Concurrent User Actions

Applicable examples:

- double click submit
- rapid toggle
- rapidly changing filters
- typing during pending request
- navigating away during request
- two tabs editing same entity
- stale page submits after server state changed

### H. Accessibility

For meaningful interactive UI, include applicable:

- keyboard operability
- focus order
- visible/managed focus
- accessible name/label
- dialog focus trap/restore
- error announcement
- form labels/descriptions
- disabled semantics
- screen-reader-relevant state

Use `ak:web-testing` guidance for browser/accessibility-specific implementation when available.

### I. Responsive / Environment

When layout or behavior changes by environment:

- desktop
- mobile
- tablet/breakpoints
- touch vs pointer
- orientation
- reduced motion when relevant
- slow network
- locale/timezone
- browser differences when project supports multiple browsers

### J. Observable Side Effects

Assert relevant effects, for example:

- exactly one API request
- no API request when validation fails
- correct request payload
- cache invalidation
- optimistic update
- rollback
- toast/banner
- analytics event if product-critical
- download started
- clipboard updated
- navigation occurred
- modal closed/remained open

---

## 4B. Backend Test Generation Rules

For every external contract and business-significant symbol, evaluate relevant categories below.

### A. Happy Path

- valid request/input
- valid authenticated user
- valid state
- expected return/status/schema
- expected persistence/side effects

### B. Authentication

When protected:

- no credentials
- valid credentials
- invalid credentials
- expired credentials
- revoked credentials when supported

### C. Authorization

When role/ownership rules exist:

- allowed role
- forbidden role
- resource owner
- non-owner
- tenant isolation
- privilege escalation attempt

### D. Input Validation

Applicable classes:

- missing required field
- null
- empty
- wrong type
- malformed value
- invalid enum
- unknown/extra fields
- min/max boundary
- string length boundaries
- numeric boundaries
- negative/zero values
- unicode/encoding
- oversized payload
- duplicate identifiers
- invalid references

### E. Business Rules

Generate explicit cases for every discovered branch/invariant, such as:

- invalid state transition
- insufficient balance/stock/quota
- expired object
- already completed/canceled object
- duplicate operation
- conflicting operation
- pricing/discount combinations
- rounding
- limits/free-tier rules
- ownership rules

### F. Persistence and Transactions

Applicable cases:

- write succeeds
- unique constraint violation
- referenced object missing
- transaction rollback
- partial failure
- DB unavailable
- read-after-write behavior
- concurrent write conflict
- stale version/optimistic locking

### G. Idempotency and Concurrency

Applicable cases:

- same request repeated
- same idempotency key repeated
- same operation executed concurrently
- duplicate webhook
- retry after timeout
- out-of-order events
- race between update/delete
- race around quota/stock/balance

### H. Integration Failures

For third-party dependencies:

- success
- timeout
- 4xx
- 5xx
- invalid response
- partial response
- retryable failure
- non-retryable failure
- rate limit
- circuit breaker/fallback if implemented
- duplicate callback/webhook

### I. Response Contract

Assert applicable:

- status/error code
- schema
- required fields
- types
- headers
- pagination metadata
- stable error shape
- no sensitive fields
- no stack trace/internal leakage

### J. Functions and Services

For business-significant functions, cover applicable:

- typical inputs
- empty inputs
- boundaries
- invalid inputs
- branch conditions
- exception propagation/translation
- side effects
- mutation vs immutability expectations
- deterministic behavior
- ordering
- rounding/precision
- time-dependent behavior with controllable clock where architecture supports it

---

# Phase 5 — Edge-Case Saturation

After baseline cases exist, expand them using `ak:scenario` when available.

For each logical feature or high-value behavior group, apply the relevant dimensions from `ak:scenario`:

1. User Types
2. Input Extremes
3. Timing
4. Scale
5. State Transitions
6. Environment
7. Error Cascades
8. Authorization
9. Data Integrity
10. Integration
11. Compliance
12. Business Logic

Do not duplicate `ak:scenario`'s internal guidance inside this workflow when the skill is available; invoke/use it as the source of truth for scenario expansion.

### Saturation Policy

If `--saturation` is set:

1. Generate a pass of novel cases.
2. Deduplicate against all already-kept cases.
3. Run another pass emphasizing uncovered dimensions, combinations, ordering, interruption, stale state, and persona changes.
4. Continue until **two consecutive passes produce zero meaningful new cases**.
5. Report how many cases were discarded as duplicates/out-of-scope.

If `ak:scenario` is unavailable, perform the same novelty/deduplication loop manually using the 12 dimensions above.

Do not claim "exhaustive" merely because a fixed number of scenarios was generated.

---

# Phase 6 — Assign the Right Test Level

Avoid forcing every scenario into E2E.

Choose the cheapest reliable level that proves the behavior.

| Behavior | Preferred Level |
|----------|-----------------|
| Pure calculation/validation | Unit |
| UI component state/event | Component |
| Service + DB behavior | Integration |
| HTTP/GraphQL contract | API/Contract |
| Critical FE->BE user journey | E2E |
| Browser layout rendering | Visual regression |
| Keyboard/ARIA behavior | Accessibility/component/E2E |
| Cross-service schema compatibility | Contract |
| Concurrency/transaction behavior | Integration |
| Load/capacity behavior | Performance/load |
| Security boundary | Security/API/integration |

Use `ak:web-testing` as the source of truth for Playwright/Vitest/browser/visual/a11y/load-specific implementation patterns when available.

Use `ak:test` as the source of truth for test execution, coverage execution, and QA result reporting when available.

---

# Phase 7 — Completeness and Gap Audit

Perform a final audit against the inventory, not against the generated test list alone.

## Frontend Completeness Gates

For every discovered FE surface, verify:

- every route/screen is represented
- every independently interactive control is represented
- every meaningful event is represented
- every meaningful state transition is represented
- every permission/role variant is represented
- every network-backed control has success + relevant failure behavior
- every critical navigation path is represented
- every form has field-level + submission-level behavior represented
- relevant accessibility behavior is represented
- relevant responsive/environment behavior is represented

## Backend Completeness Gates

Verify:

- every public API/handler in scope is represented
- every auth/authz boundary is represented
- every request field/schema rule is represented where behavior differs
- every business-significant branch/invariant is represented
- persistence failure/transaction behavior is represented where applicable
- concurrency/idempotency is represented where applicable
- every external integration has success + relevant failure behavior
- jobs/queues/webhooks are represented
- important exported/public business functions are represented

## Cross-Layer Completeness Gates

When FE and BE coexist:

- critical FE actions map to expected backend contracts
- critical backend capabilities have an invoking client/admin/integration path or are explicitly backend-only
- FE and BE validation differences are identified
- auth visibility vs auth enforcement differences are identified
- response-schema assumptions are identified
- retries/optimistic UI/duplicate-write risks are identified

### Unknowns

Any unresolved behavior must be listed under:

```text
UNKNOWN / NEEDS SPEC
```

An audit with unknowns may still be useful, but it must not be labeled fully complete.

---

# Phase 8 — Output Artifacts

Produce a structured report. Prefer project organization conventions if an installed project-organization skill defines them.

At minimum, the report must contain the following sections.

## 1. Audit Scope

- target
- layers
- detected stack
- exclusions
- assumptions
- unknowns

## 2. Surface Inventory Summary

Example:

```text
Frontend
  Routes/screens:          18
  Interactive controls:  127
  Forms:                   22
  Distinct user events:   311
  Network-backed actions:  64

Backend
  API operations:          67
  Services/use cases:      41
  Business functions:      94
  Jobs/workers:            12
  Webhooks:                 7
  External integrations:    5
```

Counts must come from the actual audit. Never invent placeholder numbers in a real report.

## 3. Existing Coverage Mapping

```text
COVERED: N
PARTIAL: N
MISSING: N
MANUAL: N
UNKNOWN: N
```

Break down by FE/BE and by test level when useful.

## 4. Master Test Matrix

Recommended columns:

| ID | Layer | Inventory ID | Feature | Target | Scenario | Preconditions | Steps/Input | Expected | Side Effects | Type | Priority | Existing Test | Status |
|----|-------|--------------|---------|--------|----------|---------------|-------------|----------|--------------|------|----------|---------------|--------|

## 5. Coverage Gaps

Group by:

- P0 missing
- P1 missing
- partially covered
- untestable/needs refactor
- unknown/needs product spec
- environment/setup blockers

## 6. Traceability

Provide enough source references so engineers can trace each major inventory area and high-priority test case back to code.

## 7. Saturation Summary

When saturation is used:

```text
Passes: N
New cases kept: N
Variants kept: N
Duplicates discarded: N
Out-of-scope discarded: N
Final dry passes: 2
Remaining known gaps: [...]
```

## 8. Recommended Automation Order

Prefer:

1. P0 missing tests
2. auth/data integrity/payment/destructive paths
3. P1 critical user journeys
4. business-rule integration tests
5. component/unit gaps
6. broader regression/visual/a11y/performance coverage

---

# Priority Rules

| Priority | Meaning |
|----------|---------|
| **P0** | Security boundary, auth bypass, money, irreversible action, data loss/corruption, critical outage path |
| **P1** | Core user/business flow or major regression risk |
| **P2** | Important secondary behavior, recoverable errors, meaningful UX |
| **P3** | Low-impact polish or rare non-critical behavior |

Priority must reflect product impact and failure severity, not how easy the test is to write.

---

# Optional: `--write-tests`

Only after the inventory, matrix, and gap audit are complete:

1. Select missing cases in priority order.
2. Preserve repository testing conventions.
3. Use `ak:test` for code-level test implementation/execution guidance when available.
4. Use `ak:web-testing` for Playwright/Vitest/browser/API/load/a11y-specific patterns when available.
5. Prefer deterministic tests and stable selectors/contracts.
6. Avoid unnecessary production-code changes solely to satisfy tests.
7. If production changes are required for testability, report them separately and do not silently refactor.
8. Keep each test traceable to a test-case ID when practical.

Do not implement every low-value permutation merely to maximize test count. Implement the smallest set of automated tests that proves the distinct behaviors identified by the audit.

---

# Optional: `--run`

When test execution is requested:

1. Run the narrowest relevant suites first.
2. Then run broader regression suites if scope warrants it.
3. Record exact commands and actual results.
4. Never report tests as passing if they were not executed successfully.
5. Do not ignore, disable, skip, or weaken failing tests to obtain green status.
6. Route real failures into `ak:debug` / `ak:fix` when available and when fixing is within user scope.

---

# Anti-Patterns

Do NOT:

- generate 500 generic cases without mapping them to actual source behavior
- equate line coverage with feature coverage
- test only happy paths
- treat a rendered button as tested without testing its action
- test only frontend validation for security-sensitive rules
- assume hidden UI controls provide backend authorization
- write one giant E2E test for an entire product journey
- duplicate the same scenario across unit/component/E2E without a reason
- invent expected outcomes from intuition
- ignore jobs, webhooks, queues, retries, or background behavior
- ignore browser back/refresh/session restoration for stateful flows
- ignore concurrency/idempotency for money, stock, quota, booking, or destructive operations
- silently skip files or surfaces because the repository is large
- claim exhaustive coverage when discovery was sampled or bounded

---

# Completion Definition

The workflow is complete only when all of the following are true for the requested scope:

- scope and exclusions are explicit
- FE/BE surfaces have been inventoried as applicable
- existing tests have been mapped to discovered behavior
- detailed test cases exist for all meaningful discovered behaviors
- critical edge cases have been expanded
- every inventory item is `COVERED`, `PARTIAL`, `MISSING`, `MANUAL`, or `UNKNOWN`
- P0/P1 gaps are explicitly listed
- source traceability exists for major/high-risk behavior
- unresolved expected behavior is marked `UNKNOWN / NEEDS SPEC`
- no unsupported completeness claim is made
- if `--saturation` was requested, two consecutive dry passes produced no meaningful new scenarios
- if `--write-tests` was requested, created tests map back to audit cases
- if `--run` was requested, actual execution results are recorded

---

# Skill Routing

Use installed skills as specialized sources of truth instead of duplicating their full implementation guidance:

```text
Repository discovery        -> ak:scout
Deep behavior tracing       -> /analyze if installed; otherwise native read/search
Edge-case expansion         -> ak:scenario [--saturation]
Browser/UI/API test patterns-> ak:web-testing
Test implementation/run     -> ak:test
Failure diagnosis           -> ak:debug
Bug fixing                  -> ak:fix
```

If a referenced skill/workflow is unavailable, continue with the safest native equivalent instead of blocking or pretending the skill exists.

---

# Recommended Execution Shape for Large Repositories

For a full application audit, work feature-by-feature rather than file-by-file:

```text
Discover all surfaces
        |
        v
Build feature groups
        |
        +--> FE inventory per feature
        |
        +--> BE inventory per feature
        |
        +--> FE <-> BE trace
        |
        +--> existing test mapping
        |
        +--> baseline test cases
        |
        +--> scenario saturation
        |
        v
Global completeness sweep
        |
        v
Deduplicate + prioritize
        |
        v
Master test matrix + gaps
```

For very large codebases, intermediate results may be persisted incrementally, but the final completeness sweep must compare all discovered inventory items against the final test matrix.

---

# Final Response Shape

Keep the chat response concise and point to the audit artifacts when files were created. Summarize:

```text
Test audit complete for: [scope]

FE surfaces: N
BE surfaces: N
Test cases: N
P0 gaps: N
P1 gaps: N
Unknown/spec gaps: N
Existing coverage: [summary]
Saturation: [not requested | completed after N passes]

Top gaps:
1. ...
2. ...
3. ...
```

Do not dump the entire master matrix into chat when it is too large; persist it using the repository's normal documentation/output conventions and provide the path.
