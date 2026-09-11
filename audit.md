---
description: 🩺 Comprehensive Code Health, Clean Code & Duplication Audit Engine. Inspect code quality against 16 clean-code dimensions, scan cross-file AST/token clones, evaluate architecture drift, and score technical debt with zero code mutations.
argument-hint: "[target-path] [--strict] [--dups-only] [--changed] [--out]"
---

# /audit - Code Health, Clean Code & Duplication Audit Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **AUDIT & CODE HEALTH ASSESSMENT MODE** to perform rigorous, multi-dimensional inspections of source files, modules, pull requests, or entire repositories without modifying a single line of code.

> **IRON INVARIANTS:**
> 1. **STRICTLY READ-ONLY:** Never mutate code, format files, or introduce fixes during an audit. Audit produces diagnostic intelligence; remediation is deferred to `/refactor` or `/plan`.
> 2. **EVIDENCE-GROUNDED FINDINGS:** Every defect must cite exact file paths, line numbers, and concrete code evidence. No vague claims ("this code looks complex").
> 3. **SEVERITY INTEGRITY (P1/P2/P3):** Distinguish real business/security/integrity risks from minor aesthetic style choices. Never inflate P3 style preferences into P1 emergencies.
> 4. **AHA-AWARE DUPLICATION (DRY vs Premature Abstraction):** Distinguish harmful duplication from coincidental similarity across distinct business bounded contexts.
> 5. **ACTIONABLE REMEDIATION ROADMAP:** Every finding must pair with a concrete, behavior-preserving remediation pattern ready for handoff to `/refactor`.

---

## ⚙️ Argument Parsing & Usage Modes

```text
# Full Codebase Audit (Zero-config: auto-detects src/, app/, or workspace root):
/audit [--strict] [--out]

# Targeted Audit (Specific file or directory):
/audit <path/to/file-or-directory> [--strict] [--out]

# Duplication-Focused Scan (Cross-file clones, test fixture bloat):
/audit [path] --dups-only [--min-tokens 40]

# Pre-PR / Incremental Audit (Audit git-modified files only):
/audit --changed [--strict]
```

### Scope & Target Resolution:
- **Default (Zero Arguments):** When no path is passed, `/audit` automatically targets the primary source directory (resolves in order: `src/` → `app/` → `lib/` → workspace root).
- **Automatic Exclusions:** Strictly ignores `node_modules/`, `dist/`, `build/`, `.git/`, `.next/`, `vendor/`, and coverage directories.

### Flags:
- `--strict`: Includes lower-priority maintainability debt, minor naming, and style inconsistencies (P3).
- `--dups-only`: Focuses exclusively on cross-file clone analysis, token similarity, and utility duplication.
- `--changed`: Restricts audit scope strictly to git-modified or uncommitted files and direct callers.
- `--min-tokens <n>`: Minimum token threshold for clone detection (default: 40 tokens / ~10 lines).
- `--out`: Saves comprehensive audit report to `reports/AUDIT-<YYYYMMDD>-<slug>.md`.

---

## 🔬 The 5 Audit Pillars

```
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│ 1. Clean Code & Smell   │     │ 2. Duplication & Clones │     │ 3. Architecture Drift   │
│    (16 Dimensions)      │     │    (Type 1-4, AHA Matrix)│    │    (Layer Boundaries)   │
└────────────┬────────────┘     └────────────┬────────────┘     └────────────┬────────────┘
             │                               │                               │
             └───────────────────────┬───────┴───────────────────────────────┘
                                     │
                    ┌────────────────┴────────────────┐
                    │ 4. Security & Boundary Leaks    │
                    │ 5. Test Quality & Fragility     │
                    └────────────────┬────────────────┘
                                     │
                                     ▼
                        ┌───────────────────────────┐
                        │ Code Health Scorecard &   │
                        │ Prioritized Remediation   │
                        └───────────────────────────┘
```

### Pillar 1: The 16 Clean-Code Dimensions

| Dimension | Target Defect | Severity Baseline | Recommended Remediation |
| :--- | :--- | :---: | :--- |
| **1. Single Responsibility (SRP)** | Functions doing >1 thing; mixing UI + DB | P2 | Extract domain helpers, custom hooks, or dedicated services. |
| **2. Naming & Intent** | Generic names (`data`, `temp`, `handle`) | P3 | Rename to domain-specific verbs/nouns reflecting true intent. |
| **3. Functions & Parameters** | Parameter clusters (>3 parameters) | P2 | Group into a typed Options / DTO object. |
| **4. Nesting & Control Flow** | Deep indentation (>3 levels), nested `if/else` | P2 | Apply guard clauses and early returns. |
| **5. Harmful Duplication (DRY)**| Repeated business rules, validation logic | P2 | Consolidate under single owner (respect Rule of Three). |
| **6. Abstraction (YAGNI/KISS)** | 1-use speculative wrappers / over-engineering | P3 | Inline unnecessary abstractions; simplify indirection. |
| **7. Error Handling** | Swallowed errors (`catch {}`), lost context | P1 | Explicit re-throw, retain stack trace, or structured domain error. |
| **8. State & Side Effects** | Implicit mutations, hidden cache updates | P1 | Make mutations explicit; separate pure calculations from I/O. |
| **9. Dead Code & Comments** | Zombie code, commented-out blocks, obvious comments | P3 | Delete outright (git preserves history); keep "why" comments only. |
| **10. Colocation & Placement** | Variables declared far from usage, scattered constants | P3 | Colocate variables near usage; extract magic constants to module top. |
| **11. Data Transformations** | Mutating input arguments directly | P1 | Enforce immutability; centralize DTO ↔ Domain ↔ DB mappings. |
| **12. Command-Query Separation**| Functions querying data while mutating state | P2 | Separate pure queries (no side effects) from state-mutating commands. |
| **13. Type Safety & Nullability**| `any`, loose casts, missing return types | P1 | Use explicit interfaces, discriminators, optional chaining (`?.`), and `??`. |
| **14. Async / Promises** | Callback hell, mixing `.then()` with `async/await` | P2 | Standardize on clean `async/await` with localized `try/catch`. |
| **15. Testability** | Hardcoded clocks, network, or global singletons | P2 | Inject dependencies or parameterize external inputs. |
| **16. Architecture Boundaries** | Lower layer importing higher layer, leaky transport | P1 | Enforce top-down dependency flow; decouple transport from domain. |

---

### Pillar 2: Cross-File Duplication & Clone Discovery Protocol

#### 1. Clone Taxonomy:
- **Type 1 (Exact Clones):** Copy-pasted verbatim blocks (whitespace/comment differences only).
  - *Tooling:* Run `npx jscpd <path> --min-tokens 40` (if Node.js environment available) or grep matching function signatures.
- **Type 2 (Syntactic / Renamed Clones):** Identical logic flow with renamed variables, types, or function identifiers.
  - *Tooling:* Token/AST clone scan; analyze structurally identical loops and conditions.
- **Type 3 (Semantic / Re-invented Utilities):** Sibling modules implementing duplicate utility logic (`formatDate`, `slugify`, `safeJsonParse`) instead of centralizing in `utils/` or project dependencies.
- **Type 4 (Test Fixture & Mock Bloat):** Repetitive mock data setup, authorization tokens, or DB seed records repeated across multiple test suites.

#### 2. Duplication Decision Matrix (DRY vs AHA):
> **AHA Principle (Avoid Hasty Abstractions):** Duplication is far cheaper than the wrong abstraction.
- **Flag for Consolidation WHEN:**
  - Logic shares the exact same business owner/actor AND changes at the same rate.
  - Pattern appears **>= 3 times** (*Rule of Three*).
- **Flag to PRESERVE (Coincidental Similarity) WHEN:**
  - Code belongs to distinct business domains (e.g., Shipping vs Billing calculating "hours" or "rate").
  - Consolidation would force premature coupling or create leaky shared dependencies across bounded contexts.

---

### Pillar 3: Architecture & Module Drift
- **Layer Violations:** Controller directly querying DB/ORM without Service layer; React presentation component calling `fetch()` or SDK directly.
- **Circular Imports:** Modules importing each other (`A -> B -> A`), causing Temporal Dead Zone (TDZ) or bundling failures.
- **Server/Client Boundary Violations:** Secrets (`process.env.SECRET`), DB clients, or private SDKs imported into client-bundle files (`"use client"`).

---

### Pillar 4: Security, Dependencies & Runtime Fragility
- **Supply Chain & Vulnerable Dependencies:** Audit dependencies for known CVEs (`npm audit`, lockfile check), unmaintained/abandoned packages (>2 years without commits), and license conflicts (GPL in proprietary codebases).
- **Unsanitized Inputs & Injections:** Raw SQL concatenation, unescaped HTML injection, unchecked path traversal parameters.
- **Module Top-Level Side Effects:** Executing network requests, database initialization, or reading dynamic `process.env` at module load time (breaks container lifecycles and unit tests).
- **Transaction Boundary Leaks:** DB operations inside transaction blocks not receiving the active transaction client (`tx`).

---

### Pillar 5: Test Quality & Fragility
- **Coverage Gaps:** Core business services or payment/auth branching without characterization unit tests.
- **Flaky Test Signals:** Tests dependent on wall-clock time (`Date.now()`), unmocked timers (`setTimeout`), non-deterministic randomness (`Math.random()`), or leaky global state between runs.
- **Mock Fragility:** Over-mocking internal methods (`jest.spyOn(service, 'privateMethod')`) which prevents safe refactoring.
- **Weak Assertions:** Tests only asserting `expect(result).toBeDefined()` or `expect(res.status).toBe(200)` without validating returned payload invariants.

---

## 📋 Standard Output Template

```markdown
# 🩺 Code Health & Duplication Audit Report: [Target Scope]

## 📊 1. Code Health Scorecard

| Assessment Pillar | Rating (A-F) | Critical (P1) | Major (P2) | Minor (P3) | Status |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Clean Code & Smells** | A / B / C / D / F | 0 | 2 | 3 | Needs Attention |
| **Code Duplication & Clones** | A / B / C / D / F | 0 | 1 | 2 | High Redundancy |
| **Architecture Boundaries** | A / B / C / D / F | 1 | 0 | 0 | Boundary Breach |
| **Security & Runtime Safety**| A / B / C / D / F | 0 | 0 | 1 | Healthy |
| **Test Quality & Coverage** | A / B / C / D / F | 0 | 1 | 1 | Moderate Gaps |
| **OVERALL HEALTH GRADE** | **[A / B / C / D / F]** | **1** | **4** | **7** | **Refactor Recommended** |

---

## 🚨 2. Prioritized Findings Matrix

| ID | Sev | Pillar | Location | Defect & Code Evidence | Recommended Refactor Pattern | Risk |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `AUD-01` | P1 | Architecture | `src/controllers/order.ts:45` | Controller queries ORM directly bypassing OrderService | Move queries to `order.repository.ts` | Med |
| `AUD-02` | P2 | Duplication | `src/cart.ts:60` & `src/checkout.ts:40` | Type 1 Clone: 38 lines of discount calculation duplicated | Consolidate into `pricing.service.ts` | Low |
| `AUD-03` | P2 | Clean Code | `src/services/payment.ts:112` | 4-level nested `if/else` checks | Apply Guard Clauses & Early Returns | Low |
| `AUD-04` | P3 | Clean Code | `src/utils/helpers.ts:15` | Generic identifier `data` used across 50 lines | Rename to `parsedUserPayload` | Low |

---

## 🔍 3. Cross-File Duplication & Clone Catalog

| Clone ID | Clone Type | Primary File | Duplicate Location(s) | Tokens / Lines | Decision (Consolidate / Keep) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `CL-01` | Type 1 | `src/cart/calc.ts:25` | `src/order/calc.ts:30` | 55 tokens / 16 lines | Consolidate (Rule of Three satisfied) |
| `CL-02` | Type 3 | `src/user/avatar.ts:10` | `src/org/logo.ts:14` | 40 tokens / 12 lines | Keep (Coincidental similarity across domains) |

---

## 🗺️ 4. Recommended Next Steps & Handoff

1. **Immediate P1 Remediations:** Fix `AUD-01` to restore architecture boundary integrity.
2. **Refactor Batch (P2):**
   - Run `/refactor src/services/payment.ts` to reduce nesting.
   - Run `/refactor src/cart.ts` to extract shared pricing logic.
3. **Large-Scale Structural Debt:** If total file LOC > 500 lines, proceed with `/refactor --split <file>`.
```
