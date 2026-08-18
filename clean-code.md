---
name: clean-code
description: Audit and refactor code for maintainability, clarity, duplication, complexity, coupling, naming, testability, and unnecessary abstraction while preserving behavior and public contracts.
argument-hint: "[scope] [--audit|--apply] [--changed] [--strict] [--tdd] [--review]"
---

# /clean-code — Behavior-Preserving Clean Code Workflow

$ARGUMENTS

## Purpose & Core Contract

Audit maintainability issues with evidence and apply the smallest safe, behavior-preserving refactors.

> **CLEAN CODE = IMPROVE CODE SHAPE, PRESERVE BEHAVIOR CONTRACT**

- **Preserve strictly:** Public APIs, routes, request/response formats, exported interfaces, database/transaction semantics, auth rules, emitted events, and error behavior.
- **Type-Safety, Transactions & Secrets:** Never replace strongly typed contracts with `any`/`unknown`. Preserve ORM entity schemas and atomic database transaction blocks. Never leak server-only secrets or bypass sanitization filters.
- Do NOT combine clean-code passes with new feature work, speculative architecture rewrites, or library migrations.

---

## Flags & Modes

| Flag | Behavior |
| :--- | :--- |
| `--audit` | Read-only inspection; outputs ranked findings table without modifying source. |
| `--apply` | *(Default)* Applies confirmed findings via atomic, behavior-preserving edits. |
| `--changed` | Restricts audit/refactor to changed files and direct call sites. |
| `--strict` | Includes lower-priority (P3) maintainability debt. |
| `--tdd` | Establishes/runs characterization tests before applying non-trivial refactors. |
| `--review` | Triggers a final code review pass even for small scopes. |

---

## Clean-Code Audit Dimensions (14 Key Areas)

1. **Naming & Intent:** Flag misleading names, ambiguous abbreviations, or generic names (`data`, `temp`, `handle`) hiding side effects.
2. **Functions & Methods:** Flag monolithic responsibilities, deep nesting, boolean flag arguments, or excessive parameter clusters.
3. **Harmful Duplication:** Flag repeated business logic, validation rules, or pricing algorithms. *(Note: Do not abstract coincidental similarity).*
4. **Complexity & Branching:** Simplify deep indentation, hidden state machines, and confusing boolean logic with guard clauses and explicit flow.
5. **Cohesion & Coupling:** Enforce single responsibility; flag UI mixing with SQL/API calls or circular dependencies.
6. **Abstraction Quality:** Remove 1-use wrappers and speculative factories (over-abstraction); consolidate repeated protocol orchestration (under-abstraction).
7. **Error Handling:** Fix swallowed exceptions, loss of error context, and inconsistent error translations.
8. **State & Side Effects:** Make hidden mutations, implicit cache writes, and ordering-sensitive state updates explicit.
9. **Dead Code:** Remove unreferenced private helpers, stale feature flags, and commented-out blocks. *(Preserve exported symbols).*
10. **Comments Quality:** Keep rationale ("why"); remove comments narrating obvious syntax or compensating for bad naming.
11. **Constants & Magic Values:** Extract domain thresholds, timeouts, and business numbers; avoid extracting self-explanatory literals.
12. **Data Transformations:** Centralize DTO ↔ Domain ↔ DB mappings with clear domain owners.
13. **Testability:** Decouple hard dependencies (clocks, network, globals) only when it clarifies ownership.
14. **Frontend & Backend Specifics:**
    - *FE:* Prune prop drilling, giant components, and redundant state derivation.
    - *BE:* Enforce trust boundary validation, transaction scopes, and separate transport from domain logic.

---

## Severity Classification

- **P1 (Critical):** Drifting business/security invariants, hidden side effects, unmaintainable state mutations.
- **P2 (Major):** High cyclomatic complexity, domain duplication, mixed architectural layers, untestable logic.
- **P3 (Minor):** Local naming debt, redundant private wrappers, stale comments.

---

## Execution Protocol

### Phase 1: Baseline & Context
- Identify framework conventions, existing test suites, lint/typecheck commands, and public boundaries.

### Phase 2: Discovery & Audit
- Trace entry points, callers, dependencies, mutations, and tests. Generate ranked findings list.

### Phase 3: Challenge Findings (Safety Filter)
- Filter out purely subjective preferences. Ensure proposed changes reduce real maintenance cost without adding indirection.

### Phase 4: Safety Harness & Tests
- Locate or add characterization tests (`--tdd`) to pin existing behavior before touching risky logic.

### Phase 5: Atomic Refactoring
- Execute step-by-step: Dead code → Naming → Control flow → Helper extraction → Business logic consolidation. Verify after each step.

### Phase 6: Verification & Parity Gate
- Run test suite, linter, and type checker. Verify zero behavioral drift on all public contracts.

---

## Transformation Patterns

- **Guard Clauses:** Exit early to eliminate deep nesting.
- **Extract Pure Logic:** Separate calculation/validation from I/O (DB, HTTP, UI).
- **Domain Invariant Consolidation:** Unify identical business rules across call sites under a single owner.
- **Explicit State Transitions:** Replace complex boolean combinations with explicit status/state mappings.

---

## Anti-Patterns to Avoid

- Mechanical application of design patterns (e.g. creating 1-to-1 interfaces/repositories for everything).
- Fragmenting readable code into dozens of tiny disconnected micro-helpers.
- Swapping proven code with clever abstractions that increase indirection.
- Changing public contracts or database semantics during a cleanup pass.
- Weakening tests or altering test assertions to make refactored code pass.

---

## Output Formats

### `--audit` Output

```markdown
## 📋 Clean Code Audit: [Scope]

### Findings Matrix
| ID | Sev | Category | Location | Evidence & Impact | Proposed Refactor | Risk |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `CC-01` | P1 | Duplication | `src/auth.ts:45` | Duplicate token check | Extract token validator | Low |

### Refactor Plan & Intentional Exclusions
1. **Planned:** [Step 1 -> Step 2]
2. **Left Unchanged:** [Area + Rationale why keeping as-is is safer]
```

### `--apply` Output

```markdown
## ✨ Clean Code Result: [Scope]

- **Addressed Findings:** [Count] confirmed issues refactored.
- **Preserved Contracts:** [Public APIs, routes, schemas verified stable].
- **Key Changes:**
  - `src/...`: [What was simplified / extracted]
- **Verification:** [Test / Typecheck / Lint results]
- **Residual Debt:** [Any remaining low-priority notes]
```

---

## Definition of Done

1. Findings grounded in empirical code evidence and classified by severity.
2. Refactors applied in atomic steps with zero observable contract breakages.
3. Full verification (unit tests, typecheck, lint) executed and passing cleanly.
