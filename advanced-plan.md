---
description: ☠️ Advanced planning workflow for high-quality execution plans. Use for complex features, risky changes, refactors, security work, performance work, or multi-step delivery where speed still needs structure.
---

# /advance-plan - High-Quality Planning Mode

$ARGUMENTS

---

## Purpose

This command creates a deeper execution plan for work that needs quality, safety, and clear sequencing before implementation.

Use it when `/plan` feels too shallow, but you still want a practical plan that helps ship instead of becoming a long document.

---

## Core Rule

> **ADVANCE PLAN = THINK DEEPLY ENOUGH TO EXECUTE SAFELY, BUT DO NOT OVER-PLAN**

Default behavior:

- create a plan only
- do not write application code
- identify assumptions and risks early
- produce tasks that are small, ordered, and verifiable
- keep the plan actionable enough that implementation can start immediately after approval

---

## When To Use

Use `/advance-plan` for:

- complex new features
- multi-file enhancements
- risky refactors
- large file splitting
- security-sensitive changes
- performance optimization work
- production release preparation
- unfamiliar existing code
- changes touching auth, billing, admin, database, data exports, uploads, or permissions

Do **not** use `/advance-plan` for:

- tiny edits
- typo fixes
- obvious one-file changes
- tasks that only need a quick checklist

For simple planning, use `/plan`.

---

## Relationship To Other Workflows

| Need | Workflow |
|------|----------|
| Quick plan | `/plan` |
| Deep execution plan | `/advance-plan` |
| Understand code first | `/analyze` |
| Build new | `/create` |
| Extend existing behavior | `/enhance` |
| Improve structure only | `/refactor` |
| Split large file | `/split-file` |
| Fix bug | `/debug` |
| Validate behavior | `/test` |
| Check security risk | `/security` |
| Ship | `/deploy` |

If the codebase is unclear, run `/analyze` before or inside `/advance-plan`.

---

## Skill Usage

Use `.agent/skills` only when they materially improve the plan.

Do not add skills for decoration. If no specialized skill is needed, write:

```text
No specialized skill required.
```

### Recommended Skills

| Planning Need | Recommended Skill |
|---------------|-------------------|
| Task breakdown, dependency order, verification criteria | `plan-writing` |
| Code quality, maintainability, refactor safety | `clean-code` |
| New app or full application build | `app-builder` |
| Unclear product direction or multiple possible approaches | `brainstorming` |
| Frontend/UI implementation planning | `frontend-design` or `ui-ux-pro-max` |
| Security-sensitive scope, auth, secrets, permissions, uploads | `vulnerability-scanner` |
| Web app testing, E2E checks, browser validation | `webapp-testing` |
| Performance, Lighthouse, profiling, bundle/runtime cost | `performance-profiling` |

### Skill Selection Rules

- Use the smallest skill set that improves execution quality.
- Prefer `plan-writing` for most advanced plans.
- Add domain skills only when the task touches that domain.
- Do not force every task to use a skill.
- Do not use skill recommendations as a substitute for concrete task verification.

---

## Socratic Gate

Ask only what is needed to avoid a wrong plan.

### Question Limit

- Ask 0 questions if the request is clear enough.
- Ask 1-3 questions for normal ambiguity.
- Ask up to 5 questions only for high-risk work.
- If remaining details are safely inferable, state assumptions and proceed.

### Ask About

- success criteria
- target users or roles
- tech stack constraints
- data/security sensitivity
- deadline or MVP boundary
- integration requirements
- must-not-change behavior

### Do Not Ask About

- details that can be safely inferred
- preferences that do not affect execution
- broad open-ended brainstorming if the user asked for a plan

---

## Plan File Rules

Create one plan file only.

### Naming

Use dynamic task-based naming:

```text
docs/ADVANCE-PLAN-{task-slug}.md
```

Naming rules:

- extract 2-3 key words from the request
- lowercase
- kebab-case
- max 30 characters for the slug
- no special characters except hyphen

Examples:

| Request | Plan File |
|---------|-----------|
| `/advance-plan add admin permissions` | `docs/ADVANCE-PLAN-admin-permissions.md` |
| `/advance-plan refactor checkout flow` | `docs/ADVANCE-PLAN-checkout-refactor.md` |
| `/advance-plan optimize dashboard performance` | `docs/ADVANCE-PLAN-dashboard-performance.md` |
| `/advance-plan secure upload pipeline` | `docs/ADVANCE-PLAN-upload-security.md` |

---

## Plan Depth Rule

The plan should be deep enough to execute, not deep enough to impress.

### Target Size

- small task: 5-7 tasks
- medium task: 8-12 tasks
- large task: split into phases, but keep each phase independently shippable

If the plan needs more than 15 implementation tasks, split it into multiple plans.

---

## Required Plan Structure

Every advanced plan must include these 10 sections.

### 1. Goal

Answer:

- what are we building, fixing, refactoring, securing, or optimizing?
- why does it matter?
- what outcome should exist when this is done?

Good goal:

```text
Add server-enforced admin role permissions for user management so only authorized admins can view, edit, and deactivate users.
```

Bad goal:

```text
Improve admin stuff.
```

### 2. Scope

Define what is included.

Include:

- user-facing behavior
- backend/API behavior
- data/schema behavior
- UI areas
- integrations
- environments affected

Keep scope concrete and bounded.

### 3. Assumptions

List decisions being made because the user did not specify every detail.

Examples:

- existing auth provider remains unchanged
- current database schema can be extended
- no new paid service is introduced
- MVP prioritizes correctness over advanced UI polish

Assumptions must be safe, reasonable, and easy to revise.

### 4. Non-goals

State what this plan intentionally will not do.

Examples:

- no full redesign
- no payment provider migration
- no new role hierarchy beyond admin/user
- no production deploy in this plan
- no large architecture rewrite

Non-goals prevent scope creep.

### 5. Technical Approach

Explain how the work will be done.

Include when relevant:

- architecture direction
- data flow
- API design
- state management
- auth/authz approach
- validation approach
- migration strategy
- compatibility constraints
- dependency choices
- performance or security posture

Keep this practical. Do not write a research paper.

### 6. Task Breakdown

Tasks must be small, ordered, and verifiable.

Each task should include:

- task id
- action
- workflow hint
- agent recommendation
- skill recommendation
- dependencies
- input
- output
- verify

Task format:

```markdown
- [ ] T01: [specific action]
  - Workflow: `/analyze` | `/enhance` | `/refactor` | `/test` | `/security`
  - Agent: `project-planner` | `frontend-specialist` | `backend-specialist` | `security-auditor` | `test-engineer` | none
  - Skill: `plan-writing` | `clean-code` | `app-builder` | `frontend-design` | `vulnerability-scanner` | none
  - Depends on: none | T01 | T02
  - Input: [what this task needs]
  - Output: [what this task produces]
  - Verify: [exact check that proves completion]
```

Rules:

- each task should have one clear outcome
- avoid vague tasks like "clean up code"
- use explicit dependencies only
- mark the critical path when obvious
- keep implementation tasks practical and shippable

### 7. Affected Areas

List likely impacted files, folders, modules, or systems.

Use confidence labels:

- **Confirmed**: directly known from request or code analysis
- **Likely**: strongly expected but not yet verified
- **Unknown**: needs `/analyze`

Include:

- frontend areas
- backend/API areas
- database/schema areas
- config/env areas
- test areas
- docs/deploy areas

### 8. Risks

List realistic risks and mitigations.

Risk format:

```markdown
| Risk | Impact | Mitigation |
|------|--------|------------|
| Missing ownership check | User data exposure | Add server-side authorization tests |
```

Consider:

- behavior regression
- data loss
- auth/authz gaps
- performance regression
- migration failure
- third-party integration failure
- unclear ownership
- insufficient test coverage
- deployment/config risk

### 9. Validation Plan

Define how the work will be verified.

Include only checks relevant to the task.

Possible checks:

- unit tests
- integration tests
- E2E/manual flow
- lint/typecheck/build
- API request check
- database migration check
- security review
- performance baseline
- preview verification

Do not copy every possible check into every plan.

Validation format:

```markdown
- [ ] Run `[command]` → expected: [result]
- [ ] Manual check: [scenario] → expected: [result]
- [ ] Security check: [area] → expected: [risk addressed]
```

### 10. Definition Of Done

Define the completion gate.

Include:

- implementation tasks complete
- validation checks pass or are explicitly documented as not run
- risks addressed or accepted
- no unintended scope added
- user-facing behavior matches goal
- follow-up items documented

Do not mark done just because code was written.

---

## Optional Sections

Add only when useful.

### Milestones

Use when work is large enough to ship in stages.

Example:

```markdown
## Milestones
- M1: Backend permissions enforced
- M2: Admin UI connected
- M3: Tests and security checks complete
```

### Rollback Plan

Use when production, migration, config, auth, billing, or data changes are involved.

Example:

```markdown
## Rollback Plan
- Revert feature flag to disabled
- Revert migration only if no production data depends on it
- Restore previous env config
```

### Open Questions

Use when unresolved details are not blockers.

Example:

```markdown
## Open Questions
- Should deleted users be soft-deleted or deactivated?
```

---

## Anti-Patterns To Avoid

Do not:

- write code during planning
- create generic plans that could apply to any project
- ask too many questions before planning
- hide assumptions
- skip validation criteria
- create 30-task plans for small work
- include irrelevant scripts/checks
- plan a rewrite when a smaller change is enough
- mix unrelated features into one plan
- mark risks without mitigations

---

## Output Format

After creating the plan file, respond:

```markdown
## 🧭 Advanced Plan Created

### File
`docs/ADVANCE-PLAN-{task-slug}.md`

### Summary
- Goal: [one sentence]
- Scope: [one sentence]
- Tasks: [count]
- Highest risk: [one sentence]
- Validation: [main checks]

### Next Step
Use `/enhance`, `/debug`, `/refactor`, `/split-file`, `/optimize`, or `/security` based on the plan.
```

---

## Plan File Template

Use this structure inside the created plan file:

```markdown
# Advance Plan: [Task Name]

## 1. Goal
[Clear outcome and why it matters]

## 2. Scope
[What is included]

## 3. Assumptions
- [Assumption]

## 4. Non-goals
- [Non-goal]

## 5. Technical Approach
[Practical implementation approach]

## 6. Task Breakdown
- [ ] T01: [Specific action]
  - Workflow: `/analyze`
  - Agent: `project-planner`
  - Skill: `plan-writing`
  - Depends on: none
  - Input: [input]
  - Output: [output]
  - Verify: [check]

## 7. Affected Areas
| Area | Confidence | Notes |
|------|------------|-------|
| [path/module] | Confirmed/Likely/Unknown | [why] |

## 8. Risks
| Risk | Impact | Mitigation |
|------|--------|------------|
| [risk] | [impact] | [mitigation] |

## 9. Validation Plan
- [ ] [check] → expected: [result]

## 10. Definition Of Done
- [ ] [completion gate]
```

---

## Definition Of Done For This Workflow

`/advance-plan` is complete only when:

- one advanced plan file exists
- all 10 required sections are present
- tasks are ordered and verifiable
- risks include mitigations
- validation checks are task-specific
- assumptions and non-goals are explicit
- no application code was written

---

## Final Rule

Plan deeply enough that execution is safe.

Keep it short enough that execution actually starts.
