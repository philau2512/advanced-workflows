---
description: 🐞 Precision Root-Cause Debugging Engine. Clarify ambiguous bugs with intake interviews, trace backward from errors, eliminate hypotheses, and apply minimal regression-proof fixes.
argument-hint: "[issue or stacktrace] [--bisect] [--clean] [--out]"
---

# /debug - Precision Root-Cause Debugging Engine

$ARGUMENTS

---

## 🎯 Core Rules & Invariants

Activates **ROOT-CAUSE DEBUGGING MODE** to systematically isolate, diagnose, and permanently eradicate software bugs without symptom-masking.

> **IRON INVARIANTS:**
> 1. **ZERO GUESSWORK & NO BLIND PATCHING:** If the bug report lacks repro steps or payload, interview the user before touching code.
> 2. **NO SYMPTOM MASKING:** Never swallow errors with empty `catch {}`, defensive `?.`, or fallback `|| null`. Fix the root trigger at the origin of corrupt data.
> 3. **REPRODUCTION BEFORE EDIT:** Establish a failing test, minimal repro script, or reproducible command before writing any implementation fix.
> 4. **MINIMAL BLAST RADIUS:** Apply the smallest safe patch that permanently resolves the root defect without modifying public contracts or introducing regressions.
> 5. **CLEAN INSTRUMENTATION:** All temporary diagnostic logging injected during investigation must be completely removed before declaring completion.
> 6. **READ-ONLY DATA ACCESS:** Database/cache inspection is strictly read-only, local-only, and engine-enforced. Never mutate data, schema, or migrations. Data repair is *proposed*, never executed.
> 7. **STOP-LOSS:** After 3 eliminated hypotheses or 3 failed fix attempts, STOP patching. Re-examine architectural assumptions and report findings to the user.

---

## ⚙️ Argument Parsing & Execution Flags

- `--bisect`: Runs automated git bisect / commit binary search to identify the exact regression commit.
- `--clean`: Purges build and bundler caches (`node_modules/.cache`, `.next`, `.turbo`, `dist`) before diagnosing.
- `--out`: Saves full diagnostic report to `reports/DEBUG-<YYYYMMDD>-<issue-name>.md`.
- `$ARGUMENTS`: Error message, stack trace, failing endpoint, test failure, or log snippet.

---

## 🔬 6-Phase Precision Debugging Protocol

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Intake & Clarify    │ ──► │ 2. 5-Tier Reproduction │ ──► │ 3. Backward Tracing    │
│    (3 Questions Gate)  │     │    (Failing Test/Gate) │     │    (Crash -> Origin)   │
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
┌────────────────────────┐     ┌────────────────────────┐                 ▼
│ 6. Verify & Guard      │ ◄── │ 5. Minimal Root Fix    │ ◄── ┌────────────────────────┐
│    (Regression Check)  │     │    (Add Regression Test│     │ 4. Hypothesis Tree     │
└────────────────────────┘     └────────────────────────┘     │    & Elimination Matrix│
                                                              └────────────────────────┘
```

> *Phase 3.5 (Read-Only Data Forensics) automatically and proactively runs between Phase 3 and Phase 4 whenever the bug touches persisted state, data integrity, or backend/ORM logic.*

> **Evidence Log (all phases):** Record every command/query executed, its key output, and which hypothesis it supports or eliminates. This log feeds the final report.

### Phase 1: Intake Clarification & Error Classification

#### 1. Ambiguity Detection & User Interview Gate
If the bug report is vague (*"payment failed"*, *"button doesn't work"*, *"500 error"*):
**STOP. Ask the 3 Diagnostic Questions:**
1. **Trigger Action:** *"What exact sequence of actions or inputs did you perform right before the error appeared?"*
2. **Context & Environment:** *"Under what specific conditions did this occur? (User role/auth, local vs prod, browser/OS, specific payload?)"*
3. **Exact Observed Symptom:** *"What exact error message, toast, HTTP status code in Network tab, or console error was logged?"*
*(Skip interview only if exact stack traces or clear repro steps are already provided).*

#### 2. Classify into the 8 Error Categories:

| Error Category | Typical Symptoms | Root Investigation Angle |
| :--- | :--- | :--- |
| **Type 1: Runtime / Crash / Syntax** | Uncaught exceptions, `TypeError: cannot read property of undefined`, panic | Trace call stack to null/undefined dereference point. |
| **Type 2: Logic / State Desync** | Incorrect calculations, stale closures, desynchronized UI/store | Trace reactive dependency arrays and mutation lifecycles. |
| **Type 3: Async / Concurrency / Race** | Out-of-order responses, TOCTOU, double-spend/duplicate record, unhandled rejections | Simulate concurrent calls via `Promise.all()`; trace mutex locks, DB row locks, and network waterfalls. |
| **Type 4: Auth / Session / CORS / SSR** | Token expiry, cookie flags (`SameSite`/`Secure`), hydration mismatch | Trace token refresh loops, CORS preflight headers, SSR/client delta. |
| **Type 5: DB / ORM / Data Drift** | Deadlocks, dirty reads, missing schema migrations, corrupt rows | Trace query execution plan, transaction locks, and schema version. |
| **Type 6: Build & Env Drift** | Works locally but fails in CI/Prod, Timezone UTC vs local, OS path `\` vs `/`, case-sensitive filenames | Verify Timezone offsets, filesystem case-sensitivity, path normalizers (`path.join`), and `.env` variable completeness. |
| **Type 7: Circular Dependency** | Runtime `[Function] is undefined`, empty exports at startup | Trace module import graph for cyclic imports (`A -> B -> A`). |
| **Type 8: Heisenbug / Stale Cache** | Inconsistent failures, ghost HMR state, stale Redis/cache keys | Execute `--clean` cache purge; verify cache TTL and invalidation keys. |

---

### Phase 2: Mandatory 5-Tier Reproduction Gate

Do NOT modify implementation code until reproduction is proven:
- **Tier 1 (Automated Test - Preferred):** Write an isolated failing test (`RED`) in the test suite (e.g. `tests/regression/bug.test.ts`).
- **Tier 2 (Script / Curl):** Prepare a minimal standalone script in the system temp directory or an exact curl command reproducing the error.
  - *Concurrency Repro:* For race conditions, trigger parallel bursts using `Promise.all([request(), request()])` or `ab -n 10 -c 5`.
- **Tier 3 (UI Flow):** Use browser automation tools (`agent-browser` or `chrome-profile`) to capture network traces and console logs.
- **Tier 4 (Ghost Bug Cache Purge):** If non-reproducible, run `--clean` to eliminate stale bundler artifacts and verify if the issue persists.
  - *Flaky Repro:* Run the repro N times (e.g. 20) and record the failure rate (e.g. `7/20`). A fix is only verified when the rate drops to `0/N`.
- **Tier 5 (Git Regression):** If the defect is recent, use `git log -p -n 10` or `--bisect` to identify the regression commit.
- **Known-Good vs Known-Bad Diff:** When a case works and a similar case fails, diff them side-by-side (env vars, payload, user role, DB row, config, version). The delta is the strongest lead.

---

### Phase 3: Backward Tracing & Dynamic Instrumentation

Trace backward from crash site to data origin:
`Crash / Failure Site <- Data Mutation <- Storage / Service Layer <- Entry Point`

- **Logs-First (before touching code):** Read existing evidence first: app/server logs, `docker logs <container> --tail 200`, DB logs (slow query, deadlock, constraint violation), browser console & network.
- **Frontend Flow:** Use `/analyze --fe <component>` to trace `User Interaction -> State Hooks -> API Call`.
- **Backend Flow:** Use `/analyze --be <endpoint>` to trace `Route Handler -> Guard/Middleware -> Domain Service -> DB Query`.
- **Dynamic Instrumentation (only if logs are insufficient):** Inject temporary structured logs:
  `console.log('[DEBUG-TRACE]', { step: 'validation', payload: data, ts: Date.now() })`
  *(Must be completely removed before completion).*

---

### Phase 3.5: Read-Only Data Forensics (Autonomous Gate)

**Proactively & automatically triggered** whenever:
- The defect relates to database, ORM, caching, or backend services (especially Type 5).
- Code logic appears correct but runtime output/validation fails (suspected data drift or corrupted records).
- Reproduction involves persisted records (users, orders, settings, foreign keys).
*(Skip only for purely client-side/UI layout bugs with zero backend or storage footprint).*

#### 1. Safety Gates (ALL mandatory before the first query)
- **Host Guard:** Resolve the connection from `.env`, `docker-compose.yml`, or ORM config (`schema.prisma`, `ormconfig`, `settings.py`). Allowed targets ONLY: `localhost`, `127.0.0.1`, `::1`, a local docker-compose service, or a local SQLite file. Anything else (staging, prod, cloud hostnames) -> **STOP and ask the user.**
- **Credential Hygiene:** Never print `DATABASE_URL`, passwords, or connection strings in output, logs, or reports. Reference them via env vars.
- **Engine-Enforced Read-Only:** Open every session in read-only mode so the DB itself rejects writes:

| Engine | Read-Only Session Setup |
| :--- | :--- |
| **PostgreSQL** | `PGOPTIONS="-c default_transaction_read_only=on -c statement_timeout=5000"` before `psql`; or wrap in `BEGIN READ ONLY; ... ROLLBACK;` |
| **MySQL / MariaDB** | `SET SESSION TRANSACTION READ ONLY; SET SESSION MAX_EXECUTION_TIME=5000;` before querying |
| **SQLite** | `sqlite3 -readonly <file>` or URI `file:<file>?mode=ro` |
| **MongoDB** | Only `find`, `findOne`, `aggregate` (no `$out`/`$merge`), `countDocuments`, `getIndexes` |
| **Redis** | Only `GET`, `MGET`, `HGETALL`, `TTL`, `TYPE`, `SCAN` (never `KEYS *`, `FLUSH*`, `DEL`, `SET`) |

- **Preferred:** If the project has a dedicated read-only DB user (`GRANT SELECT` only) or a read-only DB MCP server, use it over session flags.
- **Sandbox Note:** Local DB connections may require network access outside the sandbox; request user approval for that single query command only.

#### 2. Query Rules
- **Allowed:** `SELECT`, `SHOW`, `DESCRIBE`, `EXPLAIN` (plain), schema introspection (`\d <table>`, `information_schema`, `PRAGMA table_info`).
- **Forbidden (even inside SELECT):** `INSERT`/`UPDATE`/`DELETE`/`MERGE`/`UPSERT`, any DDL, `TRUNCATE`, `EXPLAIN ANALYZE` (executes the statement), `SELECT ... INTO`, CTEs containing DML (`WITH x AS (DELETE ... RETURNING *)`), `COPY ... TO/FROM`, side-effect functions (`nextval`, `setval`, `pg_terminate_backend`, `pg_cancel_backend`), `SET ... read_only = off`, `LOCK`, `GRANT`/`REVOKE`.
- **One statement per invocation.** No `;`-chained statements.
- **Always bounded:** `LIMIT <= 100`; prefer `COUNT(*)`/`GROUP BY` aggregates; filter by primary key or narrow `WHERE` from the repro entity.

#### 3. Forensics Sequence
1. **Schema & Migration Drift:** Check migration status (`prisma migrate status`, `alembic current`, `rails db:migrate:status`, etc.) and compare live columns/constraints with the ORM model.
2. **Targeted Row Inspection:** Query the exact entity from the repro (by ID) and the related rows it joins.
3. **Invariant Checks:** Look for orphaned FKs, unexpected duplicates, `NULL` in logically required fields, out-of-range enums, timezone-shifted timestamps, encoding/special-character issues.
4. **Good vs Bad Row Diff:** Compare a working entity with the failing one column by column.

#### 4. PII & Secret Redaction
Before displaying any result, mask sensitive values: emails (`j***@domain.com`), phone numbers, tokens, API keys, password hashes, payment data. Select only the columns needed for the hypothesis — never `SELECT *` on user/auth tables.

---

### Phase 4: Hypothesis Tree & Elimination Matrix

Formulate **2 to 3 specific hypotheses** and validate against empirical code evidence:

| # | Hypothesis (Suspected Cause) | Supporting / Conflicting Evidence | Validation Method | Result |
| :--- | :--- | :--- | :--- | :--- |
| **H1** | Circular import between Module A & B | AST import graph shows cyclic dependency | Extract shared types into dedicated file | ❌ / ✅ |
| **H2** | Schema allows `undefined` optional field | Schema lacks `.min(1)` check on DTO | Run test with partial payload | ❌ / ✅ |
| **H3** | Race condition in async token refresh | Interceptor timestamp shows out-of-order request | Add mutex queue to refresh handler | ❌ / ✅ |

> **Root Cause Acceptance Criteria:** The verified hypothesis must explain:
> 1. Exact mechanism of failure.
> 2. Why it manifests under specific observed conditions.
> 3. Why the proposed fix permanently eradicates it without side effects.

> **Stop-Loss Gate:** If 3 hypotheses are eliminated without a confirmed root cause, or 3 fix attempts fail, STOP. Summarize the Evidence Log, list the remaining unknowns, question architectural assumptions, and ask the user before continuing.

---

### Phase 5: Minimal Targeted Fix & Regression Test

1. Apply fix directly at the origin of the invalid data, NOT at downstream display sites.
2. Maintain all existing public API contracts, return types, and interface signatures.
3. Remove all temporary instrumentation logs injected during Phase 3.
4. **Mandatory Regression Test:** Add a permanent automated test case covering this specific defect to prevent future regressions.
5. **Data Repair Is Separate:** A code fix does not repair rows already corrupted. If Phase 3.5 found bad persisted data, write a **proposed** repair/backfill script (idempotent, scoped by `WHERE`, with a pre-check `SELECT COUNT(*)` of affected rows) and hand it to the user. **Never execute it.**

---

### Phase 6: Verification & Multi-Surface Guard

- Re-run reproduction test: Verify it turns from **RED -> GREEN** (for flaky bugs: failure rate `0/N`).
- Run existing related test suites to verify zero regression.
- Execute workspace typecheck (`tsc --noEmit` or language equivalent) to verify clean compilation.
- **Instrumentation Cleanup Check:** Search the workspace for `[DEBUG-TRACE]` (e.g. `rg "\[DEBUG-TRACE\]"`). Must return **0 matches**.

---

## 📋 Standard Debug Report Template

```markdown
## 🐞 Debug Diagnostic Report: [Bug Title]

### 1. Defect Summary
- **Symptom:** [User-visible symptom or failing response]
- **Category:** [Type 1 to Type 8 Error Classification]
- **Reproduction:** `[Failing test path or curl command used]`

---

### 2. Root Cause Analysis
- **Failure Point:** `[file.ts:line]`
- **Originating Trigger:** `[file.ts:line]` (where bad state or corrupt payload was introduced)
- **Mechanics:** [Technical explanation of why the defect occurred]

---

### 2.5 Data Evidence *(if Phase 3.5 ran)*
- **Target DB:** [engine] @ local (credentials redacted)
- **Read-Only Mode:** [session flag / read-only user / MCP]
- **Key Findings:** [schema drift, corrupt rows count, invariant violations — PII masked]

---

### 3. Hypothesis Elimination Matrix
- **H1:** [Hypothesis] -> *Eliminated by [Evidence]*
- **H2 (Root Cause):** [Hypothesis] -> *Confirmed by [Evidence]*

---

### 4. Applied Permanent Fix
- **Modified File:** `[path/to/file.ts:line]`
- **Changes Applied:** [Summary of structural root fix]
- **Regression Test Added:** `[tests/regression/issue-xxx.test.ts]`
- **Instrumentation Cleaned:** [x] All temporary logs removed (`[DEBUG-TRACE]` search -> 0 matches)
- **Data Repair (Proposed, NOT executed):** `[path/to/repair-script.sql]` — affected rows: [N] *(omit if none)*

---

### 5. Verification Gate
- **Reproduction Test:** **PASS (Green)**
- **Related Test Suite:** **PASS (Zero regressions)**
- **Type Check:** `tsc --noEmit` -> **CLEAN (0 errors)**

---

### 6. Evidence Log
| # | Command / Query | Key Output | Hypothesis Impact |
| :--- | :--- | :--- | :--- |
| 1 | [command] | [result summary] | [H1 supported / eliminated] |
```
