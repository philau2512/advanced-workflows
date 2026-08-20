---
description: 🐞 Precision root-cause debugging workflow combining reproduction gates, backward tracing, hypothesis elimination, and regression guards.
---

# /debug - Precision Root Cause & Hardcore Debugging Mode

$ARGUMENTS

---

## 🎯 Core Rules & Invariants

> 1. **ZERO GUESSWORK:** Every claim must be backed by empirical evidence (logs, stack traces, AST, tests, or database queries). Never guess.
> 2. **NO SYMPTOM PATCHING:** Do NOT swallow errors with empty `try/catch`, excessive optional chaining `?.`, or fallback `|| null` checks. Fix the root trigger at its source.
> 3. **REPRODUCTION BEFORE FIX:** Establish an automated failing test, a minimal repro script, or concrete steps before touching any code.
> 4. **MINIMAL BLAST RADIUS:** Apply the smallest safe fix that eliminates the defect without introducing unrelated refactoring or regressions.

---

## ⚙️ Argument Parsing & Execution Flags

- `--out`: Save diagnostic report to `reports/DEBUG-<YYYYMMDD>-<issue-name>.md`.
- `--bisect`: Run automated git bisect / commit binary search to find the regression commit.
- `--clean`: Clear all build caches (`node_modules/.cache`, `.next`, `.turbo`, `dist`, etc.) before testing.
- `$ARGUMENTS`: Error description, error code, failing endpoint, stack trace, or target file.

---

## 🔬 6-Phase Hardcore Debugging Protocol

```
┌──────────────┐     ┌──────────────┐     ┌────────────────────────┐
│  1. TRIAGE   │ ──► │2. REPRODUCE  │ ──► │ 3. BACKWARD TRACE      │
│  & Classify  │     │  & Test Gate │     │  (/trace-fe, /trace-be)│
└──────────────┘     └──────────────┘     └───────────┬────────────┘
                                                      │
┌──────────────┐     ┌──────────────┐                 ▼
│ 6. VERIFY &  │ ◄── │5. TARGET FIX │ ◄── ┌────────────────────────┐
│  REGRESSION  │     │  & Test Guard│     │ 4. HYPOTHESIS TREE     │
└──────────────┘     └──────────────┘     │   & Elimination Matrix │
                                          └────────────────────────┘
```

---

### Phase 1: Triage & Classification

Identify the exact error category:
- **Type 1 (Runtime / Crash / Syntax):** Uncaught exceptions, `TypeError`, `NullPointerException`, build/compilation crashes.
- **Type 2 (Logic / State Desynchronization):** Incorrect calculations, stale closures, desynchronized UI/store state.
- **Type 3 (Async / Concurrency / Race Condition):** Stale promises, out-of-order execution, distributed lock failure.
- **Type 4 (Auth / Session / CORS):** Token expiration, cookie flags (`SameSite`/`Secure`), hydration mismatch, RBAC leaks.
- **Type 5 (Database / ORM / Legacy Data Drift):** N+1 queries, deadlocks, dirty reads, missing migrations, corrupt legacy rows.
- **Type 6 (Build / Transpiler / Environment Drift):** Works locally but fails in CI/Prod, OS path separators (`\\` vs `/`), ESM vs CJS.
- **Type 7 (Circular Dependency):** Runtime `undefined is not a function` caused by cyclic module imports.
- **Type 8 (Heisenbug / Stale Cache):** Stale bundler artifacts, HMR ghost state, service worker, un-invalidated Redis keys.

---

### Phase 2: Reproduction Gate (Mandatory)

Do NOT modify implementation code until reproduction is proven:
1. **Tier 1 (Best):** Create an isolated automated failing test (`RED`) in the test suite.
2. **Tier 2:** Write a minimal reproduction script in temporary directory or prepare an exact curl payload.
3. **Tier 3 (UI Flow):** Use browser automation tools (`agent-browser` or `chrome-profile`) to capture screenshots and network traces.
4. **Tier 4 (Ghost Bug):** Execute a clean cache purge (`--clean`) to ensure the bug is reproducible from a pristine state.
5. **Tier 5 (Git Regression):** Use `git log -p -n 10` or `--bisect` to pinpoint the offending commit.

---

### Phase 3: Backward Tracing & Sub-Engine Routing

Trace backwards from failure point to source: **Failure Site $\leftarrow$ Data Mutation $\leftarrow$ Storage/Service $\leftarrow$ Entry Point**.

Route to specialized trace workflows when appropriate:
- 🖥️ **For Frontend / UI Data Flow:**
  - Run `/trace-fe <component-or-page>` to trace `UI Event -> Handler -> State/Hooks -> API Call`.
- ⚙️ **For Backend / API / Database Flow:**
  - Run `/trace-be <endpoint-or-service>` to trace `Route -> Middleware -> Validation -> Service -> DB -> Events`.
- 🌐 **For Fullstack / Cross-Layer Flow:**
  - Chain `/trace-fe` output (API endpoint + payload) directly into `/trace-be` input for end-to-end trace.
- 💉 **Dynamic Instrumentation (Temporary Markers):**
  - If static analysis is insufficient, inject structured logs:
    `console.log('[DEBUG-TRACE]', { step: 1, payload, time: Date.now() })`
  - Re-run reproduction to capture runtime timeline, then remove temporary logs.

---

### Phase 4: Hypothesis Tree & Elimination Matrix

Formulate **2 to 4 distinct hypotheses** and test against empirical evidence:

| # | Hypothesis (Suspected Cause) | Supporting / Conflicting Evidence | Validation Method | Result |
|---|------------------------------|-----------------------------------|-------------------|--------|
| H1 | Cyclic import between Module A and B | AST analysis shows dependency cycle | Extract shared types into separate file | ❌ / ✅ |
| H2 | DTO schema allows `undefined` fields | Zod/Joi schema misses required rule | Run unit test with partial payload | ❌ / ✅ |
| H3 | Stale cache key in Redis | DB updated but UI shows old record | Inspect Redis TTL and cache invalidation | ❌ / ✅ |

> **Root Cause Acceptance Criteria:** An accepted root cause MUST explain:
> 1. *Why the defect happens.*
> 2. *Why it occurs specifically in this environment / condition.*
> 3. *Why the proposed fix permanently resolves it without side effects.*

---

### Phase 5: Minimal Targeted Fix & Regression Guard

1. **Fix Rules:**
   - Patch at the root origin of dirty data, not at downstream display sites.
   - Preserve existing public API contracts and TypeScript interfaces unless explicitly requested.
   - Clean up all temporary instrumentation logs injected during Phase 3.
2. **Regression Guard:**
   - Turn the Phase 2 reproduction test from `RED -> GREEN`.
   - Add schema validation guards or invariant assertions at data entry boundaries.

---

### Phase 6: Blast Radius & Multi-Environment Verification

1. **Reproduction Check:** Re-run the Phase 2 reproduction test $\rightarrow$ MUST PASS.
2. **Blast Radius Check:** Run all related unit and integration tests $\rightarrow$ Zero regressions.
3. **Build & Type Check:** Run `build` and `type-check` commands to ensure bundle and typings are clean.

---

## 🧭 Domain Diagnostic Checklists

### 1. Build, Bundler & Environment Drift
- [ ] OS path separator discrepancy (`\\` vs `/`) in regex or glob patterns?
- [ ] Case-sensitive file naming mismatches (`User.ts` vs `user.ts`) on Linux/CI?
- [ ] ESM (`import`) vs CommonJS (`require` / `.default`) interop collision?
- [ ] Tree-shaking / Minifier accidentally stripping global side-effects or CSS?

### 2. Circular Dependencies
- [ ] Cyclic import between Module A $\leftrightarrow$ Module B resulting in uninitialized `{}` / `undefined`?
- [ ] Can shared interfaces/types be decoupled into a dedicated `types.ts` file?

### 3. Stale Cache, HMR & Resource Leaks
- [ ] Cleared bundler caches (`.next`, `.turbo`, `.vite`, `dist`)?
- [ ] Unclosed database connection pools, un-cleared intervals/event listeners on unmount?

### 4. Legacy Data & Schema Drift
- [ ] Existing DB records missing columns or defaults required by new code?
- [ ] Is a data backfill / migration required alongside the code fix?

### 5. Frontend Lifecycle & State (Use `/trace-fe`)
- [ ] Stale closures in `useEffect`, `useCallback`, or `useMemo`?
- [ ] Hydration mismatch between SSR server markup and CSR client DOM?

### 6. Backend, Transactions & Concurrency (Use `/trace-be`)
- [ ] Multi-table writes wrapped inside database transactions (`@Transactional` / `BEGIN-COMMIT`)?
- [ ] Race conditions or lost updates during concurrent writes to shared resources?

### 7. Auth, Cookies & Permissions
- [ ] Token refresh race conditions when concurrent requests hit expired sessions?
- [ ] Cookie attributes missing `SameSite=None; Secure` in cross-origin environments?

---

## 🛑 Stop Conditions

Pause execution and prompt the user when:
1. Bug cannot be reproduced after 3 distinct scenarios and full cache purge.
2. Fix requires breaking changes to Public APIs, protocols, or production database schemas.
3. Access to private secrets, third-party API credentials, or remote servers is required.
4. Critical security vulnerability (auth bypass, data leakage) is discovered outside current scope.

---

## 📋 Output Report Template

```markdown
## 🐞 Debug Report: [Issue Name / Target]

### 1. Classification & Severity
- **Category:** [Type 1 to 8]
- **Severity:** Critical | High | Medium | Low
- **Affected Environment:** [Local / CI / Staging / Production]

### 2. Reproduction Evidence
- **Reproduction Method:** `tests/.../repro.test.ts` or script
- **Trace Sub-Engine Used:** [`/trace-fe` | `/trace-be` | None]
- **Expected Behavior:** [Specification / Intended outcome]
- **Actual Behavior:** [Error message, stack trace, or actual output]

### 3. Root Cause Analysis
- **Location:** [`path/to/file.ts:L45`](file:///path/to/file.ts#L45)
- **Mechanism:** [Explain step-by-step why the failure occurred]

### 4. Applied Fix
- **Summary:** [Concise description of the fix]
- **Modified Files:** [`fileA.ts`](file:///path/to/fileA.ts)

### 5. Verification & Regression Guard
- **Reproduction Test:** `PASS`
- **Blast Radius Test Suite:** `PASS` (X tests passing)
- **Build / Type-Check:** `PASS`
- **Regression Guard:** [New test case or validation schema added]
```
