This configuration outline follows standard Human Capital Management (HCM) setup logic (applicable to systems like Workday, Oracle, or UKG).

### **Time-Off Plan Profile: Georgia Sick Leave**

| Field | Configuration Value |
| :--- | :--- |
| **Plan Name** | GA Sick Leave (Weekly) |
| **Time Off Type** | Sick |
| **Effective Date** | 09/01/2026 |
| **Period Schedule** | US Weekly |
| **Plan Year** | Calendar Year (or as defined by the Period Schedule) |

---

### **1. Eligibility Rules**
For a worker to be enrolled in this plan, they must meet **all** the following criteria:

*   **Primary Work State:** Georgia (GA)
*   **Worker Classification:** Full-Time
*   **Waiting Period:** 1 Year of Service (calculated from Hire Date or Seniority Date).
    *   *Logic:* Eligibility Date = Hire Date + 1 Year.

---

### **2. Accrual Calculation (Hours Worked Based)**
The system will calculate accruals at the end of every **US Weekly** period.

*   **Accrual Logic:** 1 hour accrued for every 30 hours worked.
*   **Calculation Engine:**
    *   `Sum of [Reported Hours] / 30`
*   **Rounding:** (Optional) Recommend rounding to 2 or 3 decimal places to ensure accuracy over the 30:1 ratio.
*   **Accrual Frequency:** Weekly (aligned with the period schedule).

---

### **3. Balance Management & Limits**
These rules prevent over-accumulation and excessive carry-forward.

| Rule Type | Limit Value | Description |
| :--- | :--- | :--- |
| **Upper Balance Cap** | 150 Hours | Total balance can never exceed 150 hours. Accruals will pause once this ceiling is hit. |
| **Carryover Limit** | 50 Hours | At the end of the Plan Year, any balance above 50 hours is forfeited. |
| **Usage Limit** | 90 Hours | Employees may not take more than 90 hours of sick time within a single Plan Year. |

---

### **4. System Logic Summary (Workflow)**
1.  **Effective 09/01/2026:** The plan becomes active.
2.  **On-Cycle Processing:** Every week, the system checks "Hours Worked" for Full-Time employees in Georgia.
3.  **Waiting Period:** If the employee has reached their 1-year anniversary, the system begins calculating the 1:30 accrual.
4.  **Threshold Check:** 
    *   If current balance is < 150, add weekly accrual.
    *   If current balance is ≥ 150, accrual = 0.
5.  **Usage Check:** When an employee requests time off, the system validates that total "Sick Taken" in the current year is ≤ 90 hours.
6.  **Year-End Process:** On the final day of the plan year, the system runs a "Carryover Rule":
    *   `New Balance = Min(Current Balance, 50)`

---

### **Important Compliance Note for Georgia:**
While Georgia does not currently have a state-mandated paid sick leave law for all private employers, the **Georgia Kin Care Law** (O.C.G.A. § 34-1-10) requires that if an employer *provides* paid sick leave, they must allow employees to use up to 5 days (or their accrued equivalent) to care for immediate family members. Ensure your Time-Off Type "Sick" is configured to allow for "Family Care" to remain compliant with Kin Care regulations.