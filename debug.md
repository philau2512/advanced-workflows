---
description: 🐞 Precision Root-Cause Debugging Engine. Clarify ambiguous bugs with intake interviews, trace backward from errors, eliminate hypotheses, and apply minimal regression-proof fixes.
argument-hint: "[issue or stacktrace] [--bisect] [--clean] [--out]"
---

# /debug - Precision Root-Cause Debugging Engine

$ARGUMENTS

---

## 🎯 Core Rules & Invariants

Activates **ROOT-CAUSE DEBUGGING MODE** to systematically isolate, diagnose, and permanently eradicate software bugs without symptom-masking.

> **IRON INVARIANTS:**
> 1. **ZERO GUESSWORK & NO BLIND PATCHING:** If the bug report lacks repro steps or payload, interview the user before touching code.
> 2. **NO SYMPTOM MASKING:** Never swallow errors with empty `catch {}`, defensive `?.`, or fallback `|| null`. Fix the root trigger at the origin of corrupt data.
> 3. **REPRODUCTION BEFORE EDIT:** Establish a failing test, minimal repro script, or reproducible command before writing any implementation fix.
> 4. **MINIMAL BLAST RADIUS:** Apply the smallest safe patch that permanently resolves the root defect without modifying public contracts or introducing regressions.
> 5. **CLEAN INSTRUMENTATION:** All temporary diagnostic logging injected during investigation must be completely removed before declaring completion.

---

## ⚙️ Argument Parsing & Execution Flags

- `--bisect`: Runs automated git bisect / commit binary search to identify the exact regression commit.
- `--clean`: Purges build and bundler caches (`node_modules/.cache`, `.next`, `.turbo`, `dist`) before diagnosing.
- `--out`: Saves full diagnostic report to `reports/DEBUG-<YYYYMMDD>-<issue-name>.md`.
- `$ARGUMENTS`: Error message, stack trace, failing endpoint, test failure, or log snippet.

---

## 🔬 6-Phase Precision Debugging Protocol

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Intake & Clarify    │ ──► │ 2. 5-Tier Reproduction │ ──► │ 3. Backward Tracing    │
│    (3 Questions Gate)  │     │    (Failing Test/Gate) │     │    (Crash -> Origin)   │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 6. Verify & Guard      │ ◄── │ 5. Minimal Root Fix    │ ◄── ┌────────────────────────┐
│    (Regression Check)  │     │    (Add Regression Test│     │ 4. Hypothesis Tree     │
└────────────────────────┘     └────────────────────────┘     │    & Elimination Matrix│
                                                              └────────────────────────┘
```

### Phase 1: Intake Clarification & Error Classification

#### 1. Ambiguity Detection & User Interview Gate
If the bug report is vague (*"payment failed"*, *"button doesn't work"*, *"500 error"*):
**STOP. Ask the 3 Diagnostic Questions:**
1. **Trigger Action:** *"What exact sequence of actions or inputs did you perform right before the error appeared?"*
2. **Context & Environment:** *"Under what specific conditions did this occur? (User role/auth, local vs prod, browser/OS, specific payload?)"*
3. **Exact Observed Symptom:** *"What exact error message, toast, HTTP status code in Network tab, or console error was logged?"*
*(Skip interview only if exact stack traces or clear repro steps are already provided).*

#### 2. Classify into the 8 Error Categories:

| Error Category | Typical Symptoms | Root Investigation Angle |
| :--- | :--- | :--- |
| **Type 1: Runtime / Crash / Syntax** | Uncaught exceptions, `TypeError: cannot read property of undefined`, panic | Trace call stack to null/undefined dereference point. |
| **Type 2: Logic / State Desync** | Incorrect calculations, stale closures, desynchronized UI/store | Trace reactive dependency arrays and mutation lifecycles. |
| **Type 3: Async / Concurrency / Race** | Out-of-order responses, unhandled rejections, race conditions | Trace `await` chains, mutex locks, and network waterfalls. |
| **Type 4: Auth / Session / CORS / SSR** | Token expiry, cookie flags (`SameSite`/`Secure`), hydration mismatch | Trace token refresh loops, CORS preflight headers, SSR/client delta. |
| **Type 5: DB / ORM / Data Drift** | Deadlocks, dirty reads, missing schema migrations, corrupt rows | Trace query execution plan, transaction locks, and schema version. |
| **Type 6: Build & Env Drift** | Works locally but fails in CI/Prod, OS path `\\` vs `/`, ESM vs CJS | Trace node version, path normalizers, dynamic imports, and env vars. |
| **Type 7: Circular Dependency** | Runtime `[Function] is undefined`, empty exports at startup | Trace module import graph for cyclic imports (`A -> B -> A`). |
| **Type 8: Heisenbug / Stale Cache** | Inconsistent failures, ghost HMR state, stale Redis/cache keys | Execute `--clean` cache purge; verify cache TTL and invalidation keys. |

---

### Phase 2: Mandatory 5-Tier Reproduction Gate

Do NOT modify implementation code until reproduction is proven:
- **Tier 1 (Automated Test - Preferred):** Write an isolated failing test (`RED`) in the test suite (e.g. `tests/regression/bug.test.ts`).
- **Tier 2 (Script / Curl):** Prepare a minimal standalone script in the system temp directory or an exact curl command reproducing the error.
- **Tier 3 (UI Flow):** Use browser automation tools (`agent-browser` or `chrome-profile`) to capture network traces and console logs.
- **Tier 4 (Ghost Bug Cache Purge):** If non-reproducible, run `--clean` to eliminate stale bundler artifacts and verify if the issue persists.
- **Tier 5 (Git Regression):** If the defect is recent, use `git log -p -n 10` or `--bisect` to identify the regression commit.

---

### Phase 3: Backward Tracing & Dynamic Instrumentation

Trace backward from crash site to data origin:
$$\text{Crash / Failure Site} \longleftarrow \text{Data Mutation} \longleftarrow \text{Storage / Service Layer} \longleftarrow \text{Entry Point}$$

- **Frontend Flow:** Use `/analyze --fe <component>` to trace `User Interaction -> State Hooks -> API Call`.
- **Backend Flow:** Use `/analyze --be <endpoint>` to trace `Route Handler -> Guard/Middleware -> Domain Service -> DB Query`.
- **Dynamic Instrumentation:** Inject temporary structured logs:
  `console.log('[DEBUG-TRACE]', { step: 'validation', payload: data, ts: Date.now() })`
  *(Must be completely removed before completion).*

---

### Phase 4: Hypothesis Tree & Elimination Matrix

Formulate **2 to 3 specific hypotheses** and validate against empirical code evidence:

| # | Hypothesis (Suspected Cause) | Supporting / Conflicting Evidence | Validation Method | Result |
| :--- | :--- | :--- | :--- | :--- |
| **H1** | Circular import between Module A & B | AST import graph shows cyclic dependency | Extract shared types into dedicated file | ❌ / ✅ |
| **H2** | Schema allows `undefined` optional field | Schema lacks `.min(1)` check on DTO | Run test with partial payload | ❌ / ✅ |
| **H3** | Race condition in async token refresh | Interceptor timestamp shows out-of-order request | Add mutex queue to refresh handler | ❌ / ✅ |

> **Root Cause Acceptance Criteria:** The verified hypothesis must explain:
> 1. Exact mechanism of failure.
> 2. Why it manifests under specific observed conditions.
> 3. Why the proposed fix permanently eradicates it without side effects.

---

### Phase 5: Minimal Targeted Fix & Regression Test

1. Apply fix directly at the origin of the invalid data, NOT at downstream display sites.
2. Maintain all existing public API contracts, return types, and interface signatures.
3. Remove all temporary instrumentation logs injected during Phase 3.
4. **Mandatory Regression Test:** Add a permanent automated test case covering this specific defect to prevent future regressions.

---

### Phase 6: Verification & Multi-Surface Guard

- Re-run reproduction test: Verify it turns from **RED $\rightarrow$ GREEN**.
- Run existing related test suites to verify zero regression.
- Execute workspace typecheck (`tsc --noEmit` or language equivalent) to verify clean compilation.

---

## 📋 Standard Debug Report Template

```markdown
## 🐞 Debug Diagnostic Report: [Bug Title]

### 1. Defect Summary
- **Symptom:** [User-visible symptom or failing response]
- **Category:** [Type 1 to Type 8 Error Classification]
- **Reproduction:** `[Failing test path or curl command used]`

---

### 2. Root Cause Analysis
- **Failure Point:** `[file.ts:line]`
- **Originating Trigger:** `[file.ts:line]` (where bad state or corrupt payload was introduced)
- **Mechanics:** [Technical explanation of why the defect occurred]

---

### 3. Hypothesis Elimination Matrix
- **H1:** [Hypothesis] -> *Eliminated by [Evidence]*
- **H2 (Root Cause):** [Hypothesis] -> *Confirmed by [Evidence]*

---

### 4. Applied Permanent Fix
- **Modified File:** `[path/to/file.ts:line]`
- **Changes Applied:** [Summary of structural root fix]
- **Regression Test Added:** `[tests/regression/issue-xxx.test.ts]`
- **Instrumentation Cleaned:** [x] All temporary logs removed

---

### 5. Verification Gate
- **Reproduction Test:** **PASS (Green)**
- **Related Test Suite:** **PASS (Zero regressions)**
- **Type Check:** `tsc --noEmit` -> **CLEAN (0 errors)**
```
