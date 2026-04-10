---
description: 🚀 Advanced feature enhancement workflow for complex, multi-file, risky, or production-quality changes in existing applications.
---

# /advanced-enhance - Deep Feature Enhancement Mode

$ARGUMENTS

---

## Purpose

This command adds or changes feature behavior in an existing application with stronger planning, safety, validation, and release readiness than `/enhance`.

Use it when the change is bigger than a quick feature tweak but not necessarily large enough to require full orchestration.

---

## Core Rule

> **ADVANCED ENHANCE = CHANGE BEHAVIOR INTENTIONALLY, PRESERVE EVERYTHING ELSE DELIBERATELY**

Default behavior:

- understand existing behavior before changing it
- define scope and non-goals
- implement in small safe increments
- validate behavior after each meaningful change
- run relevant tests/security/preview checks before calling it done

---

## When To Use

Use `/advanced-enhance` for:

- multi-file feature additions
- changes touching existing business logic
- features involving auth, roles, permissions, billing, uploads, exports, or user data
- UI + API + database changes
- feature work that could regress existing flows
- user-facing workflow changes
- production-quality MVP features
- enhancements that need tests and security checks

Use `/enhance` instead for:

- small UI tweaks
- simple copy/style changes
- one-file feature changes
- low-risk additions
- quick iteration with obvious impact

Use `/execute-plan` instead when:

- a `/plan` or `/advance-plan` file already exists and should be followed task-by-task

---

## Relationship To Other Workflows

| Need | Workflow |
|------|----------|
| Quick feature update | `/enhance` |
| Complex feature update | `/advanced-enhance` |
| Deep plan first | `/advance-plan` |
| Execute existing plan | `/execute-plan` |
| Understand unclear code | `/analyze` |
| Fix bug discovered during feature work | `/debug` or `/advanced-debug` |
| Refactor before feature work | `/refactor` |
| Security-sensitive feature | `/security` |
| Validate behavior | `/test` |
| Preview result | `/preview` |
| Ship result | `/deploy` |

---

## Enhancement Type

Classify the request before implementation.

### Small

Use `/enhance` instead unless the user explicitly asks for advanced handling.

Examples:

- add one field to UI only
- change button behavior
- add one simple setting

### Medium

Use `/advanced-enhance`.

Examples:

- add feature across UI and API
- add new state flow
- add validation and tests
- modify existing user journey

### High-Risk

Use `/advance-plan` first or create a short plan inside this workflow before implementation.

Examples:

- auth/permission changes
- billing/payment changes
- database migration
- user data export/import
- upload pipeline
- production config
- broad behavior changes

---

## Pre-Enhancement Gate

Before editing code, confirm:

- goal is clear
- existing behavior is understood
- scope is bounded
- non-goals are explicit
- affected areas are identified
- risk level is known
- validation path exists

If the code path is unclear, run `/analyze`.

If the change is high-risk or broad, run `/advance-plan`.

If a plan already exists, use `/execute-plan`.

---

## Scope Contract

Every advanced enhancement must define:

- what behavior will change
- what behavior must not change
- which users/roles are affected
- which files/modules are likely affected
- which validation checks prove success

Do not add adjacent features just because they are nearby.

---

## Implementation Protocol

Follow this order unless the feature requires a safer sequence.

### Phase 1: Understand Current State

Read:

- entry points
- existing feature flow
- related components/routes/endpoints
- data models/types/schemas
- state/store/context
- tests
- relevant config/env

Capture:

- current behavior
- assumptions
- constraints
- likely regression points

### Phase 2: Define Target Behavior

State:

- new behavior
- changed behavior
- unchanged behavior
- success criteria
- UX/API/data expectations

If the target behavior is ambiguous, ask only the minimum critical questions.

### Phase 3: Design The Smallest Safe Change

Choose the narrowest implementation that satisfies the scope.

Prefer:

- extending existing patterns
- reusing existing abstractions
- adding tests near changed behavior
- keeping public contracts stable unless the feature requires change

Avoid:

- broad rewrites
- unrelated refactors
- new dependencies without clear need
- changing architecture for a small feature

### Phase 4: Implement In Slices

Use small vertical slices when possible.

Suggested order:

1. types/schema/contracts
2. backend/service/data logic
3. API or integration boundary
4. frontend state/data loading
5. UI behavior
6. tests
7. security/preview checks

After each slice:

- re-read changed files
- check imports/references
- run targeted verification if available

### Phase 5: Validate Feature Behavior

Verify:

- happy path
- main error path
- permission/role behavior if relevant
- loading/empty states if UI
- data persistence if relevant
- backward compatibility if existing users/data are affected

### Phase 6: Run Risk-Based Checks

Run only relevant checks:

- `/test` for behavior coverage
- `/security` for auth, data, uploads, secrets, permissions
- `/optimize` only if performance risk is real
- `/preview` for UI/runtime behavior

Do not run heavy checks just for appearance.

### Phase 7: Finalize

Report:

- what changed
- what stayed unchanged
- files updated
- validation results
- security/performance notes
- remaining risks
- next step

---

## Skill Usage

Use skills only when they improve execution quality.

Recommended:

| Need | Skill |
|------|-------|
| App-level feature build | `app-builder` |
| Task planning and verification | `plan-writing` |
| Maintainable implementation | `clean-code` |
| UI-heavy enhancement | `frontend-design` or `ui-ux-pro-max` |
| Security-sensitive feature | `vulnerability-scanner` |
| Web/E2E validation | `webapp-testing` |
| Performance-sensitive feature | `performance-profiling` |

If no specialized skill is needed, proceed without one.

---

## Security Gate

Run or recommend `/security` when the feature touches:

- authentication
- authorization
- roles/permissions
- user data
- billing/payment
- file upload
- exports/imports
- secrets/env config
- admin actions
- webhooks

Security-sensitive enhancements are not done until the relevant risk is checked.

---

## Validation Checklist

Before completion, check:

- target behavior works
- existing related behavior still works
- tests pass or missing tests are documented
- security-sensitive paths were reviewed
- no unrelated feature was added
- preview works if UI/runtime behavior changed
- remaining risk is stated honestly

---

## Stop Conditions

Stop and ask before continuing if:

- scope becomes larger than requested
- feature requires a risky migration not planned
- public API contract must change unexpectedly
- security impact is unclear
- required credentials/secrets are unavailable
- validation fails 3 times
- implementation conflicts with non-goals

---

## Anti-Patterns To Avoid

Do not:

- start coding before understanding current flow
- turn feature work into broad refactor
- add unrequested features
- skip validation because the UI "looks right"
- rely on frontend-only permission checks
- introduce dependency just to save a few lines
- ignore existing project conventions
- call work done without checking related behavior

---

## Output Format

```markdown
## 🚀 Advanced Enhance Report: [Feature]

### Scope
- Goal: [target behavior]
- Included: [what changed]
- Non-goals: [what stayed out]

### Current Behavior
[brief summary of existing flow]

### Target Behavior
[brief summary of new/changed behavior]

### Changes Made
| Area | Change | Reason |
|------|--------|--------|
| `src/...` | [change] | [reason] |

### Validation
- Tests: Pass | Fail | Not run
- Security: Checked | Not relevant | Needs follow-up
- Preview: Pass | Not run | Not relevant

### Risks / Notes
- [remaining risk or none]

### Next Step
- [deploy / run more tests / security follow-up / continue feature]
```

---

## Short Output Format

For medium but straightforward enhancements:

```markdown
## 🚀 Advanced Enhance: [Feature]

- Changed: [one line]
- Preserved: [one line]
- Validation: [one line]
- Risk: [one line]
```

---

## Definition Of Done

`/advanced-enhance` is complete only when:

- target behavior is implemented
- scope and non-goals were respected
- affected areas were checked
- relevant validation passed or is documented
- security-sensitive areas were reviewed
- preview was checked when relevant
- remaining risks are reported

---

## Final Rule

Enhance the product, not the scope.

Ship the requested capability with the smallest safe change and enough verification to trust it.
