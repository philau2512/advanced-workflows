---
description: ♻️ Behavior-Preserving Refactoring & Safe File Splitting Engine. Safely refactor code structure, reduce complexity, consolidate domain duplication, or split oversized files with zero behavioral drift.
argument-hint: "[target-path] [--split] [--changed] [--strict] [--tdd]"
---

# /refactor - Behavior-Preserving Refactoring & Safe File-Splitting Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **REFACTOR & EXECUTION MODE** to safely improve code readability, cohesion, architecture boundaries, and maintainability without altering external behavior.

> **IRON INVARIANTS:**
> 1. **STRICTLY BEHAVIOR-PRESERVING:** Improve code shape, preserve behavior contracts. Never add features, modify public APIs, change error payloads, or alter DB persistence semantics.
> 2. **ZERO-LOSS SNAPSHOT & INSTANT ROLLBACK:** Before mutating any file (Mode A or Mode B), create an explicit snapshot (`cp <target> <target>.bak`). If compilation, type checks, or tests fail and cannot be cleanly resolved, instantly restore from `.bak` (`mv <target>.bak <target>`). Never leave orphaned `.bak` files upon task completion.
> 3. **NO TYPE REGRESSION & BOUNDARY INTEGRITY:** Never replace strong types with `any`/`unknown`. Preserve ORM schemas, `@Transactional` boundaries, auth guards, and secret containment.
> 4. **COPY-FIRST & GRADUAL REPLACEMENT:** When splitting files, NEVER delete original code prematurely. Copy to new files -> import into original -> replace unit-by-unit -> verify -> cleanup.
> 5. **BARREL RE-EXPORT GUARANTEE:** The original file path must remain active and re-export all extracted symbols to guarantee zero breaking changes for external callers.
> 6. **TEST-VERIFIED ATOMIC STEPS:** Capture a passing baseline before non-trivial edits; verify syntax, types, and tests after every single extraction or transformation.
> 7. **AUDIT FIRST ON UNFAMILIAR CODE:** For codebase-wide inspection, debt catalogs, or scorecards before refactoring, run `/audit` first.

---

## ⚙️ Argument Parsing & Usage Modes

```text
# Standard Refactor (Extract functions, reduce nesting, consolidate logic, improve naming):
/refactor <path/to/file or module> [--tdd] [--changed]

# Safe File-Splitting (Splitting oversized >500 LOC monolithic files):
/refactor --split <path/to/large-file> [--out-dir <dir>]
```

### Flags:
- `--split`: Engages the Staged File-Splitting protocol to decompose monolithic files into layered architectures.
- `--changed`: Restricts refactoring scope strictly to git-modified files and direct callers.
- `--strict`: Enforces strict clean-code cleanup, including minor naming debt and micro-optimizations.
- `--tdd`: Enforces characterization tests before modifying logic and runs targeted tests after each step.

---

## 🛡️ The 4-Tier Defensive Refactoring Pipeline

Every refactoring operation must traverse these 4 defensive phases:

```
┌────────────────────────┐     ┌────────────────────────┐
│ Phase 0: Pre-Flight    │ ──► │ Phase 1: Execution     │
│ - cp <file> <file>.bak │     │ - Mode A (Targeted) OR │
│ - Capture Baseline Run │     │ - Mode B (--split)     │
└────────────────────────┘     └───────────┬────────────┘
                                           │
┌────────────────────────┐     ┌───────────▼────────────┐
│ Phase 3: Rollback /    │ ◄── │ Phase 2: Logic Parity  │
│          Sanitization  │     │ - 5 Semantic Traps     │
│ - Pass: rm *.bak       │     │ - Direct diff vs .bak  │
│ - Fail: mv *.bak <file>│     │ - 10-Point Gate Check  │
└────────────────────────┘     └────────────────────────┘
```

### Phase 0: Pre-Flight Snapshot & Baseline Capture
1. **Atomic Backup:** Create an uncommitted backup file for every file targeted for modification:
   ```bash
   cp path/to/file.ts path/to/file.ts.bak
   ```
2. **Baseline Verification:**
   - Run targeted typecheck (`tsc --noEmit` or linter) on the untouched file.
   - Run targeted test suite if available. If no tests exist, record characterization notes or expected input/output edge cases before editing.

---

## 🔬 Mode A: Targeted Logic Refactoring

### Core Transformation Playbook:

1. **Reduce Nesting & Cognitive Complexity:**
   - Invert `if/else` checks into guard clauses with early `return`, `continue`, or `throw`.
   - Flatten nested ternary expressions and nested callbacks into linear pipelines or `async/await`.

2. **Parameter & DTO Encapsulation:**
   - Replace parameter clusters (>3 parameters) with strongly-typed parameter objects or DTOs.
   - Separate command inputs from context dependencies via dependency injection.

3. **Duplication Consolidation (DRY vs AHA):**
   - *Rule of Three:* Consolidate repeated logic only when identical code appears **>= 3 times** and shares the **same domain owner**.
   - *AHA Invariant:* Keep coincidental similarities intact if they belong to different business subdomains to avoid premature coupling.

4. **Immutability & Pure Separation:**
   - Eliminate direct mutations of input parameters or global caches.
   - Separate pure domain calculations from impure I/O operations.

### Anti-Patterns to Avoid:
- Mechanical over-abstraction (creating 1-to-1 interfaces or repositories for everything).
- Fragmenting readable code into dozens of disconnected 2-line micro-helpers.
- Deduplicating logic that looks identical but belongs to distinct business domains (premature abstraction).
- Module top-level runtime side-effects (e.g., reading `process.env` or initializing network clients at module import time).
- Weakening existing test assertions to force refactored code to pass.

---

## 📁 Mode B: Staged File-Splitting Protocol (`--split`)

### Architecture Layer Matrix:

#### Frontend Architecture (React / Next.js / Vue):
- `features/` -> Domain-specific modules & orchestration (**Primary**)
- `components/` -> UI presentation only (**STRICT: No direct API calls, no DB access**)
- `hooks/` -> Reusable stateful & lifecycle logic (**STRICT: No JSX return**)
- `services/` -> API clients & remote data fetching
- `store/` -> Global / client state management
- `utils/` -> Pure helper functions & stateless formatters

#### Backend Architecture (Node / Express / NestJS / FastAPI / Spring):
- `controller/router` -> Request parsing, validation, HTTP response (**STRICT: No business logic**)
- `service/` -> Core business logic & orchestration (**Sits between controller and repository**)
- `repository/dao` -> Database queries & persistence (**STRICT: No business rules**)
- `model/entity` -> Database schemas & domain entities
- `dto/schema` -> Request/response contracts & transport types
- `utils/` -> Pure calculation helpers & formatters

---

### Staged Splitting Workflow:

1. **Pre-Scan & Chunking:**
   - For files >800 LOC: Group functions by domain feature / lifecycle before extracting.
2. **Target File Planning:**
   - Define target files following the Architecture Layer Matrix above.
   - Enforce unidirectional top-down imports; strictly forbid circular dependencies.
3. **Copy-First Extraction:**
   - Copy types, helpers, and services into new files. **DO NOT delete original code yet.**
   - Extract in sequence: Types (`.types.ts`) -> Pure Utils (`.utils.ts`) -> Services/Hooks -> Subcomponents.
4. **Wiring & Gradual Replacement:**
   - Import new modules back into the original file.
   - Replace old internal references ONE logical unit at a time.
5. **Barrel Re-Export Guarantee:**
   - Keep the original file path active. Re-export all extracted public symbols from it so existing consumers never break.

---

## 🔍 Semantic Parity & Logic Cross-Check (The 5 Semantic Traps)

Before approving any refactored code, systematically cross-examine the new file against `<target>.bak` across these 5 high-risk areas:

| # | Semantic Trap | Risk Scenario | Safe Refactoring Rule |
| :--- | :--- | :--- | :--- |
| **1** | **Falsy vs Nullish** | Converting `if (!x)` to `if (x == null)` or `??` to `\|\|` accidentally swallows valid `0`, `""`, or `false`. | Distinguish explicitly between `undefined`/`null` checks and general falsiness checks. |
| **2** | **Short-Circuiting & Side-Effects** | Extracting conditional sub-expressions into local variables prematurely executes expressions that previously short-circuited. | Keep expressions containing I/O, DB calls, or function invocations inside their conditional branches. |
| **3** | **Default Params & Destructuring** | Consolidating parameter lists into an options object drops parameter defaults or breaks callers passing `undefined`. | Ensure parameter object types specify defaults and preserve optionality contracts identically. |
| **4** | **Closures, Scope & `this` Context** | Extracting methods into standalone functions or changing function declarations to arrows strips `this` binding or breaks closed-over variables. | Preserve closure state scope; bind or pass context explicitly when decoupling instance methods. |
| **5** | **Error Handling & Exception Propagation** | Simplifying `try/catch` blocks converts typed exceptions into generic errors or alters error payload shapes. | Ensure re-thrown errors preserve the original stack trace, error code, and HTTP status mappings. |

---

## 🛡️ The 10-Point Multi-Level Self-Verification Checklist

Before sanitizing backups and completing the task, verify all 10 gates:
- [ ] **1. Syntax & Circular Dependencies:** Zero undefined symbols; no circular imports (`A -> B -> A`). Shared types moved to dedicated `types.ts`.
- [ ] **2. Execution Order & Flow:** Function call sequence, middleware pipeline, and argument mappings remain 100% identical.
- [ ] **3. State & Closure Integrity:** No lost state mutations, closures retain access to required variables, logging/caching calls preserved.
- [ ] **4. Async & Concurrency:** `await` chains intact; no race conditions or unhandled rejections introduced.
- [ ] **5. Server/Client Boundary Safety:** In fullstack frameworks (Next.js/Remix), NEVER import server-only logic, DB clients, or environment secrets into `"use client"` components.
- [ ] **6. CSS Cascade & DOM Hierarchy:** Subcomponents preserve CSS specificity, flex/grid parent-child relationships, and Tailwind class priorities.
- [ ] **7. Barrel Re-Export Integrity:** All external consumers import seamlessly from the original path with zero regressions.
- [ ] **8. Compiler & Test Gate:** `tsc --noEmit` (or language linter) is clean (0 errors) and characterization tests pass 100%.
- [ ] **9. Transaction & Context Propagation:** DB transactions (`tx`), Tenant ID, and request contexts propagate through all extracted services/helpers without escaping ACID boundaries.
- [ ] **10. Test Mocking Surface & TDZ:** Existing test mocks (`jest.spyOn`, `vi.mock`) remain functional with relocated symbols; no Temporal Dead Zone (TDZ) runtime import errors from barrel circularity.

---

## 🧹 Phase 3: Rollback & Sanitization Protocol

Once verification concludes:

*   **IF ALL CHECKS PASS (Clean Compile + Green Tests + Semantic Parity Verified):**
    *   Sanitize the workspace by deleting all `.bak` files:
        ```bash
        rm path/to/file.ts.bak
        ```
    *   Confirm `git status` shows only desired, intentional code edits with zero temporary file pollution.

*   **IF CHECKS FAIL OR UNRESOLVABLE ERRORS OCCUR:**
    *   Execute instant rollback from `.bak` to restore original state:
        ```bash
        mv path/to/file.ts.bak path/to/file.ts
        ```
    *   Halt execution, report the specific failure reason, and avoid partial or corrupted working tree states.

---

## 📋 Standard Output Template

```markdown
## ♻️ Refactoring Report: [Target Module / File]

### 1. Scope & Execution Mode
- **Target:** `[path/to/file]`
- **Mode:** [Targeted Refactor / File Split]
- **Backup Snapshot:** Created & Verified (`.bak`)
- **Verification Baseline:** [Pre-refactor test/typecheck: PASS]

---

### 2. Summary of Structural Changes

| File / Component | Layer / Change Type | Improvements Applied |
| :--- | :--- | :--- |
| `src/order.types.ts` | Types Layer | Extracted shared DTOs & Interfaces |
| `src/order.utils.ts` | Utils (Pure) | Extracted `calculateDiscount()` & formatting helpers |
| `src/order.service.ts` | Domain Service | Encapsulated DB persistence & payment orchestration |
| `src/order.ts` | Barrel Entrypoint | Re-exports all public symbols; delegates to service |

---

### 3. Logic Parity & Verification Gate Status
- [x] Pre-flight `.bak` created prior to mutations.
- [x] Semantic Parity verified against `.bak` (Falsy/Nullish, Short-circuits, Closures, Defaults, Exceptions).
- [x] Zero circular dependencies detected.
- [x] Server/Client boundary intact (No secrets leaked to UI).
- [x] Public contracts & barrel re-exports verified with caller test suite.
- [x] Database transaction (`tx`) and request context propagated without leakage.
- [x] Existing test mock surfaces and module loading order verified.
- **Type Check:** `tsc --noEmit` -> **CLEAN (0 errors)**
- **Test Suite:** `npm test` -> **PASS (All green)**
- **Sanitization:** All `.bak` files cleaned. Workspace spotless.
```
