This specification covers the implementation of a new **"Mental Health & Wellness Day"** Time Off plan. This is a common global request designed to support employee well-being while ensuring strict controls over accruals and payroll integration.

---

# Part 1: Agile Backlog Item & Governance

### Executive Summary & Business Objective
To support the "Wellness First" corporate initiative, we are introducing a non-accruing, front-loaded "Mental Health & Wellness" (MHW) time off plan. The goal is to provide employees with 2 days (16 hours) per calendar year for mental rejuvenation. This plan must be separate from standard Sick or Vacation time to track utilization and impact on employee retention.

### Downstream Impact Analysis
*   **Payroll:** New Earning Code `MHW_PAID` must be mapped in Workday Payroll (or exported to external providers like ADP/CloudPay). It should be treated as regular taxable income.
*   **Sensitive Personal Information (SPI):** Reason codes for these requests must be restricted. Managers should see "Wellness Day" but not detailed medical notes.
*   **Business Process (BP):** This will leverage the *Absence Request* BP. To minimize administrative burden, requests of 1 day or less will be auto-approved.

### Agile User Story
**As an** eligible Global Employee,
**I want** to request a specific Mental Health & Wellness day in Workday,
**So that** I can take time off to recharge without depleting my standard Vacation or Sick Leave balances.

### Gherkin Acceptance Criteria
*   **Scenario 1: Initial Balance Allocation**
    *   **Given** a Full-Time employee is active on Jan 1st
    *   **When** the "MHW" plan is initialized
    *   **Then** the worker should see a front-loaded balance of 16 hours.
*   **Scenario 2: Auto-Approval Logic**
    *   **Given** an employee has a 16-hour balance
    *   **When** they submit a request for 8 hours (1 day)
    *   **Then** the status should move immediately to "Successfully Completed" without Manager intervention.
*   **Scenario 3: Validation Rule (Insufficient Balance)**
    *   **Given** an employee has 4 hours remaining
    *   **When** they attempt to request 8 hours
    *   **Then** a hard error should trigger: *"Insufficient Wellness Balance. You only have 4 hours remaining."*

---

# Part 2: Workday Technical Configuration Blueprint

### 1. Time Off Plan Definition
*   **Plan Type:** Time Off Plan
*   **Time Off Type:** Mental Health & Wellness
*   **Units:** Hours
*   **Balance Tracked:** Yes
*   **Carryover Limit:** 0 Hours (Use it or lose it at Year End)
*   **Rounding Rule:** Round down to 2 decimal places.

### 2. Eligibility & Validation Rules
*   **Eligibility Rule:** `ER_MHW_Global_Eligible`
    *   *Logic:* (Worker Status = Active) AND (Time Type = Full_Time) AND (Regular/Temporary = Regular).
*   **Validation Rule (Critical):** `VAL_MHW_No_Negative_Balance`
    *   *Logic:* If (Absence_Request_Amount > Remaining_Balance) then "Error".
*   **Validation Rule (Advance Notice):** `VAL_MHW_Backdated_Limit`
    *   *Logic:* Prevent backdating requests more than 3 days in the past.

### 3. Calculation Rules & Accrual Matrix
*   **Accrual Frequency:** Annual (January 1st).
*   **Accrual Type:** Front-loaded.
*   **Calculation Logic:**
    *   Use a **Calculated Field** (Constant Value) of 16.
    *   **Proration:** If Hire Date is after July 1st, grant 8 hours; otherwise 16 hours.

### 4. Business Process (BP) Routing & Approval Logic
*   **BP:** Absence Request
*   **Step A (Entry):** Employee initiates request.
*   **Step B (Condition):** 
    *   *Rule:* `Request_Duration <= 8 hours`.
    *   *Action:* **Auto-Approve** (Skip to Finish).
*   **Step C (Condition):** 
    *   *Rule:* `Request_Duration > 8 hours`.
    *   *Action:* **Approval** by Manager.
*   **Step D (Service):** Update Absence Balance.

### 5. Security Policies & Attachment Privacy Configuration
*   **Domain:** `Self-Service: Absence Requests`
    *   *Permissions:* Employee (View/Modify), Manager (View), HR Partner (View).
*   **Attachment Security:** No attachments allowed for this Time Off Type to prevent storage of HIPAA-protected or sensitive medical notes.

---

# Part 3: Comprehensive UAT Test Matrix

| Test ID | Test Category | Scenario / Description | Test Data / Role | Expected Result | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **UAT-01** | **Happy Path** | FT Employee requests 8 hours MHW. | FT Employee (US) | Request auto-approves; Balance reduces to 8. | Not Run |
| **UAT-02** | **Boundary** | Employee requests 16.01 hours. | FT Employee (UK) | Hard error: "Insufficient Balance." | Not Run |
| **UAT-03** | **Eligibility** | Part-Time employee attempts to select MHW plan. | PT Employee | MHW plan is not visible in the "Time Off Type" dropdown. | Not Run |
| **UAT-04** | **Routing** | Employee requests 2 consecutive days (16 hrs). | FT Employee | BP routes to Manager for approval (does not auto-approve). | Not Run |
| **UAT-05** | **Privacy** | Peer tries to view Wellness balance via Worker Profile. | Peer / Colleague | Balance and Request history hidden from non-related workers. | Not Run |
| **UAT-06** | **Payroll** | Run "Pay Calculation" for worker with MHW taken. | Payroll Partner | Earning Code `MHW_PAID` appears on Payslip for 8 hours. | Not Run |
| **UAT-07** | **Proration** | New Hire (Start Date: Oct 1) checks balance. | New Hire | Balance should be 8 hours (Mid-year proration logic). | Not Run |
| **UAT-08** | **Year-End** | Advance system clock to Jan 1 of next year. | System Admin | Balance resets to 16; 0 hours carryover from previous year. | Not Run |