This configuration guide outlines the parameters for a new Georgia-specific Sick Time-Off Plan. This setup is designed for an HRIS (Human Resources Information System) environment such as Workday, SAP SuccessFactors, or UKG.

### **1. General Plan Information**
*   **Plan Name:** Georgia Sick Leave (FT)
*   **Time Off Type:** Sick Leave
*   **Effective Date:** 09/01/2026
*   **Status:** Active
*   **Plan Year:** Calendar Year (or as defined by the organization)

---

### **2. Eligibility Criteria**
To qualify for this plan, a worker must meet all three of the following conditions:
1.  **Primary Work State:** Georgia (GA)
2.  **Worker Type/Status:** Full-Time
3.  **Service Requirement:** 1 Year (12 months) of continuous service.
    *   *Configuration Note:* Set the "Waiting Period" or "Eligibility Date" to *Service Date + 1 Year*.

---

### **3. Accrual Calculation Engine**
This plan uses a "calculated hours worked" model rather than a flat grant.
*   **Accrual Frequency:** Weekly (aligned with the US Weekly Period Schedule).
*   **Accrual Metric:** 1 hour for every 30 hours worked.
*   **Accrual Rate Formula:** `Hours Worked` × `0.03333`
*   **Eligible Earnings/Hours:** All regular hours worked, including overtime (standard GA/FLSA practice).

---

### **4. Limits and Caps**
Strict enforcement of the following rules is required to prevent over-accrual:
*   **Upper Balance Cap:** 150 Hours.
    *   *Rule:* Once a worker’s balance reaches 150 hours, accruals will cease until the balance drops below this threshold.
*   **Usage Limit:** 90 Hours.
    *   *Rule:* Workers may not take more than 90 hours of sick leave within a single plan year, regardless of their total balance.
*   **Carryover Limit:** 50 Hours.
    *   *Rule:* At the end of the plan year (e.g., Dec 31st), any balance exceeding 50 hours will be forfeited (lapsed).

---

### **5. Period Schedule**
*   **Schedule Name:** US Weekly Period Schedule
*   **Run Frequency:** Every 7 days.
*   **Accrual Posting Date:** The day following the end of the weekly pay period (or as per payroll sync).

---

### **6. Legal Compliance Note (Georgia SB 201)**
While Georgia does not mandate paid sick leave, because this company is providing it, it must comply with the **Georgia Kin Care Law (SB 201)**. 
*   **System Requirement:** Ensure the "Time Off Reason" allows for the care of immediate family members (spouse, child, parent, grandparent, or grandchild) if the employee has accrued this time under the plan terms.

---

### **Summary Table for System Entry**

| Field | Value |
| :--- | :--- |
| **Effective Date** | 09/01/2026 |
| **Eligibility Group** | (State: GA) AND (Status: Full-Time) AND (Service >= 1 Year) |
| **Accrual Rate** | 0.03333 per hour worked |
| **Period Schedule** | US Weekly |
| **Max Balance** | 150 Hours |
| **Annual Usage Limit** | 90 Hours |
| **Carryover Limit** | 50 Hours |
| **Carryover Frequency** | Annual (End of Year) |