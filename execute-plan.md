---
description: 🚦 Execute an existing plan file task-by-task with dependency ordering, verification, progress updates, and safe handoff to specialized workflows when needed.
---

# /execute-plan - Plan Execution Mode

$ARGUMENTS

---

## Purpose

This command executes an existing plan file.

Use it after `/plan` or `/advance-plan` has created a plan and the user is ready to implement it.

---

## Core Rule

> **EXECUTE PLAN = READ THE PLAN, FOLLOW THE PLAN, VERIFY EACH TASK, UPDATE PROGRESS**

Default behavior:

- read the plan before coding
- preserve the plan scope
- execute tasks in dependency order
- verify each task before marking it complete
- update the plan as progress is made
- stop when the plan is unsafe, unclear, or blocked

---

## What This Is Not

`/execute-plan` is not:

- a planning workflow
- a brainstorming workflow
- a rewrite of the plan without permission
- a shortcut around verification
- an excuse to implement out-of-scope features

If the plan is missing or too vague, use `/plan` or `/advance-plan` first.

If the plan spans many independent domains and requires multiple agents, use `/orchestrate`.

---

## When To Use

Use `/execute-plan` when:

- a plan file already exists
- user approves implementation
- tasks are listed with enough detail to execute
- validation criteria exist or can be safely inferred
- implementation should follow a known sequence

Examples:

```text
/execute-plan docs/ADVANCE-PLAN-admin-permissions.md
/execute-plan docs/PLAN-dashboard.md
/execute-plan latest advance plan
/execute-plan checkout refactor plan
```

---

## Relationship To Other Workflows

| Need | Workflow |
|------|----------|
| Create quick plan | `/plan` |
| Create deep plan | `/advance-plan` |
| Execute existing plan | `/execute-plan` |
| Coordinate many agents/domains | `/orchestrate` |
| Add feature behavior inside plan | `/enhance` |
| Fix bug inside plan | `/debug` |
| Refactor inside plan | `/refactor` |
| Split large file inside plan | `/split-file` |
| Optimize inside plan | `/optimize` |
| Security check inside plan | `/security` |
| Validate implementation | `/test` |

---

## Plan Discovery

If the user provides a path, use that exact plan file.

If no path is provided:

1. Look for recent plan files in `docs/`
2. Prefer `ADVANCE-PLAN-*.md`
3. Then prefer `PLAN-*.md`
4. If multiple candidates exist, choose the most relevant by task name
5. If relevance is unclear, ask the user which plan to execute

Never execute a random plan when multiple plausible plans exist.

---

## Pre-Execution Gate

Before editing code, read the full plan and confirm:

- goal is clear
- scope is clear
- task breakdown exists
- dependencies are understandable
- validation plan exists or can be derived
- definition of done exists or can be derived
- no critical ambiguity blocks execution

If any item is missing:

- safely infer only if low risk
- otherwise stop and ask user to clarify or run `/advance-plan`

---

## Execution Modes

Choose the smallest execution mode that fits the plan.

### Mode 1: Single-Agent Execution

Use when:

- tasks are sequential
- changes are within one main domain
- no complex parallel coordination is needed

This is the default.

### Mode 2: Specialized Workflow Handoff

Use when a task clearly maps to another workflow:

- feature task → `/enhance`
- bug task → `/debug`
- structure task → `/refactor`
- file split task → `/split-file`
- performance task → `/optimize`
- security task → `/security`
- validation task → `/test`

The executor remains responsible for progress tracking and final synthesis.

### Mode 3: Orchestrated Execution

Use `/orchestrate` when:

- plan spans 3+ independent domains
- multiple agents can safely work in parallel
- backend, frontend, DB, security, and tests all need substantial work
- task ownership is clearly separable

Do not use `/orchestrate` just because the plan is long.

---

## Execution Protocol

Follow this order unless the plan explicitly requires a safer sequence.

### Phase 1: Load Plan

Read:

- goal
- scope
- assumptions
- non-goals
- technical approach
- task breakdown
- affected areas
- risks
- validation plan
- definition of done

### Phase 2: Build Execution Queue

Create a task queue from the plan.

Rules:

- respect dependencies
- skip tasks already marked complete only if verification is still valid
- do not start a blocked task
- do not reorder tasks unless dependency logic requires it
- identify critical path if obvious

### Phase 3: Execute One Task At A Time

For each task:

1. restate task id and purpose
2. check inputs and dependencies
3. read relevant files before editing
4. make the smallest safe change
5. verify the task-specific criteria
6. update the plan checkbox only if verification passes

Do not batch unrelated tasks together.

### Phase 4: Verify After Each Task

Use the task's `Verify` instruction first.

If missing, derive the smallest relevant check:

- read changed files
- run lint/typecheck/build if relevant
- run targeted tests if available
- run manual scenario if automation is unavailable

If verification fails:

1. fix and retry up to 3 times
2. if still failing, stop
3. report completed work, remaining work, and blocker

### Phase 5: Update Plan Progress

After a task passes verification:

- mark `[ ]` as `[x]`
- add short note if useful
- do not mark tasks complete without proof

Progress updates must reflect actual work done.

### Phase 6: Run Final Validation Plan

After implementation tasks are complete:

- run the plan's validation checks
- run security/performance checks only if relevant
- run preview if the plan affects UI or runtime behavior
- document checks not run and why

### Phase 7: Final Synthesis

Report:

- completed tasks
- files changed
- validation results
- unresolved risks
- follow-up tasks
- whether Definition Of Done is satisfied

---

## Plan Update Rules

The plan file is the source of truth for execution progress.

Allowed updates:

- mark completed task checkboxes
- add verification notes
- add blocker notes
- add final execution summary

Not allowed without user approval:

- changing goal
- expanding scope
- removing risks
- deleting tasks to make progress look complete
- changing non-goals
- rewriting the plan into a new direction

---

## Safety Gates

Stop before executing if:

- plan file cannot be found
- multiple matching plans exist and intent is unclear
- plan has no task breakdown
- plan scope conflicts with user request
- task requires secrets or credentials not available
- task would change production data/config without approval
- task would exceed stated non-goals

Stop during execution if:

- verification fails 3 times
- dependency is missing
- unexpected unrelated file changes appear
- implementation requires changing public contract not covered by plan
- risk becomes higher than plan assumed

---

## Verification Standards

A task is complete only when:

- its output exists
- its verify step passes
- changed files were checked
- no obvious dependency is broken
- no out-of-scope change was introduced

If verification is unavailable, mark the task as complete only when:

- manual reasoning is documented
- risk is low
- user does not require automated proof

---

## Output Format

```markdown
## 🚦 Execute Plan Report: [Plan Name]

### Plan
- File: `docs/ADVANCE-PLAN-...md`
- Goal: [goal]
- Mode: Single-Agent | Specialized Workflow Handoff | Orchestrated

### Progress
| Task | Status | Verification |
|------|--------|--------------|
| T01 | Done | [check passed] |
| T02 | Done | [check passed] |
| T03 | Blocked | [reason] |

### Files Changed
- `src/...`
- `tests/...`

### Validation
- [check]: Pass | Fail | Not run

### Risks / Notes
- [remaining risk or assumption]

### Definition Of Done
Met | Partially met | Not met

### Next Step
[continue execution / run preview / run deploy / fix blocker]
```

---

## Stop Output Format

Use this when execution cannot continue:

```markdown
## ❌ Execute Plan Stopped: [Plan Name]

### Completed
- [completed tasks]

### Remaining
- [remaining tasks]

### Blocker
[why execution stopped]

### Recommended Next Step
[clarify / fix dependency / revise plan / approve risky change]
```

---

## Anti-Patterns To Avoid

Do not:

- execute without reading the plan
- skip dependencies
- mark tasks complete without verification
- silently expand scope
- use `/orchestrate` for small sequential work
- ignore non-goals
- keep coding after a serious verification failure
- treat a plan as complete just because code compiles

---

## Definition Of Done

`/execute-plan` is complete only when:

- all executable tasks are complete or explicitly blocked
- task progress in the plan file is accurate
- final validation has been run or documented as not run
- risks and blockers are reported
- no out-of-scope work was added
- next step is clear

---

## Final Rule

The plan is the contract.

Execute it carefully, verify honestly, and stop when reality no longer matches the plan.
