---
description: 🚀 Autonomous master orchestrator that chains /brainstorm -> /plan -> /validate-plan -> /cook -> verification in a single seamless run.
---

# /vibe - Autonomous Master Orchestrator Pipeline

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates the **MASTER ORCHESTRATION PIPELINE** to autonomously chain your existing modular workflows (`/brainstorm` $\rightarrow$ `/plan` $\rightarrow$ `/validate-plan` $\rightarrow$ `/cook`) into an uninterrupted, end-to-end delivery stream.

```
┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                                   /vibe Master Pipeline                                     │
├─────────────────┬─────────────────┬───────────────────┬──────────────────┬──────────────────┤
│ 1. /brainstorm  │ 2. /plan        │ 3. /validate-plan │ 4. /cook --auto  │ 5. Verify & Done │
│ Select Solution │ Scaffold Dir    │ Audit Reality     │ Execute Phases   │ Type-Check & E2E │
└─────────────────┴─────────────────┴───────────────────┴──────────────────┴──────────────────┘
```

> **IRON INVARIANTS:**
> 1. **REUSE EXISTING WORKFLOWS (DRY):** Do NOT reinvent logic. Delegate each stage to its dedicated sub-workflow engine (`/brainstorm`, `/plan`, `/validate-plan`, `/cook`).
> 2. **VIBE PREFIX ISOLATION:** All generated plans MUST use the `plans/vibe-<YYYYMMDD>-<slug>/` directory prefix.
> 3. **ZERO-TOUCH CONTINUITY:** Automatically advance from one stage to the next without prompting the user, unless blocked by missing credentials or failing tests.
> 4. **STRICT PRE-FLIGHT AUDIT:** Never run `/cook` without passing the `/validate-plan` check first.

---

## ⚙️ Argument Parsing & Execution Flags

- `--tdd`: Forward `--tdd` to `/plan` and `/cook` to enforce test-first development.
- `--fast`: Runs in condensed mode (quick 1-phase plan, skips multi-file scaffolding).
- `--no-test`: Forward `--no-test` to `/cook` if automated test runners are not available.
- `$ARGUMENTS`: The feature request or user story (e.g. `/vibe "Add VnPAY payment gateway integration"`).

---

## 🔬 5-Stage Orchestration Protocol

---

### Stage 1: Solution Ideation (Delegated to `/brainstorm`)

1. **Execute `/brainstorm` logic internally:**
   - Analyze user request, tech stack, and constraints.
   - Formulate 3 distinct approaches (*Lean*, *Robust*, *Radical*).
   - Automatically select the optimal **Recommended Direction** (e.g. Robust & Lean pattern matching the repository).
2. **Output:** A concrete Bounded Delivery Contract (*Outcome, Constraints, Non-Goals, Acceptance Criteria*).

---

### Stage 2: Reality-Grounded Planning (Delegated to `/plan`)

1. **Execute `/plan` protocol:**
   - Scout repository ground-truth using `fast_context_search` and `grep_search`.
   - Scaffold the multi-phase directory with prefix:
     `plans/vibe-<YYYYMMDD>-<feature-slug>/`
   - Generate master `plan.md` (Architecture, Dependency Graph, Phases Table).
   - Generate individual `phase-01-*.md`, `phase-02-*.md`, `phase-NN-*.md` files.
2. **Output:** A complete, modular plan directory ready for validation.

---

### Stage 3: Pre-Flight Reality Audit (Delegated to `/validate-plan`)

1. **Execute `/validate-plan` check:**
   - Audit all cited file paths, method signatures, and imports against actual files in the repository.
   - Verify zero hallucinated paths or broken assumptions.
2. **Gate:** If validation passes $\rightarrow$ proceed immediately to Stage 4. If discrepancies exist $\rightarrow$ auto-correct phase files before proceeding.

---

### Stage 4: Disciplined Execution (Delegated to `/cook --auto`)

1. **Execute `/cook --auto plans/vibe-<YYYYMMDD>-<slug>/`:**
   - Sequentially process `phase-01-*.md` $\rightarrow$ `phase-02-*.md` $\rightarrow$ `phase-NN-*.md`.
   - For each phase:
     1. Touch ONLY files declared in `Affected Files`.
     2. Run the phase's `Test Command`.
     3. Ensure tests PASS 100%.
     4. Mutate frontmatter `status: completed` and master `plan.md` index.
2. **Output:** 100% of phases marked as completed with passing unit/integration tests.

---

### Stage 5: Final Quality Sweep & Delivery Report

1. **Project-Wide Quality Audit:**
   - Run `npm run type-check`, `tsc --noEmit`, `go test ./...`, or equivalent compiler build check.
   - Verify zero regressions across adjacent modules.
2. **Emit Final Vibe Report:** Output a structured summary with modified files, test results, and usage guide.

---

## 🛑 Stop Conditions & Escalation

Pause autonomous execution and prompt the user ONLY if:
1. Missing private API secrets, payment credentials, or external webhook access.
2. `/validate-plan` or `/cook` encounters an unresolvable conflict after 3 auto-remediation attempts.

---

## 📋 Final Completion Report Template

```markdown
## 🚀 Vibe Delivery Report: [Feature Name]

### 1. Executive Summary
- **Request:** "[User prompt / feature request]"
- **Workflow Pipeline:** `/brainstorm` ➔ `/plan` ➔ `/validate-plan` ➔ `/cook --auto`
- **Plan Directory:** [`plans/vibe-<YYYYMMDD>-<slug>/`](file:///path/to/plans/vibe-<slug>/)
- **Status:** ✅ **ALL PHASES DELIVERED & VERIFIED**

### 2. Executed Phases Summary
| Phase | Title | Files Created / Modified | Test Gate | Status |
| :--- | :--- | :--- | :--- | :--- |
| **Phase 1** | Types & Schemas | `src/types/...` | `PASS` | ✅ Completed |
| **Phase 2** | Service & Domain Logic | `src/services/...` | `PASS` | ✅ Completed |
| **Phase 3** | Endpoints & Integration | `src/routes/...` | `PASS` | ✅ Completed |

### 3. Key Files Changed
- **[NEW]** [`src/services/vnpayService.ts`](file:///path/to/src/services/vnpayService.ts) — Core payment signature and URL generator
- **[NEW]** [`src/routes/payment.ts`](file:///path/to/src/routes/payment.ts) — Payment redirect and IPN webhook handlers
- **[MODIFY]** [`src/app.ts`](file:///path/to/src/app.ts) — Registered payment router

### 4. Quality & Regression Verification
- **Unit / Phase Tests:** `PASS` (X tests passed)
- **Compiler / Type-Check:** `PASS` (0 errors)

---

### 5. How to Test & Next Steps
1. **Environment Variables:**
   ```env
   VNPAY_TMN_CODE=your_tmn_code
   VNPAY_HASH_SECRET=your_hash_secret
   VNPAY_URL=https://sandbox.vnpayment.vn/paymentv2/vpcpay.html
   ```
2. **Verification Command:**
   - Execute: `npm test -- tests/payment.test.ts`
```
