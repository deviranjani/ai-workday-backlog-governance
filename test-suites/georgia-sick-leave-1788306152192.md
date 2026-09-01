This configuration guide outlines the steps to set up a **Multi-State Bereavement Time-Off Plan** tailored for Full-Time employees in Georgia and New York.

### **1. Basic Plan Configuration**
*   **Plan Name:** Multi-State Bereavement Plan
*   **Time Off Type:** Bereavement
*   **Unit of Time:** Hours
*   **Period Schedule:** Monthly (Accruals or resets occur on the 1st of each month or the anniversary date).

---

### **2. Eligibility Rules**
To ensure only the correct employees can access this plan, you must build a composite eligibility rule:

*   **Rule Logic:** `(Worker Type = Full-Time) AND (Location = Georgia OR New York) AND (Length of Service >= 90 Days)`
*   **Waiting Period Details:** 
    *   **Reference Date:** Hire Date.
    *   **Offset:** 90 Days.
    *   **Result:** The plan will not appear in the employee’s self-service portal, and accruals will not begin until the 91st day of employment.

---

### **3. Accrual & Balance Rules**
This section defines how time is earned and capped.

*   **Accrual Frequency:** Monthly.
*   **Upper Balance Cap (Ceiling):** 50 Hours.
    *   *System Logic:* Once an employee’s balance reaches 50 hours, the system will stop calculating new accruals until the balance drops below this threshold.
*   **Usage Limit:** 40 Hours per Plan Year.
    *   *System Logic:* Prevents employees from taking more than 40 hours of bereavement within a single year, even if their total balance is higher (up to the 50-hour cap).

---

### **4. Carryover & Year-End Processing**
*   **Carryover Limit:** 20 Hours.
    *   *System Logic:* On the last day of the plan year (e.g., Dec 31st), any balance exceeding 20 hours will be forfeited. 
    *   *Example:* If an employee has 35 hours on Dec 31st, 15 hours are removed, and 20 hours "carry over" to Jan 1st.

---

### **5. Termination Policy**
*   **Termination Rule:** Zero out balance.
*   **Configuration:** 
    *   Set the **Balance Payout** to $0 or "No Payout."
    *   Configure a "Balance Adjustment on Termination" to automatically forfeit all remaining hours upon the effective date of termination. 
    *   *Compliance Note:* Georgia and New York law generally do not require the payout of bereavement leave unless specified in a collective bargaining agreement or an explicit company contract.

---

### **Summary Table for HRIS Entry**

| Field | Configuration Value |
| :--- | :--- |
| **Plan Type** | Bereavement |
| **Eligibility** | FT + (GA or NY) + 90 Days Service |
| **Unit** | Hours |
| **Accrual Frequency** | Monthly |
| **Balance Ceiling (Cap)** | 50 Hours |
| **Annual Usage Limit** | 40 Hours |
| **Carryover Max** | 20 Hours |
| **Termination Rule** | Forfeit / No Payout |

---

### **Recommended Implementation Steps**
1.  **Create Eligibility Group:** Filter by State (GA, NY) and Employee Class (FT).
2.  **Define Waiting Period Rule:** Create a "90-day Hire Date" seniority calculation.
3.  **Configure Accrual Engine:** Set the monthly grant rate (e.g., if you want them to reach the 50-hour cap in one year, set the rate to 4.17 hours/month).
4.  **Set Validation Rules:** Add a "Usage Validation" to the time-entry calendar to prevent requests exceeding 40 hours per year.
5.  **Test Case:** Run a "New Hire" simulation for an employee in Georgia to ensure the plan remains invisible until Day 91.