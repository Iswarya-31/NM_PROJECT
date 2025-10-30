# NM_PROJECT

🧩 ServiceNow Project — Role-Based Access & Flow Automation

 📘 Project Overview

This project demonstrates **role-based access control (RBAC)** and **flow automation** in ServiceNow.
It uses custom tables, roles, and ACL configurations to manage data visibility, field accessibility, and workflow automation for a simulated project management system.



 🧱 Tables Used

# 1. **u_project_table**

* Stores project-level details.
* Accessible only to users with:

  * `project_member`
  * `u_project_table_user` roles.

# 2. **u_task_table**

* Contains individual task records linked to projects.
* Accessible to:

  * `project_member`
  * `u_task_table_user`
  * `team_member`


👥 Roles and Users

| User        | Roles                                                         | Permissions                                                 |
| ----------- | ------------------------------------------------------------- | ----------------------------------------------------------- |
| **Alice P** | `project_member`, `u_task_table_user`, `u_project_table_user` | Full project & task access; also approver in flow           |
| **Bob**     | `team_member`, `u_task_table_user`                            | Limited task access (only “Status” and “Comments” editable) |



🔐 Access Control (ACL) Configuration

# Step 1: Table-Level ACLs

1. Go to **System Security → Access Control (ACL)**.
2. Create **read**, **write**, **create**, and **delete** ACLs for both:

   * `u_project_table`
   * `u_task_table`
3. Add required roles in **Requires Role**:

   * For `u_project_table`: `project_member`, `u_project_table_user`
   * For `u_task_table`: `project_member`, `u_task_table_user`, `team_member`


# Step 2: Field-Level ACLs (for `u_task_table2`)

Purpose: Restrict *Bob* (team member) to only edit **Status** and **Comments**.

| Field           | Access | Roles Allowed                      | Description       |
| --------------- | ------ | ---------------------------------- | ----------------- |
| `u_status`      | Write  | `team_member`, `u_task_table_user` | Editable          |
| `u_comments`    | Write  | `team_member`, `u_task_table_user` | Editable          |
| `u_assigned_to` | Write  | `project_member`                   | Read-only for Bob |
| `u_task_id`     | Write  | `project_member`                   | Read-only for Bob |
| `u_task_name`   | Write  | `project_member`                   | Read-only for Bob |
| `u_due_date`    | Write  | `project_member`                   | Read-only for Bob |

✅ After applying ACLs, impersonate *Bob* — you should see only **Status** and **Comments** editable; others greyed out.


⚙️ Flow Designer Automation

# Objective:

Automatically update the task **Status** to *Completed* and send approval to *Alice P* when specific conditions are met.

#### Steps:

1. Navigate to **Flow Designer → New Flow**
2. **Flow Name:** `task table`
3. **Trigger:**

   * Table: `u_task_table`
   * Condition:

     * `Status` **is** `In Progress`
     * `Comments` **is** `Feedback`
     * `Assigned To` **is** `Bob`
4. **Action 1:** Update Record

   * Table: `u_task_table`
   * Field: `Status = Completed`
5. **Action 2:** Ask for Approval

   * Approver: `Alice P`
   * Approve field: `Status`

✅ **Test the flow** using the *Test* option → Approval request appears in *My Approvals* for Alice → On approval, status changes to *Completed*.


 🧠 Key Learnings

* Implementing **record- and field-level ACLs** for different roles.
* Using **Flow Designer** to automate task progression.
* Understanding **impersonation testing** for role validation.
* Managing **ServiceNow roles**, **conditions**, and **approval processes**.



🧾 Expected Outputs

| User        | View               | Editable Fields                       | Status Change             |
| ----------- | ------------------ | ------------------------------------- | ------------------------- |
| **Admin**   | All fields         | All                                   | Manual/Flow               |
| **Alice P** | All fields         | All                                   | Approves via My Approvals |
| **Bob**     | All fields visible | Only *Status* and *Comments* editable | Auto-updates via Flow     |


 🏁 Conclusion

This project successfully demonstrates **access control**, **role-based security**, and **workflow automation** in ServiceNow, simulating real-world enterprise task management.


 📂 Files / Deliverables

* `u_project_table` and `u_task_table` schema
* ACL Configuration entries
* Flow Designer export 

  * Impersonation (Bob → limited access)
  * Approval request (Alice)
  * Flow Designer “Test” results showing Status = Completed
