This configuration guide outlines the setup for a Georgia-specific Sick Time-Off Plan, typically designed for an HRIS (Human Resources Information System) like Workday, SAP SuccessFactors, or UKG.

### **1. Plan General Information**
*   **Plan Name:** GA Paid Sick Leave
*   **Time Off Type:** Sick
*   **Effective Date:** 09/01/2026
*   **Plan Year:** Calendar Year (assuming standard, unless otherwise specified)
*   **Period Schedule:** US Weekly

---

### **2. Eligibility Rules**
To be eligible for this plan, a worker must meet **all** of the following criteria:
*   **Primary Work State:** Georgia
*   **Worker Classification:** Full-Time
*   **Service Requirement:** Minimum 1 year of service (Length of Service $\ge$ 12 months)
    *   *Note: If using a system like Workday, you will create an Eligibility Rule: `(Primary Work State = Georgia) AND (Employee Type = Full-Time) AND (Continuous Service Date <= 1 Year Ago)`.*

---

### **3. Accrual Calculation**
This is a "Hours Worked" based accrual model.
*   **Accrual Frequency:** Per Pay Period (Weekly)
*   **Accrual Rate:** 1 hour for every 30 hours worked.
*   **Calculation Formula:** `Total Hours Worked in Period / 30`
    *   *Decimal equivalent (if needed):* 0.03333 per hour worked.
*   **Accrual Basis:** Regular hours worked (excluding Overtime or Leave of Absence hours, depending on company policy).

---

### **4. Plan Limits & Rules**
| Rule Category | Limit Value | Description |
| :--- | :--- | :--- |
| **Carryover Limit** | 50 Hours | The maximum number of unused hours that can roll over from one plan year to the next. |
| **Upper Balance Cap** | 150 Hours | The maximum balance an employee can hold at any given time. Accruals stop once this ceiling is reached. |
| **Usage Limit** | 90 Hours | The maximum amount of sick time an employee can take within a single plan year. |

---

### **5. Compliance & Georgia Specifics (SB 201)**
While Georgia does not mandate paid sick leave, the **Georgia Family Care Act (SB 201)** applies to employers who *choose* to provide it:
*   **Family Care Requirement:** Since you are offering a sick plan, Georgia law requires that you allow employees to use at least **5 days (or 40 hours)** of their accrued sick leave to care for immediate family members (child, spouse, parent, grandparent, grandchild).
*   **Wait Period:** Your "1 year of service" requirement is compliant as Georgia law does not restrict the length of a waiting period for employer-provided plans.

---

### **6. Configuration Checklist for Admin**
1.  **Define the Time Off Component:** Create "Sick GA" and link it to the "US Weekly" period schedule.
2.  **Define Accrual Rule:** Set the calculation to look at "Hours Worked" for the weekly period and divide by 30.
3.  **Set Carryover Rule:** Configure the "Year-End Processing" to truncate any balance above 50 hours on December 31st (or the end of your plan year).
4.  **Enrollment:** Set the system to auto-enroll workers on the day they hit their 1-year anniversary, provided their work state is Georgia and status is Full-Time.