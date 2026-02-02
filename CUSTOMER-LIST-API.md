# Customer List API (Backend)

**Type:** Story / Task  
**Priority:** Medium  
**Component:** customer-be

**Related:** [CUSTOMER-LIST-TABLE-UI.md](./CUSTOMER-LIST-TABLE-UI.md) — View All
Customers in a Sortable Table (Frontend)

---

## Summary

Provide a **list customers** API that the customer UI can use to display all
customers in a sortable table. Support optional **sort** query parameters so the
frontend can request server-side sorting and keep the table and API contract
aligned.

---

## Description

The customer list table (see related UI ticket) needs:

1. **GET /api/customers** — Return all customers (or a paginated subset) with a
   consistent response shape.
2. **Optional sort parameters** — Accept query params (e.g. `sortBy`, `order`)
   so the backend can return the list pre-sorted, enabling a responsive sortable
   table without client-side sort of large datasets.

If the list is small, the UI may sort client-side; this ticket ensures the list
endpoint exists, returns the right shape, and optionally supports sort for
scalability.

---

## Acceptance Criteria

- [ ] **GET /api/customers**  
      Returns a list of customers. Response shape is documented and stable (e.g.
      `{ "customers": [ { "id", "name", "email", ... } ] }` or
      `[ { "id", "name", "email", ... } ]`). Data comes from the same
      persistence used by create/update/get-by-id.

- [ ] **Response consistency**  
      Each customer object in the list matches the shape returned by GET
      /api/customers/:id (same fields and types), so the UI can use one type for
      list rows and edit form.

- [ ] **Optional sort query params**  
      Accept at least one of:

  - `sortBy` (e.g. `name`, `email`, `createdAt`) and `order` (`asc` | `desc`),
    or
  - Single param like `sort=name_asc` / `sort=-name` for descending.  
    Return the list ordered accordingly. Invalid `sortBy` values return 400 or
    fall back to a default sort and document behavior.

- [ ] **Default sort**  
      When no sort params are provided, return list in a defined default order
      (e.g. by name ascending) so the UI has a predictable first load.

- [ ] **Empty list**  
      When there are no customers, return 200 with an empty array (or
      `customers: []`), not an error.

- [ ] **Errors**  
      On server or validation errors, return appropriate status (e.g. 400 for
      bad sort params, 500 for unexpected errors) with a consistent error body
      (e.g. `{ "error": "..." }`).

---

## API Contract (Suggested)

| Method | Path           | Query params                 | Purpose                           |
| ------ | -------------- | ---------------------------- | --------------------------------- |
| GET    | /api/customers | `sortBy`, `order` (optional) | List customers, optionally sorted |

**Example:**  
`GET /api/customers`  
`GET /api/customers?sortBy=name&order=asc`  
`GET /api/customers?sortBy=email&order=desc`

**Success response (200):**  
`{ "customers": [ { "id": "...", "name": "...", "email": "...", ... } ] }`  
or  
`[ { "id": "...", "name": "...", "email": "...", ... } ]`

**Empty:**  
`200` + `{ "customers": [] }` or `[]`

**Error (e.g. invalid sort):**  
`400` + `{ "error": "Invalid sort parameter", "details": "..." }`

---

## Technical Notes

- Align list response with GET /api/customers/:id and with create/update
  response so the UI can reuse one customer type.
- If persistence is a database, implement sort in the query for efficiency; if
  in-memory, sort the array before returning.
- Document which fields are valid for `sortBy` (e.g. name, email, id,
  createdAt).

---

## Out of Scope

- Pagination (separate ticket; list can return all until pagination is required)
- Filtering/search (separate ticket if needed)
- Authentication/authorization (unless required by project)

---

## Labels

`backend` `api` `customer-be` `list` `sorting` `rest`
