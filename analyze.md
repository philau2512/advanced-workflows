---
description: 🔍 Deep codebase understanding workflow. Use to investigate architecture, trace behavior, map dependencies, and reduce wrong assumptions before making changes.
---

# /analyze - Codebase Understanding Mode

$ARGUMENTS

---

## Purpose

This command activates ANALYZE mode for deep understanding before implementation, refactoring, debugging, or review.

Use it when the priority is to understand how the system currently works before touching code.

---

## Why This Exists

Many mistakes happen because implementation starts too early:

- Wrong file edited because the real entry point was not identified
- Bug fixed at symptom level while root cause lives elsewhere
- Refactor breaks behavior because hidden dependencies were missed
- Feature work duplicates existing logic already present in another module
- Review feedback is shallow because architecture and data flow were not traced

`/analyze` exists to slow down assumptions and speed up correct decisions.

---

## When To Use

Use `/analyze` when:

- The codebase is unfamiliar
- The task touches multiple files or layers
- You need to understand feature behavior before changing it
- A bug is hard to reproduce or root cause is unclear
- You want to map architecture, module boundaries, or data flow
- You need confidence before using `/enhance`, `/debug`, `/refactor`, `/review`, or `/security`

Do **not** use `/analyze` as the default for tiny edits that are already obvious.

---

## Supported Intents

```text
/analyze
/analyze auth flow
/analyze checkout feature
/analyze why dashboard is slow
/analyze api error handling
/analyze state management
/analyze routing and page entry points
/analyze dependency graph for billing
/analyze before refactor user module
```

---

## Core Rule

> **ANALYZE = UNDERSTAND FIRST, CHANGE NOTHING UNLESS EXPLICITLY ASKED**

Default behavior:

- Read code
- Trace behavior
- Gather evidence
- Summarize findings
- Identify unknowns and risks

Do **not** implement fixes, refactors, or new features unless the user explicitly asks for that as a second step.

---

## Primary Goals

When `/analyze` is triggered, the assistant should answer as many of these as possible:

1. What does this part of the system do?
2. Where does execution begin?
3. Which files, modules, routes, services, and components are involved?
4. How does data move through the system?
5. What state is read, transformed, persisted, or cached?
6. What are the dependencies and side effects?
7. What assumptions does the current code make?
8. What risks exist if we change this area?
9. What is still unknown after investigation?
10. What should be changed next, if anything?

---

## Investigation Depth Levels

Choose the smallest level that fully answers the user request.

### Level 1: Surface Scan

Use for:

- quick orientation
- file discovery
- locating entry points
- identifying relevant modules

Deliverables:

- relevant file list
- top-level architecture guess based on evidence
- next recommended reads

### Level 2: Flow Trace

Use for:

- feature understanding
- bug reproduction tracing
- request/response path mapping
- component/service/store interaction analysis

Deliverables:

- execution path
- data flow
- control flow
- side effects
- likely risk points

### Level 3: Deep System Analysis

Use for:

- large refactors
- architecture validation
- fragile legacy code
- unclear ownership or hidden coupling
- performance or reliability investigation

Deliverables:

- full subsystem map
- module boundaries
- dependency map
- invariants
- hotspots
- refactor or redesign guidance

---

## Analysis Protocol

Follow this order unless evidence shows a better path.

### Phase 1: Clarify Scope

Identify:

- Target feature, bug, module, or question
- Expected outcome of the analysis
- Whether user wants breadth or depth

If the request is vague, infer the most likely scope from the user prompt and state the assumption.

### Phase 2: Find Entry Points

Locate where the target behavior begins:

- route definitions
- page/component entry
- API handlers
- CLI commands
- job schedulers
- event listeners
- exported public functions

Questions to answer:

- What initiates this flow?
- Is there more than one entry point?
- Which path is primary vs fallback?

### Phase 3: Map the Execution Chain

Trace outward from the entry point:

- imports and exports
- function calls
- service boundaries
- hooks and stores
- middleware and interceptors
- database queries
- external APIs
- async jobs

At each step, capture:

- file
- symbol
- role
- incoming dependency
- outgoing dependency

### Phase 4: Understand Data and State

Track:

- inputs
- validation
- transformation
- persistence
- caching
- derived state
- error states
- loading states

Look for:

- schema assumptions
- nullable fields
- implicit defaults
- duplicated transformations
- state synchronization issues

### Phase 5: Identify Constraints and Invariants

Find rules the system appears to rely on:

- authentication required before access
- IDs must exist before update
- feature flags gate behavior
- request must pass validation before persistence
- UI assumes store is hydrated
- retry logic assumes idempotent side effects

If an invariant is only inferred, mark it clearly as an inference.

### Phase 6: Find Risks and Unknowns

List:

- fragile coupling
- dead code suspicion
- duplicated logic
- hidden side effects
- race conditions
- missing validation
- missing tests
- configuration dependence
- environment dependence

Also list what is still not proven.

### Phase 7: Recommend Next Step

Choose the best follow-up workflow:

- `/debug` for root cause isolation and fix
- `/enhance` for adding behavior
- `/refactor` for structure cleanup without behavior change
- `/review` for quality/risk assessment
- `/security` for threat-focused audit
- `/test` for coverage generation or validation

---

## Reading Strategy

Prefer evidence over guesses.

### Read Order

1. Entry points
2. Immediate imports/callees
3. Shared types/interfaces/schemas
4. State/store/context modules
5. Service/repository/data access layer
6. Related tests
7. Config files only if they affect behavior

### For Large Files

If a file is large:

- read the relevant section first
- expand only around referenced symbols
- read full file only when local context is insufficient

### Stop Expanding When

Stop reading deeper when all are true:

- execution path is clear
- data flow is clear
- side effects are identified
- remaining branches are out of scope

---

## Evidence Checklist

During analysis, gather concrete evidence for each area that matters:

| Area | Questions |
|------|-----------|
| Entry Point | Where does the flow begin? |
| Ownership | Which module owns the logic? |
| Dependencies | What does it call or rely on? |
| State | What state is read/written? |
| Data | What shape enters and exits? |
| Side Effects | DB, network, storage, logs, cache, events? |
| Errors | How are failures handled? |
| Tests | What behavior is already covered? |
| Config | What flags/env/settings change behavior? |
| Risk | What is easy to break? |

---

## Heuristics By Problem Type

### Feature Understanding

Focus on:

- route/component entry
- UI to service flow
- API contracts
- store/state ownership
- feature flags
- related tests

### Bug Investigation Prep

Focus on:

- reproduction path
- recent change surface
- input/output mismatch
- silent failure points
- error swallowing
- retry or fallback behavior

### Refactor Prep

Focus on:

- module responsibilities
- duplicated logic
- layering violations
- public API surfaces
- hidden consumers
- behavior-preserving boundaries

### Architecture Analysis

Focus on:

- layer boundaries
- dependency direction
- domain ownership
- circular coupling
- shared utilities abuse
- policy vs implementation separation

### Performance Analysis Prep

Focus on:

- repeated work
- heavy renders
- unnecessary queries
- N+1 patterns
- cache misses
- synchronous bottlenecks

---

## Anti-Patterns To Avoid

Do not:

- assume behavior from file names alone
- stop after finding the first plausible file
- treat comments as proof without checking implementation
- infer architecture from folder structure only
- recommend changes before tracing dependencies
- confuse usage sites with ownership sites
- ignore tests when trying to understand intended behavior
- present speculation as fact

---

## Output Format

```markdown
## 🔬 Analysis Report: [Topic]

### Scope
- Target: [feature/module/question]
- Depth: Surface Scan | Flow Trace | Deep System Analysis
- Goal: [what this analysis is trying to answer]

### Executive Summary
[1 short paragraph explaining how this part currently works]

### Relevant Files
| File | Role |
|------|------|
| `src/...` | Entry point |
| `src/...` | Business logic |
| `src/...` | Persistence |

### Execution Flow
1. [Where flow starts]
2. [What calls what]
3. [Where state/data changes]
4. [Where result is returned or rendered]

### Data / State Flow
- Input: [source]
- Validation: [where/how]
- Transformation: [where/how]
- Persistence or side effect: [db/api/cache/log/event]
- Output: [UI/API/result]

### Key Findings
1. **Observed:** [fact grounded in code]
2. **Observed:** [fact grounded in code]
3. **Inferred:** [carefully labeled inference]

### Constraints / Invariants
- [rule the system appears to rely on]
- [another rule]

### Risks
- [fragile dependency or change risk]
- [hidden coupling or missing coverage]

### Unknowns
- [what still cannot be proven from current evidence]

### Recommended Next Step
- Use `/debug` to isolate root cause in [area]
- Use `/refactor` to separate [concern] from [concern]
- Use `/test` to add coverage around [behavior]
```

---

## Short Output Format

For small requests, use a condensed version:

```markdown
## 🔬 Analysis: [Topic]

- Entry point: `...`
- Main files: `...`, `...`, `...`
- Current flow: [one paragraph]
- Main risk: [one line]
- Next best step: `/debug` | `/enhance` | `/refactor` | `/test`
```

---

## Confidence Labels

When useful, label conclusions with confidence:

- **High confidence**: directly confirmed by code path or test
- **Medium confidence**: strongly supported but not fully proven end-to-end
- **Low confidence**: plausible inference requiring more evidence

Never hide uncertainty when tracing incomplete or dynamic flows.

---

## Escalation Rules

Escalate depth if any of these appear:

- multiple entry points for the same behavior
- shared utility with many callers
- dynamic imports or plugin loading
- heavy config-driven behavior
- inconsistent naming across layers
- test behavior contradicts implementation
- side effects hidden behind wrappers
- legacy code with unclear ownership

If confidence stays low after reasonable reading, stop and report unknowns instead of pretending certainty.

---

## Handoff Matrix

After `/analyze`, choose the next workflow intentionally:

| Next Need | Workflow |
|-----------|----------|
| Fix a proven problem | `/debug` |
| Add capability | `/enhance` |
| Build from scratch | `/create` |
| Improve structure only | `/refactor` |
| Validate correctness | `/test` |
| Audit quality and risks | `/review` |
| Investigate attack surface | `/security` |
| Prepare multi-agent execution | `/orchestrate` |

---

## Example Prompts

```text
/analyze authentication flow from login form to session storage
/analyze why invoice status can become inconsistent
/analyze checkout architecture before refactor
/analyze dashboard data loading and caching
/analyze how notifications are triggered
/analyze admin permission model
/analyze upload pipeline and failure points
```

---

## Definition Of Done

`/analyze` is complete only when:

- relevant entry point is identified
- main execution path is traced
- key files are mapped
- data or state flow is described
- risks and unknowns are listed separately
- speculation is labeled as inference
- a sensible next workflow is recommended

If these are not complete, analysis is not done yet.

---

## Final Rule

Be rigorous, not noisy.

The goal is not to read the entire repository.

The goal is to understand the target area deeply enough that the next action is based on evidence, not intuition.
