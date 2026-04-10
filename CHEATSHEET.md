---
description: 📌 Super-short workflow selection cheatsheet for fast project execution.
---

# Workflow Cheatsheet

Use this file when you want to decide fast.

---

## Pick Fast

- Need ideas? → `/brainstorm`
- Need a plan first? → `/plan`
- Need deep plan for risky/complex work? → `/advanced-plan`
- Need to execute a plan file? → `/execute-plan`
- Need to understand existing code? → `/analyze`
- Need to assess system boundaries/ownership? → `/architecture`
- Building from scratch? → `/create`
- Adding/changing feature behavior? → `/enhance`
- Adding/changing complex feature behavior? → `/advanced-enhance`
- Fixing a bug? → `/debug`
- Fixing hard/intermittent/production bug? → `/advanced-debug`
- Need final senior-style risk review? → `/review`
- Cleaning code without changing behavior? → `/refactor`
- Splitting a large file? → `/split-file`
- Need tests? → `/test`
- Need better performance? → `/optimize`
- Need security check? → `/security`
- Need local run/check? → `/preview`
- Need deploy? → `/deploy`
- Need current project state? → `/status`
- Need many agents for a big task? → `/orchestrate`
- Need heavy UI/UX work? → `/ui-ux-pro-max`

---

## If Confused

- Mixed task, unclear code, or risky area? → `/analyze`

---

## Common Decisions

- Change behavior → `/enhance`
- Complex/risky behavior change → `/advanced-enhance`
- Keep behavior, improve structure → `/refactor`
- Wrong behavior / broken flow → `/debug`
- Hard or recurring wrong behavior → `/advanced-debug`
- Boundary / ownership / layer issues → `/architecture`
- Final bug/regression/risk audit → `/review`
- Slow / heavy / expensive → `/optimize`
- Risk / auth / secrets / permissions → `/security`

---

## Fast Flows

### Build New

```text
/plan
→ /create
→ /test
→ /security
→ /preview
→ /deploy
```

### Build From Plan

```text
/advanced-plan
→ /execute-plan
→ /test
→ /security
→ /review
→ /preview
```

### Change Existing

```text
/analyze
→ /architecture (if needed)
→ /enhance or /advanced-enhance
→ /test
→ /security
→ /review
→ /preview
```

### Fix Bug

```text
/analyze
→ /debug or /advanced-debug
→ /test
→ /review
→ /preview
```

### Cleanup First

```text
/analyze
→ /architecture or /refactor or /split-file
→ /test
→ /review
→ /enhance
```

### Ship Safely

```text
/test
→ /security
→ /preview
→ /deploy
```

---

## Core Set

If shipping fast is the priority, the main ones are:

- `/plan`
- `/advanced-plan`
- `/execute-plan`
- `/analyze`
- `/architecture`
- `/create`
- `/enhance`
- `/advanced-enhance`
- `/debug`
- `/advanced-debug`
- `/review`
- `/test`
- `/security`
- `/preview`
- `/deploy`

---

## Rules

- Do not use `/refactor` for feature work
- Do not use `/architecture` for tiny cleanup
- Do not use `/advanced-enhance` for tiny tweaks
- Do not use `/advanced-debug` for obvious bugs
- Do not use `/review` instead of `/security` for sensitive risk
- Do not use `/optimize` without a real bottleneck
- Do not skip `/security` before sensitive release
- Do not skip `/test` after risky changes
- Start with `/analyze` when unsure
