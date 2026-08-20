---
description: 🐞 Precision root-cause debugging workflow combining intake clarification interviews, reproduction gates, backward tracing, hypothesis elimination, and regression guards.
---

# /debug - Precision Root Cause & Hardcore Debugging Mode

$ARGUMENTS

---

## 🎯 Core Rules & Invariants

> 1. **ZERO GUESSWORK & NO BLIND SEARCH:** If the bug report is vague, interview the user about their exact behavior before guessing or modifying code.
> 2. **NO SYMPTOM PATCHING:** Do NOT swallow errors with empty `try/catch`, excessive `?.`, or fallback `|| null` checks. Fix the root trigger at source.
> 3. **REPRODUCTION BEFORE FIX:** Establish an automated failing test, minimal repro script, or concrete steps before touching code.
> 4. **MINIMAL BLAST RADIUS:** Apply the smallest safe fix that eliminates the defect without introducing regressions.

---

## ⚙️ Argument Parsing & Execution Flags

- `--out`: Save diagnostic report to `reports/DEBUG-<YYYYMMDD>-<issue-name>.md`.
- `--bisect`: Run automated git bisect / commit binary search to find the regression commit.
- `--clean`: Clear all build caches (`node_modules/.cache`, `.next`, `.turbo`, `dist`, etc.) before testing.
- `$ARGUMENTS`: Error description, error code, failing endpoint, stack trace, or target file.

---

## 🔬 6-Phase Hardcore Debugging Protocol

```
┌────────────────────────┐     ┌──────────────┐     ┌────────────────────────┐
│ 1. INTAKE & CLARIFY    │ ──► │2. REPRODUCE  │ ──► │ 3. BACKWARD TRACE      │
│    (Behavior Interview)│     │  & Test Gate │     │  (/trace-fe, /trace-be)│
└────────────────────────┘     └──────────────┘     └───────────┬────────────┘
                                                                │
┌────────────────────────┐     ┌──────────────┐                 ▼
│ 6. VERIFY & REGRESSION │ ◄── │5. TARGET FIX │ ◄── ┌────────────────────────┐
│    (Multi-Env Check)   │     │  & Test Guard│     │ 4. HYPOTHESIS TREE     │
└────────────────────────┘     └──────────────┘     │   & Elimination Matrix │
                                                    └────────────────────────┘
```

---

### Phase 1: Intake Clarification & Behavior Interview Gate

#### 1. Ambiguity Detection & User Interview (Mandatory on Vague Inputs):
If the bug report is vague, lacks repro steps, or misses error logs (e.g. *"payment failed"*, *"button doesn't work"*, *"500 error"*):
**STOP. DO NOT GUESS OR EDIT CODE.** Prompt the user with the **3 Diagnostic Questions**:
1. **Trigger Action:** *"What exact sequence of actions or inputs did you perform right before the error appeared?"*
2. **Context & State:** *"Under what specific conditions did this occur? (User role/auth, local vs prod, specific test account, or payload data?)"*
3. **Exact Observed Symptom:** *"What exact error message, toast, HTTP status code in Network tab, or console error was logged?"*

*Proportionality:* If exact logs, stack traces, or clear repro steps are already present, skip the interview and proceed immediately.

#### 2. Classify into 8 Error Categories:
- **Type 1 (Runtime / Crash / Syntax):** Uncaught exceptions, `TypeError`, `NullPointerException`, build crashes.
- **Type 2 (Logic / State Desync):** Incorrect calculations, stale closures, desynchronized UI/store state.
- **Type 3 (Async / Concurrency / Race):** Stale promises, out-of-order execution, distributed lock failure.
- **Type 4 (Auth / Session / CORS):** Token expiration, cookie flags (`SameSite`/`Secure`), hydration mismatch, RBAC leaks.
- **Type 5 (DB / ORM / Legacy Data Drift):** N+1 queries, deadlocks, dirty reads, missing migrations, corrupt legacy rows.
- **Type 6 (Build / Env Drift):** Works locally but fails in CI/Prod, OS path separators (`\\` vs `/`), ESM vs CJS.
- **Type 7 (Circular Dependency):** Runtime `undefined is not a function` caused by cyclic module imports.
- **Type 8 (Heisenbug / Stale Cache):** Stale bundler artifacts, HMR ghost state, service worker, un-invalidated Redis keys.

---

### Phase 2: Reproduction Gate (Mandatory)

Do NOT modify implementation code until reproduction is proven:
1. **Tier 1 (Automated):** Create an isolated failing test (`RED`) in the test suite.
2. **Tier 2 (Script / Curl):** Write a minimal repro script in temp dir or prepare exact curl payload.
3. **Tier 3 (UI Flow):** Use browser automation tools (`agent-browser` or `chrome-profile`) to capture traces.
4. **Tier 4 (Ghost Bug):** Execute a clean cache purge (`--clean`) to ensure bug reproduces from a clean state.
5. **Tier 5 (Git Regression):** Use `git log -p -n 10` or `--bisect` to pinpoint the offending commit.

---

### Phase 3: Backward Tracing & Sub-Engine Routing

Trace backwards: **Failure Site $\leftarrow$ Data Mutation $\leftarrow$ Storage/Service $\leftarrow$ Entry Point**.

- 🖥️ **Frontend / UI Flow:** Run `/trace-fe <component-or-page>` to trace `UI Event -> State/Hooks -> API Call`.
- ⚙️ **Backend / API / DB Flow:** Run `/trace-be <endpoint-or-service>` to trace `Route -> Guard -> Service -> DB`.
- 🌐 **Fullstack / Cross-Layer Flow:** Chain `/trace-fe` output directly into `/trace-be` input.
- 💉 **Dynamic Instrumentation:** Inject temporary logs `console.log('[DEBUG-TRACE]', { step, payload, time: Date.now() })` $\rightarrow$ Capture timeline, then remove.

---

### Phase 4: Hypothesis Tree & Elimination Matrix

Formulate **2 to 4 distinct hypotheses** and test against empirical evidence:

| # | Hypothesis (Suspected Cause) | Supporting / Conflicting Evidence | Validation Method | Result |
|---|------------------------------|-----------------------------------|-------------------|--------|
| H1 | Cyclic import between Module A and B | AST shows dependency cycle | Extract shared types into separate file | ❌ / ✅ |
| H2 | DTO schema allows `undefined` fields | Schema misses required rule | Run unit test with partial payload | ❌ / ✅ |
| H3 | Stale cache key in Redis | DB updated but UI shows old record | Inspect Redis TTL and invalidation | ❌ / ✅ |

> **Root Cause Acceptance:** Must explain: (1) Why it happens, (2) Why under these specific conditions, and (3) Why the fix permanently resolves it without side effects.

---

### Phase 5: Minimal Targeted Fix & Regression Guard

1. **Fix Rules:**
   - Patch at the root origin of dirty data, not at downstream display sites.
   - Preserve existing public API contracts and TypeScript interfaces.
   - Clean up all temporary instrumentation logs injected during Phase 3.
2. **Regression Guard:**
   - Turn the Phase 2 reproduction test from `RED -> GREEN`.
   - Add schema validation guards or invariant assertions at data entry boundaries.

---

### Phase 6: Blast Radius & Verification

1. **Reproduction Check:** Re-run Phase 2 reproduction test $\rightarrow$ MUST PASS.
2. **Blast Radius Check:** Run all related unit and integration tests $\rightarrow$ Zero regressions.
3. **Build & Type Check:** Run `build` and `type-check` commands to ensure bundle and typings are clean.

---

## 🧭 Domain Diagnostic Checklists

### 1. Build, Bundler & Environment Drift
- [ ] OS path separator discrepancy (`\\` vs `/`) in regex or glob patterns?
- [ ] Case-sensitive file naming mismatches (`User.ts` vs `user.ts`) on Linux/CI?
- [ ] ESM (`import`) vs CommonJS (`require` / `.default`) interop collision?
- [ ] Tree-shaking / Minifier accidentally stripping global side-effects or CSS?

### 2. Circular Dependencies & Stale Cache
- [ ] Cyclic import between Module A $\leftrightarrow$ Module B resulting in `{}` / `undefined`?
- [ ] Cleared bundler caches (`.next`, `.turbo`, `.vite`, `dist`)?
- [ ] Unclosed database connection pools or un-cleared intervals on unmount?

### 3. Frontend & Backend Invariants (Use `/trace-fe` & `/trace-be`)
- [ ] Stale closures in `useEffect`, `useCallback`, or `useMemo`?
- [ ] Hydration mismatch between SSR server markup and CSR client DOM?
- [ ] Multi-table writes wrapped inside database transactions (`@Transactional` / `BEGIN-COMMIT`)?
- [ ] Token refresh race condition or missing `SameSite=None; Secure` cookies?

---

## 🛑 Stop Conditions

Pause execution and prompt the user when:
1. Bug report is ambiguous and requires answers to the 3 Diagnostic Questions.
2. Bug cannot be reproduced after 3 distinct scenarios and full cache purge.
3. Fix requires breaking changes to Public APIs, protocols, or production database schemas.
4. Access to private secrets, third-party API credentials, or remote servers is required.

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
- **Expected vs Actual:** [Intended outcome vs actual error/logs]

### 3. Root Cause Analysis
- **Location:** [`path/to/file.ts:L45`](file:///path/to/file.ts#L45)
- **Mechanism:** [Explain step-by-step why the failure occurred]

### 4. Applied Fix & Verification
- **Summary:** [Concise description of the fix in `fileA.ts`]
- **Reproduction Test:** `PASS`
- **Blast Radius Suite:** `PASS` (X tests passing)
- **Regression Guard:** [New test case or validation schema added]
```
