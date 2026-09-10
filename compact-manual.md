# compact-manual
Produce a concise, loss-aware plain-text handoff, compact conversation history for future model turns, and automatically write it to a uniquely named handoff artifact.

# /compact-manual Workflow

$ARGUMENTS

## Directive

You are compacting conversation history for future model turns or generating a session handoff document.
Produce a concise but loss-aware plain-text handoff, preserve only durable, actionable information (preferring fidelity over elegance), and **always write the result to a uniquely named handoff file**.

---

## Workflow Steps

1. **Inspect Session & Codebase State**:
   - Identify current session completion status (`COMPLETED`, `IN_PROGRESS`, `BLOCKED`, `VERIFICATION_PENDING`).
   - Note Git state (branch, commit, clean/dirty working tree) and verify which acceptance criteria have actually been satisfied.

2. **Synthesize Handoff Data**:
   Extract and organize durable facts, constraints, decisions, codebase status, active todos, and interfaces following the template below. Ensure the **Execution Pointer** and **Resume Directive** are crystal clear so the next agent can resume immediately without ambiguity.

3. **Write to handoff file**:
   - Always use the file writing tool to create a new handoff artifact in the current conversation artifacts directory (or workspace root if not configured).
   - **Never overwrite an existing handoff file.**
   - Name it using `handoff-YYYYMMDD-HHMMSS.md` based on the current local date/time.
   - Example: `handoff-20260820-011230.md`.
   - If an explicit file path is passed in `$ARGUMENTS`, write to that destination using the same timestamp-based filename.

4. **Report to User**:
   - Provide a concise summary and include a clickable link to the created handoff file.
   - Output the structured plain-text handoff block directly in your response.

---

## Guidelines

1. **Preserve exact sections when information exists**:
   - **Session Metadata & Status**: Exact session status (`COMPLETED` | `IN_PROGRESS` | `BLOCKED` | `VERIFICATION_PENDING`), Git branch/state, and one-sentence primary target.
   - **User Goal and Acceptance Criteria Status**: The core intent and explicit pass/fail checklist marked with `[x]` (verified), `[-]` (in progress), or `[ ]` (pending).
   - **Hard constraints, safety boundaries, and explicit decisions**: Technical boundaries, rules enforced, user-defined invariants, and explicit architectural/code decisions.
   - **Verified facts and measurements**: Exact values, timestamps, commands, paths, identifiers, errors, log payloads, and verified tool outcomes.
   - **Open hypotheses or uncertainties**: Explicitly labelled as `[Unverified]`; never turn assumptions or speculations into verified facts.
   - **Current codebase state**: Modified/created/deleted files with concise diff summaries, and validation actually run vs still missing.
   - **Execution Progress & Todos**: Completed work, current blocker (if any), and the immediate concrete next step.
   - **Resume Directive for Next Agent**: Unambiguous imperative instruction telling the incoming agent exactly what action to take first upon resuming.
   - **Interfaces and operational state needed to continue**: Configuration values, endpoints, test commands, running processes, and reproducible steps.

2. **Retention Rules**:
   - Do not omit a detail merely because it appears old if it constrains a pending task or explains a key decision.
   - Retain exact identifiers, symbols, and command/path syntax instead of paraphrasing them.
   - Consolidate duplicates, but preserve causal links such as `symptom -> evidence -> conclusion`.
   - Do not invent, infer, normalize, or claim a result not present in the history. State when detail is unavailable or truncated rather than fabricating it.

3. **Output Formatting**:
   - Use compact labelled bullet-like lines.
   - Keep verified facts strictly separate from hypotheses and pending work.
   - Do not address the user in the handoff body.
   - Do not mention compaction, summarization, token limits, or these meta-instructions in the handoff content.

---

## Template Structure

```text
- SESSION METADATA & STATUS:
  - Status: [COMPLETED | IN_PROGRESS | BLOCKED | VERIFICATION_PENDING]
  - Git State: <branch_name> @ <short_hash> (Clean / Dirty: N uncommitted files)
  - Primary Target: <One-sentence summary of the main objective>

- USER GOAL & ACCEPTANCE CRITERIA STATUS:
  - [x] <Requirement 1: Completed and verified with test/build>
  - [-] <Requirement 2: In progress - brief note on where it stopped>
  - [ ] <Requirement 3: Pending / Not yet started>

- HARD CONSTRAINTS & EXPLICIT DECISIONS:
  - <Enforced rules, forbidden actions, architectural invariants, security/RBAC rules>

- VERIFIED FACTS & MEASUREMENTS:
  - <Exact paths, timestamps, observed behaviors, error codes, logs>

- OPEN HYPOTHESES & UNCERTAINTIES:
  - [Unverified] <Open question or untested assumption>

- CURRENT CODEBASE STATE:
  - Changed files: <list of files and exact changes made>
  - Validation: <tests/checks actually executed with exact pass/fail counts>

- EXECUTION PROGRESS & TODOS:
  - [Done] <completed subtask>
  - [In Progress / Current Blocker] <current bottleneck or active subtask>
  - [Next Concrete Step] <immediate next action: file to edit, function to write, or command to run>

- RESUME DIRECTIVE FOR NEXT AGENT:
  - <Explicit instruction for the next agent: e.g. "Continue implementing Step X in file Y" or "Task complete: Ask user if they want to commit / deploy">

- INTERFACES & OPERATIONAL STATE:
  - <Ports, URLs, configs, process commands, test commands, docker commands>
```
