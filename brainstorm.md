---
description: 💡 Pure solution thinking and ideation workflow to explore, stress-test, and compare 3 (or 5 with --ultra) viable approaches before commitment.
---

# /brainstorm - Solution Ideation & Trade-off Thinking Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **SOLUTION THINKING MODE** to explore, evaluate, and stress-test multiple viable strategies for any problem, feature, or architectural dilemma before any detailed design or implementation begins.

> **IRON INVARIANTS:**
> 1. **PURE SOLUTION THINKING:** Do NOT jump into detailed designs (DB tables, API schemas, UI components) or code edits. Focus entirely on *conceptual approaches, mechanics, trade-offs, and failure modes*.
> 2. **NO STRAWMAN OPTIONS:** Every presented approach must be a genuinely viable, distinct path to solving the problem. No fake or deliberately bad options.
> 3. **WORST-CASE STRESS TESTING:** Evaluate each approach on where and how it fails, not just its best-case promise.
> 4. **BOUNDED CONTRACT:** Ground every brainstorm in a 4-part delivery contract (*Outcome, Constraints, Non-Goals, Acceptance Criteria*).

---

## ⚙️ Argument Parsing & Flags

- `--ultra`: Expands ideation to **5 distinct approaches** (instead of default 3) for high-stakes or complex decisions.
- `--out`: Saves the brainstorm brief to `reports/BRAINSTORM-<YYYYMMDD>-<topic-slug>.md`.
- `--yagni`: Ruthlessly challenges and prunes any unnecessary complexity or speculative requirements.
- `$ARGUMENTS`: The problem statement, feature concept, or technical dilemma to solve.

---

## 🔬 4-Phase Solution Ideation Protocol

```
┌────────────────────────┐     ┌────────────────────────────────┐
│ 1. PROBLEM FRAMING     │ ──► │ 2. DIVERGENT IDEATION          │
│    & Bounded Contract  │     │    (3 Default / 5 with --ultra)│
└────────────────────────┘     └───────────────┬────────────────┘
                                               │
┌────────────────────────┐     ┌───────────────▼────────────────┐
│ 4. SYNTHESIS &         │ ◄── │ 3. TRADE-OFF MATRIX &          │
│    Recommendation      │     │    Failure Mode Stress Test    │
└────────────────────────┘     └────────────────────────────────┘
```

---

### Phase 1: Problem Framing & Bounded Contract

Deconstruct the core challenge and establish the non-negotiable boundaries:

1. **Target Outcome:** What exact user-visible or operational end-state must exist when this problem is solved?
2. **Hard Constraints:** Technical, performance, security, time, and backward-compatibility boundaries.
3. **Non-Goals:** Adjacent work, nice-to-haves, or future features that this solution explicitly will NOT absorb.
4. **Acceptance Criteria:** 2 to 4 observable, testable conditions proving the outcome was reached.

---

### Phase 2: Divergent Solution Ideation

Generate distinct, well-reasoned approaches (**3 by default**, or **5 when `--ultra` is specified**):

#### Default Core Approaches (1 to 3):
1. **Approach 1: The Lean / Minimalist Path**
   - *Philosophy:* Fastest time-to-value, zero/minimal new dependencies, maximum reuse of existing codebase primitives.
   - *Focus:* High leverage with lowest immediate blast radius.
2. **Approach 2: The Robust / Industry-Standard Path**
   - *Philosophy:* Canonical best practice, structured abstraction, high maintainability, future-proof scalability.
   - *Focus:* Clean domain separation and long-term developer experience.
3. **Approach 3: The Radical / Alternative Path**
   - *Philosophy:* Inverts assumptions, uses alternative paradigms (e.g., client-side vs server-side compute, event-driven reactive flow, stream vs batch).
   - *Focus:* Maximum efficiency or architectural elegance by challenging traditional constraints.

#### Extended Approaches (4 & 5 - Activated only with `--ultra`):
4. **Approach 4: The Ecosystem / Managed / Library-First Path**
   - *Philosophy:* Offload problem domain to battle-tested open-source libraries or third-party engines.
   - *Focus:* Minimum custom maintenance burden at the cost of external dependency.
5. **Approach 5: The Resilient / Fault-Tolerant Path**
   - *Philosophy:* Built for hostile environments (offline-first, zero-downtime, eventual consistency, high concurrency).
   - *Focus:* Maximum stability and graceful degradation under failure.

For each approach, concisely articulate:
- **Core Concept:** One-line explanation of the solution model.
- **Execution Mechanism:** How it works under the hood.
- **Primary Strength:** The #1 reason to pick this.
- **Primary Liability:** The #1 cost or compromise incurred.

---

### Phase 3: Trade-Off Matrix & Failure Mode Stress-Testing

Compare all approaches side-by-side using empirical trade-off dimensions:

| Evaluation Dimension | Approach 1 (Lean) | Approach 2 (Robust) | Approach 3 (Radical) | [App 4] | [App 5] |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Complexity** | Low | Medium | Med-High | ... | ... |
| **Time-to-Value** | Immediate | Moderate | Moderate | ... | ... |
| **Maintainability** | Good | Excellent | Requires Context | ... | ... |
| **Reversibility** | Very High | Moderate | Low | ... | ... |
| **Key Assumption** | Existing code suffices | Team adheres to pattern | Environment supports model | ... | ... |
| **Worst-Case Failure Mode** | Tech debt accumulation | Over-engineering overhead | Edge-case breakdown | ... | ... |

#### Red-Team Stress Test:
For each approach, answer the brutal question:
> *"Under what exact stress condition, high load, or edge case does this approach break first?"*

---

### Phase 4: Synthesis & Strategic Recommendation

1. **The Recommended Direction:**
   - Clearly state which approach is recommended and **why it wins over the alternatives**.
   - If load-bearing assumptions remain unverified, prioritize the approach that is **cheapest to abandon or reverse** (*Two-Way Door Decision*).
2. **Rejection Rationale:** State in 1 sentence why each non-recommended approach was dismissed.
3. **Next Steps Handoff:**
   - Transition to `/analyze` or `/architecture` for architectural scoping.
   - Transition to `/plan` for phase-by-phase implementation planning.

---

## 🛑 Brainstorm Boundaries & Guardrails

- **DO NOT** produce database migration files, API request/response JSON schemas, or UI component code.
- **DO NOT** recommend an approach based purely on familiarity if evidence shows it carries severe liabilities.
- **DO NOT** invent extra layers, microservices, or state machines merely to look sophisticated. Apply KISS and YAGNI.

---

## 📋 Output Report Template

```markdown
## 💡 Brainstorm Report: [Topic / Problem Statement]

### 1. Bounded Delivery Contract
- **Outcome:** [Clear, single-sentence target end-state]
- **Constraints:** [Hard boundaries: tech stack, latency, safety, backward compatibility]
- **Non-Goals:** [What we are deliberately NOT doing]
- **Acceptance Criteria:**
  - [ ] [Criterion 1]
  - [ ] [Criterion 2]

---

### 2. Solution Approaches Compared ([3 / 5] Evaluated)

#### 🔹 Option 1: [Approach Name] (Lean / Minimalist)
- **Concept:** [How this solves the problem]
- **Mechanism:** [Underlying mechanics in 2-3 bullets]
- **Pros:** [Key advantages]
- **Cons:** [Key tradeoffs]
- **Worst-Case Failure:** [Where it breaks under stress]

#### 🔹 Option 2: [Approach Name] (Robust / Standard)
- **Concept:** [How this solves the problem]
- **Mechanism:** [Underlying mechanics in 2-3 bullets]
- **Pros:** [Key advantages]
- **Cons:** [Key tradeoffs]
- **Worst-Case Failure:** [Where it breaks under stress]

#### 🔹 Option 3: [Approach Name] (Radical / Alternative)
- **Concept:** [How this solves the problem]
- **Mechanism:** [Underlying mechanics in 2-3 bullets]
- **Pros:** [Key advantages]
- **Cons:** [Key tradeoffs]
- **Worst-Case Failure:** [Where it breaks under stress]

<!-- If --ultra: include Option 4 & Option 5 -->

---

### 3. Decision & Trade-Off Matrix

| Metric / Dimension | Option 1 | Option 2 | Option 3 | [Option 4] | [Option 5] |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Complexity** | [Low/Med/High] | [Low/Med/High] | [Low/Med/High] | ... | ... |
| **Effort to Ship** | [Low/Med/High] | [Low/Med/High] | [Low/Med/High] | ... | ... |
| **Flexibility / Scale** | [Low/Med/High] | [Low/Med/High] | [Low/Med/High] | ... | ... |
| **Reversibility** | [Easy/Hard] | [Easy/Hard] | [Easy/Hard] | ... | ... |

---

### 4. Strategic Recommendation & Next Step

- 🏆 **Recommended Path:** **Option [X]** - [Clear rationale why this is the optimal path].
- 🚫 **Why not others:** [1-sentence reason dismissing the alternative options].
- ➡️ **Recommended Next Workflow:** `/architecture` (for system design) or `/plan` (for execution roadmap).
```
