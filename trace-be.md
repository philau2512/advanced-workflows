# trace-be
🔍 Trace Backend API endpoint or data flow (Router -> Middleware -> Controller -> Service -> Repository/DB -> Events). Outputs structured report with dependency map, DB queries, and side effects.

# /trace-be - Backend Data Flow & API Endpoint Tracing Mode

Entry provided by user: `$ARGUMENTS`

Parse arguments:
- `--out` flag present: save report to file (format: `reports/TRACE-BE-<YYYYMMDD>-<filename>.md`)
- Everything before `--out` = entry path, controller file, or endpoint string (e.g. `POST /api/v1/orders`, `src/controllers/orderController.ts`)

---

## Purpose

The `/trace-be` workflow traces the execution path of a Backend request or asynchronous job end-to-end:
1. **Entry & Routing** (Routes, Framework Controllers, gRPC/GraphQL Resolvers)
2. **Middleware & Validation** (Auth guards, CORS, Rate Limiters, DTO / Schema Validation)
3. **Business Logic Layer** (Service classes, Domain rules, Transaction boundaries)
4. **Data Access & Storage** (Repository, ORM/Query Builder, SQL/NoSQL, Redis Caching)
5. **Side Effects & Integration** (External APIs, Queue Producers, Event Emitters, Microservice calls)

---

## Workflow Steps

### Step 0 - Validate Input & Setup

If `$ARGUMENTS` is empty, stop and print:
```text
Usage: /trace-be [--out] <endpoint-or-file>
Examples:
  /trace-be src/controllers/user.controller.ts
  /trace-be POST /api/v1/checkout
  /trace-be --out src/services/PaymentService.ts
  /trace-be --out src/routes/orders.ts
```

Parse `$ARGUMENTS`:
- If starts with `--out`: set `SAVE_TO_FILE = true`, strip `--out`
- Otherwise: set `SAVE_TO_FILE = false`
- `ENTRY_INPUT` = remaining argument

If `SAVE_TO_FILE = true`:
- Set `YYYYMMDD` = current date
- Derive safe filename from `ENTRY_INPUT` (kebab-case)
- Set `REPORT_PATH = reports/TRACE-BE-<YYYYMMDD>-<filename>.md`
- Ensure `reports/` directory exists

---

### Step 1 - Resolve Entry & Route Mapping

1. **Locate Route / Handler**:
   - If a file path is provided: locate entry functions/methods (e.g. `@Post()`, `router.post()`, `@Query()`).
   - If an HTTP endpoint string is provided (e.g. `POST /api/v1/users`): search for route registration in `routes/`, `controllers/`, or router modules.
2. **Map Route Decorators / Handlers**:
   - HTTP Method & Path URL
   - Associated Controller/Handler class & method
   - Request DTO or Body Schema (Zod, Joi, Class-Validator, Pydantic, Marshmallow, Go Struct)

---

### Step 2 - Trace Middleware & Validation Pipeline

Trace the request pipeline before business logic executes:
- **Authentication & Authorization**: JWT verify, Session check, RBAC / ABAC guards
- **Validation**: Schema validation, body/param sanitization
- **System Interceptors**: Logging, Rate Limiting, Request ID tracing, CORS
- **Context Injections**: User payload attached to request object (e.g., `req.user`, `ctx.state.user`)

---

### Step 3 - Trace Business & Domain Logic (Service Layer)

Follow execution into Service/Domain layer:
- **Core Functions & Methods**: List functions called with input parameters
- **Business Rules**: Validations, state checks, decision trees
- **Transaction Management**: Database transactions (`BEGIN`, `COMMIT`, `ROLLBACK`, `@Transactional`)
- **Error Handling**: Custom exceptions, try/catch blocks, error mappers

---

### Step 4 - Trace Data Access Layer & Caching

Trace interactions with databases and storage:
- **ORM / Query Builder**: Prisma, TypeORM, Sequelize, Mongoose, SQLAlchemy, Gorm, Dapper, etc.
- **Raw SQL / Queries**: Database queries executed, tables affected (Read vs. Write)
- **Cache Layer**: Redis/Memcached lookups, invalidation strategies, TTL
- **Indexes & Performance Assumptions**: Potential N+1 query patterns, missing indexes, unindexed filtering

---

### Step 5 - Trace Side Effects & External Integrations

Identify all outbound communication and async side effects:
- **External HTTP/gRPC APIs**: Payment gateways, third-party REST services, internal microservices
- **Message Queues & Events**: RabbitMQ, Kafka, BullMQ, SQS, Redis PubSub (Events published vs. consumed)
- **Background Tasks**: Async worker dispatch, cron jobs, file storage uploads (S3, Cloudinary)

---

### Step 6 - Generate Backend Trace Report

Format the final report as shown below:

````markdown
# 🔍 Backend Trace Report: `<Endpoint / Component>`

**Target Entry:** `<ENTRY_INPUT>`
**Timestamp:** `<ISO timestamp>`

---

## 1. Pipeline Overview

```
[Client Request]
    │
    ▼
[Middleware Layer] ➔ Auth Guards, Validation Schemas, Rate Limiters
    │
    ▼
[Controller / Handler] ➔ Request extraction, DTO mapping
    │
    ▼
[Service Layer] ➔ Business rules, Domain logic, Transactions
    │
    ├──► [Data Access / DB] ➔ Tables: `<table1>`, `<table2>` (Read/Write)
    ├──► [Cache Layer] ➔ Redis Key: `<pattern>`
    └──► [External Services] ➔ `<Service / API>`
    │
    ▼
[Response / Side Effects] ➔ HTTP Status `<Code>`, Published Events: `<Events>`
```

---

## 2. Execution Flow Details

| Phase | Module / File | Method / Function | Role & Action |
|-------|---------------|-------------------|---------------|
| **Entry / Route** | [`route.ts`](file:///path) | `POST /api/...` | Route definition & handler binding |
| **Middleware** | [`auth.guard.ts`](file:///path) | `canActivate()` | Validates JWT & injects `req.user` |
| **Validation** | [`create-dto.ts`](file:///path) | `validate()` | Body schema validation |
| **Controller** | [`user.controller.ts`](file:///path) | `create()` | Extracts payload, calls service |
| **Service** | [`user.service.ts`](file:///path) | `createUser()` | Business validation, hashes password |
| **Data Access** | [`user.repository.ts`](file:///path) | `save()` | Inserts row into `users` table |
| **Side Effects** | [`event.emitter.ts`](file:///path) | `emit('user.created')` | Sends welcome email via queue |

---

## 3. Data Transformations & Schemas

- **Input Payload (Request DTO / Params):** `<Fields & Types>`
- **Database Model / Entity:** `<Tables / Collections involved>`
- **Output Response (Response DTO):** `<Fields & Status Code>`

---

## 4. Database & External Dependencies

| Dependency | Type | Operation | Risk / Bottleneck Note |
|------------|------|-----------|------------------------|
| `users` table | PostgreSQL | `INSERT` | Unique constraint on `email` |
| Redis Cache | Cache | `DEL user:list:*` | Cache invalidation on create |
| Stripe API | External HTTP | `POST /v1/customers` | Network latency / Third-party outage risk |
| RabbitMQ | Message Queue | `PUBLISH user_events` | Async background processing |

---

## 5. Potential Bottlenecks & Code Risks

- ⚠️ **Error Handling:** List any uncaught exceptions or silent failures.
- ⚠️ **N+1 / DB Performance:** Highlight repeated DB calls in loops.
- ⚠️ **Transaction Safety:** Check if multi-table writes are properly wrapped in DB transactions.
- ⚠️ **Security / Auth:** Check if authorization guards are missing on child functions.

---

## 6. Recommended Next Step

- Use `/debug` to isolate a runtime exception in `<Service/Function>`
- Use `/optimize` to optimize slow DB queries or N+1 patterns
- Use `/security` to audit authorization & input sanitization
- Use `/test` to generate backend unit/integration tests
````

If `SAVE_TO_FILE = true`:
- Save content to `<REPORT_PATH>`
- Print confirmation message: `✅ Report saved to: <REPORT_PATH>`

If `SAVE_TO_FILE = false`:
- Render report directly in chat output.
