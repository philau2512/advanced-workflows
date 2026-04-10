---
description: 📘 Quick reference for available workflows in `.agent/workflows & C:\Users\ADMIN\.gemini\antigravity\global_workflows`.
---

# Workflows README

This folder contains practical workflows for building, changing, testing, securing, and shipping software quickly with clear intent.

The goal is simple:

- choose the right workflow fast
- avoid mixing different task types
- keep execution disciplined
- ship quickly without unnecessary process overhead

---

## Quick Start

Use these as the default guide:

- Need ideas before building? → `/brainstorm`
- Need a concrete plan first? → `/plan`
- Need a deeper plan for risky/complex work? → `/advanced-plan`
- Need to execute an existing plan? → `/execute-plan`
- Need to understand existing code? → `/analyze`
- Need to assess or enforce system boundaries/ownership? → `/architecture`
- Building something new? → `/create`
- Extending an existing feature? → `/enhance`
- Extending a complex/risky feature? → `/advanced-enhance`
- Cleaning structure without changing behavior? → `/refactor`
- Splitting a large file safely? → `/split-file`
- Fixing a bug? → `/debug`
- Debugging a hard/intermittent/production bug? → `/advanced-debug`
- Need a senior risk-focused review before shipping? → `/review`
- Adding or running tests? → `/test`
- Improving performance or efficiency? → `/optimize`
- Checking practical security risks? → `/security`
- Running or checking local preview? → `/preview`
- Deploying to staging/production? → `/deploy`
- Checking current status? → `/status`
- Coordinating a large multi-agent task? → `/orchestrate`
- Doing UI/UX-heavy design/build work? → `/ui-ux-pro-max`

---

## Workflow List

### Planning

| Workflow | Use For |
|----------|---------|
| `/brainstorm` | Explore multiple approaches before committing |
| `/plan` | Create a concrete execution plan without coding |
| `/advanced-plan` | Create a deeper plan for complex, risky, or multi-step work |
| `/execute-plan` | Execute an existing plan task-by-task with verification |
| `/analyze` | Understand current code, flow, dependencies, and risks before making changes |
| `/architecture` | Review or improve boundaries, ownership, layers, and dependency direction |

### Build And Change

| Workflow | Use For |
|----------|---------|
| `/create` | Build a new app or feature from scratch |
| `/enhance` | Add or extend behavior in an existing codebase |
| `/advanced-enhance` | Add complex or risky feature behavior with stronger validation |
| `/refactor` | Improve structure without intentionally changing behavior |
| `/split-file` | Split a large file into smaller files safely |

### Quality And Risk

| Workflow | Use For |
|----------|---------|
| `/debug` | Investigate and fix bugs systematically |
| `/advanced-debug` | Investigate hard, intermittent, production, or multi-layer bugs |
| `/review` | Review changes for bugs, regressions, missing tests, and practical risk |
| `/test` | Generate tests, run tests, and check coverage |
| `/optimize` | Improve performance, memory, query, payload, or cost efficiency |
| `/security` | Audit practical code-level and config-level security risks |

### Runtime And Delivery

| Workflow | Use For |
|----------|---------|
| `/preview` | Start, stop, restart, or inspect local preview |
| `/deploy` | Run deploy checks, deploy, verify, or rollback |
| `/status` | Inspect current project, agent, and preview status |

### Coordination And Specialty

| Workflow | Use For |
|----------|---------|
| `/orchestrate` | Coordinate large or multi-domain tasks across multiple agents |
| `/ui-ux-pro-max` | UI/UX design system generation and design-heavy implementation guidance |

---

## Common Flows

### Fast Product Shipping

```text
/brainstorm
→ /plan or /advanced-plan
→ /create, /enhance, or /execute-plan
→ /test
→ /security
→ /preview
→ /deploy
```

### Safe Change In Existing Code

```text
/analyze
→ /architecture (if boundaries are messy)
→ /enhance, /advanced-enhance, /debug, or /advanced-debug
→ /test
→ /security
→ /review
→ /preview
```

### Cleanup Before Bigger Feature Work

```text
/analyze
→ /architecture
→ /refactor or /split-file
→ /test
→ /review
→ /enhance or /advanced-enhance
```

### Planned Execution

```text
/advanced-plan
→ /execute-plan
→ /test
→ /security
→ /review
→ /preview
```

### Performance Hardening

```text
/analyze
→ /optimize
→ /test
→ /preview
```

### Release Readiness

```text
/test
→ /security
→ /optimize (if needed)
→ /preview
→ /deploy
```

---

## Which One To Use

Choose based on the primary goal, not the wording alone.

- If the goal is **understand first**, use `/analyze`
- If the goal is **clarify or enforce architecture**, use `/architecture`
- If the goal is **plan complex work**, use `/advanced-plan`
- If the goal is **execute an existing plan**, use `/execute-plan`
- If the goal is **change behavior**, use `/enhance`
- If the goal is **change complex/risky behavior**, use `/advanced-enhance`
- If the goal is **fix wrong behavior**, use `/debug`
- If the goal is **fix hard or recurring wrong behavior**, use `/advanced-debug`
- If the goal is **keep behavior but improve structure**, use `/refactor`
- If the goal is **audit risk before shipping**, use `/review`
- If the goal is **make it faster or cheaper**, use `/optimize`
- If the goal is **reduce practical security risk**, use `/security`

If a task feels mixed, start with `/analyze`.

---

## Minimal Core Set

If the goal is to ship products fast, the most important workflows are:

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

Everything else is support, specialization, or acceleration.

---

## Rules Of Thumb

- Do not use `/refactor` when the real goal is feature work
- Do not use `/architecture` for tiny code cleanup
- Do not use `/advanced-enhance` for tiny feature tweaks
- Do not use `/advanced-debug` for obvious one-file bugs
- Do not use `/review` as a substitute for `/security` on sensitive changes
- Do not use `/optimize` without an actual performance reason
- Do not skip `/security` before shipping sensitive features
- Do not skip `/test` after risky changes
- Use `/analyze` whenever the codebase or flow is unclear
- Use `/orchestrate` only for genuinely large or multi-domain tasks

---

## Final Note

This workflow set is intentionally practical.

It is designed to help you move fast, stay clear about intent, and avoid mixing planning, debugging, refactoring, optimization, and security into one vague task.
