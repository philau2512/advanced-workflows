---
description: ⚡ Disciplined phase-by-phase implementation engine that executes technical plans with incremental verification gates, status mutations, and strict scope confinement.
---

# /cook - Phase-by-Phase Plan Execution & Implementation Mode

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **IMPLEMENTATION MODE** to systematically execute technical plans (both multi-phase directories and standalone plans) with strict phase-by-phase discipline, automated testing gates, and progress tracking.

> **IRON INVARIANTS:**
> 1. **STRICT PHASE SEQUENCING:** Execute ONE phase at a time. NEVER merge multiple phases into a single unverified turn.
> 2. **STRICT SCOPE CONFINEMENT:** Touch ONLY the files enumerated in the active phase's `Affected Files` list. Zero unrelated edits.
> 3. **INCREMENTAL VERIFICATION GATE:** A phase is NOT complete until its specific `Test Command` runs and passes 100%. No moving forward on red tests.
> 4. **DURABLE STATUS MUTATION:** Update the phase file frontmatter (`status: in-progress` $\rightarrow$ `status: completed`) and the master `plan.md` index table upon completion.
> 5. **NATIVE TOOLING ONLY:** NEVER use terminal commands (`echo`, `cat >`, redirection) to edit files. ALWAYS use native IDE editing tools (`replace_file_content`, `write_to_file`).

---

## ⚙️ Argument Parsing & Execution Flags

- `--auto`: Automatically proceed through all pending phases sequentially as long as test gates pass.
- `--phase <N>`: Target and execute ONLY Phase N (e.g. `/cook --phase 2 plans/20260820-user-auth`).
- `--tdd`: Enforce test-first authoring (write failing tests before business logic).
- `--no-test`: Skip automated test execution (surfaces warning in final report).
- `$ARGUMENTS`: Path to a plan directory (`plans/<slug>/`), a specific phase file (`phase-02.md`), or a standalone plan (`implementation_plan.md`).

---

## 🔬 5-Step Execution Protocol

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. RESOLVE TARGET      │ ──► │ 2. READ PHASE SPEC     │ ──► │ 3. IMPLEMENT CODE      │
│    Plan & Active Phase │     │    (Affected Files)    │     │    (Strict Scope Only) │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 5. UPDATE STATUS       │ ◄── │ 4. RUN TEST GATE       │ ◄────────────────────────┘
│    & Next Phase Handoff│     │    (Incremental Check) │
└────────────────────────┘     └────────────────────────┘
```

---

### Step 1: Target Resolution & Plan Discovery

1. **Detect Input Type:**
   - **Plan Directory:** (e.g. `plans/<slug>/`) $\rightarrow$ Read `plan.md` to identify the first phase with `status: pending` (or the phase specified by `--phase <N>`).
   - **Specific Phase File:** (e.g. `plans/<slug>/phase-02-services.md`) $\rightarrow$ Execute this exact phase.
   - **Single Standalone Plan:** (e.g. `implementation_plan.md`) $\rightarrow$ Execute the plan sections in logical order.
2. **If no path is provided:**
   - Check `plans/` for the latest in-progress plan or check for `implementation_plan.md` in workspace root.

---

### Step 2: Read Phase Specification & Context Check

1. **Read the active phase file completely** (e.g. `phase-01-types.md`).
2. **Extract Target Touchpoints:**
   - List of `[NEW]` files to create.
   - List of `[MODIFY]` files to update.
   - Specific contract/signature changes and acceptance criteria.
3. **Mark Status in Progress:**
   - Update frontmatter in the phase file: `status: in-progress`.

---

### Step 3: Implement Code (Strict Scope Confinement)

1. **Follow Step-by-Step Instructions:**
   - Create new files using native `write_to_file`.
   - Modify existing files using native `replace_file_content` / `multi_replace_file_content`.
2. **Implementation Rules:**
   - Keep edits surgical and minimal.
   - Do NOT introduce breaking signature changes to un-migrated consumers.
   - Do NOT edit files outside the current phase's declared scope.
   - If `--tdd` is active, write or update unit test files FIRST before implementing business logic.

---

### Step 4: Incremental Verification Gate

1. **Run the Phase Test Command:**
   - Execute the exact command specified in the phase file (e.g. `npm test -- tests/services/auth.test.ts`, `go test -v ./internal/...`).
2. **Evaluate Results:**
   - **If Tests PASS:** Proceed immediately to Step 5.
   - **If Tests FAIL:**
     - Analyze error output $\rightarrow$ apply minimal targeted fix $\rightarrow$ re-run test.
     - Maximum 3 retry loops. If still failing, STOP and report the blocker.

---

### Step 5: Status Mutation & Next Phase Progression

1. **Update Frontmatter & Master Plan:**
   - In active phase file: update `status: completed`.
   - In master `plan.md`: update phase row in the summary table to `Completed`.
2. **Progression Decision:**
   - **If `--auto` flag is present:** Check for next pending phase $\rightarrow$ Loop back to Step 2 for the next phase.
   - **If `--auto` is NOT present (default interactive):** Present the Phase Summary and prompt the user before proceeding to the next phase.
   - **If all phases are completed:** Run full project test suite and type check $\rightarrow$ Deliver Final Completion Report.

---

## 🛑 Cook Boundaries & Anti-Patterns

- **DO NOT** attempt to implement Phase 2 while Phase 1 tests are still failing.
- **DO NOT** skip updating phase status files upon completion.
- **DO NOT** invent new dependencies or libraries not specified in the plan.
- **DO NOT** use bash/powershell redirection (`>`, `>>`, `echo`) to modify source code.

---

## 📋 Phase Execution Report Template

```markdown
## ⚡ Cook Execution Report: [Plan Name] — Phase [N]: [Phase Title]

### 1. Phase Status: ✅ COMPLETED
- **Target File:** [`plans/<slug>/phase-01-types.md`](file:///path/to/phase-01-types.md)
- **Status Mutation:** `pending` $\rightarrow$ `completed`

### 2. Files Modified & Created
- **[NEW]** [`src/types/feature.ts`](file:///path/to/src/types/feature.ts) — Defined request/response DTOs
- **[MODIFY]** [`src/models/schema.ts`](file:///path/to/src/models/schema.ts) — Added status column

### 3. Verification & Test Gate
- **Test Command:** `npm test -- tests/types/feature.test.ts`
- **Result:** `PASS` (X tests passed, 0 failures)
- **Type Check:** `npm run type-check` $\rightarrow$ `Clean (0 errors)`

---

### 4. Next Step
- ➡️ **Next Pending Phase:** **Phase [N+1]: [Next Title]** (`plans/<slug>/phase-02-services.md`)
- *Run `/cook plans/<slug>/phase-02-services.md` or confirm to proceed.*
```
