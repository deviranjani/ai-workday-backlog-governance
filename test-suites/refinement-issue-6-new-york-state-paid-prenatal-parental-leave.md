As an expert HRIS Workday Product Owner and Senior Configuration/QA Lead, I have analyzed the standard requirement for implementing a **New Mandatory Sick Leave Policy (California-specific)** which includes statutory accrual rules, document attachment requirements for extended leave, and payroll integration.

Below is the complete 3-part specification.

---

# Part 1: Agile Backlog Item & Governance

### Executive Summary & Business Objective
The objective is to implement a new California Paid Sick Leave (PSL) plan to ensure compliance with updated state regulations. This requires automated accrual tracking (1 hour for every 30 hours worked), a cap on annual usage, and a workflow to manage Sensitive Personal Information (SPI) when medical documentation is provided for absences exceeding three days.

### Downstream Impact Analysis
*   **Payroll Earning Codes:** Requires a new Earning Code `SICK_CA_PROD` mapped to the Workday Payroll interface to ensure accurate gross-to-net calculations and "Paid Leave" reporting on pay slips.
*   **Sensitive Personal Information (SPI):** Medical certifications attached to requests must be restricted. Only HR Leave Specialists and the specific Employee should have "View" access; Managers should only see the "Status" of the request, not the attachment.
*   **Business Process Routing:** The `Absence Request` BP will be modified to include a conditional step for "Evidence Required" for requests > 24 consecutive work hours.

### Agile User Story
**As a** California-based hourly employee,
**I want** my sick leave to accrue automatically based on my hours worked and be able to request time off via mobile or desktop,
**So that** I am paid accurately for my time away and the company remains compliant with California labor laws.

### Gherkin Acceptance Criteria
*   **Scenario 1: Statutory Accrual Calculation**
    *   **Given** an hourly worker based in Los Angeles
    *   **When** they complete a 40-hour work week
    *   **Then** Workday should calculate an accrual of 1.33 hours (40/30) during the next period calculation.
*   **Scenario 2: Validation of Usage Limit**
    *   **Given** an employee has already used 40 hours of CA Sick Leave in the calendar year
    *   **When** they attempt to request an additional 8 hours
    *   **Then** the system should trigger a "Critical Error" preventing submission.
*   **Scenario 3: Attachment Security**
    *   **Given** a manager is reviewing a sick leave request with an uploaded doctor's note
    *   **When** the manager opens the "View Absence Request" task
    *   **Then** the attachment field should be masked or hidden based on Security Group permissions.

---

# Part 2: Workday Technical Configuration Blueprint

### Time Off Plan Definition
*   **Plan Type:** Accrual
*   **Units:** Hours
*   **Accrual Frequency:** At the end of each Pay Period (linked to Time Tracking total hours).
*   **Carryover:** Maximum carryover of 48 hours; any excess is forfeited at the end of the Plan Year (Jan 1).

### Eligibility & Validation Rules
*   **Eligibility Rule:** `ER_State_of_California_Hourly`
    *   *Criteria:* `Location State = California` AND `Worker Type = Employee` AND `Pay Type = Hourly`.
*   **Validation Rule:** `VAL_CA_Sick_Max_Usage`
    *   *Logic:* Sum of `Time Off Taken` in `Current Calendar Year` > 40 hours.
    *   *Severity:* Critical (Prevents Submission).

### Calculation Rules & Accrual Matrix
*   **Accrual Base:** `Units Worked` (Sum of hours from Time Tracking for the period).
*   **Matrix:** 
    *   *Calculation:* `(Units Worked / 30) * 1`
    *   *Rounding:* Round down to two decimal places.

### Business Process (BP) Routing & Approval Logic
*   **BP:** `Absence Request`
*   **Step 1 (Action):** Employee submits request.
*   **Step 2 (Condition):** If `Duration > 24 hours` OR `3 consecutive days`:
    *   **Action:** Upload Required (Medical Certificate).
    *   **Routing:** To HR Leave Specialist.
*   **Step 3 (Approval):** If `Duration <= 24 hours`:
    *   **Action:** Auto-approval (with notification to Manager).

### Security Policies & Attachment Privacy
*   **Functional Area:** Absence Management
*   **Secured Report/Task:** `Manage Attachments on Absence Request`
*   **Security Groups:** Restricted to `Self` and `HR_Leave_Specialist`. 
*   **Domain Security Policy:** `Worker Data: Absence Attachments` set to View/Modify for HR only.

---

# Part 3: Comprehensive UAT Test Matrix

| Test ID | Test Category | Scenario / Description | Test Data / Role | Expected Result | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **UAT-01** | Happy Path | California worker requests 8 hours of accrued sick leave. | CA Hourly Worker | Request submits successfully; Manager receives notification. | Not Run |
| **UAT-02** | Eligibility | New York worker tries to select "CA Sick Leave" plan. | NY Hourly Worker | Plan is not visible in the "Time Off Type" dropdown. | Not Run |
| **UAT-03** | Boundary | Employee attempts to request 41st hour of sick leave in a year. | CA Hourly Worker | Validation error: "You have reached the 40-hour annual limit." | Not Run |
| **UAT-04** | Routing | Request > 3 days submitted. | CA Hourly Worker | BP routes to HR Leave Specialist instead of Auto-Approval. | Not Run |
| **UAT-05** | Security/SPI | Manager views a request that contains a medical attachment. | Manager Role | Manager can see request details but cannot see/download the file. | Not Run |
| **UAT-06** | Accrual Logic | Run accrual process for 60 hours worked in period. | Batch Process | Accrual balance increases by exactly 2.00 hours. | Not Run |
| **UAT-07** | Integration | Terminated employee pay-out check. | Terminated Worker | CA Sick Leave balance does NOT pay out (verify mapping). | Not Run |
| **UAT-08** | Payroll | Verify Earning Code on Payslip. | Payroll Admin | Hours appear under the "SICK_CA_PROD" code in results. | Not Run |

---
**Approval Sign-off:**
*Date:* 2023-10-27
*Lead HRIS Architect:* [System Generated]