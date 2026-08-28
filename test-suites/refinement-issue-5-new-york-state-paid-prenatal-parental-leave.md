# Part 1: Agile Backlog Item & Implementation Plan

### Executive Summary & Downstream Impact Analysis
**Project Title:** Implementation of New York Paid Prenatal Personal Leave (Effective Jan 1, 2025)
**Objective:** To comply with NY State Labor Law requirements, we must implement a new "Prenatal Leave" time-off plan providing up to 20 hours of paid leave per year for pregnant employees to attend medical appointments and procedures.

**Downstream Impact Analysis:**
*   **Payroll:** A new earning code `NY_PRENATAL` must be created and mapped to the HCM system. This must ensure the rate of pay is the employee’s regular rate and does not decrement the standard Paid Sick Leave (PSL) balance.
*   **Security:** Medical documentation attached to these requests contains Sensitive Personal Information (SPI). Security groups must be configured so that while a Manager can see the *time* requested, only Absence Administrators/HR can view or download the attachments.
*   **Business Processes (BP):** The "Request Time Off" BP requires a conditional rule. To reduce administrative burden, requests for standard appointments (<= 4 hours) will bypass manager approval.

---

### User Story
**As a** New York-based employee,
**I want to** select "NY Prenatal Leave" from my time-off options,
**So that** I can receive paid time off for prenatal healthcare without exhausting my regular sick leave or vacation balance.

#### Gherkin Acceptance Criteria

**Scenario 1: Auto-Approval for Short Duration**
*   **Given** I am an active employee assigned to a New York work location
*   **And** I have a remaining balance for NY Prenatal Leave
*   **When** I submit a request for 4 hours or less
*   **Then** the system should automatically approve the request
*   **And** the status should move immediately to "Completed."

**Scenario 2: Routing for Longer Duration**
*   **Given** I am an active employee in New York
*   **When** I submit a request for more than 4 hours (e.g., a full day for a procedure)
*   **Then** the system should route the request to my Direct Manager for approval
*   **And** the request status should remain "In Progress" until approved.

**Scenario 3: Privacy of Medical Documentation**
*   **Given** I have uploaded a medical note to my leave request
*   **When** my Manager views the request details
*   **Then** the Manager should see the date and hours requested
*   **But** the "Attachments" section should be hidden or restricted from the Manager's view.

---

# Part 2: Comprehensive UAT Test Matrix

| Test ID | Test Category | Scenario / Description | Test Data / Role | Expected Result | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| TC-01 | Positive (Auto-Approve) | Submit request <= 4 hrs | NY Active Employee | Auto-approved, skips manager | Not Run |
| TC-02 | Positive (Routing) | Submit request > 4 hrs | NY Active Employee | Routes to Manager for approval | Not Run |
| TC-03 | Negative / Validation | Submit request outside NY | Non-NY Worker | System blocks or prevents selection | Not Run |
| TC-04 | Security / Privacy | Upload medical attachment | Manager Role | Manager cannot view attachment | Not Run |
| TC-05 | Payroll Integration | Run Pay Calc with leave taken | NY Hourly/Salary | NY_PRENATAL earning code appears | Not Run |