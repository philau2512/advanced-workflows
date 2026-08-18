---
description: 🔍 Deep codebase understanding workflow. Use to investigate architecture, trace behavior, map dependencies, and reduce wrong assumptions before making changes.
---

# /analyze - Codebase Understanding Mode

$ARGUMENTS

## Purpose & Core Rule

Activates ANALYZE mode for deep code comprehension before implementation, refactoring, debugging, or review.

> **ANALYZE = UNDERSTAND FIRST, CHANGE NOTHING UNLESS EXPLICITLY ASKED**

- Trace execution flows and gather empirical evidence.
- Map boundaries, dependencies, and state transitions.
- Identify risks and explicitly state remaining unknowns.
- **Strictly read-only:** Do NOT implement fixes, refactors, or features unless explicitly requested in a subsequent turn.

---

## When to Use

- **Use when:** Unfamiliar codebase, multi-file/multi-layer changes, hard-to-reproduce bugs, mapping architecture/data flow, or preparing for `/enhance`, `/debug`, `/refactor`, `/review`, or `/security`.
- **Do not use:** Obvious single-line or localized edits.

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

## Investigation Depth Levels

| Level | Scope | Deliverables |
| :--- | :--- | :--- |
| **Level 1: Surface Scan** | Quick orientation, file discovery, entry points | Relevant files list, top-level architecture overview, recommended next reads |
| **Level 2: Flow Trace** | Feature understanding, bug tracing, request/response paths | End-to-end execution path, data/control flow, side effects, risk points |
| **Level 3: Deep System Analysis** | Subsystems, large refactors, legacy code, hidden coupling | Full component/domain map, dependency directions, invariants, hotspots, redesign guidance |

---

## Analysis Protocol

### Phase 1: Clarify Scope & Objectives
- Identify target feature, bug, or module. Infer most likely scope if vague and declare assumptions.

### Phase 2: Locate Entry Points
- Identify starting points: routes, UI components, API handlers, CLI commands, workers, event listeners, or exported functions.
- Determine if multiple or fallback entry points exist.

### Phase 3: Map the Execution Chain
- Trace callers, callees, service boundaries, hooks/stores, middleware, database queries, and async jobs.
- Record file, symbol, role, incoming, and outgoing dependencies at each hop.
- **Dynamic & Magic Resolution:** Uncover indirect invocations via Dependency Injection (IoC containers), dynamic reflection, event emitters/subscribers, and GraphQL dynamic schema resolvers.

### Phase 4: Understand Data & State Flow
- Track inputs, validation, transformation, caching, persistence, and UI/API outputs.
- Identify nullable fields, implicit defaults, race conditions, and synchronization issues.

### Phase 5: Identify Constraints & Invariants
- Uncover business rules, auth gates, idempotency guarantees, schema assumptions, and required execution order. (Label inferred rules clearly).

### Phase 6: Surface Risks & Unknowns
- Flag fragile coupling, dead code, duplicated logic, hidden side effects, missing validations/tests, and unproven hypotheses.

### Phase 7: Recommend Next Steps
- Select the next best workflow (`/debug`, `/enhance`, `/refactor`, `/review`, `/security`, `/test`).

---

## Reading Strategy & Evidence Checklist

**Read Order:** Entry points → Immediate callees/imports → Shared types/schemas → State/store modules → Services/data layer → Tests → Config.

**Stop Expanding When:** Execution path, data flow, and side effects are proven, and unread branches are out of scope.

| Area | Key Verification Questions |
| :--- | :--- |
| **Entry Point** | Where does the flow begin? |
| **Ownership** | Which module owns the business logic? |
| **Dependencies** | What does it rely on or call? |
| **State & Data** | What state/schema enters, transforms, and exits? |
| **Side Effects** | DB writes, network calls, cache mutations, logs, emitted events? |
| **Errors & Tests** | How are failures handled? What behavior is covered by tests? |
| **Risks** | What breaks if this area is modified? |

---

## Heuristics by Problem Type

- **Feature Flow:** Focus on UI entry, service calls, API contracts, store updates, and feature flags.
- **Bug Diagnosis:** Focus on reproduction path, input/output drift, swallowed errors, and fallback branches.
- **Refactor Prep:** Focus on module boundaries, duplicated logic, public APIs, and consumers.
- **Architecture:** Focus on layer boundaries, dependency direction, circular coupling, and domain leaks.
- **Performance:** Focus on repeated computation, hot loops, heavy renders, N+1 queries, and cache misses.

---

## Anti-Patterns to Avoid

- Inferring behavior from file names or folder structure alone.
- Stopping at the first plausible file without tracing callers.
- Believing comments without verifying code implementation.
- Recommending edits before mapping dependencies.
- Presenting unverified speculation as established fact.

---

## Output Formats

### Standard Format

```markdown
## 🔬 Analysis Report: [Topic]

### Scope & Executive Summary
- **Target & Depth:** [Target area] | [Level 1 / 2 / 3]
- **Summary:** [Concise paragraph on how this flow currently functions]

### Relevant Files & Roles
| File | Role / Responsibility |
| :--- | :--- |
| `src/...` | [Entry / Logic / State / Persistence] |

### Execution & Data Flow
1. **Entry:** [Trigger point]
2. **Chain:** [Step-by-step caller -> callee sequence]
3. **Data Transformations:** [Input -> Validation -> State/DB -> Output]

### Key Findings & Invariants
- **Observed (Facts):** [Evidence grounded in code]
- **Inferred (Hypotheses):** [Carefully labeled deductions]
- **Invariants:** [Assumptions and rules the code relies on]

### Risks & Unknowns
- **Risks:** [Coupling, race conditions, edge cases]
- **Unknowns:** [What remains unproven without runtime/logs]

### Recommended Next Step
- `/debug` | `/enhance` | `/refactor` | `/test` | `/review` with exact focus area
```

### Short Format

```markdown
## 🔬 Analysis: [Topic]
- **Entry Point:** `path/to/entry`
- **Key Files:** `fileA`, `fileB`
- **Execution Flow:** [Brief 1-paragraph summary]
- **Primary Risk:** [Key vulnerability or coupling]
- **Next Step:** `/workflow [args]`
```

---

## Confidence & Escalation

- **Confidence Labels:** Tag findings as **High** (confirmed by code/tests), **Medium** (strongly supported), or **Low** (plausible inference).
- **Escalate Depth when:** Encountering multiple entry points, dynamic imports, plugin architectures, conflicting tests vs code, or hidden side effects.

---

## Handoff Matrix

| Target Goal | Next Workflow |
| :--- | :--- |
| Fix a diagnosed bug | `/debug` |
| Add new features | `/enhance` |
| Improve structure without behavior change | `/refactor` |
| Add/audit test coverage | `/test` or `/test-audit` |
| Code quality or security audit | `/review` or `/security` |

---

## Definition of Done

1. Relevant entry point and primary execution path mapped.
2. Key files, dependencies, and state transitions documented.
3. Invariants, risks, and unknowns explicitly separated.
4. Concrete next workflow recommended.
