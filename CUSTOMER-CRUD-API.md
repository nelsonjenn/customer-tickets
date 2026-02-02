# Customer Create & Update API (Backend)

**Type:** Story / Task  
**Priority:** Medium  
**Component:** customer-be

**Related:** [CUSTOMER-FORM-COMPONENT.md](./CUSTOMER-FORM-COMPONENT.md) —
Reusable Customer Form (UI)

---

## Summary

Add backend API support for **creating** and **updating** customers so the
customer UI form can persist new and edited customer data. This ticket enables
the create and edit flows described in the Customer Form Component ticket.

---

## Description

The customer form component will call:

1. **Create** — Submit new customer data and receive the created customer (with
   id).
2. **Get by ID** — Load a single customer for pre-filling the edit form.
3. **Update** — Submit changed customer data for an existing customer.

Implement endpoints that accept the same customer payload shape the form will
send, validate input, and return consistent success/error responses.

---

## Acceptance Criteria

- [ ] **POST /api/customers** — Create customer

  - Accepts JSON body with customer fields (e.g. `name`, `email`, and any other
    form fields).
  - Validates required fields and formats; returns 400 with clear validation
    errors when invalid.
  - On success: returns 201 and the created customer object including `id`.
  - Response shape is consistent with GET single customer (so UI can use same
    type).

- [ ] **GET /api/customers/:id** — Get single customer

  - Returns the customer for the given `id`.
  - Returns 404 when customer does not exist.
  - Response shape matches what the edit form expects as `initialValues`.

- [ ] **PUT /api/customers/:id** (or **PATCH /api/customers/:id**) — Update
      customer

  - Accepts JSON body with customer fields to update.
  - Validates required fields and formats; returns 400 with clear validation
    errors when invalid.
  - Returns 404 when customer does not exist.
  - On success: returns 200 and the updated customer object.
  - Request body shape aligns with the form payload (same as create where
    applicable).

- [ ] **Validation**

  - Required fields and formats (e.g. email) validated on create and update.
  - Error responses include field-level or message details so the UI can surface
    them.

- [ ] **Persistence**
  - Create and update persist to a data store (e.g. in-memory, file, or database
    as per project standards).
  - GET list (`/api/customers`) and GET by id return data from the same store.

---

## API Contract (Suggested)

Align with the existing mock in `index.js` (e.g. `id`, `name`, `email`). Extend
as needed for the form.

| Method | Path               | Purpose                                                 |
| ------ | ------------------ | ------------------------------------------------------- |
| GET    | /api/customers     | List customers (existing)                               |
| GET    | /api/customers/:id | Get one customer (for edit form)                        |
| POST   | /api/customers     | Create customer                                         |
| PUT    | /api/customers/:id | Update customer (full replace) or use PATCH for partial |

**Create request body (example):**  
`{ "name": "string", "email": "string", ... }`

**Update request body (example):**  
Same shape as create; only provided fields updated if using PATCH.

**Success response (create):**  
`201` + `{ "id": "...", "name": "...", "email": "...", ... }`

**Success response (get one / update):**  
`200` + same customer object shape.

**Error response (validation):**  
`400` + `{ "error": "Validation failed", "details": [...] }`

**Error response (not found):**  
`404` + `{ "error": "Customer not found" }`

---

## Technical Notes

- Reuse or define a shared schema/validation for customer (e.g. required `name`,
  valid `email`) for both create and update.
- Consider id format (numeric vs UUID) and how GET list currently returns data;
  ensure GET by id and list stay consistent.
- CORS: ensure customer-ui origin is allowed if frontend runs on a different
  port.

---

## Out of Scope

- Authentication/authorization (unless required by project)
- Delete customer endpoint (separate ticket if needed)
- Pagination or filtering on GET /api/customers (existing behavior can remain)

---

## Labels

`backend` `api` `customer-be` `crud` `rest`
