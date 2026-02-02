# View All Customers in a Sortable Table

**Type:** Story / Task  
**Priority:** Medium  
**Component:** customer-ui

**Related:** [CUSTOMER-LIST-API.md](./CUSTOMER-LIST-API.md) — Customer List API
(Backend)

---

## Summary

Implement a **customers list** view that displays all customers in a **sortable
table**, so users can browse and sort by column (e.g. name, email, created date)
without leaving the page.

---

## Description

Build a page or section that:

- Fetches the full customer list from the backend (e.g. `GET /api/customers`).
- Renders customers in a **table** with clear column headers.
- Makes one or more columns **sortable** (click header to sort
  ascending/descending, with visible sort indicator).
- Handles loading and error states (e.g. empty state when no customers, message
  when fetch fails).
- Optionally links rows to edit (e.g. click row or "Edit" to open edit form), so
  the list works with the reusable customer form.

---

## Acceptance Criteria

- [ ] **Table view**  
      All customers are displayed in a table. Columns align with customer data
      (e.g. id, name, email, and any other fields from the API).

- [ ] **Sortable columns**  
      At least the primary columns (e.g. name, email) are sortable. Clicking a
      column header toggles sort direction (asc/desc); visual indicator (icon or
      label) shows current sort column and direction.

- [ ] **Sort behavior**  
      Sorting works correctly (client-side sort of fetched list, or server-side
      via sort query params if the backend supports it). Default sort (e.g. by
      name ascending) is applied on first load.

- [ ] **Loading & empty states**  
      Loading state is shown while fetching. Empty state is shown when the list
      is empty (e.g. "No customers yet" with optional link to create).

- [ ] **Error handling**  
      If the list request fails, show a clear error message and optional retry.

- [ ] **Integration**  
      List uses the customer list API; if backend supports sort params, use
      them; otherwise sort in the frontend after fetch.

---

## Technical Notes

- Reuse or align with existing design system (e.g. MUI Table, or plain HTML
  table with minimal styling).
- Consider accessibility: sortable headers should be focusable and announce sort
  state (e.g. aria-sort).
- Optional: link each row to the edit customer flow (e.g. navigate to edit page
  with customer id or open form with initial values).

---

## Out of Scope

- Pagination (separate ticket if list grows large)
- Inline editing in the table (edit remains via dedicated form)
- Filtering/search (separate ticket if needed)

---

## Labels

`frontend` `customer-ui` `table` `sortable` `list-view`
