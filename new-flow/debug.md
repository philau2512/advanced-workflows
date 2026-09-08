---
description: 🐞 Precision Root-Cause Debugging Engine. Clarify ambiguous bugs with intake interviews, trace backward from errors, eliminate hypotheses, and apply minimal regression-proof fixes.
argument-hint: "[issue or stacktrace] [--bisect] [--clean] [--out]"
---

# /debug - Precision Root-Cause Debugging Engine

$ARGUMENTS

---

## 🎯 Core Rules & Invariants

Activates **ROOT-CAUSE DEBUGGING MODE** to isolate, understand, and permanently eradicate software bugs.

> **IRON INVARIANTS:**
> 1. **ZERO GUESSWORK & NO BLIND PATCHING:** If bug report is vague, interview the user before touching code.
> 2. **NO SYMPTOM MASKING:** Never swallow errors with empty `catch {}`, excessive `?.`, or fallback `|| null`. Fix the root trigger at the origin.
> 3. **REPRODUCTION BEFORE EDIT:** Establish a failing test, minimal repro script, or verifiable curl/command before writing the fix.
> 4. **MINIMAL BLAST RADIUS:** Apply the smallest safe patch that fixes the defect without introducing regression.

---

## ⚙️ Argument Parsing & Flags

- `--bisect`: Run automated git bisect / commit binary search to find the regression commit.
- `--clean`: Clear build caches (`node_modules/.cache`, `.next`, `dist`, `.turbo`) before diagnosing.
- `--out`: Save diagnostic report to `reports/DEBUG-<YYYYMMDD>-<issue-name>.md`.
- `$ARGUMENTS`: Error description, stack trace, failing endpoint, test failure, or log snippet.

---

## 🔬 6-Phase Precision Debugging Protocol

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Intake & Clarify    │ ──► │ 2. Reproduce           │ ──► │ 3. Backward Trace      │
│    (3 Questions if vague)│   │    (Failing test/gate) │     │    (Crash -> Origin)   │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 6. Verify & Guard      │ ◄── │ 5. Target Root Fix     │ ◄── ┌────────────────────────┐
│    (Regression Check)  │     │    (Permanent solution)│     │ 4. Hypothesis Tree     │
└────────────────────────┘     └────────────────────────┘     │    & Elimination Matrix│
                                                              └────────────────────────┘
```

### Phase 1: Intake Clarification & Behavior Interview Gate
If the bug report lacks reproduction steps, payload, or logs:
**STOP. Ask the 3 Diagnostic Questions:**
1. **Trigger Action:** *"What exact sequence of actions or inputs did you perform right before the error appeared?"*
2. **Context & Environment:** *"What specific account, role, browser/OS, or environment (local/staging/prod) was this on?"*
3. **Exact Observed Symptom:** *"What exact error message, toast, HTTP status code, or console log was produced?"*
*(Skip interview if exact stack traces or clear repro steps are already provided).*

#### Classify Error Category:
- **Crash / Syntax:** Uncaught exceptions, `TypeError: cannot read property of undefined`, panic.
- **Logic / State Desync:** Stale UI closures, incorrect calculation, missing state sync.
- **Async / Concurrency / Race:** Unhandled promise, race condition, out-of-order responses.
- **Boundary / Auth / Data:** Schema mismatch, 401/403 RBAC failure, DB constraint violation, CORS.

### Phase 2: Reproduction Gate
- Replicate the failure locally using a targeted unit/integration test (`vitest run <test>`) or minimal curl command.
- Confirm the defect is 100% reproducible before inspecting source implementation.

### Phase 3: Backward Tracing
Trace backward from the point of failure to the entry point:
- **Point of Crash:** The exact line throwing the exception or returning bad data.
- **Immediate Caller:** Where arguments were passed into the crashing function.
- **Originating Trigger:** The initial user interaction, route handler, or API payload that introduced corrupt data.

### Phase 4: Hypothesis Tree & Evidence-Driven Elimination
Formulate 2-3 specific hypotheses explaining the defect. Test each with empirical evidence:

| Hypothesis | Test / Verification Method | Evidence Found | Status |
| :--- | :--- | :--- | :--- |
| H1: Missing null check on user profile | Inspect DB record returned by repo | Record has profile, issue still occurs | **Eliminated** |
| H2: Race condition in async token refresh | Add timestamp logs to interceptor | Token refresh resolves after request fires | **CONFIRMED** |

### Phase 5: Target Root Fix
- Modify ONLY the code responsible for the defect.
- Maintain existing API contracts and public signatures.
- Add an automated regression test covering this exact edge case.

### Phase 6: Verification & Multi-Surface Guard
- Re-run the reproduction test: Verify it now passes (**Red -> Green**).
- Run existing related test suites to ensure zero regression.
- Verify build and typechecks pass (`npm run type-check` or `tsc --noEmit`).

---

## 📋 Standard Debug Report Template

```markdown
## 🐞 Debug Diagnostic Report: [Bug Title]

### 1. Defect Summary
- **Symptom:** [What was broken]
- **Root Cause Category:** [Crash / Logic / Race / Boundary]
- **Reproduction:** `[Command or test path used to trigger bug]`

### 2. Root Cause Analysis
- **Point of Failure:** `[file.ts:line]`
- **Root Trigger:** [Explanation of why the bug occurred, not just where]

### 3. Hypothesis Elimination Matrix
- **Hypothesis 1:** [Claim] -> *Eliminated by [Evidence]*
- **Hypothesis 2 (Root Cause):** [Claim] -> *Confirmed by [Evidence]*

### 4. Applied Root-Cause Fix
- **Modified File:** `[path/to/file.ts:line]`
- **Changes Applied:** [Summary of structural fix]
- **Regression Test Added:** `[tests/regression/issue-xxx.test.ts]`

### 5. Verification
- Reproduction test: **PASS**
- Related test suite: **PASS** (Zero regression)
```
