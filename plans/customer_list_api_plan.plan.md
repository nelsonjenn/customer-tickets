---
name: Customer List API Plan
overview:
  Add optional sort query params to GET /api/customers in customer-be per
  [CUSTOMER-LIST-API.md](jira-tickets/CUSTOMER-LIST-API.md), with stable
  response shape, default sort, and error handling; document the plan in
  list-api-plan.md.
todos: []
isProject: false
---

# Customer List API Implementation Plan

Deliverable: save this plan (steps, testing, tests during implementation) to
**list-api-plan.md** in the project root.

---

## Current state

- **Backend:** [customer-be/index.js](customer-be/index.js) — Express app with
  **GET /api/customers** returning `{ customers }` from
  [customer-be/data/customers.json](customer-be/data/customers.json). No sort
  params yet.
- **Data shape:** `{ id, name, email, createdAt }`; list items match shape used
  by GET /api/customers/:id (once implemented).
- **Jira ticket:**
  [jira-tickets/CUSTOMER-LIST-API.md](jira-tickets/CUSTOMER-LIST-API.md) —
  requires GET /api/customers with optional sort (sortBy, order), default sort,
  empty list as 200 + [], and consistent error body.

---

## Implementation steps

### 1. Response shape and consistency

- Ensure **GET /api/customers** returns a stable shape (e.g.
  `{ customers: [...] }` or `[...]`). Current implementation already returns
  `{ customers }`.
- Confirm each item has the same fields as GET /api/customers/:id (id, name,
  email, createdAt).
- **Test during implementation:** GET /api/customers returns 200 and array of
  objects with expected fields. Run after verifying response shape.

### 2. Default sort

- When **no sort params** are provided, return the list in a **default order**
  (e.g. by name ascending). Sort in-memory after reading from file (same
  persistence as list/CRUD).
- **Test during implementation:** GET /api/customers (no params) returns list
  sorted by default (e.g. name asc). Run after adding default sort.

### 3. Sort query parameters

- Accept **sortBy** and **order** query params (e.g. `sortBy=name`, `order=asc`
  or `order=desc`).
- **Valid sortBy values:** document and allow at least `name`, `email`, `id`,
  `createdAt`. Invalid sortBy: return **400** with consistent body (e.g.
  `{ "error": "Invalid sort parameter", "details": "..." }`) or fall back to
  default sort and document behavior.
- **Valid order:** `asc` or `desc`; invalid value return 400 or default to asc.
- **Test during implementation:** GET /api/customers?sortBy=name&order=asc
  returns list sorted by name asc; sortBy=email&order=desc by email desc.
  Invalid sortBy or order returns 400. Run after adding sort logic.

### 4. Empty list and errors

- When there are **no customers**, return **200** with `{ customers: [] }` (or
  `[]`), not an error.
- On **server/read errors**, return **500** with `{ "error": "..." }`.
- **Test during implementation:** Empty data file (or fixture) returns 200 +
  empty array. Simulated read error returns 500 with error body. Run after
  handling edge cases.

### 5. Route order

- Keep **GET /api/customers** before GET /api/customers/:id so "customers" is
  not matched as an id. Confirm 404 handler still catches unknown routes.
- **Test during implementation:** GET /api/customers and GET
  /api/customers?sortBy=name both hit list handler; invalid path returns 404.
  Run after verifying order.

---

## Testing

### Tests during implementation (per step)

Run these as you complete each step to confirm the step is done correctly:

| Step            | Test during implementation                                                             |
| --------------- | -------------------------------------------------------------------------------------- |
| 1. Response     | GET /api/customers → 200 + { customers: [ { id, name, email, createdAt }, ... ] }.     |
| 2. Default sort | GET /api/customers (no params) → list sorted by default (e.g. name asc).               |
| 3. Sort params  | sortBy=name&order=asc / sortBy=email&order=desc → correct order; invalid params → 400. |
| 4. Empty/errors | Empty list → 200 + customers: []; server error → 500 + { error }.                      |
| 5. Route order  | List and list?sortBy=name work; unknown path → 404.                                    |

### Full manual / curl regression

After all steps: GET /api/customers with no params, sortBy=name, sortBy=email,
order=desc, invalid sortBy, empty list, and confirm response shape matches GET
:id item shape.

### Automated tests (recommended)

- Use **supertest** (and jest/mocha) in customer-be.
- **Tests to add:** GET /api/customers (200, shape); default sort; sortBy +
  order (asc/desc); invalid sortBy/order (400); empty list (200 + []).
- Use a **test data file** or restore customers.json after tests.
- **Test during implementation:** Run `npm test` and confirm list API tests
  pass.

---

## Files to touch

- [customer-be/index.js](customer-be/index.js) — add default sort, parse
  sortBy/order, sort list before sending, 400 for invalid params, empty list
  handling.
- [customer-be/package.json](customer-be/package.json) — add test script and
  devDependencies if adding automated tests.
- New: `customer-be/test/list-api.test.js` (or similar) if adding automated
  tests.
- New: **list-api-plan.md** — write this plan into the repo for reference.

---

## Out of scope (per ticket)

- Pagination (list returns all until separate ticket)
- Filtering/search
- Authentication/authorization
