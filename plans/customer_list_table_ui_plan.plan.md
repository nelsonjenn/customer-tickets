---
name: Customer List Table UI Plan
overview:
  Implement a customers list view with a sortable table in customer-ui per
  [CUSTOMER-LIST-TABLE-UI.md](jira-tickets/CUSTOMER-LIST-TABLE-UI.md), fetching
  from GET /api/customers, with sort indicators and loading/empty/error states;
  document the plan in list-table-ui-plan.md.
todos: []
isProject: false
---

# Customer List Table UI Implementation Plan

Deliverable: save this plan (steps, testing, tests during implementation) to
**list-table-ui-plan.md** in the project root.

---

## Current state

- **Frontend:** [customer-ui/src/App.jsx](customer-ui/src/App.jsx) — React app
  with `/customers` route rendering
  [CustomersPage.jsx](customer-ui/src/pages/CustomersPage.jsx). No table list
  view yet (or minimal placeholder).
- **Backend:** GET /api/customers returns
  `{ customers: [ { id, name, email, createdAt } ] }`; optional sort params
  (sortBy, order) per list API ticket.
- **Jira ticket:**
  [jira-tickets/CUSTOMER-LIST-TABLE-UI.md](jira-tickets/CUSTOMER-LIST-TABLE-UI.md)
  — requires table with columns for customer data, sortable columns (click
  header asc/desc with indicator), loading/empty/error states, and optional link
  to edit.

---

## Implementation steps

### 1. Fetch list and display table

- On **CustomersPage** (or dedicated list component), **fetch** GET
  /api/customers on mount (useEffect or data library). Use backend base URL from
  env or config.
- Render a **table** with columns for id, name, email, createdAt (or equivalent
  display). Use existing design system (e.g. MUI Table) or semantic HTML table
  with minimal styling.
- **Test during implementation:** Load /customers; assert request to
  /api/customers and table renders with correct columns and rows. Run after
  wiring fetch and table.

### 2. Sortable column headers

- Make at least **name** and **email** (and optionally id, createdAt)
  **sortable**: clicking the header toggles sort direction (asc → desc → asc)
  and re-sorts the list.
- **Sort behavior:** Prefer **server-side** sort if backend supports
  sortBy/order (refetch with query params). Otherwise **client-side** sort the
  fetched list. Document which approach is used.
- **Test during implementation:** Click name header → list sorts by name; click
  again → direction toggles. Same for email. Run after adding sort logic.

### 3. Sort indicator

- Show a **visible sort indicator** (icon or label) on the active sort column
  and direction (e.g. arrow up for asc, arrow down for desc).
- **Test during implementation:** After sort, active column shows indicator;
  direction matches current sort. Run after adding indicator UI.

### 4. Default sort on first load

- Apply a **default sort** on first load (e.g. name ascending). If using
  server-side sort, initial request includes sortBy and order; if client-side,
  sort the list once after fetch.
- **Test during implementation:** First load shows list in default order (e.g.
  name asc). Run after setting default sort.

### 5. Loading state

- While **fetching**, show a **loading state** (e.g. spinner or skeleton)
  instead of empty table or stale data.
- **Test during implementation:** During load (or with slow network), loading UI
  is visible; after load, table appears. Run after adding loading state.

### 6. Empty state

- When the list is **empty** (customers.length === 0), show an **empty state**
  (e.g. "No customers yet" with optional link to create customer).
- **Test during implementation:** With empty API response, empty state is shown
  instead of empty table. Run after adding empty state.

### 7. Error handling

- If the **list request fails**, show a clear **error message** and optional
  **retry** (e.g. button to refetch).
- **Test during implementation:** Simulate failure (e.g. wrong URL or mock);
  error message and retry appear. Run after adding error UI.

### 8. Optional link to edit

- **Optional:** Add per-row link or button to **edit** (e.g. navigate to
  /customers/:id/edit or open edit form with customer id). Integrates with
  CustomerForm (edit flow) when that exists.
- **Test during implementation:** Click edit on a row navigates or opens form
  with correct customer. Run after adding edit link (if in scope).

### 9. Accessibility

- Use semantic table (table, th, td); **sortable headers** are focusable and
  indicate sort state (e.g. aria-sort="ascending" / "descending").
- **Test during implementation:** Keyboard navigate headers; screen reader or
  aXe reports sort state. Run after a11y tweaks.

---

## Testing

### Tests during implementation (per step)

Run these as you complete each step to confirm the step is done correctly:

| Step         | Test during implementation                                                        |
| ------------ | --------------------------------------------------------------------------------- |
| 1. Fetch     | Load /customers → GET /api/customers; table shows columns and rows from response. |
| 2. Sortable  | Click name/email header → list re-sorts; click again → direction toggles.         |
| 3. Indicator | Active sort column shows visual indicator; direction correct.                     |
| 4. Default   | First load shows default sort (e.g. name asc).                                    |
| 5. Loading   | Loading state visible while fetching; table after load.                           |
| 6. Empty     | Empty list shows "No customers" (or similar) and optional create link.            |
| 7. Error     | Failed fetch shows error message and retry.                                       |
| 8. Edit link | (Optional) Edit navigates or opens form with correct customer.                    |
| 9. A11y      | Sort headers focusable; aria-sort set; no critical a11y violations.               |

### Full manual regression

After all steps: load list, sort by each column, toggle direction, loading and
empty and error states, and (if implemented) edit link from row.

### Automated tests (recommended)

- Use **React Testing Library** and possibly **MSW** to mock GET /api/customers.
- **Tests to add:** Renders loading then table; empty response shows empty
  state; error response shows error; click sort updates order (and indicator).
- **Test during implementation:** Run `npm test` and confirm list/table tests
  pass.

---

## Files to touch

- [customer-ui/src/pages/CustomersPage.jsx](customer-ui/src/pages/CustomersPage.jsx)
  — fetch list, table, sort state, loading/empty/error, optional edit link.
- Optional: `customer-ui/src/components/CustomerTable.jsx` (or similar) —
  extract table and sortable headers for reuse/testability.
- [customer-ui/src/App.jsx](customer-ui/src/App.jsx) — ensure /customers route
  and any edit route (e.g. /customers/:id/edit) if added.
- New: **list-table-ui-plan.md** — write this plan into the repo for reference.

---

## Out of scope (per ticket)

- Pagination (separate ticket if list grows)
- Inline editing in table (edit via dedicated form)
- Filtering/search
