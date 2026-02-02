---
name: Customer Form Component Plan
overview:
  Implement a reusable CustomerForm component for create and edit flows in
  customer-ui per
  [CUSTOMER-FORM-COMPONENT.md](jira-tickets/CUSTOMER-FORM-COMPONENT.md), with
  shared validation, optional initial values, and clear props/API; document the
  plan in form-component-plan.md.
todos: []
isProject: false
---

# Customer Form Component Implementation Plan

Deliverable: save this plan (steps, testing, tests during implementation) to
**form-component-plan.md** in the project root.

---

## Current state

- **Frontend:** [customer-ui/src/App.jsx](customer-ui/src/App.jsx) — React app
  with routes for `/` and `/customers`;
  [CustomersPage.jsx](customer-ui/src/pages/CustomersPage.jsx) exists. No shared
  customer form component yet.
- **Data shape:** Customer objects have `{ id, name, email, createdAt }`; same
  shape as backend list and GET by id.
- **Jira ticket:**
  [jira-tickets/CUSTOMER-FORM-COMPONENT.md](jira-tickets/CUSTOMER-FORM-COMPONENT.md)
  — requires one form component for create and edit, optional initial values,
  single submit handler with normalized data, validation, accessibility, and
  loading/error states.

---

## Implementation steps

### 1. Component shell and props API

- Create **CustomerForm** (e.g. in
  `customer-ui/src/components/CustomerForm.jsx`).
- Define props: `initialValues` (optional; when absent or null → create mode),
  `onSubmit` (callback receiving normalized customer data), `isLoading`
  (optional boolean), `submitLabel` (optional; e.g. "Create" vs "Save"), `title`
  (optional; for create vs edit heading).
- **Test during implementation:** Render CustomerForm with no initialValues;
  render with initialValues; assert props are passed through. Run after creating
  the component shell.

### 2. Form fields and controlled state

- Add **fields** for customer: `name`, `email` (match backend shape; omit id and
  createdAt in form input, add in submit handler as needed).
- Use **controlled state** (useState or shared hook) for field values; when
  initialValues is provided, seed state from it.
- **Test during implementation:** Change name/email in form; assert state
  updates. With initialValues, assert fields show pre-filled values. Run after
  wiring controlled inputs.

### 3. Validation (shared for create and edit)

- Define **validation rules** once: required `name` (non-empty), required
  `email` (non-empty, valid email format).
- Show **inline or summary errors** (e.g. under fields or at top); do not submit
  until valid.
- **Test during implementation:** Submit with empty name or invalid email;
  assert errors shown and onSubmit not called. Valid submit calls onSubmit with
  correct shape. Run after adding validation.

### 4. Submit handler and normalized payload

- On submit: validate; if invalid, show errors and return. If valid, call
  **onSubmit** with normalized data (e.g. `{ name, email }` for create; for edit
  parent may need `id` — pass via initialValues.id or separate prop as needed).
- Parent (create page vs edit page) is responsible for calling POST or PUT; form
  only emits the payload.
- **Test during implementation:** Submit valid data; assert onSubmit called once
  with correct object (name, email; id/createdAt handled by parent or omitted
  for create). Run after wiring submit.

### 5. Loading and error states

- When **isLoading** is true, disable submit button and optionally show loading
  indicator.
- If component accepts an **error** prop (e.g. API error message), display it
  (e.g. above form or near submit).
- **Test during implementation:** Set isLoading true; assert button disabled.
  Pass error prop; assert message visible. Run after adding loading/error UI.

### 6. Accessibility and UX

- Use semantic form, labels, and inputs; associate labels with inputs (e.g. id +
  htmlFor).
- Ensure submit button and focus order are keyboard-friendly; consider aria-live
  for errors.
- **Test during implementation:** Tab through form; submit with keyboard; check
  screen reader or aXe if available. Run after a11y tweaks.

### 7. Create and Edit usage

- **Create page/flow:** Render CustomerForm with no initialValues (or null),
  submitLabel="Create", onSubmit that calls POST /api/customers and then
  redirects or refreshes list.
- **Edit page/flow:** Load customer (e.g. GET /api/customers/:id), pass data as
  initialValues, submitLabel="Save", onSubmit that calls PUT /api/customers/:id.
- **Test during implementation:** Create flow submits and creates customer; edit
  flow pre-fills and updates customer. Run after wiring both flows (may require
  routes for /customers/new and /customers/:id/edit).

---

## Testing

### Tests during implementation (per step)

Run these as you complete each step to confirm the step is done correctly:

| Step               | Test during implementation                                                             |
| ------------------ | -------------------------------------------------------------------------------------- |
| 1. Component shell | Render with/without initialValues; props (submitLabel, title) render correctly.        |
| 2. Fields & state  | Typing updates state; initialValues pre-fills name and email.                          |
| 3. Validation      | Empty/invalid submit shows errors; valid submit does not show errors.                  |
| 4. Submit payload  | Valid submit calls onSubmit once with { name, email } (and id for edit if applicable). |
| 5. Loading/error   | isLoading disables submit; error prop displays message.                                |
| 6. Accessibility   | Labels associated; keyboard submit works; no critical a11y violations.                 |
| 7. Create/Edit     | Create flow POSTs; edit flow pre-fills and PUTs; list/form stay in sync.               |

### Full manual regression

After all steps: create customer, edit customer, validation errors, loading
state, empty initialValues (create), and with initialValues (edit).

### Automated tests (recommended)

- Use **React Testing Library** (or existing test setup) in customer-ui.
- **Tests to add:** Render with/without initialValues; assert submit payload on
  valid submit; assert validation blocks submit and shows errors.
- **Test during implementation:** Run `npm test` and confirm form tests pass.

---

## Files to touch

- New:
  [customer-ui/src/components/CustomerForm.jsx](customer-ui/src/components/CustomerForm.jsx)
  — form component with props, state, validation, submit.
- Optional: `customer-ui/src/hooks/useCustomerForm.js` (or similar) — shared
  state/validation logic if extracted.
- [customer-ui/src/App.jsx](customer-ui/src/App.jsx) or pages — add routes for
  create/edit and use CustomerForm (e.g. CreateCustomerPage, EditCustomerPage,
  or integrate into CustomersPage).
- New: **form-component-plan.md** — write this plan into the repo for reference.

---

## Out of scope (per ticket)

- Backend API changes (assume create/update endpoints exist)
- List/detail views (only the shared form and its usage in create/edit flows)
