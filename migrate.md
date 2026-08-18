---
description: 🔄 Safe migration, major version upgrade, and framework/library modernization workflow with zero regression and codemod verification.
---

# /migrate - Framework & Dependency Migration Mode

$ARGUMENTS

## Purpose & Core Rule

Activates MIGRATE mode for upgrading major dependencies, adopting new framework conventions, or transitioning libraries without breaking existing behavior.

> **MIGRATE = AUDIT BREAKING CHANGES, MODERNIZE IN STAGES, VERIFY PARITY AT EACH STEP**

- Audit changelogs, peer dependency conflicts, and deprecated APIs before updating packages.
- Apply codemods or atomic AST/syntax replacements; preserve business logic and public contracts.
- Isolate migration diffs from functional refactoring or new feature development.

---

## When to Use

- **Use when:** Major framework upgrades (e.g. Next.js Pages to App Router, React 18 to 19, Vue 2 to 3), build tool transitions (Webpack to Vite/Turbopack), ORM migrations (Prisma to Drizzle/Kysely), or module system changes (CommonJS to ESM).
- **Do not use:** Minor/patch routine package updates (`npm update`) or cosmetic refactoring.

---

## Supported Intents

```text
/migrate react 19
/migrate nextjs pages router to app router
/migrate commonjs to esm
/migrate webpack to vite
/migrate prisma to drizzle
/migrate upgrade tailwindcss to v4
/migrate express to nestjs
```

---

## Migration Safety Contract

### Must Preserve
- All existing API endpoints, request/response formats, URL routing parameters, and database states.
- Session storage formats, authentication cookie contracts, and client-facing UI layouts.

### Requires Explicit Staging
- Never upgrade multiple major frameworks simultaneously (e.g. Do NOT upgrade Node.js, React, and Next.js in a single unverified step).
- Keep old and new adapters running in parallel (Strangler Fig pattern) when migrating large subsystems.

---

## Migration Categories

| Category | Typical Triggers | Target Interventions |
| :--- | :--- | :--- |
| **1. Major Library Upgrade** | React 19, Node LTS, Tailwind v4 | Audit deprecated APIs, update peer deps, apply official codemods |
| **2. Architecture/Routing** | Next.js Pages ➔ App Router, Remix | Migrate server components, convert `getServerSideProps` to async fetches |
| **3. Module System & Build** | CJS ➔ ESM, Webpack ➔ Vite | Convert `require` to `import`, update `tsconfig.json`, adjust build plugins |
| **4. ORM & Data Layer** | Prisma ➔ Drizzle / Kysely | Translate schemas, verify SQL query output parity, test migration integrity |
| **5. State & Data Fetching** | Redux ➔ Zustand, Axios ➔ TanStack | Convert global store slices, maintain consistent hook return shapes |

---

## Migration Protocol

### Phase 1: Pre-Upgrade Audit & Dependency Graph
- Run `npm outdated` / dependency check. Identify breaking changes, peer conflicts, and runtime requirements (Node/OS).

### Phase 2: Establish Parity Baseline
- Run the entire test suite, build commands, and typecheck before modifying code or `package.json`.

### Phase 3: Update Dependencies & Lockfile
- Install target package versions. Resolve dependency tree collisions without `--force` / `--legacy-peer-deps` unless documented.

### Phase 4: Apply Syntax & Codemod Transformations
- Replace deprecated lifecycle methods, config schemas, and import paths in atomic file batches.

### Phase 5: Fix Type Errors & Compatibility Shims
- Resolve TypeScript compile errors. Add temporary compatibility adapters if external callers depend on legacy interfaces.

### Phase 6: Verify Behavioral Parity & Build
- Execute full test suite, E2E paths, and production build bundle inspection. Verify zero regressions.

### Phase 7: Cleanup Deprecated Adapters
- Remove legacy polyfills, obsolete build configs, and unused packages after verifying clean runtime behavior.

---

## Anti-Patterns to Avoid

- Upgrading dependencies blindly without reading official migration guides and breaking change lists.
- Using `--force` to silence peer dependency errors without verifying runtime compatibility.
- Combining migration changes with unrelated business logic refactoring or feature additions.
- Forgetting to update CI/CD pipelines, Dockerfiles, and deployment configs to match new runtime versions.

---

## Output Formats

### Standard Report

```markdown
## 🔄 Migration Report: [Target Migration]

### Scope & Target Versions
- **From / To:** [e.g. React 18.2.0 -> React 19.0.0 / Webpack -> Vite 6]
- **Target Files:** [Core modules / configs affected]

### Breaking Changes & Applied Remediations
| Area / Dependency | Breaking Change | Applied Fix / Codemod |
| :--- | :--- | :--- |
| `src/routes/...` | Deprecated API removal | Replaced with modern equivalent |
| `package.json` | Peer dependency collision | Aligned version constraints |

### Verification & Test Parity
- **Build Status:** [Clean production build verified]
- **Test Suite:** [Passed / Parity confirmed]
- **Typecheck:** [0 errors]
- **Next Follow-up:** `/test` | `/review`
```

---

## Definition of Done

1. Package versions and lockfiles cleanly updated without unverified peer dependency overrides.
2. All deprecated APIs and breaking syntax updated across the target scope.
3. Production build, typecheck, and test suite passing cleanly with zero behavioral regressions.