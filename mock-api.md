---
description: 🧪 API Mocking, contract simulation, and Frontend-First testing workflow using Mock Service Worker (MSW), JSON-Server, and OpenAPI schemas.
---

# /mock-api - API Mocking & Simulation Mode

$ARGUMENTS

## Purpose & Core Rule

Activates MOCK-API mode for creating realistic mock servers, network handlers, and contract fixtures to enable frontend-first development and deterministic testing.

> **MOCK-API = CONTRACT-FAITHFUL HANDLERS, DETERMINISTIC FIXTURES, ZERO PRODUCTION LEAK**

- Intercept client network requests (REST, GraphQL) via MSW or local mock servers without modifying backend code.
- Simulate complex backend behaviors: realistic latency, pagination, 4xx/5xx errors, auth expiry, and rate limits.
- Keep mocks strictly isolated from production bundles.

---

## When to Use

- **Use when:** Frontend development before backend APIs are ready, simulating flaky networks/errors, offline testing, UI component stories, or integration tests.
- **Do not use:** In production environments or when end-to-end tests require actual database validation.

---

## Supported Intents

```text
/mock-api create msw handlers for checkout flow
/mock-api generate mock data from openapi spec
/mock-api simulate 500 error and slow network on orders api
/mock-api mock graphql query for user profile
/mock-api setup browser service worker for local dev
```

---

## Mocking Dimensions

| Dimension | Implementation Standard |
| :--- | :--- |
| **1. Tooling** | Prefer **MSW (Mock Service Worker)** for browser/Node interception; use lightweight express/json-server for standalone CLI mocks. |
| **2. Schema Fidelity** | Validate mock response payloads against TypeScript types, Zod schemas, or OpenAPI specifications. |
| **3. Statefulness** | Use in-memory databases (e.g. `@mswjs/data`) to support realistic CRUD mutations during user sessions. |
| **4. Network Chaos** | Configurable response delay (`delay(800)`), status overrides (`ctx.status(401)`), and network drops. |
| **5. Bundle Isolation** | Initialize service worker handlers only in `development` or `test` mode (`process.env.NODE_ENV !== 'production'`). |

---

## Execution Protocol

### Phase 1: Identify API Contracts & Endpoints
- Locate API client definitions, OpenAPI specs, or TypeScript interface contracts for target endpoints.

### Phase 2: Design Handler Fixtures
- Create realistic payload fixtures with varied data sets (empty lists, single items, large paginated responses).

### Phase 3: Implement MSW Handlers / Mock Server
- Write request handlers matching exact HTTP method, path, headers, query params, and body parsing.

### Phase 4: Configure Chaos Scenarios
- Provide toggles/scenarios for Happy Path (200), Validation Failure (422), Unauthorized (401), and Server Error (500).

### Phase 5: Integrate with Dev / Test Lifecycle
- Register service worker for browser dev (`src/mocks/browser.ts`) or server setup for tests (`src/mocks/server.ts`).

### Phase 6: Verify Interception
- Trigger UI/test calls and verify network tab displays intercepted mock data without console errors.

---

## Output Format

```markdown
## 🧪 Mock API Report: [Feature/Scope]

### 1. Intercepted Endpoints
| Method | Endpoint | Response Type | Scenarios Supported |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/v1/orders` | `OrderListResponse` | 200 (Populated/Empty), 500 |
| `POST` | `/api/v1/checkout` | `CheckoutResult` | 200 (Success), 422 (Card Invalid) |

### 2. Integration Files Created/Updated
- `src/mocks/handlers/orders.ts`
- `src/mocks/browser.ts`

### 3. Usage Guide
```typescript
// Enable in local dev:
if (process.env.NODE_ENV === 'development') {
  const { worker } = await import('./mocks/browser');
  worker.start();
}
```

### 4. Next Follow-up
- Run `/test` or preview UI with active mock handlers
```

---

## Definition of Done

1. Mock handlers created matching exact endpoint contracts and response schemas.
2. Error and edge-case scenarios (delays, status codes) supported and configurable.
3. Production bundle isolation strictly verified.