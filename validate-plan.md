---
description: 🛡️ Audit and validate technical implementation plans against actual codebase reality, verifying file paths, symbols, contracts, and execution feasibility.
---

# /validate-plan - Codebase Reality & Plan Verification Mode

$ARGUMENTS

## Purpose & Core Rule

Activates VALIDATE-PLAN mode to audit an implementation plan against the actual codebase, verifying every referenced file path, symbol, schema, endpoint, and architectural assumption.

> **VALIDATE-PLAN = EVIDENCE FIRST, ZERO HALLUCINATED PATHS, CONTRACT REALITY CHECK**

- **Fact-Check Code References:** Verify that all cited file paths, functions, models, and classes exist in the current repository.
- **Contract & Call-Site Audit:** Verify that proposed API/function signature changes account for all real callers in the codebase.
- **Feasibility & Dependency Verification:** Prove that the execution sequence has no circular dependencies, missing steps, or broken invariants.
- **Strictly Read-Only:** Audits the plan against code without modifying application source files.

---

## When to Use

- **Use when:** Before running `/cook` or implementing any multi-phase plan (e.g. `plans/<slug>/plan.md`), verifying AI-generated plans for hallucinations, or auditing breaking changes.
- **Do not use:** General code review of git diffs (use `/review` or `ak:code-review`).

---

## Supported Intents

```text
# Multi-phase plan folder
/validate-plan plans/260819-0934-role-capability-authorization

# Single standalone markdown plan file
/validate-plan implementation_plan.md
/validate-plan plans/feature-checkout.md
/validate-plan D:\projects\app\docs\migration-plan.md
```

---

## 4 Verification Roles & Inspection Checklist

| Role | Verification Target | Inspection Method |
| :--- | :--- | :--- |
| **1. Fact Checker** | File paths, symbols, routes, configs | `Glob` paths; `Grep` exact class/method/variable names. Flag missing files or typos. |
| **2. Flow & Traceability** | Call chains, async flows, middlewares | Trace claimed sequence (`entry -> middleware -> service -> DB`). Flag skipped middleware or impossible call chains. |
| **3. Contract Verifier** | Changed signatures, DTOs, exports | Grep all invocation call sites. Verify all callers, tests, and barrel re-exports (`index.ts`) are accounted for. |
| **4. State & Scope Auditor** | Invariants, DB transactions, auth | Check transaction boundaries (`@Transactional`), session scopes, multi-tenant leaks, and RBAC rules. |

---

## Execution Protocol

### Phase 1: Parse Plan & Enumerate Claims
- **Target Resolution:** Supports both **Single-file plan** (`*.md`) and **Multi-phase plan directory** (`plan.md` + `phase-*.md`).
- Read the target file(s) completely and extract concrete claims:
  - Mentioned file paths (`Create`, `Modify`, `Delete`, imported files).
  - Mentioned symbols (classes, functions, decorators, DB models, props).
  - Claimed behaviors ("API X calls Service Y", "Field Z stores status", "Middleware M protects Route R").

### Phase 2: Codebase Ground-Truth Verification
- **Paths Check:** Validate existence via `Glob` / file read.
- **Symbols Check:** Search exact symbols via `Grep` across the repository.
- **Call-Sites Check:** Count actual callers vs claimed callers in the plan.
- **Schema & Migration Check:** Verify referenced DB tables, fields, and ORM models match actual database schemas.

### Phase 3: Classify Findings & Inconsistencies
Categorize each verified claim into:
- `[VERIFIED]`: Directly matched code reality with file:line evidence.
- `[FAILED - NOT FOUND]`: File path or symbol does not exist (likely hallucinated or moved).
- `[FAILED - CONTRACT DRIFT]`: Method signature, caller count, or DB schema mismatch.
- `[UNVERIFIED - NEEDS SPEC]`: Claim depends on external business rules not verifiable from code.

### Phase 4: Whole-Plan Consistency Sweep
- Detect contradictions between `plan.md` overview and individual `phase-*.md` step details.

---

## Output Report Format

```markdown
## 🛡️ Plan Validation Report: [Plan Name]

### 1. Verification Summary
- **Plan Path:** `[absolute/path/to/plan.md]`
- **Phases Audited:** [N phases]
- **Claims Audited:** [Total: N] | ✅ Verified: N | ❌ Failed: N | ⚠️ Unverified: N
- **Feasibility Verdict:** READY TO IMPLEMENT | REVISION REQUIRED | BLOCKED

### 2. Codebase Reality Discrepancies (Failures & Risks)
| Phase / Location | Claimed in Plan | Actual Codebase Reality | Recommended Correction |
| :--- | :--- | :--- | :--- |
| `phase-02.md` | `src/utils/auth.ts` | Path does not exist. Actual: `src/lib/auth.ts` | Update path in phase 2 |
| `phase-03.md` | `has_capability(user)` | Method signature takes `(user, action, scope)` | Update step 3 parameters |

### 3. Missing Steps & Contract Gaps
- [List any unmentioned callers, missing migrations, or unhandled test files]

### 4. Next Recommended Action
- Revise failed claims in phase files, or
- Ready for implementation via `/cook [plan-path]`
```

---

## Definition of Done

1. 100% of cited file paths and symbols verified against the actual repository.
2. Call sites and contract changes enumerated with concrete code evidence.
3. Discrepancies reported with exact corrective actions before code implementation begins.
