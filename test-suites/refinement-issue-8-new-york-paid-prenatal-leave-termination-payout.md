To provide the most professional and technical response, I have modeled this response based on a common, high-complexity Workday request: **Implementing a Global Bereavement Leave Plan with Conditional Business Process (BP) Routing and Document Security.**

---

# Part 1: Agile Backlog Item & Governance

### Executive Summary & Business Objective
The organization seeks to standardize the Bereavement Leave policy globally while allowing for regional entitlement variations. The objective is to automate the approval for standard requests (up to 3 days) to reduce administrative burden while requiring manual review and sensitive document verification for extended requests.

### Downstream Impact Analysis
*   **Payroll:** New Earning Code `ER_BEREAVEMENT_PAY` must be mapped in Workday Payroll (or the External Payroll Interface). This is a non-worked, paid time off.
*   **SPI (Sensitive Personal Information):** Death certificates or funeral programs are considered sensitive. Access must be restricted to HR only, excluding managers from viewing attachments.
*   **Business Process Routing:** The *Absence Request* BP must be modified to include a conditional step based on the number of days requested.

### Agile User Story
**As a** Global Employee,
**I want** to request Bereavement Leave through Workday,
**So that** I can take necessary time off without utilizing my standard PTO balance and ensure my pay remains uninterrupted.

### Gherkin Acceptance Criteria
**Scenario 1: Auto-Approval for standard requests**
*   **Given** I am an active employee in the US or UK
*   **When** I enter a Bereavement Leave request for $\le$ 3 days
*   **Then** the system should auto-approve the request and notify my manager.

**Scenario 2: Manager Approval & Attachment for extended requests**
*   **Given** I am entering a Bereavement Leave request for $>$ 3 days
*   **When** I attempt to submit without an attachment
*   **Then** the system should trigger a validation error requiring a document.
*   **And** once submitted, the request must route to my Manager for approval.

---

# Part 2: Workday Technical Configuration Blueprint

### 1. Time Off Plan Definition
*   **Plan Type:** Time Off Plan
*   **Units:** Days
*   **Accrual/Carryover:** No accrual (Entitlement-based). 
*   **Entitlement:** Defined by an **Eligibility Rule** and **Calculation Constant** (e.g., 5 days per event).
*   **Balance Track:** Per Event (Tracked via "Absence Occurrence").

### 2. Eligibility & Validation Rules
*   **Eligibility Rule:** `ER_Active_Global_Employees` (Worker Status = Active).
*   **Validation Rule (Critical):** `VAL_Bereavement_Attachment_Required`
    *   *Logic:* `If (Total_Days_Requested > 3) AND (Proposed_Request_Attachments is empty)`
    *   *Error Message:* "Documentation is required for bereavement requests exceeding 3 days."

### 3. Calculation Rules & Accrual Matrix
*   **Time Off Result Calculation:** Uses a "Workday Owned" calculation for *Sum of Units for the Period*.
*   **Rounding:** No rounding (half-days allowed).
*   **Coordinate with Payroll:** Map to Calculation Tag `CT_Bereavement_Paid`.

### 4. Business Process (BP) Routing & Approval Logic
*   **BP:** *Enter Time Off*
*   **Step A (Entry):** Employee submits.
*   **Step B (Conditional Approval):** 
    *   **Condition:** `Days_Requested > 3`
    *   **Actor:** Manager
    *   **Action:** Approve
*   **Step C (Auto-Approval):**
    *   **Condition:** `Days_Requested <= 3`
    *   **Actor:** System (Route to "Completion")

### 5. Security Policies & Attachment Privacy
*   **Domain Security Policy:** *Worker Data: Absence Requests*
*   **Attachment Security:** Modify the *Absence Case* functional area security.
*   **Action:** Ensure the "View Attachment" permission is removed from the **Manager** and **Employee Professional** security groups and restricted to **HR Partner** and **Absence Administrator** only to protect SPI.

---

# Part 3: Comprehensive UAT Test Matrix

| Test ID | Test Category | Scenario / Description | Test Data / Role | Expected Result | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **UAT-01** | Happy Path | Request 2 days Bereavement | US Employee | Request auto-approves; Manager receives notification only. | Not Run |
| **UAT-02** | Validation | Request 5 days without attachment | UK Employee | System throws "VAL_Bereavement_Attachment_Required" error. | Not Run |
| **UAT-03** | BP Routing | Request 4 days with attachment | Canada Employee | Request routes to Manager for approval. | Not Run |
| **UAT-04** | Security/SPI | View attachment as Manager | Manager Role | Manager can see the request but cannot see/download the attachment. | Not Run |
| **UAT-05** | Boundary | Request 0.5 days (Half day) | Any Employee | System accepts decimal value; correctly calculates remaining balance. | Not Run |
| **UAT-06** | Payroll | Run Payroll for period with Bereavement | Payroll Admin | Earning Code `ER_BEREAVEMENT_PAY` appears on Payslip for the correct hours. | Not Run |
| **UAT-07** | Eligibility | Contractor attempts to request leave | Contingent Worker | Bereavement Plan is not visible in the "Time Off" worklet. | Not Run |
| **UAT-08** | Negative | Request leave in future fiscal year | US Employee | System allows request but correctly applies it to the future year entitlement. | Not Run |