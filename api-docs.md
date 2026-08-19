---
description: 📖 Automated API documentation, OpenAPI/Swagger schema extraction, and contract reference generator workflow from backend routes and handlers.
---

# /api-docs - API Documentation & Contract Export Mode

$ARGUMENTS

## Purpose & Core Rule

Activates API-DOCS mode to inspect backend routes, controllers, and validation schemas, generating clean OpenAPI/Swagger specs or human-readable Markdown API references.

> **API-DOCS = ACCURATE SCHEMAS, ZERO PHANTOM PARAMETERS, REAL-WORLD EXAMPLES**

- Extract HTTP methods, route paths, URL parameters, query strings, headers, request bodies, and responses directly from source code.
- Support popular frameworks (Express, NestJS, FastAPI, Django REST, Spring Boot, Go Fiber/Gin).
- Provide real-world, valid JSON request/response payload examples for client and frontend teams.

---

## When to Use

- **Use when:** Exporting API contracts for frontend/mobile teams, updating `openapi.yaml`/`swagger.json`, documenting webhook payloads, or auditing API parameter drift.
- **Do not use:** General architecture documentation (use `/architecture` or `ak:docs`).

---

## Supported Intents

```text
/api-docs
/api-docs src/routes/orders.ts
/api-docs export openapi.yaml for billing module
/api-docs generate markdown api reference for mobile team
/api-docs document webhook payload contracts
```

---

## Documentation Extraction Dimensions

| Dimension | Inspection Target | Standard |
| :--- | :--- | :--- |
| **1. Route & Method** | `@Get()`, `router.post()`, `@api_view` | HTTP Method, Base Path, Dynamic Path Params (`:id`, `{orderId}`) |
| **2. Auth & Headers** | Auth Guards, Middleware, Headers | Bearer Token, API Key, `Authorization`, `X-Workspace-Id` |
| **3. Request Schema** | Zod, Joi, Pydantic, DTO classes, Body | Required vs Optional fields, data types, validation regex, min/max |
| **4. Response Matrix** | 200/201 Success, 400 Validation, 401/403 Auth, 404, 500 | Exact response JSON schema, pagination metadata, error payload format |
| **5. Query Parameters**| URL Search Params (`?page=1&limit=20&sort=desc`) | Pagination, filtering, sorting, enum values |

---

## Execution Protocol

### Phase 1: Scan & Identify Route Entry Points
- Locate route definitions in `routes/`, `controllers/`, or router modules.

### Phase 2: Trace Middleware & Auth Gates
- Identify authentication requirements (JWT, session, public) and required custom headers.

### Phase 3: Extract Request & Response Schemas
- Inspect DTOs, Zod/Joi validators, Serializers, or Pydantic models to construct exact JSON schemas.

### Phase 4: Construct Real-World Payload Examples
- Generate realistic, populated request and response JSON examples (no generic `string` or `0` placeholders).

### Phase 5: Format Output (OpenAPI YAML or Markdown)
- Write output to `docs/api-reference.md` or export to `openapi.yaml` based on user request.

---

## Output Format

```markdown
## 📖 API Documentation: [Module/Feature]

### `POST /api/v1/orders`

**Description:** Creates a new customer order and reserves inventory items.  
**Authentication:** `Bearer <JWT_TOKEN>` (Role: `customer`, `sales`)  
**Content-Type:** `application/json`

#### Request Body Schema
| Field | Type | Required | Description |
| :--- | :--- | :---: | :--- |
| `customerId` | `string (UUID)` | Yes | ID of the ordering customer |
| `items` | `Array<OrderItem>` | Yes | List of product items to purchase |
| `items[].productId` | `string` | Yes | Unique SKU or product identifier |
| `items[].quantity` | `number` | Yes | Integer quantity (min: 1) |

#### Example Request
```json
{
  "customerId": "c8f9b201-48a1-4234-89bc-112233445566",
  "items": [
    { "productId": "PROD-1024", "quantity": 2 }
  ]
}
```

#### Response Status Codes
- `201 Created`: Order placed successfully.
- `400 Bad Request`: Validation failure or insufficient stock.
- `401 Unauthorized`: Missing or expired token.

#### Example Response (`201 Created`)
```json
{
  "success": true,
  "data": {
    "orderId": "ORD-2026-9981",
    "status": "pending_payment",
    "totalAmount": 1500000,
    "createdAt": "2026-08-19T10:00:00Z"
  }
}
```
```

---

## Definition of Done

1. All routes and HTTP methods in scope discovered and documented.
2. Request bodies, query parameters, headers, and status codes accurately mapped to source code schemas.
3. Realistic JSON payload examples provided for both request and response contracts.