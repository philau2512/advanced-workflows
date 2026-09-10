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
> 4. **DEADLOCK PROTECTION (MAX 3 ATTEMPTS):** If a test gate fails, auto-retry targeted fixes at most 3 times. If still failing, STOP and report the blocker to the user. Never loop indefinitely.
> 5. **DURABLE STATUS TRACKING:** Mutate phase status (`status: pending -> in-progress -> completed`) in both the phase file and the master `plan.md` index upon verification.
> 6. **NATIVE TOOLING ONLY:** NEVER use terminal commands (`echo`, `cat >`, redirection) to edit files. ALWAYS use native IDE tools (`replace_file_content`, `write_to_file`).

---

## ⚙️ Argument Parsing & Usage Modes

```text
# Standard Phased Execution (Targeting existing plan):
/cook [path/to/plan.md or plans/slug/] [--auto] [--phase <N>] [--tdd]

# Autonomous Pipeline Mode (End-to-end Idea -> Plan -> Cook -> Verify):
/cook --vibe "<feature description or task>" [--fast] [--tdd]
```

### Flags:
- `--vibe`: Activates the autonomous pipeline: triggers `/brainstorm` -> `/plan` -> pre-flight validate -> runs `/cook --auto` sequentially.
- `--auto`: Automatically loops through all pending phases in the plan as long as tests pass.
- `--phase <N>`: Executes only Phase N (e.g. `/cook --phase 2 plans/20260910-user-auth/`).
- `--tdd`: Enforces test-first authoring (writes failing characterization tests before editing business logic).
- `--no-test`: Skips automated test execution (must be explicitly declared; surfaces warning in report).

---

## 🔬 Mode A: Phased Implementation Protocol (Default)

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Resolve Target Plan │ ──► │ 2. Read Phase Spec     │ ──► │ 3. Implement Code      │
│    & Pre-Flight Check  │     │    (Target Files Only) │     │    (Atomic Scope)      │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 5. Update Plan Status  │ ◄── │ 4. Run Test Gate       │ ◄────────────────────────┘
│    & Next Phase Handoff│     │    (100% Green Required)│
└────────────────────────┘     └────────────────────────┘
```

### Step 1: Target Resolution & Pre-Flight Check
1. Resolve input:
   - If argument is a plan directory (e.g. `plans/<slug>/`) -> Find the first phase with `status: pending`.
   - If argument is a specific phase file (e.g. `phase-02.md`) -> Target this exact phase.
   - If argument is empty -> Check workspace for `implementation_plan.md` or latest active plan in `plans/`.
2. **Pre-Flight Sanity Gate:** Verify that all target files in the phase exist or are explicitly tagged as `[NEW]`.
3. Mark active phase as `status: in-progress` in frontmatter and master `plan.md`.

### Step 2: Implementation & Scope Confinement
- Read the active phase specifications: affected files, new signatures, required imports.
- Make targeted edits using native IDE tools (`replace_file_content`, `write_to_file`).
- Preserve existing code patterns, logging formats, and strong typing. Do not touch out-of-scope files.

### Step 3: Incremental Verification Gate & Deadlock Guard
- Execute the phase's designated test command (e.g. `npx vitest run tests/unit/order.test.ts`).
- If tests **FAIL**:
  1. Analyze the exact assertion failure.
  2. Apply immediate targeted fix within the current phase scope.
  3. Re-run test command.
  4. **Loop Protection:** Limit to **maximum 3 fix attempts**. If tests remain red after attempt 3, halt execution, preserve modified files, and prompt the user with diagnostic logs.
- Never advance to the next phase while tests are red.

### Step 4: Status Mutation & Next Phase Handoff
- Mark completed phase as `status: completed` in the phase file frontmatter and master `plan.md` table.
- Report concise summary: files modified, test results, next pending phase.
- If `--auto` is enabled: Advance directly to the next pending phase.

---

## 🚀 Mode B: Autonomous Pipeline Protocol (`--vibe`)

Chains the full delivery lifecycle into an autonomous stream with strict safety gates:

```
┌─────────────────┬─────────────────┬───────────────────┬──────────────────┬──────────────────┐
│ 1. Brainstorm   │ 2. Plan         │ 3. Pre-Flight Gate│ 4. Cook --auto   │ 5. Delivery Check│
│ Select Approach │ Scaffold Dir    │ Reality Validation│ Execute Phases   │ Final Typecheck  │
└─────────────────┴─────────────────┴───────────────────┴──────────────────┴──────────────────┘
```

1. **Ideation:** Automatically evaluates and selects the optimal solution direction.
2. **Scaffold with Prefix Isolation:** All plans generated by `--vibe` must use the prefix:
   `plans/vibe-<YYYYMMDD>-<slug>/`
3. **Pre-Flight Validation Gate:** Automatically executes `/plan --validate` to verify all referenced symbols, imports, and caller impacts before writing implementation code.
4. **Autonomous Execution:** Runs `/cook --auto` through all phases, enforcing incremental test gates and deadlock protection.
5. **Final Delivery Check:** Runs full workspace typecheck (`tsc --noEmit`) to verify zero regressions.

---

## 📋 Standard Execution Summary Template

```markdown
## ⚡ Cook Execution Report: Phase [N] Complete

### 1. Phase Status
- **Plan:** `[path/to/plan.md]`
- **Active Phase:** `Phase [N]: [Phase Name]` -> **COMPLETED**
- **Next Phase:** `Phase [N+1]: [Next Name]` (or `ALL PHASES COMPLETED`)

---

### 2. Files Modified
- `[NEW/MODIFIED]` `[path/to/file.ts:line]` - [Summary of applied change]

---

### 3. Verification Gate
- **Command:** `[exact test command run]`
- **Result:** **PASS (All N tests green, 0 errors)**
- **Fix Attempts Required:** 0 / 3

➡️ **Next Step:** Run `/cook` for Phase [N+1] or proceed to `/test` / `/refactor`.
```
