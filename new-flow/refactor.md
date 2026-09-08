---
description: ♻️ Behavior-Preserving Refactoring, Clean Code & File Splitting Engine. Refactor code structure, audit code health against 12 clean-code dimensions, or safely split oversized files.
argument-hint: "[target-path] [--audit] [--split] [--changed] [--strict] [--tdd]"
---

# /refactor - Behavior-Preserving Refactor & Clean Code Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **REFACTOR & CLEAN CODE MODE** to safely improve software design, readability, cohesion, and maintainability without altering external behavior.

> **IRON INVARIANTS:**
> 1. **STRICTLY BEHAVIOR-PRESERVING:** Improve code shape, preserve behavior contracts. Never add features, modify public API contracts, or change error semantics during a refactor.
> 2. **NO TYPE REGRESSION:** Never replace strong types with `any`/`unknown`. Preserve ORM entities, DB transaction boundaries, and auth filters.
> 3. **STAGED ATOMIC EDITS:** Extract dependencies first, update call-sites second, re-export from original entry points to prevent broken imports.
> 4. **TEST-VERIFIED STEPS:** Characterization tests must pass before and after every structural edit.

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
- `--audit`: Read-only inspection; outputs prioritized clean-code findings table without modifying files.
- `--split`: Engages Staged File-Splitting protocol to decompose monolithic files into clean modular architectures.
- `--changed`: Restricts refactoring scope only to git-modified files and direct callers.
- `--strict`: Audits lower-priority maintainability debt and style inconsistencies.
- `--tdd`: Enforces running characterization tests before and after each atomic refactoring step.

---

## 🔬 Mode A: Standard Clean Code Refactoring (Default)

Follows the **12 Clean-Code Audit Dimensions**:

| Dimension | Target Defect | Safe Refactoring Pattern |
| :--- | :--- | :--- |
| **1. Single Responsibility (SRP)** | Functions doing >1 thing; mixing UI + DB | Extract domain helpers or custom hooks. |
| **2. Naming & Intent** | Misleading or generic names (`data`, `handle`) | Rename to domain-specific verbs and nouns. |
| **3. Functions & Parameters** | Parameter clusters (>3 parameters) | Introduce parameter Options/DTO object. |
| **4. Nesting & Control Flow** | Deep if/else nesting (>3 levels) | Use guard clauses and early returns. |
| **5. Harmful Duplication (DRY)**| Identical business or validation logic | Extract shared utility (preserve accidental likeness). |
| **6. Dead Code & Comments** | Zombie code, commented-out blocks | Delete outright (git history preserves history). |
| **7. Type Safety** | `any`, loose casts, missing return types | Add explicit interfaces and discriminators. |
| **8. Magic Values** | Inline numeric constants or raw strings | Extract named typed constants or enums. |
| **9. Side-Effect Clarity** | Hidden global state mutations in getters | Separate pure queries from mutating commands (CQS). |
| **10. Async / Promises** | Callback hell, mixing async/await and `.then` | Standardize on clean `async/await` with `try/catch`. |
| **11. Error Handling** | Swallowed errors, silent `catch {}` | Explicit re-throws or structured domain errors. |
| **12. Architecture Boundary** | Lower layer importing higher layer | Introduce dependency injection or invert interface. |

---

## 📁 Mode B: Staged File-Splitting Protocol (`--split`)

Safely decomposes a monolithic file (>400-1000 LOC) without breaking existing consumers:

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Dependency Analysis │ ──► │ 2. Target File Tree    │ ──► │ 3. Staged Extraction   │
│    (Types, Hooks, Pure)│     │    (Domain grouping)   │     │    (New modular files) │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 6. Typecheck & Tests   │ ◄── │ 5. Barrel Re-Export    │ ◄── ┌────────────────────────┐
│    (Zero broken calls) │     │    (Preserve old path) │     │ 4. Update Original     │
└────────────────────────┘     └────────────────────────┘     │    (Wiring only)       │
                                                              └────────────────────────┘
```

### Staged Extraction Sequence:
1. **Types & Interfaces First:** Extract shared types into `[target].types.ts`. Zero runtime code.
2. **Pure Helper Utilities:** Extract stateless functions, calculations, and formatters into `[target].utils.ts`.
3. **Domain Services / Custom Hooks:** Extract stateful business logic, state machines, or DB access into dedicated files.
4. **Subcomponents (if UI):** Extract internal UI sections into clean, focused subcomponents.
5. **Barrel Re-Export Guarantee:**
   - Keep the original file path active.
   - Re-export all extracted public functions and types from the original file so existing callers continue to work with zero breaking changes.
6. **Compile Verification:** Run `tsc --noEmit` and relevant tests to verify zero regression.

---

## 📋 Standard Refactor Output Template

```markdown
## ♻️ Refactoring Report: [Target Module / File]

### 1. Scope & Execution Mode
- **Target:** `[path/to/file]`
- **Mode:** [Standard Refactor / Audit / File Split]
- **Verification Baseline:** [Initial test command: PASS]

---

### 2. Summary of Applied Improvements

| File / Component | Change Type | Improvements Applied |
| :--- | :--- | :--- |
| `src/modules/order.ts` | Extracted Helper | Extracted `calculateDiscount()` to reduce function length |
| `src/modules/order.ts` | Guard Clauses | Converted 4-level nested `if` into early returns |
| `src/modules/order.types.ts` | Extracted Types | Centralized DTO interfaces with strict typing |

---

### 3. Public Contract & Behavioral Invariant Guarantee
- [x] All public exports, method signatures, and return shapes preserved.
- [x] Zero changes to database queries, schemas, or transaction semantics.
- [x] Zero changes to authentication, route guards, or error codes.

---

### 4. Verification Gate
- **Type Check:** `npx tsc --noEmit` -> **CLEAN (0 errors)**
- **Automated Tests:** `npx vitest run tests/order/` -> **PASS (All green)**
```
