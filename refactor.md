---
description: ♻️ Behavior-Preserving Refactoring, Clean Code & Safe File Splitting Engine. Refactor code structure, audit code health against 16 clean-code dimensions, or safely split oversized files with zero behavioral drift.
argument-hint: "[target-path] [--audit] [--split] [--changed] [--strict] [--tdd]"
---

# /refactor - Behavior-Preserving Refactor, Clean Code & File-Splitting Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **REFACTOR & CLEAN CODE MODE** to safely improve code readability, cohesion, architecture boundaries, and maintainability without altering external behavior.

> **IRON INVARIANTS:**
> 1. **STRICTLY BEHAVIOR-PRESERVING:** Improve code shape, preserve behavior contracts. Never add features, modify public APIs, change error payloads, or alter DB persistence semantics.
> 2. **NO TYPE REGRESSION & BOUNDARY INTEGRITY:** Never replace strong types with `any`/`unknown`. Preserve ORM schemas, `@Transactional` boundaries, auth guards, and secret containment.
> 3. **COPY-FIRST & GRADUAL REPLACEMENT:** When splitting files, NEVER delete original code prematurely. Copy to new files -> import into original -> replace unit-by-unit -> verify -> cleanup.
> 4. **BARREL RE-EXPORT GUARANTEE:** The original file path must remain active and re-export all extracted symbols to guarantee zero breaking changes for external callers.
> 5. **TEST-VERIFIED ATOMIC STEPS:** Establish characterization tests before non-trivial edits; verify syntax, types, and tests after every single extraction.

---

## ⚙️ Argument Parsing & Usage Modes

```text
# Standard Refactor (Extract functions, reduce nesting, improve naming):
/refactor <path/to/file or module> [--tdd] [--changed]

# Clean-Code Inspection & Audit:
/refactor --audit <file or directory> [--strict]

# Safe File-Splitting (Splitting oversized >500 LOC files):
/refactor --split <path/to/large-file> [--out-dir <dir>]
```

### Flags:
- `--audit`: Read-only inspection; generates a prioritized findings table (P1/P2/P3) without modifying files.
- `--split`: Engages the Staged File-Splitting protocol to decompose monolithic files into layered architectures.
- `--changed`: Restricts refactoring scope strictly to git-modified files and direct callers.
- `--strict`: Includes lower-priority maintainability debt, minor naming, and style inconsistencies.
- `--tdd`: Enforces characterization tests before modifying logic and runs targeted tests after each step.

---

## 🔬 Mode A: Clean Code Refactoring & Audit

### Severity Classification:
- **P1 (Critical):** Drifting business/security invariants, hidden mutations, broken transaction boundaries, untyped leaks (`as any`).
- **P2 (Major):** High cyclomatic complexity (>3 nesting levels), domain duplication, mixed architectural concerns, parameter clusters (>3 params).
- **P3 (Minor):** Local naming debt, redundant 1-use private wrappers, stale narrative comments, minor colocation debt.

### The 16 Clean-Code Dimensions:

| Dimension | Target Defect | Safe Refactoring Pattern |
| :--- | :--- | :--- |
| **1. Single Responsibility (SRP)** | Functions doing >1 thing; mixing UI + DB | Extract domain helpers, custom hooks, or dedicated services. |
| **2. Naming & Intent** | Generic names (`data`, `temp`, `handle`) | Rename to domain-specific verbs/nouns reflecting true intent. |
| **3. Functions & Parameters** | Parameter clusters (>3 parameters) | Group into a typed Options / DTO object. |
| **4. Nesting & Control Flow** | Deep indentation (>3 levels), nested `if/else` | Apply guard clauses and early returns. |
| **5. Harmful Duplication (DRY)**| Repeated business rules, validation logic | Consolidate under single owner (preserve coincidental similarity). |
| **6. Abstraction (YAGNI/KISS)** | 1-use speculative wrappers / over-engineering | Inline unnecessary abstractions; simplify indirection. |
| **7. Error Handling** | Swallowed errors (`catch {}`), lost context | Explicit re-throw, retain stack trace, or structured domain error. |
| **8. State & Side Effects** | Implicit mutations, hidden cache updates | Make mutations explicit; separate pure calculations from I/O. |
| **9. Dead Code & Comments** | Zombie code, commented-out blocks, obvious comments | Delete outright (git preserves history); keep "why" comments only. |
| **10. Colocation & Placement** | Variables declared far from usage, scattered constants | Colocate variables near usage; extract magic constants to module top. |
| **11. Data Transformations** | Mutating input arguments directly | Enforce immutability; centralize DTO ↔ Domain ↔ DB mappings. |
| **12. Command-Query Separation**| Functions querying data while mutating state | Separate pure queries (no side effects) from state-mutating commands. |
| **13. Type Safety & Nullability**| `any`, loose casts, missing return types | Use explicit interfaces, discriminators, optional chaining (`?.`), and `??`. |
| **14. Async / Promises** | Callback hell, mixing `.then()` with `async/await` | Standardize on clean `async/await` with localized `try/catch`. |
| **15. Testability** | Hardcoded clocks, network, or global singletons | Inject dependencies or parameterize external inputs. |
| **16. Architecture Boundaries** | Lower layer importing higher layer, leaky transport | Enforce top-down dependency flow; decouple transport from domain. |

### Anti-Patterns to Avoid:
- Mechanical application of patterns (creating 1-to-1 interfaces or repositories for everything).
- Fragmenting readable code into dozens of disconnected 2-line micro-helpers.
- Deduplicating logic that looks identical but belongs to distinct business domains.
- Weakening existing test assertions to force refactored code to pass.

---

## 📁 Mode B: Staged File-Splitting Protocol (`--split`)

### Architecture Layer Matrix:

#### Frontend Architecture (React / Next.js / Vue):
- `features/` → Domain-specific modules & orchestration (**Primary**)
- `components/` → UI presentation only (**STRICT: No direct API calls, no DB access**)
- `hooks/` → Reusable stateful & lifecycle logic (**STRICT: No JSX return**)
- `services/` → API clients & remote data fetching
- `store/` → Global / client state management
- `utils/` → Pure helper functions & stateless formatters

#### Backend Architecture (Node / Express / NestJS / FastAPI / Spring):
- `controller/router` → Request parsing, validation, HTTP response (**STRICT: No business logic**)
- `service/` → Core business logic & orchestration (**Sits between controller and repository**)
- `repository/dao` → Database queries & persistence (**STRICT: No business rules**)
- `model/entity` → Database schemas & domain entities
- `dto/schema` → Request/response contracts & transport types
- `utils/` → Pure calculation helpers & formatters

---

### Staged Splitting Workflow:

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Pre-Scan & Chunking │ ──► │ 2. Plan Architecture   │ ──► │ 3. Copy-First Extract  │
│    (Domain groups)     │     │    (FE/BE Layer Matrix)│     │    (DO NOT delete old) │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 6. Guarded Cleanup     │ ◄── │ 5. 8-Point Gate Check  │ ◄── ┌────────────────────────┐
│    (Remove old code)   │     │    (Parity & Zero Leaks│     │ 4. Wire & Gradual Repl.│
└────────────────────────┘     └────────────────────────┘     │    (Unit-by-unit)      │
                                                              └────────────────────────┘
```

1. **Pre-Scan & Chunking:**
   - For files >800 LOC: Group functions by domain feature / lifecycle before extracting.
2. **Target File Planning:**
   - Define target files following the Architecture Layer Matrix above.
   - Enforce unidirectional top-down imports; strictly forbid circular dependencies.
3. **Copy-First Extraction:**
   - Copy types, helpers, and services into new files. **DO NOT delete original code yet.**
   - Extract in sequence: Types (`.types.ts`) → Pure Utils (`.utils.ts`) → Services/Hooks → Subcomponents.
4. **Wiring & Gradual Replacement:**
   - Import new modules back into the original file.
   - Replace old internal references ONE logical unit at a time.
5. **Barrel Re-Export Guarantee:**
   - Keep the original file path active. Re-export all extracted public symbols from it so existing consumers never break.

---

### 🛡️ The 8-Point Multi-Level Self-Verification Checklist:

Before running cleanup, verify all 8 gates:
- [ ] **1. Syntax & Circular Dependencies:** Zero undefined symbols; no circular imports (`A -> B -> A`). Shared types moved to dedicated `types.ts`.
- [ ] **2. Execution Order & Flow:** Function call sequence, middleware pipeline, and argument mappings remain 100% identical.
- [ ] **3. State & Closure Integrity:** No lost state mutations, closures retain access to required variables, logging/caching calls preserved.
- [ ] **4. Async & Concurrency:** `await` chains intact; no race conditions or unhandled rejections introduced.
- [ ] **5. Server/Client Boundary Safety:** In fullstack frameworks (Next.js/Remix), NEVER import server-only logic, DB clients, or environment secrets into `"use client"` components.
- [ ] **6. CSS Cascade & DOM Hierarchy:** Subcomponents preserve CSS specificity, flex/grid parent-child relationships, and Tailwind class priorities.
- [ ] **7. Barrel Re-Export Integrity:** All external consumers import seamlessly from the original path with zero regressions.
- [ ] **8. Compiler & Test Gate:** `tsc --noEmit` (or language linter) is clean (0 errors) and characterization tests pass 100%.

---

## 📋 Standard Output Templates

### 1. Clean Code Audit Report (`--audit`)

```markdown
## 📋 Clean Code Audit: [Target Scope]

### Findings Matrix
| ID | Sev | Dimension | Location | Issue & Evidence | Proposed Refactor | Risk |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `CC-01` | P1 | State/Side-Effects | `src/auth.ts:45` | Global cache mutation inside getter | Extract to explicit Command | Low |
| `CC-02` | P2 | Nesting & Flow | `src/order.ts:88` | 4-level nested if/else logic | Refactor to Guard Clauses | Low |

### Refactor Strategy & Exclusions
1. **Prioritized Action Plan:** [Step 1 (P1) -> Step 2 (P2)]
2. **Intentional Exclusions:** [Flagged area kept as-is + rationale why abstraction is premature]
```

### 2. Refactor & Splitting Execution Report

```markdown
## ♻️ Refactoring Report: [Target Module / File]

### 1. Scope & Execution Mode
- **Target:** `[path/to/file]`
- **Mode:** [Standard Refactor / File Split]
- **Verification Baseline:** [Pre-refactor test: PASS]

---

### 2. Summary of Structural Changes

| File / Component | Layer / Change Type | Improvements Applied |
| :--- | :--- | :--- |
| `src/order.types.ts` | Types Layer | Extracted shared DTOs & Interfaces |
| `src/order.utils.ts` | Utils (Pure) | Extracted `calculateDiscount()` & formatting helpers |
| `src/order.service.ts` | Domain Service | Encapsulated DB persistence & payment orchestration |
| `src/order.ts` | Barrel Entrypoint | Re-exports all public symbols; delegates to service |

---

### 3. Verification & Safety Verification
- [x] Zero circular dependencies detected.
- [x] Server/Client boundary intact (No secrets leaked to UI).
- [x] Public contracts & barrel re-exports verified with caller test suite.
- **Type Check:** `tsc --noEmit` -> **CLEAN (0 errors)**
- **Test Suite:** `npm test` -> **PASS (All green)**
```
