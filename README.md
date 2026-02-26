# 🚀 Prevent User Deletion if Assigned to an Incident (ServiceNow)

## 📌 Project Overview
In IT Service Management (ITSM) environments, users are often assigned to incidents for resolution and tracking.  
Deleting a user who is still assigned to active incidents can lead to broken references, loss of accountability, and workflow disruption.

This project implements a **ServiceNow Business Rule** that prevents deletion of a user record if the user is assigned to one or more incidents.  
Deletion is allowed only after all assigned incidents are closed or reassigned.

---

## 🎯 Objective
- Prevent accidental deletion of users who are actively assigned to incidents  
- Maintain data integrity in the ITSM system  
- Ensure workflow continuity and accountability  
- Implement server-side validation using ServiceNow Business Rules  

---

## 🛠️ Technology Stack
- **Platform:** ServiceNow (Personal Developer Instance)  
- **Module:** ITSM  
- **Feature Used:** Business Rules  
- **Scripting:** GlideRecord (Server-side JavaScript)  
- **Version Control:** GitHub  
- **Deployment:** Update Sets (XML export/import)

---

## ⚙️ Implementation Details

### 🔹 Business Rule Configuration
- **Name:** Prevent User Deletion if Assigned to an Incident  
- **Table:** `sys_user` (User)  
- **When:** Before  
- **Action:** Delete  
- **Condition:** Triggered on delete operation  
- **Advanced:** Enabled  

### 🔹 Core Logic (Script)
The Business Rule queries the `incident` table to check if the user is assigned to any incidents.  
If at least one incident exists, the deletion is blocked and an error message is shown.

```javascript
(function executeRule(current, previous /*null when async*/) {

    var incGr = new GlideRecord('incident');
    incGr.addQuery('assigned_to', current.sys_id);
    incGr.setLimit(1); // Check if at least one incident exists
    incGr.query();

    if (incGr.next()) {
        gs.addErrorMessage('This user cannot be deleted because they are assigned to one or more incidents.');
        current.setAbortAction(true);
    }

})(current, previous);
