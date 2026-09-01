This configuration outline follows standard HCM (Human Capital Management) system logic (such as Workday, UKG, or SAP SuccessFactors) to meet the specific requirements for your Georgia sick leave plan.

### **1. Plan Header & Basic Information**
*   **Time Off Plan Name:** GA Sick Leave - FT 1YR
*   **Time Off Type:** Sick
*   **Effective Date:** 09/01/2026
*   **Plan Status:** Active
*   **Period Schedule:** US Weekly (Runs every 7 days)
*   **Unit of Time:** Hours

---

### **2. Eligibility Rules**
For a worker to be enrolled in this plan, they must meet all of the following criteria:
*   **Primary Work State:** Georgia
*   **Worker Classification:** Full-Time
*   **Waiting Period / Service Requirement:** 
    *   **Rule:** Continuous Service $\ge$ 12 Months.
    *   **Reference Date:** Hire Date or Seniority Date.
    *   **Eligibility Start:** On the 1st anniversary of employment.

---

### **3. Accrual Calculation Rule**
This plan uses a "Worked-Based" accrual model rather than a flat grant.
*   **Accrual Frequency:** Per Period (US Weekly).
*   **Accrual Logic:** 
    *   **Calculation:** `Hours Worked` $\div$ `30`.
    *   **Input:** Total hours worked during the weekly pay period (including overtime).
    *   **Rounding:** Recommend rounding to 2 or 4 decimal places to ensure accuracy.

---

### **4. Limits & Carryover Rules**
These rules define the "ceiling" of the plan and how it rolls over annually.
*   **Upper Balance Cap (Maximum Accrual):** 150 Hours.
    *   *Logic:* Once the balance reaches 150 hours, accruals will cease until the worker takes time off and the balance drops below this limit.
*   **Annual Usage Limit:** 90 Hours.
    *   *Logic:* A worker cannot use more than 90 hours of this sick leave within a single plan year, regardless of their total balance.
*   **Carryover Limit:** 50 Hours.
    *   *Logic:* At the end of the plan year (or anniversary year), any balance exceeding 50 hours is forfeited.

---

### **5. Plan Year Definition**
*   **Reset Date:** 01/01 (Calendar Year) **OR** 09/01 (Plan Effective Anniversary).
    *   *Recommendation:* Use 01/01 for standard reporting, but ensure the Carryover Limit logic triggers on this date.

---

### **6. Technical Configuration Summary Table**

| Component | Configuration Detail |
| :--- | :--- |
| **Effective Date** | 09/01/2026 |
| **Work State** | Georgia |
| **Eligibility** | Full-Time AND 1 Year of Service |
| **Period Schedule** | US Weekly |
| **Accrual Rate** | 1 hour per 30 hours worked |
| **Carryover Limit** | 50 Hours |
| **Maximum Balance** | 150 Hours |
| **Usage Limit** | 90 Hours per Year |

---

### **Compliance Note (Georgia Specific)**
While Georgia does not have a state-mandated paid sick leave law for private employers, the **Georgia Family Care Act (SB 201)** applies to employers with 25+ employees who *provide* paid sick leave. 
*   **Configuration Requirement:** Ensure that your "Time Off Reason" codes allow these 90 hours to be used for the care of immediate family members (spouse, child, parent, grandparent, or grandchild), as required by Georgia law if the employer chooses to offer a sick plan.