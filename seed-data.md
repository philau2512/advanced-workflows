---
description: 🎲 Relational synthetic test data generation and database seeding workflow with localized fixtures and foreign-key integrity.
---

# /seed-data - Synthetic Data & Database Seeding Mode

$ARGUMENTS

## Purpose & Core Rule

Activates SEED-DATA mode for generating realistic, relational, and domain-appropriate mock datasets and database seed scripts.

> **SEED-DATA = RELATIONAL INTEGRITY, LOCALIZED REALISM, DETERMINISTIC REPEATABILITY**

- Generate synthetic datasets preserving exact Foreign Key hierarchies (e.g. Users ➔ Teams ➔ Projects ➔ Tasks).
- Use localized names, phone numbers, addresses, and business numbers (e.g. Vietnam / US / Japan locales via Faker).
- Enforce idempotency: seeding can run multiple times without duplicate key violations.

---

## When to Use

- **Use when:** Setting up local development databases, preparing staging test data, performance/load testing with large volume data, or building demo environments.
- **Do not use:** In production environments containing real user data.

---

## Supported Intents

```text
/seed-data generate 50 users with orders and payments
/seed-data create prisma seed script with vietnamese names and addresses
/seed-data populate staging database with 10k product records
/seed-data create factory functions for user and organization models
/seed-data generate test fixtures for checkout integration tests
```

---

## Seeding Dimensions & Best Practices

| Dimension | Rule & Implementation |
| :--- | :--- |
| **1. Relational Order** | Seed parents before children (e.g. `User` ➔ `Order` ➔ `OrderItem`); clean up in reverse order. |
| **2. Deterministic Seeds** | Support random seed initialization (`faker.seed(123)`) for repeatable test data. |
| **3. Idempotent Upserts** | Use `upsert` or `ON CONFLICT DO UPDATE/NOTHING` instead of raw `insert` to prevent unique constraint crashes. |
| **4. Localized Realism** | Use appropriate Faker locales (`vi`, `en_US`, etc.) for names, emails, citizen IDs, and phone formats. |
| **5. Volume & Batching** | Batch insertions (e.g. 500–1000 rows per query) when generating high-volume datasets to prevent memory exhaustion. |

---

## Execution Protocol

### Phase 1: Analyze DB Schema & Entity Relations
- Inspect Prisma, Drizzle, TypeORM, or SQL schema to map required fields, unique constraints, and foreign keys.

### Phase 2: Design Data Factories
- Define factory helpers generating realistic defaults with support for override parameters (`createMockUser({ role: 'admin' })`).

### Phase 3: Implement Seeder Script
- Write seed orchestration script maintaining strict insertion sequence and foreign-key linking.

### Phase 4: Add Teardown / Truncate Helper
- Provide safe reset/truncate logic strictly guarded against running in production (`if (process.env.NODE_ENV === 'production') throw Error`).

### Phase 5: Execute & Validate Integrity
- Run seed script against local/test database. Verify foreign keys resolve cleanly and record counts match expectations.

---

## Output Format

```markdown
## 🎲 Data Seeding Report: [Target Database / Entities]

### 1. Generated Entity Hierarchy
```text
Users (N: 50)
  └── Organizations (N: 10)
        └── Projects (N: 30)
              └── Tasks (N: 200)
```

### 2. Seeder Files Created
- `prisma/seed.ts` (or `src/db/seeds/index.ts`)
- `src/db/factories/user.factory.ts`

### 3. Execution Commands
```bash
# Run local seeding
npm run db:seed
```

### 4. Verification
- Total rows inserted: [Summary per table]
- Foreign-key integrity check: [Passed 100%]
```

---

## Definition of Done

1. Seeder script created respecting schema relationships and foreign-key constraints.
2. Production protection guard implemented to prevent accidental database wipes.
3. Seeding executed locally and verified with zero constraint errors.