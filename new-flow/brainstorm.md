---
description: 💡 Solution Ideation & Comparative Decision Engine. Explore multi-angle approaches, stress-test trade-offs, or run side-by-side comparative matrices between options.
argument-hint: "[topic or A vs B] [--compare] [--ultra] [--yagni] [--deep] [--diff] [--out]"
---

# /brainstorm - Solution Ideation & Comparison Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **SOLUTION THINKING & COMPARISON MODE** to explore, evaluate, and stress-test multiple viable strategies before detailed design or implementation, or perform deep side-by-side analysis between competing approaches.

> **IRON INVARIANTS:**
> 1. **PURE SOLUTION THINKING:** Focus entirely on conceptual approaches, mechanics, trade-offs, and failure modes. Strictly read-only; no ad-hoc code edits.
> 2. **NO STRAWMAN OPTIONS:** Every presented approach or comparison target must be genuinely viable and evaluated with symmetrical rigor.
> 3. **WORST-CASE STRESS TESTING:** Evaluate each approach on where and how it fails under high load, hostile environments, or edge cases.
> 4. **DECISION-DRIVEN VERDICT:** Always provide a concrete recommendation and bounded delivery contract (Outcome, Constraints, Non-Goals, Acceptance Criteria).

---

## ⚙️ Argument Parsing & Usage Modes

```text
# Standard Ideation Mode:
/brainstorm <problem statement or technical dilemma> [--ultra] [--yagni] [--out]

# Comparative Mode (Comparing 2 options, libraries, files, or architectures):
/brainstorm --compare <Target A> vs <Target B> [--deep] [--diff] [--out]
/brainstorm <Target A> vs <Target B>
```

### Flags:
- `--compare`: Explicitly triggers Side-by-Side Comparison mode between two specific targets.
- `--ultra`: Expands ideation to **5 distinct approaches** (instead of default 3) for high-stakes architectural decisions.
- `--deep`: Performs line-by-line / AST / edge-case deep dive during comparison.
- `--diff`: Highlights semantic and structural diffs between options.
- `--yagni`: Aggressively prunes unnecessary abstractions and speculative requirements.
- `--out`: Saves report to `reports/BRAINSTORM-<YYYYMMDD>-<slug>.md` or `reports/COMPARE-<YYYYMMDD>-<slug>.md`.

---

## 🔬 Mode A: Divergent Ideation Protocol (Default)

Used when seeking viable strategies to solve a problem or build a feature.

```
┌────────────────────────┐     ┌────────────────────────────────┐
│ 1. Problem Framing     │ ──► │ 2. Divergent Ideation          │
│    & Bounded Contract  │     │    (3 Default / 5 with --ultra)│
└────────────────────────┘     └───────────────┬────────────────┘
                                               │
┌────────────────────────┐     ┌───────────────▼────────────────┐
│ 4. Synthesis &         │ ◄── │ 3. Trade-Off Matrix &          │
│    Strategic Verdict   │     │    Worst-Case Failure Testing  │
└────────────────────────┘     └────────────────────────────────┘
```

### Phase 1: Problem Framing & Bounded Contract
- **Target Outcome:** Exact operational or user-visible end-state.
- **Hard Constraints:** Stack boundaries, latency, memory, backward-compatibility, and security.
- **Non-Goals:** Explicitly excluded nice-to-haves and scope-creep items.
- **Acceptance Criteria:** 2-4 testable, observable validation conditions.

### Phase 2: Divergent Ideation Archetypes
1. **Approach 1: Lean / Minimalist Path** (Fastest time-to-value, zero new dependencies, max reuse of primitives).
2. **Approach 2: Robust / Industry-Standard Path** (Canonical pattern, high maintainability, future-proof scalability).
3. **Approach 3: Radical / Alternative Path** (Inverts assumptions, reactive/event-driven, stream vs batch, zero-alloc).
*(If `--ultra` active, add Approach 4: Ecosystem/Library-First & Approach 5: Resilient/Zero-Downtime).*

### Phase 3: Trade-Off Matrix & Red-Team Stress Test
Compare options across: **Complexity, Effort, Maintainability, Reversibility, Worst-Case Failure Mode**.
> *Red-Team Test: Under what exact stress condition or edge case does this approach break first?*

### Phase 4: Strategic Recommendation & Next Step
- State the winning option with concrete justification (*Two-Way Door vs One-Way Door*).
- Recommend handoff workflow: `/analyze` (to inspect existing code) or `/plan` (to design execution phases).

---

## ⚖️ Mode B: Side-by-Side Comparison Protocol (`--compare` or `A vs B`)

Used when deciding between two existing libraries, files, algorithms, or architectural patterns.

```
┌────────────────────────┐     ┌────────────────────────────────┐
│ 1. Scope & Target      │ ──► │ 2. Anatomy Deconstruction      │
│    Alignment (A vs B)  │     │    (Primitives & Mechanics)    │
└────────────────────────┘     └───────────────┬────────────────┘
                                               │
┌────────────────────────┐     ┌───────────────▼────────────────┐
│ 4. Actionable Verdict  │ ◄── │ 3. Multi-Dimension Matrix &    │
│    (Contextual Rule)   │     │    Feature Parity Diff         │
└────────────────────────┘     └────────────────────────────────┘
```

### Phase 1: Target Deconstruction
Extract syntax, operational profile, dependencies, and execution model of Target A and Target B under identical conditions.

### Phase 2: Feature Parity & Dimension Scoring
Construct an empirical comparison matrix:

| Evaluation Dimension | Target A: `[Name A]` | Target B: `[Name B]` | Winner / Advantage |
| :--- | :--- | :--- | :--- |
| **Ergonomics & Readability** | Concise / Verbose | ... | Target A |
| **Performance & Memory** | O(N), alloc profile | ... | Target B |
| **Failure Modes & Safety** | Throw exception / Fallback | ... | Tie |
| **Integration & Migration** | Zero config / Needs wrapper| ... | Target A |

### Phase 3: Actionable Verdict
- **Pick Target A when:** [Specific operational constraints, team skill, scale].
- **Pick Target B when:** [Opposite constraints, extreme throughput, security].
- **Migration / Porting Risk:** Low / Medium / High if switching from one to the other.

---

## 📋 Standard Output Template

```markdown
## 💡 Brainstorm & Decision Report: [Topic / Dilemma]

### 1. Bounded Context
- **Target Outcome:** [End-state]
- **Constraints & Non-Goals:** [Boundaries]
- **Acceptance Criteria:** [Observable conditions]

---

### 2. Solutions Evaluated / Comparison

#### 🔹 Option A / Target A: [Name]
- **Mechanics:** [Underlying mechanics in 2-3 bullets]
- **Strengths:** [Key advantages]
- **Liabilities & Worst Failure:** [Where it breaks under stress]

#### 🔹 Option B / Target B: [Name]
- **Mechanics:** [Underlying mechanics in 2-3 bullets]
- **Strengths:** [Key advantages]
- **Liabilities & Worst Failure:** [Where it breaks under stress]

---

### 3. Trade-Off & Parity Matrix

| Metric | Option A | Option B | Option C (if applicable) |
| :--- | :--- | :--- | :--- |
| **Complexity** | Low/Med/High | ... | ... |
| **Maintainability** | High/Med/Low | ... | ... |
| **Worst-Case Risk**| [Condition] | [Condition] | ... |

---

### 4. Strategic Verdict & Next Steps
- 🏆 **Verdict:** [Winning direction and clear technical justification].
- ➡️ **Next Step:** Transition to `/analyze` or `/plan`.
```
