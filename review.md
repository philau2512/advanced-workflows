---
description: 👁️ Practical review workflow for finding bugs, regressions, structural risks, missing tests, and security smells before changes are considered safe.
---

# /review - Risk-Focused Code Review Mode

$ARGUMENTS

---

## Purpose

This command activates REVIEW mode for practical code review.

Use it to inspect changes, features, refactors, fixes, or releases with a senior review mindset focused on finding real problems before they escape.

---

## Core Rule

> **REVIEW = FIND THE HIGHEST-RISK PROBLEMS FIRST, SUPPORT THEM WITH EVIDENCE, AND RECOMMEND THE SMALLEST EFFECTIVE FIX**

Default behavior:

- prioritize bugs over style
- prioritize regressions over preferences
- prioritize missing validation/tests over cosmetic comments
- distinguish confirmed findings from lower-confidence risks
- keep summaries brief after findings

---

## What This Is Not

`/review` is not:

- a formatting review
- a generic praise pass
- a rewrite proposal
- a substitute for `/security`
- a substitute for `/test`

If the main goal is security, use `/security`.
If the main goal is understanding architecture, use `/architecture`.

---

## When To Use

Use `/review` when:

- a feature is implemented and needs scrutiny
- a refactor may have hidden regressions
- a bug fix may have incomplete coverage
- a plan was executed and needs a final check
- a risky PR/change set needs senior review
- you want to catch edge cases before deploy

---

## Relationship To Other Workflows

| Need | Workflow |
|------|----------|
| Understand behavior first | `/analyze` |
| Deep feature change | `/advanced-enhance` |
| Hard bug fix | `/advanced-debug` |
| Structural integrity | `/architecture` |
| Security-specific audit | `/security` |
| Behavior validation | `/test` |
| Final quality/risk review | `/review` |

---

## Review Priorities

Review in this order:

1. correctness bugs
2. behavioral regressions
3. missing error handling
4. missing validation
5. broken contracts/interfaces
6. data integrity risk
7. auth/permission risk
8. performance landmines
9. maintainability risks
10. missing tests

Style-only feedback is last.

---

## Review Scope Levels

### Level 1: Targeted Review

Use for:

- one feature
- one bug fix
- one module
- one refactor

Deliverables:

- ranked findings
- missing tests
- residual risks

### Level 2: Change Set Review

Use for:

- multi-file feature work
- risky refactor
- release candidate slice

Deliverables:

- findings by severity
- regression concerns
- follow-up validation

### Level 3: Release Review

Use for:

- pre-deploy quality gate
- broad safety/risk pass

Deliverables:

- top blockers
- acceptable risks
- recommended pre-ship checks

---

## Review Protocol

### Phase 1: Clarify Review Target

Identify:

- what changed
- intended behavior
- affected users/flows
- risk level

### Phase 2: Read The Relevant Evidence

Read:

- changed files
- nearby dependencies
- related tests
- config changes if relevant
- plan file if work was plan-driven

### Phase 3: Check For Correctness

Look for:

- incorrect logic
- wrong assumptions
- broken edge cases
- missing state updates
- partial fixes
- inconsistent validation

### Phase 4: Check For Regression Risk

Look for:

- changed behavior not covered by tests
- side effects moved or lost
- public contract drift
- old callers no longer compatible
- missing backward compatibility guard

### Phase 5: Check For Structural And Operational Risk

Look for:

- hidden coupling
- ownership confusion
- accidental complexity
- missing observability
- security-sensitive smell worth escalating
- performance traps likely to matter

### Phase 6: Check Tests And Validation

Ask:

- what behavior changed?
- what test should prove it?
- what test is missing?
- what manual scenario still needs checking?

---

## Finding Quality Rules

A good finding must include:

- what is wrong
- where it is
- why it matters
- likely impact

A weak finding:

- is purely stylistic
- is speculative without saying so
- lacks user or system impact
- recommends broad rewrites without proving need

---

## Severity Guide

### P1

Use when the issue likely causes:

- incorrect behavior
- user-visible breakage
- data corruption/loss
- auth/permission failure
- release-blocking regression

### P2

Use when the issue likely causes:

- meaningful risk under common scenarios
- missing edge-case handling
- incomplete feature behavior
- avoidable fragility

### P3

Use when the issue:

- weakens maintainability
- leaves testing gaps
- creates lower-probability but plausible risk

---

## Anti-Patterns To Avoid

Do not:

- bury serious findings under style comments
- treat “works on happy path” as enough
- assume missing tests means safe behavior
- turn review into architecture redesign unless clearly necessary
- overstate certainty
- list too many low-signal nitpicks

---

## Output Format

```markdown
## 🔎 Review Findings: [Target]

### Findings
1. **[P1] [Title]** — [what is wrong, where, why it matters]
2. **[P2] [Title]** — [what is wrong, where, why it matters]
3. **[P3] [Title]** — [what is wrong, where, why it matters]

### Open Questions
- [question or assumption]

### Residual Risk
- [risk still not fully covered]

### Suggested Follow-Up
- Use `/test` for [missing coverage]
- Use `/security` if [security-sensitive area]
- Use `/refactor` if [structural problem]
```

If there are no findings:

```markdown
## 🔎 Review Findings: [Target]

No major findings.

### Residual Risk
- [remaining gap such as missing tests, manual-only verification, or partial confidence]
```

---

## Short Output Format

```markdown
## 🔎 Review: [Target]

- Main finding: [one line] or `No major findings`
- Test gap: [one line]
- Residual risk: [one line]
```

---

## Definition Of Done

`/review` is complete only when:

- highest-risk findings are identified first
- findings are evidence-backed
- severity is assigned practically
- missing tests or residual risks are called out
- summary does not overshadow the findings

---

## Final Rule

A good review reduces risk, not confidence theater.
