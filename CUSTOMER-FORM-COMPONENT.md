# Reusable Customer Form Component

**Type:** Story / Task  
**Priority:** Medium  
**Component:** customer-ui

---

## Summary

Create a reusable customer form component that can be used for both **creating
new customers** and **editing existing customers**, avoiding duplicate form
logic and UI.

---

## Description

Implement a single, shared customer form component that:

- Renders the same fields and validation for both create and edit flows
- Accepts optional initial values (for edit mode); when absent or null, behaves
  as create mode
- Emits a single submit handler with normalized customer data so parent views
  (create page vs edit page) can call the appropriate API
- Is self-contained for validation, accessibility, and loading/error states
  where appropriate

---

## Acceptance Criteria

- [ ] **Reusable component**  
      One form component (e.g. `CustomerForm`) is used by both "Create Customer"
      and "Edit Customer" screens.

- [ ] **Create mode**  
      When no customer id / initial data is passed (or create-only route), form
      shows empty fields and submit creates a new customer.

- [ ] **Edit mode**  
      When initial customer data is passed (e.g. from route/API), form is
      pre-filled and submit updates the existing customer.

- [ ] **Shared validation**  
      Validation rules (required fields, formats, etc.) are defined once and
      apply in both create and edit.

- [ ] **Consistent UX**  
      Same layout, labels, and behavior in both modes; only title/button text
      (e.g. "Create" vs "Save") may vary via props.

- [ ] **Clear API**  
      Component API is documented (props: e.g. `initialValues`, `onSubmit`,
      `mode` or `isEdit`; events/callbacks).

---

## Technical Notes

- Consider props such as: `initialValues`, `onSubmit`, `isLoading`, and optional
  `submitLabel` / `title` for create vs edit.
- Keep form state and validation inside the component or a shared hook so create
  and edit pages stay thin.
- Ensure the component is easy to unit test (e.g. render with/without initial
  values, assert submit payload).

---

## Out of Scope

- Backend API changes (assume create/update endpoints exist or are separate
  tickets)
- List/detail views; this ticket is only for the shared form component and its
  usage in create/edit flows

---

## Labels

`frontend` `component` `forms` `customer-ui` `reusable`
