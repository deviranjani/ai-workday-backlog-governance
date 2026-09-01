This specification document outlines the configuration requirements for the new Multi-State Bereavement Time-Off Plan within the Workday Absence Management module.

---

### Part 1: Agile Backlog Item & Governance

**Executive Summary**
This initiative implements a compliant Bereavement leave policy for Full-Time employees residing in Georgia and New York. The plan introduces specific service-based eligibility (90-day cliff) and strict balance controls (Carryover, Caps, and Usage Limits) to align with regional labor standards and corporate policy.

**Impact Analysis**
*   **Compliance:** Ensures GA and NY employees receive allocated time off for grief.
*   **Financial:** Impacts liability accruals; balances are non-payable upon termination.
*   **Downstream:** Impacts Payroll (Earnings codes) and Time Tracking (if integrated).

**User Story**
> **AS A** Full-Time Employee in Georgia or New York, 
> **I WANT** to access a Bereavement Time-Off balance after 90 days of employment, 
> **SO THAT** I can take paid leave during times of loss without exceeding the 40-hour annual usage threshold.

**Gherkin Acceptance Criteria**
*   **Scenario 1: New Hire Eligibility**
    *   **Given** a Full-Time employee is hired in New York;
    *   **When** the employee has completed 89 days of service;
    *   **Then** the Bereavement Plan balance should remain "Not Eligible" or 0.
    *   **But When** the employee reaches 90 days of service;
    *   **Then** the plan should become visible and available for use.
*   **Scenario 2: Termination Forfeiture**
    *   **Given** an employee has a remaining balance of 15 hours;
    *   **When** a "Terminate Employee" business process is completed;
    *   **Then** the Bereavement balance must be adjusted to 0 and not paid out.

---

### Part 2: Workday Technical Configuration Blueprint

| Feature | Configuration Detail |
| :--- | :--- |
| **Plan Name** | Bereavement - GA & NY FT |
| **Time Off Type** | Bereavement |
| **Unit of Time** | Hours |
| **Eligibility Rule** | `(Worker Status = Full-Time) AND (Location State = GA or NY) AND (Continuous Service > 90 Days)` |
| **Period Schedule** | Monthly (e.g., 1st of the month) |
| **Accrual Frequency** | Monthly (As per Period Schedule) |
| **Carryover Limit** | 20 Hours (Applied at Year-End/Plan Year anniversary) |
| **Upper Balance Cap** | 50 Hours (Accruals cease once balance hits 50) |
| **Usage Limit** | 40 Hours per Plan Year |
| **Balance Visibility** | Visible to Employee and Manager |
| **Termination Rule** | **Zero Out Balance:** Assign a "Balance Adjustment" or "Forfeiture" calculation triggered by the Termination Event. |

**Calculation Logic:**
*   **Accrual Calculation:** (Monthly Rate) IF (Service > 90 Days) ELSE 0.
*   **Cap Logic:** `Minimum(Accrued_Balance + New_Accrual, 50)`.
*   **Carryover Logic:** At the start of the new plan year, `Remaining_Balance = Minimum(Current_Balance, 20)`.

**Business Process (BP) Routing:**
1.  **Initiate:** Employee or Manager.
2.  **Review:** Absence Partner (Validation of relationship if required).
3.  **Approval:** Direct Manager.
4.  **Completion:** Integration to Payroll for payment (Non-payout on term).

---

### Part 3: Comprehensive UAT Test Matrix

| Test ID | Category | Scenario | Test Data | Expected Result | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **UAT-01** | Eligibility | Employee in California (Non-Impacted State) | FT Employee, Location: CA | Plan does not appear in the "Time Off" worklet. | Pending |
| **UAT-02** | Waiting Period | New Hire in NY with 45 days of service | Hire Date: [Today - 45 days] | System prevents requesting time off; Balance is 0. | Pending |
| **UAT-03** | Usage Limit | Requesting more than the allowed limit | Employee attempts to book 48 hours | System returns a critical validation error: "Usage exceeds 40-hour limit." | Pending |
| **UAT-04** | Balance Cap | Monthly accrual hitting the ceiling | Balance: 48 hrs; Accrual: 4 hrs | Balance updates to 50 hrs (not 52) due to Upper Balance Cap. | Pending |
| **UAT-05** | Termination | Balance zeroed out on exit | Term Date: [Today]; Balance: 12 hrs | Absence Balance Report shows 0.00 balance as of the day after Term. | Pending |