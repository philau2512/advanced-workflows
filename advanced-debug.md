---
description: 🧩 Advanced debugging workflow for hard, multi-layer, production, race-condition, auth/session, data inconsistency, or recurring bugs that require evidence-driven root cause analysis.
---

# /advanced-debug - Deep Root Cause Investigation Mode

$ARGUMENTS

---

## Purpose

This command activates ADVANCED DEBUG mode for difficult bugs where quick fixes are risky or insufficient.

Use it when the problem needs evidence, reproduction, hypothesis testing, root cause isolation, and regression protection.

---

## Core Rule

> **ADVANCED DEBUG = REPRODUCE, COLLECT EVIDENCE, ISOLATE ROOT CAUSE, APPLY MINIMAL FIX, PROVE IT**

Default behavior:

- do not guess
- do not patch symptoms first
- do not change unrelated code
- collect evidence before fixing
- isolate the smallest root cause
- apply the smallest safe fix
- add or recommend regression coverage

---

## When To Use

Use `/advanced-debug` for:

- production bugs
- intermittent bugs
- race conditions
- async/lifecycle bugs
- auth/session bugs
- permission bugs
- data inconsistency
- multi-layer frontend/backend/database issues
- bugs that returned after previous fixes
- performance-related failures with correctness impact
- security-sensitive defects
- bugs with unclear reproduction steps

Use `/debug` instead for:

- obvious local errors
- simple stack trace fixes
- one-file mistakes
- typo/import/config mistakes
- low-risk bugs with clear cause

---

## Relationship To Other Workflows

| Need | Workflow |
|------|----------|
| Simple bug fix | `/debug` |
| Hard bug investigation | `/advanced-debug` |
| Understand code before investigation | `/analyze` |
| Plan risky fix | `/advance-plan` |
| Execute existing debug plan | `/execute-plan` |
| Add regression coverage | `/test` |
| Security-sensitive defect | `/security` |
| Performance bottleneck | `/optimize` |

If the bug path is unclear, run `/analyze` first or as Phase 1.

---

## Investigation Scope Levels

Choose the smallest level that can prove the root cause.

### Level 1: Focused Deep Debug

Use for:

- one failing flow
- one endpoint
- one component
- one function chain

Deliverables:

- reproduction
- root cause
- minimal fix
- targeted verification

### Level 2: Cross-Layer Debug

Use for:

- UI → API → DB issues
- auth/session propagation bugs
- state synchronization bugs
- background job or webhook bugs

Deliverables:

- timeline
- cross-layer flow map
- root cause proof
- regression guard

### Level 3: Production / Intermittent Debug

Use for:

- race conditions
- environment-only bugs
- production-only bugs
- recurring incidents

Deliverables:

- evidence summary
- likely trigger conditions
- mitigation
- permanent fix plan
- monitoring/logging recommendation

---

## Debug Protocol

Follow this order unless the situation requires immediate containment.

### Phase 1: Triage And Impact

Identify:

- what is broken
- who is affected
- severity
- frequency
- environment
- recent changes
- data/security impact

Severity guide:

- **Critical**: data loss, auth bypass, payment failure, production outage
- **High**: major user flow broken, privilege/data exposure risk
- **Medium**: important feature unreliable but contained
- **Low**: minor incorrect behavior or local-only failure

If impact is critical, recommend containment before deep investigation.

### Phase 2: Reproduction Gate

Try to establish one of:

- exact reproduction steps
- failing automated test
- log/event pattern
- production trace
- minimal input that triggers the issue

If reproducible:

- document steps
- capture expected vs actual
- capture environment

If not reproducible:

- collect best available evidence
- list likely trigger conditions
- do not claim certainty

### Phase 3: Evidence Collection

Gather:

- error message
- stack trace
- logs
- request/response payload
- relevant config/env differences
- recent commits or changes
- affected data examples
- browser/network traces if frontend
- query/job/webhook traces if backend

Evidence must be tied to the suspected flow.

### Phase 4: Flow Trace

Trace the failing path:

- entry point
- validation
- state changes
- service calls
- DB/storage/network calls
- async boundaries
- error handling
- output/render/response

If the flow is unclear, use `/analyze`.

### Phase 5: Hypothesis Tree

Create 2-5 hypotheses.

Each hypothesis must include:

- suspected cause
- evidence supporting it
- evidence against it
- test to confirm or reject
- likelihood

Do not list random possibilities.

### Phase 6: Root Cause Isolation

Test hypotheses in priority order.

Use:

- targeted logs
- focused test cases
- local reproduction
- controlled input
- config comparison
- call path tracing
- binary search through recent changes if needed

The root cause is accepted only when it explains:

- why this symptom happens
- why it happens in this environment/path
- why the proposed fix prevents it

### Phase 7: Minimal Safe Fix

Apply the smallest fix that addresses the root cause.

Rules:

- fix root cause, not only symptom
- avoid unrelated refactor
- avoid broad rewrites
- preserve public contract unless approved
- keep behavior changes explicit
- add comments only for non-obvious constraints

If a proper fix is too risky:

- propose containment
- document permanent fix plan
- ask user before risky changes

### Phase 8: Regression Guard

Add or recommend at least one guard:

- unit test
- integration test
- E2E test
- reproduction test
- assertion
- validation check
- monitoring/logging improvement

If no test is practical, document why and provide manual verification.

### Phase 9: Verification

Verify:

- original reproduction no longer fails
- expected behavior works
- adjacent behavior still works
- relevant tests pass
- logs/errors are clean
- no security/data regression introduced

Do not mark complete until verification is credible.

---

## Special Debug Heuristics

### Auth / Session Bugs

Check:

- token creation and verification
- cookie flags and lifetime
- server-side guard
- client-side state hydration
- role/permission source of truth
- logout/session invalidation

### Data Inconsistency

Check:

- write path
- read path
- transaction boundary
- cache invalidation
- eventual consistency assumptions
- background jobs
- migration state

### Race Conditions

Check:

- shared mutable state
- request ordering
- async effects
- retries
- duplicate submissions
- idempotency
- locking/transaction behavior

### Frontend State Bugs

Check:

- component lifecycle
- stale closures
- derived state
- async loading states
- cache/store synchronization
- route transitions

### API / Backend Bugs

Check:

- validation
- authz
- service logic
- DB query assumptions
- external API failures
- error mapping
- retry/fallback behavior

---

## Stop Conditions

Stop and ask for help if:

- bug cannot be reproduced and evidence is insufficient
- fix requires changing public behavior beyond request
- production data/config change is required
- credentials or secrets are needed
- root cause conflicts with plan assumptions
- verification fails 3 times
- investigation reveals security risk beyond current scope

---

## Anti-Patterns To Avoid

Do not:

- fix before reproducing unless containment is urgent
- patch only the visible symptom
- make broad refactors during debugging
- ignore recent changes
- ignore environment differences
- assume frontend error means frontend cause
- assume backend error means backend cause
- mark root cause without proof
- skip regression thinking after fix

---

## Output Format

```markdown
## 🧩 Advanced Debug Report: [Issue]

### Impact
- Severity: Critical | High | Medium | Low
- Affected users/flows: [who/what]
- Environment: [local/staging/prod]

### Reproduction
- Status: Reproduced | Intermittent | Not reproduced
- Steps / Evidence: [steps, logs, traces, payload]
- Expected: [expected behavior]
- Actual: [actual behavior]

### Flow Trace
1. [entry point]
2. [state/data/API/DB step]
3. [failure point]

### Hypotheses
| # | Hypothesis | Evidence | Test | Result |
|---|------------|----------|------|--------|
| H1 | [cause] | [support] | [check] | Confirmed/Rejected |

### Root Cause
[specific cause and why it produced the symptom]

### Fix
- Change: [minimal fix]
- Scope: [files/areas touched]
- Behavior impact: [none/explicitly changed]

### Regression Guard
- [test/check/monitoring added or recommended]

### Verification
- [check]: Pass | Fail | Not run

### Remaining Risk
- [risk or none]
```

---

## Short Output Format

For smaller but still non-trivial bugs:

```markdown
## 🧩 Advanced Debug: [Issue]

- Reproduction: [status]
- Root cause: [one line]
- Fix: [one line]
- Verification: [one line]
- Regression guard: [one line]
```

---

## Definition Of Done

`/advanced-debug` is complete only when:

- impact is understood
- reproduction or best available evidence is documented
- root cause is proven or uncertainty is clearly labeled
- fix targets the root cause
- verification has been run or documented as unavailable
- regression guard exists or is explicitly recommended
- remaining risk is reported

---

## Final Rule

Debug with evidence.

The best fix is the smallest change that explains and prevents the failure.
