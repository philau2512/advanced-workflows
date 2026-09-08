---
description: ⚡ Phased Implementation & Autonomous Delivery Engine. Execute technical plans with strict test gates, or run end-to-end autonomous loops with --vibe.
argument-hint: "[plan-path] [--vibe <task>] [--auto] [--phase <N>] [--tdd] [--no-test]"
---

# /cook - Phased Implementation & Delivery Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **IMPLEMENTATION MODE** to systematically execute technical plans with disciplined phase sequencing, automated verification gates, and atomic edits. Can also run end-to-end autonomous delivery via `--vibe`.

> **IRON INVARIANTS:**
> 1. **STRICT PHASE SEQUENCING:** Execute ONE phase at a time. Never mix multiple phases into a single unverified turn.
> 2. **STRICT SCOPE CONFINEMENT:** Touch ONLY the files enumerated in the active phase's affected files list. Zero unrelated refactoring or cleanup.
> 3. **INCREMENTAL VERIFICATION GATE:** A phase is NOT complete until its declared test command passes 100%. Never advance on red tests.
> 4. **DURABLE STATUS TRACKING:** Update phase status (`status: pending -> in-progress -> completed`) in plan documents upon verification.
> 5. **NATIVE TOOLING ONLY:** NEVER use terminal commands (`echo`, `cat >`, redirection) to edit files. ALWAYS use native IDE tools (`replace_file_content`, `write_to_file`).

---

## ⚙️ Argument Parsing & Usage Modes

```text
# Standard Phased Execution (Targeting existing plan):
/cook [path/to/plan.md or plans/slug/] [--auto] [--phase <N>] [--tdd]

# Autonomous Pipeline Mode (End-to-end Idea -> Plan -> Cook -> Verify):
/cook --vibe "<feature description or task>" [--fast] [--tdd]
```

### Flags:
- `--vibe`: Activates the autonomous pipeline: triggers `/brainstorm` -> `/plan` -> validates -> runs `/cook` sequentially.
- `--auto`: Automatically loops through all pending phases in the plan as long as tests pass.
- `--phase <N>`: Executes only Phase N (e.g. `/cook --phase 2 plans/auth/`).
- `--tdd`: Enforces test-first authoring (writes failing characterization tests before editing business logic).
- `--no-test`: Skips automated test execution (must be explicitly declared; surfaces warning).

---

## 🔬 Mode A: Phased Implementation Protocol (Default)

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Resolve Target Plan │ ──► │ 2. Read Phase Spec     │ ──► │ 3. Implement Code      │
│    & Active Phase      │     │    (Target Files Only) │     │    (Atomic Scope)      │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 5. Update Plan Status  │ ◄── │ 4. Run Test Gate       │ ◄────────────────────────┘
│    & Next Phase Handoff│     │    (100% Green Required)│
└────────────────────────┘     └────────────────────────┘
```

### Step 1: Target Resolution
1. Resolve input:
   - If argument is a plan directory (e.g. `plans/<slug>/`) -> Find first `status: pending` phase.
   - If argument is a specific phase file (e.g. `phase-02.md`) -> Execute this exact phase.
   - If argument is empty -> Check workspace for `implementation_plan.md` or latest active plan in `plans/`.
2. Mark active phase as `status: in-progress`.

### Step 2: Implementation & Scope Confinement
- Read the active phase specifications: affected files, new signatures, required imports.
- Make targeted edits using native IDE tools (`replace_file_content`, `write_to_file`).
- Preserve existing code patterns, logging formats, and type safety.

### Step 3: Incremental Verification Gate
- Execute the phase's designated test command (e.g. `npx vitest run tests/unit/order.test.ts`).
- If tests FAIL:
  - Analyze failure output directly.
  - Apply immediate targeted fix within the current phase scope.
  - Re-run test command until green. Do NOT proceed to next phase while tests are red.

### Step 4: Status Mutation & Handoff
- Mark completed phase as `status: completed` in the plan document.
- Report concise summary: files modified, tests verified, next phase in queue.
- If `--auto` is enabled: Automatically advance to the next pending phase.

---

## 🚀 Mode B: Autonomous Pipeline Protocol (`--vibe`)

Chains the entire workflow lifecycle into an autonomous delivery stream:

```
┌─────────────────┬─────────────────┬───────────────────┬──────────────────┬──────────────────┐
│ 1. Brainstorm   │ 2. Plan         │ 3. Validate Gate  │ 4. Cook --auto   │ 5. Verification  │
│ Select Approach │ Scaffold Phases │ Audit Reality     │ Execute Phases   │ Final Typecheck  │
└─────────────────┴─────────────────┴───────────────────┴──────────────────┴──────────────────┘
```

1. **Ideation:** Automatically picks the leanest, most robust approach matching repository conventions.
2. **Plan & Validate:** Generates phased roadmap with file diffs and runs the reality validation gate.
3. **Autonomous Cook:** Iterates through each phase, applying atomic edits and running targeted test gates.
4. **Final Delivery Check:** Runs workspace linter/typecheck to ensure zero regression.

---

## 📋 Standard Execution Summary Template

```markdown
## ⚡ Cook Execution Report: Phase [N] Complete

### 1. Phase Status
- **Plan:** `[path/to/plan.md]`
- **Active Phase:** `Phase [N]: [Phase Name]` -> **COMPLETED**
- **Next Phase:** `Phase [N+1]: [Next Name]` (or `ALL PHASES COMPLETED`)

### 2. Files Modified
- `[NEW/MODIFIED]` `[path/to/file.ts:line]` - [Summary of changes]

### 3. Verification Gate
- **Command:** `[exact test command run]`
- **Result:** Pass (N tests passed, 0 failed).

➡️ **Next Step:** Run `/cook` for Phase [N+1] or proceed to `/test` / `/refactor`.
```
