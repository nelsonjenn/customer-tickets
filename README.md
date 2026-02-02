# jira-tickets

Specs and implementation plans for the Customers project. Each ticket describes
acceptance criteria and scope; each plan in `plans/` breaks the work into steps
and testing.

## Tickets (specs)

| File                                                       | Summary                                                      |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| [CUSTOMER-CRUD-API.md](./CUSTOMER-CRUD-API.md)             | Create & update API (POST, GET by id, PUT) — **customer-be** |
| [CUSTOMER-FORM-COMPONENT.md](./CUSTOMER-FORM-COMPONENT.md) | Reusable customer form for create/edit — **customer-ui**     |
| [CUSTOMER-LIST-API.md](./CUSTOMER-LIST-API.md)             | List customers API with optional sort — **customer-be**      |
| [CUSTOMER-LIST-TABLE-UI.md](./CUSTOMER-LIST-TABLE-UI.md)   | Sortable customers table view — **customer-ui**              |

## Plans

Implementation plans live in **`plans/`**. Each plan follows the same structure:
current state, implementation steps (with tests during implementation), testing,
and files to touch.

| Plan                                                                                       | Covers                               |
| ------------------------------------------------------------------------------------------ | ------------------------------------ |
| [customer_crud_api_plan_ea0ff450.plan.md](./plans/customer_crud_api_plan_ea0ff450.plan.md) | CRUD API (create, get by id, update) |
| [customer_form_component_plan.plan.md](./plans/customer_form_component_plan.plan.md)       | Reusable CustomerForm component      |
| [customer_list_api_plan.plan.md](./plans/customer_list_api_plan.plan.md)                   | GET /api/customers with sort params  |
| [customer_list_table_ui_plan.plan.md](./plans/customer_list_table_ui_plan.plan.md)         | Sortable list table UI               |

Use the tickets for acceptance criteria and out-of-scope; use the plans for
step-by-step implementation and testing.
