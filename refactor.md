---
description: ♻️ Safe code refactoring workflow. Improve structure, readability, and maintainability without changing intended behavior.
---

# /refactor - Behavior-Preserving Refactor Mode

$ARGUMENTS

## Purpose & Core Rule

Activates REFACTOR mode for structural improvements without altering user-visible or external behavior.

> **REFACTOR = CHANGE THE CODE SHAPE, KEEP THE BEHAVIOR CONTRACT**

- Improve structure, cohesion, separation of concerns, and naming clarity.
- Reduce duplication, control flow complexity, and coupling.
- **Strictly behavior-preserving:** Do NOT add features, change APIs, or mix bug fixes into the refactor pass. (Use `/enhance` or `/debug` if behavior must change).

---

## When to Use

- **Use when:** Modules are oversized/mixed-responsibility, duplicate logic exists, naming is misleading, dependencies violate layers, or code requires cleanup before adding features.
- **Do not use:** When adding new functional capabilities or fixing runtime bugs.

---

## Supported Intents

```text
/refactor auth service
/refactor checkout flow without changing behavior
/refactor giant user component into smaller pieces
/refactor duplicated validation logic
/refactor api client structure
/refactor state management in dashboard
/refactor billing module for maintainability
/refactor before adding role permissions
```

---

## Safety Contract & Boundaries

### Preserve by Default
- Public API signatures, routes, request/response formats, and database/persistence semantics.
- Exported interfaces, function inputs/outputs, error contracts, and timing/order guarantees.
- **Type-Safety & Generics:** Never weaken TypeScript types to `any`/`unknown` or bypass generic inference during restructuring.
- **Database Schema & Transaction Integrity:** Refactoring ORM entities must NEVER trigger unreviewed schema migrations. Never move database operations outside of their atomic `@Transactional` or `db.transaction()` boundary.
- **Security & Secret Containment:** Never expose server-only variables, secret keys, or bypass input sanitizers/validators during extraction.

### Requires Explicit User Approval to Alter
- DB schemas, migrations, endpoints, UI workflows, business logic rules, error payloads, or storage layouts.

---

## Refactor Categories

| Category | Typical Trigger | Target Action |
| :--- | :--- | :--- |
| **1. Structural Extraction** | Large files, monolithic functions, mixed view/logic | Extract pure helpers, hooks, domain services, or child components |
| **2. Duplication Reduction** | Repeated transformations, validation, error handlers | Centralize pure utilities, shared schemas, or reusable helpers |
| **3. Boundary Cleanup** | Leaking layers, dump utilities, circular dependencies | Move logic to owning domain, enforce clean dependency direction |
| **4. Naming & Intent** | Misleading or overly generic names (`data`, `handle`) | Rename symbols to match domain, extract predicates into boolean helpers |
| **5. Control Flow** | Deep nesting, complex branching, implicit state | Use guard clauses, early returns, explicit state transition tables |

---

## Refactor Protocol

### Phase 1: Understand Current Behavior
- Trace execution paths, dependencies, side effects, and existing tests. (Run `/analyze` first if unfamiliar).

### Phase 2: Define Concrete Objective
- State explicitly what structural problem is being solved and what must stay identical.

### Phase 3: Identify Safety Boundaries
- Map exported symbols, callers, database transactions, and critical order of side effects.

### Phase 4: Strategy Selection
- Choose the smallest safe step (Priority: Rename → Extract helper → Extract module/hook → Split file → Reorganize layer).

### Phase 5: Execute in Atomic Steps
- Refactor in small, verifiable units. Verify references and syntax after each step.

### Phase 6: Validate Behavior Parity
- Run existing tests. Verify identical inputs produce identical outputs, state transitions, and error paths.

### Phase 7: Document Residual Risk
- Detail improvements, preserved contracts, remaining debt, and test status.

---

## Behavior Preservation Checklist

- [ ] Identical input/output mapping for all functions.
- [ ] Routes, events, and lifecycle triggers reach unchanged outcomes.
- [ ] Validation rules, persistence operations, and error states preserved.
- [ ] No unintended side effects or altered execution ordering.

---

## Anti-Patterns to Avoid

- Mixing feature enhancements or bug fixes into refactor passes.
- Introducing clever design patterns or abstract indirection without concrete benefit.
- Deduplicating logic that looks similar but belongs to distinct business domains.
- Silently renaming public APIs or exported contracts.
- Deleting comments containing important rationale or safety constraints.

---

## Output Formats

### Standard Format

```markdown
## ♻️ Refactor Report: [Target]

### Objective & Category
- **Target:** [Module/File]
- **Category:** [Extraction / Duplication / Boundary / Naming / Control Flow]
- **Goal:** [Specific maintenance/readability benefit]

### Preserved Contracts
- [Public API, error handling, state/persistence rules kept stable]

### Changes Made
| Area | Modification | Purpose |
| :--- | :--- | :--- |
| `src/...` | [Extracted helper / Renamed / Decoupled] | [Why this was changed] |

### Validation & Residual Risk
- **Tests & Parity:** [Test status / verified parity checks]
- **Risk Level:** Low | Medium | High
- **Next Follow-up:** `/test` | `/review` | `/enhance`
```

### Short Format

```markdown
## ♻️ Refactor: [Target]
- **Goal:** [One line]
- **Preserved:** [Contracts kept stable]
- **Changes:** [Key structural modifications]
- **Validation:** [Test/syntax check result]
- **Risk:** Low | Medium | High
```

---

## Handoff Matrix & Next Steps

| Target Goal | Workflow |
| :--- | :--- |
| Verify unchanged behavior with tests | `/test` or `/test-audit` |
| Audit structural complexity & quality | `/review` |
| Deep dive into an unclear hotspot | `/analyze` |
| Add new features on clean code | `/enhance` |

---

## Definition of Done

1. Concrete structural goal stated and executed.
2. Behavior contract explicitly verified and preserved.
3. Changes scoped, tested, and documented with residual risks noted.
