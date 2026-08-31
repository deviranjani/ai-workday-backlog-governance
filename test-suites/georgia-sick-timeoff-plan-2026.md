To configure this plan in an HRIS (Human Capital Management) system such as Workday, SAP SuccessFactors, or Oracle HCM, use the following specifications.

### 1. Plan Definition & Metadata
*   **Time Off Plan Name:** GA Sick Leave - Full Time
*   **Time Off Type:** Sick
*   **Effective Date:** 09/01/2026
*   **Period Schedule:** US Weekly
*   **Unit of Time:** Hours

### 2. Eligibility Rules
The plan must only be visible/applicable to workers who meet **all** three criteria:
1.  **Primary Work State:** Georgia (GA)
2.  **Worker Type:** Full-Time (Exclude Part-Time, Seasonal, and Contractors)
3.  **Service Requirement:** 1 Year of Service. 
    *   *Configuration Logic:* Use a "Waiting Period" or "Eligibility Bridge" where `Continuous Service Date <= (Today's Date - 1 Year)`.

### 3. Accrual Calculation
This is a "Hours Worked" based accrual rather than a flat grant.
*   **Accrual Frequency:** Every Weekly Period (matching the US Weekly schedule).
*   **Accrual Rate:** 1 hour for every 30 hours worked.
*   **Formula Logic:** `[Total Hours Worked in Period] / 30`
*   **Rounding:** Recommend rounding to 2 or 3 decimal places to ensure accuracy.

### 4. Limits & Caps
These rules prevent over-accumulation and excessive usage.
*   **Carryover Limit:** 50 Hours.
    *   *Logic:* At the end of the Plan Year (usually Dec 31st or the anniversary date), any balance exceeding 50 hours is forfeited.
*   **Balance Cap (Upper Limit):** 150 Hours.
    *   *Logic:* Once an employee’s total balance reaches 150 hours, accruals stop immediately. Accruals resume only when the balance drops below this threshold.
*   **Usage Limit:** 90 Hours.
    *   *Logic:* An employee cannot take more than 90 hours of sick time within a single Plan Year, regardless of their available balance.

### 5. Configuration Summary Table

| Field | Configuration Value |
| :--- | :--- |
| **Effective Date** | 09/01/2026 |
| **Eligibility: State** | Georgia |
| **Eligibility: Employment** | Full-Time Workers only |
| **Eligibility: Service** | Minimum 1 year of service |
| **Accrual Rate** | 1 hour per 30 worked |
| **Accrual Period** | Weekly (US Weekly) |
| **Carryover Max** | 50 Hours |
| **Accrual Ceiling (Cap)** | 150 Hours |
| **Annual Usage Limit** | 90 Hours |

### 6. Compliance Note (Georgia Specific)
While Georgia does not currently mandate paid sick leave at the state level (unlike CA or NY), this plan complies with the **Georgia Kin Care Law (SB 201)**. Under this law, because you are providing a sick leave plan, you must allow employees to use their accrued sick leave (up to 5 days or a reasonable equivalent) to care for immediate family members, provided the employee works at least 30 hours per week.

**Next Steps for System Admins:**
1.  **Create Eligibility Rule:** Define the "GA FT + 1Yr Service" rule.
2.  **Create Accrual Calculation:** Map the "Hours Worked" pay component to the division factor of 30.
3.  **Validation Rule:** Add a validation error if a worker attempts to enter a time-off request that exceeds 90 hours in the plan year.