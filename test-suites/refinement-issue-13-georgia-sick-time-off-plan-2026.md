This configuration outline is designed for implementation in a standard Human Capital Management (HCM) system (such as Workday, SAP SuccessFactors, or Oracle HCM).

### **Plan Profile: Georgia Sick Leave**

| Field | Configuration Value |
| :--- | :--- |
| **Plan Name** | GA Sick Leave - Full Time |
| **Time Off Type** | Sick |
| **Effective Date** | 09/01/2026 |
| **Plan Term** | Calendar Year (or Plan Year starting 09/01) |
| **Period Schedule** | US Weekly |

---

### **1. Eligibility Rules**
To receive this plan, an employee must meet **all** of the following criteria:

*   **Primary Work State:** Georgia (GA).
*   **Worker Status:** Full-Time.
*   **Length of Service:** Minimum of 1 year (12 months) from Hire Date/Continuous Service Date.
    *   *Note: In most systems, you will create an Eligibility Rule: `(Length of Service >= 12 Months) AND (Worker Status = Full-Time) AND (Work State = Georgia)`.*

---

### **2. Accrual Configuration**
The accrual is based on actual hours worked rather than a flat rate.

*   **Accrual Method:** Hours Worked.
*   **Accrual Rate:** 1 hour for every 30 hours worked.
*   **Calculation Logic:** `[Total Hours Worked in Period] / 30`.
*   **Accrual Frequency:** Weekly (aligned with the US Weekly period schedule).
*   **Carryover Limit:** 50 hours.
    *   *At the end of the plan year, any balance exceeding 50 hours will be forfeited.*
*   **Upper Balance Cap:** 150 hours.
    *   *Once a worker reaches 150 hours in their "bank," accruals will stop until they use time and their balance falls below this threshold.*

---

### **3. Usage Rules**
*   **Annual Usage Limit:** 90 hours.
    *   *The system will prevent a user from taking more than 90 hours of sick leave within a single plan year, regardless of their total balance.*
*   **Minimum Increment:** Usually 0.5 or 1 hour (as per company policy).
*   **Waiting Period:** None (since eligibility already requires 1 year of service).

---

### **4. System Calculation Examples**

| Scenario | Hours Worked | Accrual Calculation | Result |
| :--- | :--- | :--- | :--- |
| **Standard Week** | 40 Hours | 40 / 30 | 1.33 Hours |
| **Overtime Week** | 50 Hours | 50 / 30 | 1.66 Hours |
| **At Cap** | 40 Hours | Balance is 150 | 0.00 Hours (Cap Hit) |

---

### **5. Compliance & Implementation Notes**
*   **Georgia Law Context:** While Georgia does not currently mandate paid sick leave for all private employers, the **Georgia Healthy Family Act** requires that if an employer *does* provide sick leave, they must allow employees to use up to 5 days (typically 40 hours) to care for immediate family members. Your 90-hour usage limit and 150-hour cap exceed these minimum standards.
*   **Waiting Period Logic:** Ensure your system's "Eligibility Date" is set to `Hire Date + 1 Year`. If a worker moves from Part-Time to Full-Time, the 1-year service clock should typically be checked against their original hire date unless your policy specifies "Time in Grade."
*   **Effective Date:** Since the effective date is 09/01/2026, ensure the "Year-End Processing" logic is configured to handle the partial first year (Sept–Dec 2026) if your plan runs on a standard Calendar Year.