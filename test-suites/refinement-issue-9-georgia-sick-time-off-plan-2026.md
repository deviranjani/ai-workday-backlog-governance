As an expert Workday HRIS Product Owner and Configuration Lead, I have prepared a comprehensive specification for a common high-impact feature request: **The Implementation of a "Volunteer Time Off" (VTO) Program.**

This specification assumes a global rollout with specific regional eligibility and payroll integration requirements.

---

# Part 1: Agile Backlog Item & Governance

### Executive Summary & Business Objective
To support Corporate Social Responsibility (CSR) goals, the organization is introducing a **Volunteer Time Off (VTO)** benefit. This allows eligible employees to take up to 16 hours of paid time per calendar year to volunteer at approved non-profit organizations. The objective is to automate the tracking, approval, and payroll reporting of these hours within Workday Time Tracking and Absence Management.

### Downstream Impact Analysis
*   **Payroll Integration:** A new Earning Code `VTO_PAID` must be mapped to the payroll interface (PICOF/PECI) to ensure hours are coded correctly and do not trigger overtime premiums (depending on local legislation).
*   **Sensitive Personal Information (SPI):** If employees are required to upload "Proof of Service" documents, these must be restricted via Segmented Security to ensure only specific HR roles can view attachments, protecting employee privacy.
*   **Business Process Routing:** The *Request Time Off* BP will be modified to include a conditional branch for VTO to ensure manager awareness of community engagement.

### Agile User Story
**As a** Full-Time Employee,
**I want** to request Volunteer Time Off directly in Workday,
**So that** I can contribute to my community without using my personal Vacation/PTO balance while ensuring I am paid accurately.

### Gherkin Acceptance Criteria
*   **Scenario 1: Annual Granting of Hours**
    *   **Given** a full-time employee in the US or UK
    *   **When** the period is Jan 1st of the new Plan Year
    *   **Then** the system should automatically grant 16 hours to the VTO balance.
*   **Scenario 2: Validation of Max Usage**
    *   **Given** an employee has already used 12 hours of VTO
    *   **When** they attempt to request 8 additional hours
    *   **Then** the system should trigger a hard validation error: "Maximum annual VTO limit is 16 hours."
*   **Scenario 3: Manager Approval Routing**
    *   **Given** a VTO request is submitted
    *   **When** the request is > 4 hours
    *   **Then** it must route to the Supervisory Manager for approval.

---

# Part 2: Workday Technical Configuration Blueprint

### 1. Time Off Plan Definition
*   **Plan Type:** Accrual (Front-loaded)
*   **Units:** Hours
*   **Balance Period:** Calendar Year (Jan 1 – Dec 31)
*   **Carryover:** No carryover allowed (Use it or Lose it).
*   **Negative Balance:** Not permitted.

### 2. Eligibility & Validation Rules
*   **Eligibility Rule:** `ER_VTO_Eligibility`
    *   Condition: `Worker Type = Employee` AND `Time Type = Full Time` AND `Location Country = USA or GBR`.
*   **Validation Rule:** `VAL_VTO_Max_16_Hours`
    *   Type: Critical Error.
    *   Logic: `Sum of (VTO Taken + VTO Requested) > 16` for the current calendar year.

### 3. Calculation Rules & Accrual Matrix
*   **Accrual Frequency:** Annual (Beginning of Period).
*   **Accrual Calculation:** `Calculation Variable: 16`.
*   **Proration:** New hires after July 1st receive 8 hours (Prorated Logic).

### 4. Business Process (BP) Routing & Approval Logic
*   **BP:** *Request Time Off*
*   **Condition Rule:** `CR_VTO_Request` (True if Time Off Type = Volunteer Time Off).
*   **Step 1 (Auto-Approve):** If total hours $\le$ 4, mark as Completed (Auto-approve).
*   **Step 2 (Approval):** If total hours $>$ 4, route to `Supervisory Manager`.

### 5. Security & Privacy Configuration
*   **Domain:** `Self-Service: Time Off` (Standard access for Requesting).
*   **Attachment Security:** Create a new **Attachment Category** "Volunteer Certification."
*   **Security Policy:** Modify *Worker Data: Discretionary Attachments*. Restrict "Volunteer Certification" category to `HR Partner` and `System Administrator` roles only. The `Manager` will NOT see the attachment to protect potential SPI.

---

# Part 3: Comprehensive UAT Test Matrix

| Test ID | Test Category | Scenario / Description | Test Data / Role | Expected Result | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **TC-01** | Happy Path | Request 8 hours of VTO (Full-time US) | US Worker / Employee | Request submits successfully; routes to Manager. | Not Run |
| **TC-02** | Boundary | Attempt to request 17 hours of VTO | UK Worker / Employee | Hard Error: "Maximum annual VTO limit is 16 hours." | Not Run |
| **TC-03** | Eligibility | Part-time worker attempts to see VTO plan | PT Worker / Employee | VTO plan is not visible in the 'Time Off Type' dropdown. | Not Run |
| **TC-04** | Routing | Request 2 hours of VTO (Threshold check) | US Worker / Employee | Request should Auto-Approve (Skip Manager). | Not Run |
| **TC-05** | Security/Privacy | Manager attempts to view VTO Proof Attachment | Manager | Manager can see the event but cannot see/download the attachment. | Not Run |
| **TC-06** | Integration | Run Payroll Preview for worker with VTO | Payroll Partner | Hours appear under Earning Code `VTO_PAID` on the pay result. | Not Run |
| **TC-07** | Year-End | Advance system date to Jan 1 next year | System Admin | Balance resets to 16; previous year unused hours do not carry over. | Not Run |
| **TC-08** | Negative Test | Attempt to request VTO for a date in the past | US Worker / Employee | System allows request but triggers a Warning if > 30 days old. | Not Run |