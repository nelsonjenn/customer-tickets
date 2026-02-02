---
name: Customer CRUD API Plan
overview:
  Implement POST (create), GET by id, and PUT (update) customer endpoints in
  customer-be per [CUSTOMER-CRUD-API.md](jira-tickets/CUSTOMER-CRUD-API.md),
  with validation, file persistence, CORS, and tests; document the plan in
  crud-api-plan.md.
todos: []
isProject: false
---

# Customer CRUD API Implementation Plan

Deliverable: save this plan (steps, testing, tests during implementation) to
**crud-api-plan.md** in the project root.

---

## Current state

- **Backend:** [customer-be/index.js](customer-be/index.js) — Express app with
  `GET /api/customers` only; reads from
  [customer-be/data/customers.json](customer-be/data/customers.json).
- **Data shape:** `{ id, name, email, createdAt }`; `id` is numeric; file is an
  array.
- **Jira ticket:**
  [jira-tickets/CUSTOMER-CRUD-API.md](jira-tickets/CUSTOMER-CRUD-API.md) —
  requires POST create, GET by id, PUT update, validation, same persistence,
  CORS for customer-ui.

---

## Implementation steps

### 1. Persistence helper

- Add a **write function** (e.g. `saveCustomers(customers)`) that writes the
  customers array back to `data/customers.json` (same path as `getCustomers()`).
- Keep `getCustomers()` as-is; ensure read/write use the same file so list and
  CRUD share one store.
- **Test during implementation:** Call `saveCustomers(getCustomers())` and
  confirm file contents unchanged (round-trip). Run this before adding routes.

### 2. Validation

- Define **shared validation** for create and update:
  - Required: `name` (non-empty string), `email` (non-empty string).
  - Format: `email` must be a valid email (regex or small helper).
- Return **400** with a consistent body, e.g.
  `{ "error": "Validation failed", "details": [{ "field": "email", "message": "Invalid email" }] }`.
- Implement as a small inline middleware or helper used by POST and PUT (no new
  dependency required; optional: add `express-validator` later).
- **Test during implementation:** POST with missing `name` or invalid `email`
  returns 400 and the error shape above. Run after adding validation and POST
  route (can use a minimal POST handler first).

### 3. GET /api/customers/:id

- Add route **GET /api/customers/:id** (after GET /api/customers so "customers"
  is not matched as an id).
- Load customers via `getCustomers()`, find by `id` (match param to number or
  string as per existing ids).
- If found: **200** + single customer object (same shape as list items).
- If not found: **404** + `{ "error": "Customer not found" }`.
- **Test during implementation:** GET existing id returns 200 and correct
  object; GET non-existent id returns 404. Run after adding GET :id route (e.g.
  `curl /api/customers/1` and `curl /api/customers/999`).

### 4. POST /api/customers

- Add route **POST /api/customers**.
- Run validation on body (`name`, `email`); on failure return 400 with details.
- Generate **new id**: e.g. `Math.max(...ids, 0) + 1` from current list.
- Set **createdAt** to `new Date().toISOString()`.
- Append new customer to array, call `saveCustomers()`, then return **201** and
  the created customer (same shape as GET one).
- **Test during implementation:** POST valid body returns 201 and object with id
  and createdAt; GET list and GET by new id both return the new customer. Run
  after POST create is wired to persistence.

### 5. PUT /api/customers/:id

- Add route **PUT /api/customers/:id**.
- Resolve id (same as GET by id); if not found return **404**.
- Run same validation on body; on failure return 400.
- **Full replace:** replace the existing customer’s `name` and `email` with
  body; keep existing `id` and `createdAt` (per ticket “full replace” and
  existing shape).
- Call `saveCustomers()`, return **200** and the updated customer object.
- **Test during implementation:** PUT with valid body updates customer and
  returns 200; PUT to non-existent id returns 404; invalid body returns 400. Run
  after adding PUT route.

### 6. CORS

- Enable CORS for customer-ui (e.g. `Access-Control-Allow-Origin` for the UI
  origin, or use `cors` package with origin config).
- **Test during implementation:** From customer-ui origin, GET list and GET by
  id return without CORS errors (browser or
  `curl -H "Origin: http://localhost:3001"`). Run after enabling CORS.

### 7. Route order and 404

- Ensure route order: GET /api/customers (list), then GET /api/customers/:id,
  POST /api/customers, PUT /api/customers/:id. Confirm generic 404 handler still
  catches unknown routes.
- **Test during implementation:** Invalid path returns 404 with
  `{ "error": "Route not found" }` (or existing 404 message). Run after route
  order is set (e.g. `curl /api/unknown`).

---

## Testing

### Tests during implementation (per step)

Run these as you complete each step to confirm the step is done correctly:

| Step           | Test during implementation                                                                        |
| -------------- | ------------------------------------------------------------------------------------------------- |
| 1. Persistence | Round-trip: `saveCustomers(getCustomers())` leaves file unchanged.                                |
| 2. Validation  | POST with missing `name` or invalid `email` returns 400 with `error` + `details`.                 |
| 3. GET :id     | `GET /api/customers/1` → 200 + customer; `GET /api/customers/999` → 404.                          |
| 4. POST        | POST valid body → 201 + created customer with id/createdAt; GET list and GET by new id return it. |
| 5. PUT         | PUT valid body to existing id → 200 + updated customer; PUT to bad id → 404; invalid body → 400.  |
| 6. CORS        | Request with `Origin: http://localhost:3001` (or UI port) returns CORS headers and 200.           |
| 7. Route order | `GET /api/unknown` → 404 with `{ "error": "Route not found" }`.                                   |

### Full manual / curl regression

After all steps: GET by id, POST (valid and invalid), PUT (valid, bad id,
invalid body), restart server and confirm persistence, CORS from UI origin.

### Automated tests (recommended)

- Add **supertest** (and **jest** or **mocha**) to customer-be.
- **Tests to add:** GET :id (200/404), POST (201/400), PUT (200/404/400).
- Use a **test data file** or restore `customers.json` after tests so the file
  is not mutated.
- **Test during implementation:** After adding the suite, run `npm test` and
  confirm all pass.

---

## Files to touch

- [customer-be/index.js](customer-be/index.js) — add `saveCustomers`, validation
  helper, GET :id, POST, PUT, CORS, route order.
- [customer-be/package.json](customer-be/package.json) — add test script and
  devDependencies (e.g. jest, supertest) if adding automated tests.
- New: `customer-be/test/api.test.js` (or similar) if adding automated tests.
- New: **crud-api-plan.md** — write this plan (steps, testing, tests during
  implementation) into the repo for reference.

---

## Out of scope (per ticket)

- Delete endpoint
- Auth/authz
- Pagination/filtering on GET /api/customers
