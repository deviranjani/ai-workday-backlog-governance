To implement these requirements in Workday, you will need to coordinate between **Compensation Grid configuration**, **Eligibility Rules**, and **Business Process (BP) Administration**.

Below is the step-by-step configuration guide to meet these requirements.

---

### 1. Define Eligibility for the 5% Merit Bump
You need to isolate the specific population that qualifies for the extra cap.

*   **Create a Compensation Eligibility Rule:**
    *   **Condition 1:** `Job Family` in the selection list `Tech_Services`.
    *   **Condition 2:** `Certification` (Level 2 Safety) has a `Completion Date` on or before `June 30, [Current Year]`.
    *   *Note:* You may need a Calculated Field (Lookup Related Value) to pull the "Completion Date" from the Worker’s Certification profile if the standard field isn't available in the Merit eligibility context.

### 2. Configure the Merit Plan Guidelines
To ensure the 5% cap increase is applied specifically to this group:

*   **Option A (Guideline Matrix):** Add a row to your Merit Guideline Matrix where the criteria is the Eligibility Rule created above. Set the "Max" guideline 5% higher than the standard tech tech cap.
*   **Option B (Override):** If you use a flat merit plan, use the **"Override Rules"** section within the Merit Plan. Add the Safety Eligibility Rule and define the 5% increased guideline there.
*   **Why this works:** By embedding this in the *Merit* plan rather than a *Bonus* plan, the system treats the increase as a base salary adjustment, keeping it clear of bonus-specific approval workflows.

---

### 3. Preventing the Executive Bonus Approval Chain
The "Executive Bonus" approval chain is typically triggered by one of three things: the **Plan Type**, the **Worker’s Grade**, or the **BP Entry Conditions**.

*   **Plan Type Segregation:** Ensure the 5% increase is categorized as **Merit**, not a **Bonus/One-Time Payment**. Most Executive Bonus BPs are triggered by the `Request Bonus` or `Request One-Time Payment` events.
*   **Check BP Entry Conditions:** Go to **Business Process Strategy** > **Edit Definition** > **Initiate Compensation Review**.
    *   Look for a step labeled "Executive Approval." 
    *   Ensure the "Entry Condition" for that step is restricted to `Management Level = Executive` or `Job Profile in Executive Group`. 
    *   Since your workers are in the `Tech_Services` job family, they will not meet the criteria for an Executive-level review.

---

### 4. Verifying the $3,000 Secondary VP Skip Rule
You must verify the logic in the **Compensation Review Business Process** (specifically the sub-process for Propose Compensation Change if handled during a focal cycle).

*   **How to Verify:**
    1.  Search for **Business Process Definition: Compensation Review**.
    2.  Locate the step assigned to the **Secondary VP** (or the step immediately following the Manager/Director approval).
    3.  Click on the **Condition** column for that step.
    4.  **The Rule should look like this:**
        *   `Proposed Amount - Current Amount > 3000` (Currency: USD) 
        *   *OR* `Total Proposed Increase Percent > [X]%`
    5.  **Validation:** If the rule says *"Proposed Change Amount < 3,000 -> Skip Step,"* then any Tech Service worker receiving an annual bump less than $3,000 will automatically bypass the Secondary VP and move to the next stage (usually HR or Payroll).

---

### Summary Checklist for the Admin
1.  **Calculated Field:** Verify the "Level 2 Safety Cert" date is pulling correctly into the Merit process.
2.  **Eligibility Rule:** Name it `Tech_Services_Safety_Level2_Qualified`.
3.  **Merit Matrix:** Update the Tech_Services row to reference the new eligibility rule.
4.  **BP Audit:** View "Compensation Review" BP definition; confirm the condition on the VP step is `Amount >= 3000`.
5.  **Test Case:** Run a "Compensation Review Simulation" for one Tech worker with a $2,500 raise and one with a $3,500 raise to ensure the BP routes correctly.