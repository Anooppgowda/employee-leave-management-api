# employee-leave-management-api
A secure REST API for managing employee leave — JWT authentication with role-based access (Admin/Manager/Employee), leave request approval workflow, automatic leave balance tracking, and filterable reports. Built with FastAPI, SQLAlchemy, and MySQL.
## How It Works

1. **Authentication** — Every user (Admin, Manager, Employee) logs in via `/auth/login` and receives a JWT token. All other endpoints require this token in the `Authorization: Bearer <token>` header.

2. **Employee Setup** — An Admin creates Employee and Manager accounts via `/employees/`, optionally assigning each employee a `manager_id` to establish reporting hierarchy.

3. **Leave Types** — An Admin defines leave categories (Casual, Sick, Earned, etc.) via `/leave-types/`, each with a maximum days-per-year allowance.

4. **Applying for Leave** — An Employee applies for leave via `/leave-requests/`. The system automatically creates a leave balance record (if one doesn't exist for that year) and checks the employee has enough remaining days before accepting the request. New requests start in `pending` status.

5. **Approval Workflow** — A Manager can approve/reject leave requests only for employees who report to them; an Admin can act on any request.
   - **Approve** (`PUT /leave-requests/{id}/approve`) → deducts `days_count` from the employee's leave balance for that leave type/year.
   - **Reject** (`PUT /leave-requests/{id}/reject`) → no balance change.

6. **Edit / Cancel** — Employees can edit or cancel their own request only while it's still `pending`. Cancelling an already-approved request restores the deducted days back to their balance.

7. **Leave Balance Tracking** — `/leave-balance/me` shows the logged-in user's remaining days per leave type; Admins can manually adjust an employee's total allotted days via `/leave-balance/{id}` (e.g. for carry-forward days).

8. **Reports** — Admins/Managers can query `/reports/leave-summary`, filtering by employee search, department, status, leave type, and date range, with pagination for large result sets.
