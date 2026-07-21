# UI.md

# Expenses Management Engine User Interface Specification

---

# 1. Overview

This document defines the user interface architecture, navigation, screens, components, forms, interactions, validation, responsive behavior, and usability requirements for the **Expenses Management Engine**.

The interface supports:

- Expense Requisitions
- Direct Expenses
- Staff Advances
- Advance Disbursements
- Accountabilities
- Expense Claims
- Reimbursements
- Refunds
- Recoveries
- Petty Cash
- Travel Requests
- Per Diem
- Mileage Claims
- Operational Fund Transfers
- Budget Controls
- Expense Policies
- Policy Exceptions
- Approvals
- Reporting

The Expenses Management Engine interface must follow the shared Business Suite design system and platform shell.

---

# 2. Frontend Technology Stack

The Expenses Management Engine frontend uses:

```text
React
TypeScript
Vite
Tailwind CSS
shadcn/ui
React Router
React Hook Form
Zod
React Context API
```

The interface must not use plain HTML navigation links where React Router navigation is required.

Forms must use:

- React Hook Form
- Zod validation
- Shared Business Suite form components
- Server-side validation
- Inline error handling
- Submission loading indicators

---

# 3. User Interface Principles

The interface must be:

- Clear
- Consistent
- Accessible
- Responsive
- Role-aware
- Permission-aware
- Tenant-aware
- Company-aware
- Branch-aware
- Workflow-aware
- Audit-friendly
- Financially safe
- Mobile-ready

The interface must help users understand:

- What action is required
- Who currently owns the transaction
- What stage the transaction has reached
- What amount was requested
- What amount was approved
- What amount was disbursed
- What amount was accounted for
- What balance remains
- Whether budget or policy exceptions exist

---

# 4. Platform Shell Integration

The Expenses Management Engine must operate within the shared Business Suite platform shell.

The shell provides:

- Global Navigation
- Tenant Switcher
- Company Switcher
- Branch Switcher
- User Profile
- Notification Center
- Global Search
- Breadcrumbs
- Help and Support
- Feature Availability
- Subscription Controls

The Expenses module must not duplicate these components.

---

# 5. Main Navigation

Recommended Expenses navigation:

```text
Expenses
│
├── Dashboard
├── Requisitions
├── Direct Expenses
├── Staff Advances
├── Accountabilities
├── Expense Claims
├── Reimbursements
├── Refunds & Recoveries
├── Petty Cash
├── Travel & Mileage
├── Fund Transfers
├── Approvals
├── Reports
└── Configuration
```

Navigation items must appear only when:

- The feature is enabled.
- The user has permission.
- The tenant subscription includes the feature.
- The user has access to the selected company or branch.

---

# 6. Route Structure

Recommended route structure:

```text
/expenses
/expenses/dashboard

/expenses/requisitions
/expenses/requisitions/new
/expenses/requisitions/:id
/expenses/requisitions/:id/edit
/expenses/requisitions/:id/approve

/expenses/direct-expenses
/expenses/direct-expenses/new
/expenses/direct-expenses/:id

/expenses/advances
/expenses/advances/new
/expenses/advances/:id
/expenses/advances/:id/disburse

/expenses/accountabilities
/expenses/accountabilities/new
/expenses/accountabilities/:id
/expenses/accountabilities/:id/review

/expenses/claims
/expenses/claims/new
/expenses/claims/:id

/expenses/reimbursements
/expenses/reimbursements/:id

/expenses/refunds
/expenses/refunds/:id

/expenses/recoveries
/expenses/recoveries/:id

/expenses/petty-cash
/expenses/petty-cash/funds
/expenses/petty-cash/funds/:id
/expenses/petty-cash/vouchers
/expenses/petty-cash/reconciliations

/expenses/travel
/expenses/travel/new
/expenses/travel/:id

/expenses/mileage
/expenses/mileage/new
/expenses/mileage/:id

/expenses/fund-transfers
/expenses/fund-transfers/new
/expenses/fund-transfers/:id

/expenses/approvals

/expenses/reports

/expenses/configuration/categories
/expenses/configuration/policies
/expenses/configuration/per-diem-rates
/expenses/configuration/mileage-rates
/expenses/configuration/petty-cash
/expenses/configuration/settings
```

---

# 7. Shared Page Layout

Each major page should use the following structure:

```text
Page Header
│
├── Breadcrumbs
├── Title
├── Description
├── Status
└── Primary Actions

Summary or KPI Area

Filters and Search

Main Content

Related Information Tabs

Activity and Audit Timeline
```

A transaction detail page should generally include:

```text
Header
Summary Cards
Workflow Progress
Main Details
Expense Lines
Allocations
Documents
Approvals
Financial References
Activity Timeline
```

---

# 8. Shared Page Header

The page header must support:

- Page title
- Document number
- Status badge
- Entity description
- Back button
- Save action
- Submit action
- Workflow action
- More actions menu

Example:

```text
REQ-2026-000154
Field Monitoring Activity Funds

Status: Under Review

[Back] [Save] [Submit] [More]
```

The primary action must reflect the current lifecycle state and the user’s permissions.

---

# 9. Status Badges

Status badges must use shared semantic status styling.

Suggested status groups:

```text
Neutral:
Draft
Pending

Information:
Submitted
Under Review
In Transit

Warning:
Returned for Revision
Accountability Due
Partially Approved
Partially Disbursed

Success:
Approved
Paid
Accounted
Reconciled
Settled
Closed

Danger:
Rejected
Overdue
Failed
Suspended
Variance Detected

Muted:
Cancelled
Reversed
Expired
```

Status must never be communicated by color alone.

Each badge should also include readable text and, where useful, an icon.

---

# 10. Expenses Dashboard

## 10.1 Purpose

The Expenses Dashboard provides a role-aware overview of organizational expenditure and pending actions.

---

## 10.2 Dashboard KPI Cards

Recommended cards include:

- Requisitions Pending Approval
- Approved but Not Disbursed
- Outstanding Staff Advances
- Overdue Accountabilities
- Pending Reimbursements
- Outstanding Refunds
- Petty Cash Available
- Petty Cash Variances
- Transfers In Transit
- Policy Exceptions
- Budget Overrides Pending

Cards must respect user access.

An employee may see only personal metrics, while a finance manager may see company-wide metrics.

---

## 10.3 Dashboard Charts

Recommended visualizations include:

- Expenses by Category
- Expenses by Department
- Expenses by Project
- Expense Trends by Month
- Requisition Status Distribution
- Advance Aging
- Accountability Performance
- Approval Lead Time
- Reimbursements by Status
- Petty Cash Movements
- Fund Transfers by Status

Charts must use the Reporting Engine where possible.

---

## 10.4 Dashboard Action Panels

Recommended panels include:

```text
My Pending Actions
My Drafts
My Outstanding Advances
Accountabilities Due Soon
Recent Reimbursements
Recent Approvals
Exceptions Requiring Attention
```

---

# 11. Requisition List Screen

## 11.1 Purpose

The Requisition List displays internal requests for operational funds.

---

## 11.2 List Columns

Recommended columns:

```text
Requisition Number
Title
Requester
Department
Type
Requested Amount
Approved Amount
Required Date
Status
Current Approver
Created Date
Actions
```

---

## 11.3 Filters

Recommended filters:

- Search
- Status
- Requester
- Department
- Branch
- Requisition Type
- Project
- Funding Source
- Currency
- Required Date
- Created Date
- Budget Status
- Policy Status
- My Requests
- My Approvals

---

## 11.4 List Actions

Depending on permission and status:

- View
- Edit
- Submit
- Withdraw
- Cancel
- Duplicate as New
- Print
- Export
- Open Workflow
- Convert to Procurement

Bulk actions should be limited to safe operations such as:

- Export
- Assign Review
- Send Reminder

Bulk approval must only be enabled where tenant policy permits.

---

# 12. Create Requisition Screen

## 12.1 Form Structure

The requisition form should use sections or a stepper.

Recommended sections:

```text
1. Request Details
2. Expense Lines
3. Cost Allocations
4. Budget & Policy
5. Supporting Documents
6. Review & Submit
```

---

## 12.2 Request Details Section

Fields include:

- Company
- Branch
- Requester
- Beneficiary
- Department
- Cost Center
- Project
- Grant
- Program
- Activity
- Funding Source
- Requisition Type
- Title
- Purpose
- Justification
- Currency
- Required Date
- Disbursement Method
- Accountability Required

Fields must be dynamically shown based on tenant configuration.

---

## 12.3 Expense Line Editor

The line editor should support:

- Add line
- Edit line
- Remove draft line
- Duplicate line
- Reorder lines
- Copy previous line
- Import from template

Each line may contain:

- Expense Category
- Expense Type
- Description
- Quantity
- Unit of Measure
- Unit Cost
- Tax
- Requested Amount
- Required Date
- Budget Line
- Notes

Line totals must recalculate automatically.

Server validation remains authoritative.

---

## 12.4 Allocation Editor

Allocations may be displayed in:

- Inline expandable rows
- Modal editor
- Side panel

Fields include:

- Department
- Cost Center
- Project
- Grant
- Program
- Activity
- Funding Source
- Asset
- Percentage
- Amount

The interface must display:

```text
Allocated Amount
Unallocated Amount
Allocation Percentage
```

Submission must be blocked where allocations do not balance.

---

## 12.5 Budget and Policy Panel

The form should display:

```text
Budget Status
Available Budget
Requested Amount
Remaining Budget
Policy Status
Warnings
Required Overrides
Required Documents
```

Actions may include:

- Recheck Budget
- View Budget Details
- Request Override
- View Policy
- Add Justification

---

## 12.6 Draft Autosave

The interface may autosave draft requisitions.

Autosave must:

- Show saving status
- Avoid overwriting newer versions
- Respect optimistic concurrency
- Not submit the record
- Not generate financial effects

Suggested status text:

```text
Saving...
Saved
Could not save
Changes conflict with another update
```

---

# 13. Requisition Detail Screen

The requisition detail page should include:

```text
Summary
Expense Lines
Allocations
Budget
Policies
Documents
Approval Trail
Disbursements
Procurement
Accountability
Activity Log
```

---

## 13.1 Summary Cards

Recommended cards:

- Requested Amount
- Approved Amount
- Disbursed Amount
- Accounted Amount
- Outstanding Amount
- Required Date
- Accountability Due Date

---

## 13.2 Workflow Progress

The interface should show:

```text
Submitted
Supervisor Approval
Department Approval
Finance Review
Final Approval
Disbursement
Accountability
Closure
```

Each workflow stage should display:

- Step name
- Status
- Assigned approver
- Completed by
- Date
- Comments

---

## 13.3 Available Actions

Actions depend on status and permission:

- Edit
- Submit
- Approve
- Partially Approve
- Reject
- Return for Revision
- Withdraw
- Cancel
- Create Advance
- Create Payment Request
- Convert to Procurement
- Reopen
- Reverse

Sensitive actions must require confirmation and, where appropriate, a reason.

---

# 14. Approval Workspace

## 14.1 Purpose

The Approval Workspace consolidates assigned approval tasks.

---

## 14.2 Approval Queue

Recommended columns:

```text
Document Number
Type
Requester
Purpose
Amount
Currency
Department
Submitted Date
Days Pending
Policy Warnings
Budget Status
Priority
```

---

## 14.3 Approval Review Layout

The approval screen should use a split or structured layout:

```text
Main Transaction Details
Supporting Documents
Budget and Policy Results
Approval History
Decision Panel
```

Decision actions include:

- Approve
- Partially Approve
- Return
- Reject
- Delegate
- Escalate

---

## 14.4 Approval Decision Modal

The decision modal should include:

- Decision
- Comments
- Approved amount where applicable
- Line-level decision
- Reason code
- Effective date
- Confirmation checkbox for high-risk actions

Comments must be mandatory for:

- Rejection
- Return
- Partial approval
- Policy override
- Budget override

---

# 15. Direct Expense Screens

## 15.1 Direct Expense List

Columns include:

- Expense Number
- Expense Date
- Payee
- Category
- Gross Amount
- Tax
- Net Amount
- Payment Method
- Status
- Created By
- Payment Reference

---

## 15.2 Direct Expense Form

Sections include:

```text
Payee Details
Expense Details
Expense Lines
Tax Details
Allocations
Budget & Policy
Documents
Payment Details
Review
```

The form must support both:

- Organization-paid expense
- Personally paid expense

When `Personally Paid` is selected, the interface should show employee reimbursement information.

---

# 16. Staff Advance List Screen

Recommended columns:

```text
Advance Number
Employee
Purpose
Approved Amount
Disbursed Amount
Accounted Amount
Outstanding Amount
Due Date
Age
Status
```

Recommended filters:

- Employee
- Department
- Due Date
- Status
- Overdue
- Currency
- Project
- Branch
- Outstanding Amount Range

---

# 17. Staff Advance Detail Screen

The detail page should display:

```text
Advance Summary
Source Requisition
Disbursement History
Accountabilities
Refunds
Recoveries
Settlement
Documents
Workflow
Activity
```

Summary cards include:

- Approved
- Disbursed
- Accounted
- Refunded
- Recovered
- Outstanding

The interface must clearly distinguish:

- Operational balance
- Pending accountability
- Pending refund
- Pending recovery

---

# 18. Advance Disbursement Screen

The disbursement interface is restricted to authorized users.

Fields include:

- Advance Number
- Employee
- Approved Amount
- Previously Disbursed
- Remaining Amount
- Payment Method
- Payment Account
- Amount
- Currency
- Payment Reference
- Disbursement Date
- Notes

The interface must:

- Block amounts above the remaining approved amount.
- Display segregation-of-duty warnings.
- Prevent duplicate submissions.
- Show processing status.
- Display Finance Engine confirmation.

The submit button must show a spinner while processing.

---

# 19. Accountability List Screen

Recommended columns:

```text
Accountability Number
Advance Number
Employee
Advance Amount
Submitted Amount
Approved Amount
Settlement Result
Submission Date
Status
Reviewer
```

Filters include:

- Status
- Employee
- Department
- Reviewer
- Submission Date
- Settlement Type
- Missing Receipt
- Duplicate Warning
- Overdue

---

# 20. Accountability Form

## 20.1 Layout

Recommended sections:

```text
Advance Summary
Actual Expense Lines
Allocations
Receipts
Activity Documents
Variance Summary
Review & Submit
```

---

## 20.2 Advance Summary

The form should display:

- Advance amount
- Purpose
- Disbursement date
- Due date
- Approved categories
- Project or activity
- Previous accountability submissions

---

## 20.3 Actual Expense Line Editor

Fields include:

- Expense Date
- Category
- Merchant
- Description
- Receipt Number
- Invoice Number
- Currency
- Amount
- Tax
- Allocation
- Receipt Attachment

Each row should show:

- Receipt status
- Policy status
- Duplicate status
- Review status

---

## 20.4 Variance Summary

The interface must continuously display:

```text
Advance Amount
Submitted Expenses
Difference
Expected Outcome
```

Possible outcomes:

- Fully Accounted
- Refund Expected
- Reimbursement Expected

This is an estimate until reviewer approval is completed.

---

# 21. Accountability Review Screen

The review screen must support line-level decisions.

Recommended columns:

```text
Expense Date
Category
Description
Receipt
Submitted Amount
Policy Result
Duplicate Result
Approved Amount
Decision
Comments
```

Reviewer actions per line:

- Approve
- Reduce
- Reject
- Request Clarification
- Flag

A settlement preview should display:

```text
Approved Expense Amount
Rejected Amount
Refund Required
Reimbursement Required
Recovery Required
```

The final settlement must be recalculated server-side.

---

# 22. Expense Claim Screens

## 22.1 Claim List

Columns include:

- Claim Number
- Employee
- Claim Date
- Claimed Amount
- Approved Amount
- Reimbursed Amount
- Status
- Current Approver

---

## 22.2 Claim Form

The claim form resembles the accountability form but does not require an advance.

Sections include:

- Claim Details
- Claim Lines
- Receipts
- Allocations
- Policy and Budget
- Review and Submit

The interface should display possible duplicate expenses before submission.

---

# 23. Reimbursement Screens

## 23.1 Reimbursement List

Columns include:

- Reimbursement Number
- Employee
- Source
- Approved Amount
- Payment Method
- Finance Status
- Payment Date
- Status

---

## 23.2 Reimbursement Detail

The detail page should show:

- Source claim or accountability
- Employee
- Amount
- Payment method
- Finance request reference
- Payment confirmation
- Failure history
- Retry history
- Activity log

Only authorized users may retry failed payment requests.

---

# 24. Refund Screens

## 24.1 Refund List

Columns include:

- Refund Number
- Employee or Beneficiary
- Source
- Required Amount
- Refunded Amount
- Outstanding Amount
- Due Date
- Status

---

## 24.2 Refund Detail

The page should display:

- Refund obligation
- Source accountability
- Due date
- Payment instructions
- Receipt history
- Outstanding balance
- Recovery escalation status

Authorized users may:

- Record refund evidence
- Send reminder
- Extend due date
- Start recovery evaluation
- View Finance confirmation

Uploading proof does not mark the refund as confirmed.

---

# 25. Recovery Screens

## 25.1 Recovery List

Columns include:

- Recovery Number
- Employee
- Source
- Method
- Required Amount
- Recovered Amount
- Outstanding Amount
- Payroll Status
- Status

---

## 25.2 Recovery Detail

The detail page should show:

- Recovery reason
- Approval history
- Payroll request
- Installment schedule
- Recovery confirmations
- Outstanding balance
- Activity log

---

# 26. Petty Cash Dashboard

The Petty Cash Dashboard should display:

- Active Funds
- Total Authorized Float
- Total Available Cash
- Unaccounted Vouchers
- Replenishments Pending
- Reconciliations Due
- Open Variances
- Low-Balance Funds

Fund cards may display:

```text
Fund Name
Branch
Custodian
Authorized Float
Available Balance
Unaccounted Amount
Status
```

---

# 27. Petty Cash Fund List

Columns include:

- Fund Code
- Fund Name
- Branch
- Custodian
- Currency
- Authorized Float
- Available Balance
- Unaccounted Amount
- Status

Actions include:

- View
- Issue Voucher
- Replenish
- Reconcile
- Transfer Custody
- Suspend
- Close

---

# 28. Petty Cash Fund Detail

Tabs include:

```text
Overview
Transactions
Vouchers
Replenishments
Reconciliations
Custodian History
Documents
Activity
```

Balance cards include:

- Authorized Float
- Current Cash
- Unaccounted Vouchers
- Available Balance
- Minimum Balance

---

# 29. Petty Cash Voucher Form

Fields include:

- Fund
- Requester
- Payee
- Expense Category
- Purpose
- Amount
- Documents
- Accountability Required

The interface must immediately show:

- Available fund balance
- Maximum allowed transaction
- Remaining balance after payment
- Policy warnings

---

# 30. Petty Cash Replenishment Screen

The screen should display eligible transactions:

```text
Voucher Number
Date
Payee
Category
Amount
Receipt Status
Approval Status
Eligible for Replenishment
```

Summary:

- Total vouchers
- Eligible amount
- Excluded amount
- Requested replenishment
- Approved replenishment

Unsupported vouchers must be clearly identified.

---

# 31. Petty Cash Reconciliation Screen

The reconciliation interface should display:

```text
Expected Cash
Actual Cash
Accounted Vouchers
Unaccounted Vouchers
Expected Total
Variance
```

Sections include:

- Cash denomination count
- Voucher review
- Refunds
- Adjustments
- Variance explanation
- Supporting documents
- Approval

The custodian should not see an approval action for their own reconciliation.

---

# 32. Travel Request Screens

## 32.1 Travel List

Columns include:

- Travel Request Number
- Employee
- Destination
- Departure
- Return
- Estimated Amount
- Advance Required
- Status

---

## 32.2 Travel Request Form

Sections include:

```text
Traveler
Purpose and Destination
Travel Dates
Estimated Costs
Per Diem
Advance
Budget and Policy
Documents
Review
```

The interface should automatically calculate duration and estimated per diem.

---

# 33. Per Diem Calculator

Fields include:

- Employee
- Employee Grade
- Destination
- Departure Date and Time
- Return Date and Time
- Meals Provided
- Accommodation Provided
- Travel Type
- Currency

Calculation results include:

- Eligible days
- Daily rate
- Accommodation component
- Meal component
- Incidentals
- Deductions
- Payable amount

Manual overrides must require:

- Permission
- Reason
- Approval where configured

---

# 34. Mileage Claim Screens

## 34.1 Mileage List

Columns include:

- Claim Number
- Employee
- Journey Date
- Origin
- Destination
- Distance
- Rate
- Claimed Amount
- Approved Amount
- Status

---

## 34.2 Mileage Claim Form

Fields include:

- Journey Date
- Origin
- Destination
- Purpose
- Distance
- Unit
- Vehicle Type
- Travel Request
- Supporting Evidence

The calculated amount should update automatically.

---

# 35. Operational Fund Transfer Screens

## 35.1 Transfer List

Columns include:

- Transfer Number
- Transfer Type
- Source
- Destination
- Amount
- Dispatch Date
- Receipt Date
- Status
- Variance

---

## 35.2 Transfer Form

Fields include:

- Transfer Type
- Source Company
- Source Branch
- Source Fund
- Source Custodian
- Destination Company
- Destination Branch
- Destination Fund
- Destination Custodian
- Amount
- Currency
- Purpose
- Required Date
- Documents

The interface must validate the destination separately from the source.

---

## 35.3 Transfer Dispatch Screen

Fields include:

- Approved Amount
- Dispatch Method
- Dispatch Reference
- Released By
- Dispatch Date
- Supporting Evidence

After dispatch, the transfer should display `In Transit`.

---

## 35.4 Transfer Receipt Screen

Fields include:

- Amount Received
- Receipt Date
- Receiving Method
- Receipt Reference
- Received By
- Evidence
- Difference Reason

The screen must calculate any variance.

A mismatched receipt should create a variance workflow rather than silently completing the transfer.

---

# 36. Custodian Handover Screen

The handover interface should show:

- Outgoing custodian
- Incoming custodian
- Expected cash
- Actual cash
- Outstanding vouchers
- Pending refunds
- Open variances
- Handover date

Required confirmations:

- Outgoing custodian confirmation
- Incoming custodian confirmation
- Independent reviewer approval

---

# 37. Expense Policy Screens

## 37.1 Policy List

Columns include:

- Policy Code
- Policy Name
- Scope
- Category
- Maximum Amount
- Receipt Required
- Effective Date
- Status

---

## 37.2 Policy Form

Sections include:

```text
General Information
Scope
Limits
Receipt Rules
Advance Rules
Approval Rules
Accountability Rules
Override Rules
Effective Dates
Advanced Conditions
```

The interface may offer a visual policy builder while storing validated rule configuration.

---

## 37.3 Policy Test Panel

Administrators should be able to simulate a policy using:

- Employee
- Department
- Category
- Amount
- Date
- Project
- Currency

Results should show:

- Applied policies
- Rule priority
- Passed rules
- Warnings
- Blocks
- Required approvals

Testing must not create a real expense transaction.

---

# 38. Budget Override Screens

The override request page should show:

- Source transaction
- Requested amount
- Available budget
- Variance
- Budget dimensions
- Business reason
- Supporting evidence
- Approval history

Approvers must clearly see that an override does not directly alter official budget balances.

---

# 39. Policy Exception Screens

The exception page should display:

- Exception type
- Severity
- Source record
- Policy violated
- User justification
- Supporting documents
- Risk indicators
- Approval decision
- Conditions imposed
- Resolution status

---

# 40. Configuration Screens

Recommended configuration areas include:

```text
Expense Categories
Expense Types
Requisition Types
Payment Methods
Advance Types
Accountability Settings
Petty Cash Settings
Per Diem Rates
Mileage Rates
Policy Rules
Budget Control Settings
Settlement Rules
Recovery Rules
Operational Transfer Types
Document Requirements
Numbering References
Feature Settings
```

Shared reference data should remain managed through the Reference Data Engine where applicable.

---

# 41. Reports Interface

The Reports area should expose report cards or grouped navigation.

Recommended groups:

```text
Expense Operations
Advances and Accountabilities
Claims and Reimbursements
Refunds and Recoveries
Petty Cash
Travel and Mileage
Fund Transfers
Compliance and Exceptions
Approval Performance
Budget Utilization
```

---

# 42. Report Filters

Common filters include:

- Date Range
- Company
- Branch
- Department
- Cost Center
- Project
- Grant
- Funding Source
- Employee
- Expense Category
- Status
- Currency
- Approval Level
- Payment Method

---

# 43. Report Actions

Reports may support:

- View
- Export CSV
- Export Excel
- Export PDF
- Print
- Save Filter
- Schedule Report
- Share Report

Export permissions must be independently enforced.

---

# 44. Search

The Expenses interface should integrate with the Search & Indexing Engine.

Searchable entities include:

- Requisitions
- Advances
- Accountabilities
- Claims
- Refunds
- Reimbursements
- Petty Cash Vouchers
- Travel Requests
- Mileage Claims
- Fund Transfers

Search must support:

- Document number
- Employee
- Payee
- Purpose
- Receipt
- Invoice
- Payment reference
- Project
- Status

Results must respect tenant and authorization boundaries.

---

# 45. Document Upload Interface

Document upload components should support:

- Drag and drop
- File selection
- Camera capture on mobile
- Document type selection
- Required document indicators
- Upload progress
- Preview
- Verification status
- Version history

The interface should not expose raw storage paths.

---

# 46. Activity Timeline

Each transaction detail page should include an activity timeline showing:

```text
Created
Edited
Submitted
Returned
Approved
Rejected
Disbursed
Accounted
Refunded
Recovered
Reimbursed
Reconciled
Reopened
Reversed
Closed
```

Each activity entry should display:

- Actor
- Action
- Date and time
- Comments
- Related document
- Workflow step
- Amount change where applicable

---

# 47. Confirmation Dialogs

Confirmation dialogs are required for high-impact actions.

Examples:

- Submit
- Approve
- Reject
- Disburse
- Confirm Refund
- Start Recovery
- Reconcile Petty Cash
- Dispatch Transfer
- Confirm Transfer Receipt
- Reopen
- Reverse
- Cancel Approved Request

Dialogs must clearly state the consequence of the action.

---

# 48. Reason Capture Modals

A reason must be captured for:

- Rejection
- Return for revision
- Cancellation
- Partial approval
- Policy override
- Budget override
- Reopening
- Reversal
- Cash variance
- Transfer variance
- Write-off request

The modal may include:

- Reason code
- Explanation
- Supporting document
- Effective date

---

# 49. Validation

## 49.1 Client-Side Validation

Use Zod and React Hook Form for:

- Required fields
- Data types
- Date relationships
- Positive amounts
- Currency format
- Line completeness
- Attachment requirements
- Allocation balance
- Character limits

---

## 49.2 Server-Side Validation

Server-side validation must recheck:

- Tenant access
- Permission
- Workflow assignment
- Employee eligibility
- Record status
- Version
- Budget
- Policies
- Amount authority
- Allocation balance
- Duplicate actions
- Idempotency
- Finance state

Client-side validation is only a usability aid.

---

# 50. Error Messages

Errors should be specific and actionable.

Examples:

```text
The requested amount exceeds the available budget.

This employee has an overdue accountability and cannot receive another advance.

Allocation amounts must equal the expense line total.

This receipt may already have been used in another claim.

The record was updated by another user. Refresh before continuing.

The advance has already been disbursed.

You cannot approve your own request.

The petty cash fund does not have enough available balance.
```

Avoid generic messages such as:

```text
Something went wrong.
```

unless no safer detail is available.

---

# 51. Loading States

The interface must show loading indicators for:

- Page loading
- Form submission
- Budget validation
- Policy evaluation
- Document upload
- Workflow action
- Disbursement
- Finance processing
- Reconciliation
- Report generation

Buttons must be disabled while the same action is in progress.

---

# 52. Empty States

Empty states should explain the context and provide an appropriate action.

Examples:

```text
You have no outstanding advances.

No accountabilities are waiting for review.

No petty cash funds have been configured for this branch.

No expense claims match the selected filters.
```

Where authorized, include a relevant primary action such as:

```text
Create Requisition
Create Claim
Set Up Petty Cash Fund
```

---

# 53. Responsive Design

The interface must support:

- Desktop
- Laptop
- Tablet
- Mobile

Responsive behavior should include:

- Collapsible navigation
- Stacked form sections
- Scrollable tables
- Card-based mobile list views
- Bottom action bars on mobile
- Touch-friendly controls
- Mobile receipt capture
- Reduced chart density
- Responsive approval panels

---

# 54. Mobile List Behavior

On smaller screens, table rows may convert to cards showing:

```text
Document Number
Title
Amount
Status
Requester
Date
Primary Action
```

Secondary information may appear in an expandable area.

---

# 55. Accessibility

The interface must support:

- Keyboard navigation
- Visible focus states
- Screen-reader labels
- Semantic headings
- Form labels
- Error associations
- Sufficient contrast
- Non-color status indicators
- Accessible dialogs
- Accessible tables
- Touch target sizing

---

# 56. Localization

The interface should support:

- Locale-aware dates
- Locale-aware numbers
- Currency formatting
- Time zones
- Translation keys
- Multi-language labels
- Right-to-left support where required

Stored values must remain language-neutral where possible.

---

# 57. Currency Display

Currency must always be explicit.

Recommended display:

```text
UGX 2,500,000
USD 1,250.00
```

Where base currency conversion is shown:

```text
USD 1,250.00
Equivalent: UGX 4,812,500
```

Estimated and official values must be clearly distinguished.

---

# 58. Permission-Aware Interface

The interface must hide or disable actions the user cannot perform.

Examples:

- Employees see their own claims.
- Approvers see assigned workflow tasks.
- Finance users see disbursement actions.
- Petty cash custodians see assigned funds.
- Auditors receive read-only access.
- Policy administrators see configuration screens.

Hiding an action does not replace backend authorization.

---

# 59. Segregation-of-Duty Interface Controls

The interface should display clear notices such as:

```text
You cannot approve this request because you created it.

This reconciliation requires approval by a different user.

The transfer recipient must confirm receipt.

The disbursement requires a second authorized officer.
```

---

# 60. Realtime Updates

Realtime updates may refresh:

- Workflow status
- Payment status
- Disbursement confirmation
- Reimbursement status
- Transfer receipt
- Document verification
- Approval assignment
- Petty cash balance

Realtime updates should not overwrite unsaved draft changes.

---

# 61. Notification Deep Links

Notifications should navigate directly to the relevant page.

Examples:

```text
/expenses/requisitions/:id
/expenses/accountabilities/:id/review
/expenses/refunds/:id
/expenses/petty-cash/reconciliations/:id
/expenses/fund-transfers/:id
```

The destination page must still validate authorization.

---

# 62. Print and Document Views

Printable views may include:

- Requisition
- Advance Authorization
- Accountability
- Expense Claim
- Petty Cash Voucher
- Petty Cash Reconciliation
- Travel Authorization
- Fund Transfer Note
- Refund Notice

Print layouts should include:

- Company identity
- Document number
- QR or verification reference where enabled
- Transaction details
- Approval history
- Signature placeholders where needed
- Document status

---

# 63. UI Security Rules

The interface must not:

- Trust hidden fields for authorization.
- Expose another tenant’s identifiers.
- Display confidential records without permission.
- Allow direct status editing.
- Mark payment as completed without server confirmation.
- Mark refund as received based only on uploaded proof.
- Allow finalized amounts to be edited casually.
- Expose sensitive employee bank data unnecessarily.
- Permit duplicate action submission.
- Depend only on frontend route protection.

---

# 64. Component Library

Recommended reusable components include:

```text
ExpenseStatusBadge
ExpenseAmountDisplay
ExpenseSummaryCard
ExpenseLineEditor
ExpenseAllocationEditor
ExpensePolicyResult
ExpenseBudgetResult
ExpenseDocumentUploader
ExpenseWorkflowTimeline
ExpenseActivityTimeline
ExpenseApprovalPanel
ExpenseSettlementSummary
ExpenseVarianceSummary
ExpenseFilterBar
ExpenseEntityLink
ExpenseReferenceCard
PettyCashBalanceCard
FundTransferProgress
```

---

# 65. Suggested Feature Folder Structure

```text
src/features/expenses/
│
├── components/
│   ├── shared/
│   ├── requisitions/
│   ├── direct-expenses/
│   ├── advances/
│   ├── accountabilities/
│   ├── claims/
│   ├── reimbursements/
│   ├── refunds/
│   ├── recoveries/
│   ├── petty-cash/
│   ├── travel/
│   ├── mileage/
│   ├── fund-transfers/
│   ├── policies/
│   ├── budget/
│   └── approvals/
│
├── context/
├── hooks/
├── pages/
├── routes/
├── schemas/
├── services/
├── types/
└── utils/
```

---

# 66. Form Modal Rules

Consistent with the Business Suite UI conventions:

- Simple creation forms may open in modals.
- Complex multi-section transactions should use full pages.
- Confirmation and decision actions should use modals.
- Long forms should not be compressed into small dialogs.
- Modals must have clear cancel and submit actions.
- Submit actions must show progress indicators.

Suitable modal forms include:

- Add Expense Category
- Add Allocation
- Upload Document
- Enter Approval Decision
- Record Refund Evidence
- Add Petty Cash Voucher Line
- Capture Variance Reason

---

# 67. UI Acceptance Criteria

The Expenses Management Engine UI is accepted when:

- Navigation is permission-aware and feature-aware.
- All major expense domains have list, detail, and action screens.
- Requisition forms support multiple lines and allocations.
- Budget and policy results are clearly visible.
- Approval screens support line-level decisions.
- Advances clearly show balances and due dates.
- Accountabilities show submitted, approved, and rejected amounts.
- Settlement previews identify refunds, reimbursements, and recoveries.
- Petty cash screens display accurate fund balances and variances.
- Travel screens calculate per diem.
- Mileage screens calculate claims using configured rates.
- Fund transfers support request, dispatch, receipt, and variance views.
- High-impact actions require confirmation.
- Rejection and override actions require reasons.
- Forms use React Hook Form and Zod.
- All submissions show loading states.
- Error messages are actionable.
- The interface supports mobile and desktop.
- Accessibility requirements are met.
- Tenant, company, branch, and permission restrictions are enforced.
- Realtime updates do not overwrite unsaved work.
- All financial confirmations come from trusted backend services.

---

# 68. UI Summary

The Expenses Management Engine interface provides a unified and enterprise-grade experience for managing:

- Requisitions
- Direct Expenses
- Advances
- Disbursements
- Accountabilities
- Claims
- Reimbursements
- Refunds
- Recoveries
- Petty Cash
- Travel
- Per Diem
- Mileage
- Operational Fund Transfers
- Policies
- Budget Controls
- Approvals
- Reports

The interface follows the shared Business Suite frontend stack, design system, navigation model, permission framework, workflow model, and responsive standards.

---

# 69. Next Document

The next document is:

**SECURITY.md**

It will define:

- Tenant Isolation
- Row Level Security
- Permissions
- Role Models
- Segregation of Duties
- Approval Security
- Payment Security
- Petty Cash Security
- Fund Transfer Security
- Document Security
- Fraud Controls
- Data Protection
- Audit Requirements
- Integration Security
- Incident Handling
