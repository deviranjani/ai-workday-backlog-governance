Implementing the New York Paid Prenatal Leave (effective Oct 1, 2024) requires a multi-layered configuration in Workday. Below is the technical implementation guide to meet your requirements.

---

### Phase 1: Eligibility & Calculation Rules
Before creating the plan, you must define the logic for who qualifies.

1.  **Create Eligibility Rule:**
    *   **Rule Name:** `ER_NY_Prenatal_Leave_Eligibility`
    *   **Logic:**
        *   `Work Location Hierarchy` in selection `NY_Metro` OR `NY_Upstate`
        *   `Gender` = `Female` (Note: Ensure your legal team reviews this; NY law generally applies to "pregnant employees" regardless of gender marker, but per your requirement, we will use Female).
        *   `Worker Status` = `Active`
        *   `Time Type` = `Full Time` OR `Part Time`
2.  **Proration Calculation:**
    *   **Rule Name:** `CALC_Prenatal_Proration`
    *   **Logic:** Use an Arithmetic Calculation. 
        *   *Formula:* `(20 / 12) * (Months remaining in year including current month)`
        *   *Oct 1 Launch:* For the remainder of 2024, existing employees should receive a prorated amount (approx. 5 hours) or the full 20 hours depending on your legal interpretation of the "effective date."

---

### Phase 2: Time Off Plan Configuration
1.  **Create Time Off Plan:**
    *   **Plan Name:** `NY Paid Prenatal Leave`
    *   **Plan Type:** `Front-loaded`
    *   **Balance Period:** `Calendar Year`
    *   **Units:** `Hours`
2.  **Define Accrual (Front-Load):**
    *   **Accrual Rule:** Create a rule that drops 20 hours on Jan 1.
    *   **Waiting Period:** None (as per requirement).
    *   **Carryover:** 0 hours (NY law typically does not require carryover for this specific leave as it resets annually).
3.  **Time Off Type:**
    *   **Name:** `Prenatal Leave (NY)`
    *   **Mapping:** Ensure this is linked only to the Prenatal Plan.

---

### Phase 3: Business Process (BP) & Auto-Approval Logic
You need to modify the **Request Absence** BP specifically for this Time Off Type.

1.  **Condition Rule for Approval:**
    *   **Rule Name:** `CR_Prenatal_Request_Greater_Than_4_Hours`
    *   **Logic:** `Absence_Hours_Requested` > `4` AND `Absence_Type` = `Prenatal Leave (NY)`.
2.  **BP Step Configuration:**
    *   Locate the **Approval** step assigned to the Manager.
    *   Apply the Condition Rule `CR_Prenatal_Request_Greater_Than_4_Hours`.
    *   **Result:** If a worker requests $\le$ 4 hours, the condition returns *False*, skipping the Manager approval step (Auto-Approve). If > 4 hours, it routes to the Manager.

---

### Phase 4: Payroll Integration (Workday Payroll)
1.  **Earnings Codes:**
    *   Ensure Payroll has created: `NY_PRENATAL_HRLY` and `NY_PRENATAL_SLRY`.
2.  **Mapping:**
    *   Navigate to **View Time Off** > **Payroll Mapping**.
    *   Map the `Prenatal Leave (NY)` Time Off to the respective Earnings Codes.
    *   **Calculation Logic:** Ensure these earnings are configured to be "Taxable" and included in "Gross Pay," but excluded from the "Regular PTO" reduction logic.

---

### Phase 5: Security & Confidentiality
To protect medical privacy and hide attachments from managers:

1.  **Domain Security Policy:**
    *   Review the domain: `Worker Data: Absence Case Attachments`.
    *   **Modify Permissions:** Ensure **Manager** and **Management Chain** are removed from the "View" access.
    *   Ensure **Employee (Self)**, **HR Partner**, and **Benefits Administrator** have "View" and "Modify" access.
2.  **Attachment Category:**
    *   Create a specific Attachment Category: `Prenatal_Medical_Documentation`.
    *   In the **Request Absence BP**, configure the "Upload Attachment" step to require this specific category.
    *   Restrict the visibility of this category to HR and Benefits roles only.

---

### Phase 6: Implementation Checklist for Oct 1
*   **One-Time Load:** Since the plan starts mid-year (Oct 1), you cannot wait for the Jan 1 front-load. Use the **Override Balance** or **Inbound EIB** to load the prorated hours for all eligible NY employees effective 10/01/2024.
*   **Validation Rule:** (Optional) Add a validation rule to prevent employees from selecting this leave if their work location is not in NY, as a secondary fail-safe.
*   **Testing:** 
    *   Test a 3-hour request (verify auto-approval).
    *   Test a 5-hour request (verify manager routing).
    *   Test an attachment upload (verify manager cannot see the file).
    *   Run a "Pay Calculation" for a test employee to ensure the `NY_PRENATAL` code appears on the results.