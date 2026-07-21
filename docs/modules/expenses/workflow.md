# WORKFLOWS.md

# Expenses Management Engine Workflows

---

# 1. Overview

This document defines the end-to-end business workflows for the **Expenses Management Engine**.

The engine manages internal organizational spending and operational fund movements from initiation through approval, disbursement, utilization, accountability, settlement, reconciliation, recovery, and closure.

The workflows cover:

- Expense Requisitions
- Direct Expenses
- Staff Advances
- Advance Disbursements
- Accountabilities
- Expense Claims
- Employee Reimbursements
- Refunds
- Payroll Recoveries
- Petty Cash
- Travel Requests
- Per Diem
- Mileage Claims
- Budget Controls
- Policy Exceptions
- Procurement Conversion
- Operational Fund Transfers
- Adjustments and Reopening

The Expenses Management Engine coordinates operational processes but does not:

- Post directly to the General Ledger
- Maintain employee master records
- Execute payroll deductions
- Perform procurement
- Manage official bank transfers
- Implement independent approval logic

These responsibilities remain with their respective Business Suite engines.

---

# 2. Workflow Principles

All expense workflows must follow these principles:

- Every transaction must belong to one tenant.
- Company and branch context must be established before processing.
- Approval workflows must use the Workflow Engine.
- Document numbers must use the Document Numbering Engine.
- Supporting documents must use the Document Management Engine.
- Budget validation must occur where configured.
- Policy validation must occur at defined lifecycle stages.
- Financial actions must be processed through the Finance Engine.
- Employee data must be referenced from the Human Resources Engine.
- Payroll recoveries must be executed by the Payroll Engine.
- Procurement-required requests must be transferred to the Procurement Engine.
- Completed transactions must not be physically deleted.
- All major actions must be recorded by the Activity & Audit Engine.
- All integrations must use correlation IDs and idempotency keys.
- Segregation of duties must be enforced.
- Client-side calculations must be revalidated server-side.

---

# 3. Shared Workflow Participants

The workflows may involve the following participants:

- Requester
- Employee
- Beneficiary
- Supervisor
- Department Head
- Cost Center Manager
- Project Manager
- Budget Holder
- Finance Reviewer
- Finance Approver
- Cashier
- Disbursement Officer
- Petty Cash Custodian
- Accountability Reviewer
- Internal Auditor
- Procurement Officer
- Payroll Officer
- System Administrator
- Workflow Administrator

Tenant configuration determines the exact approval levels and role assignments.

---

# 4. Shared Workflow States

Common states include:

```text
Draft
Submitted
Under Validation
Under Review
Returned for Revision
Pending Approval
Partially Approved
Approved
Rejected
Cancelled
Pending Disbursement
Partially Disbursed
Disbursed
Pending Accountability
Accountability Submitted
Settlement Pending
Partially Settled
Settled
Completed
Closed
Overdue
Suspended
Reopened
Reversed
```

Not every workflow uses every state.

---

# 5. Shared Submission Workflow

Most expense records follow this initial process.

```text
User Creates Draft

↓

Validate Active Tenant

↓

Validate Company and Branch

↓

Validate User or Employee Eligibility

↓

Validate Required Fields

↓

Validate Expense Lines

↓

Validate Supporting Documents

↓

Evaluate Expense Policies

↓

Validate Budget Availability

↓

Generate Document Number

↓

Submit to Workflow Engine

↓

Set Status to Submitted
```

A record must remain editable while in `Draft`.

After submission, modification must follow one of these controlled paths:

- Return for Revision
- Withdraw before Approval
- Authorized Amendment
- Reopening Workflow

---

# 6. Expense Requisition Workflow

## 6.1 Purpose

The Expense Requisition workflow manages internal requests for funds before expenditure occurs.

Examples include:

- Activity funds
- Field operation funds
- Office operational expenses
- Training costs
- Fuel
- Workshops
- Travel
- Emergency expenses
- Project expenses
- Departmental expenses

---

## 6.2 Requisition Creation

```text
Requester Opens New Requisition

↓

System Loads Requester Context

↓

Requester Selects:
- Company
- Branch
- Department
- Cost Center
- Project
- Grant
- Funding Source

↓

Requester Enters:
- Title
- Purpose
- Justification
- Required Date
- Requisition Type
- Beneficiary
- Currency

↓

Requester Adds Expense Lines

↓

Requester Adds Allocations

↓

Requester Uploads Supporting Documents

↓

Save as Draft
```

---

## 6.3 Requisition Validation

Before submission, the system validates:

- Requester is active.
- Employee is eligible.
- Required fields are complete.
- At least one expense line exists.
- Requested amounts are positive.
- Allocation totals equal line totals.
- Currency is valid.
- Required date is acceptable.
- Supporting documents are attached where required.
- Expense categories are allowed.
- Outstanding advance restrictions are satisfied.
- Duplicate request indicators are evaluated.
- Budget dimensions are complete.

---

## 6.4 Policy Evaluation

```text
Evaluate Applicable Policies

↓

Check:
- Expense Category Limit
- Requester Eligibility
- Department Limits
- Project Rules
- Supporting Document Requirements
- Advance Restrictions
- Required Approval Levels
- Prohibited Expense Types

↓

Policy Result

├── Passed
├── Warning
├── Additional Approval Required
├── Override Required
└── Blocked
```

A blocked request cannot be submitted unless a defined override workflow exists.

---

## 6.5 Budget Validation

```text
Resolve Budget Dimensions

↓

Send Budget Availability Request

↓

Budget Result

├── Available
│     ↓
│ Reserve Budget if Required
│
├── Soft Warning
│     ↓
│ Allow Submission with Warning
│
├── Override Required
│     ↓
│ Start Budget Override Workflow
│
└── Insufficient Budget
      ↓
Block Submission
```

---

## 6.6 Approval Workflow

```text
Requisition Submitted

↓

Workflow Engine Selects Approval Definition

↓

Approval Steps May Include:
- Supervisor
- Department Head
- Project Manager
- Budget Holder
- Finance Reviewer
- Finance Approver
- Executive Approver

↓

Each Approver Reviews:
- Purpose
- Amount
- Budget
- Policy Results
- Documents
- Previous Comments
- Allocations

↓

Approver Action

├── Approve
├── Partially Approve
├── Return for Revision
├── Reject
├── Delegate
└── Escalate
```

---

## 6.7 Partial Approval

An approver may approve:

- Selected lines
- Reduced quantities
- Reduced unit prices
- Reduced total amounts
- Specific allocation portions

The system must:

- Recalculate approved totals.
- Record original requested values.
- Preserve line-level comments.
- Revalidate budget using approved amounts.
- Release unused reservations.
- Prevent disbursement beyond approved values.

---

## 6.8 Requisition Approval Outcome

```text
Workflow Completed

↓

Final Outcome

├── Approved for Advance
│     ↓
│ Create Staff Advance
│
├── Approved for Direct Payment
│     ↓
│ Create Payment or Disbursement Request
│
├── Approved for Petty Cash
│     ↓
│ Create Petty Cash Voucher
│
├── Procurement Required
│     ↓
│ Start Procurement Conversion
│
├── Partially Approved
│     ↓
│ Continue with Approved Amount
│
└── Rejected
      ↓
Release Budget Reservation
Close Requisition
```

---

## 6.9 Requisition Cancellation

A draft requisition may be cancelled by the requester.

A submitted requisition may be withdrawn only when:

- No approval has been finalized.
- No disbursement has occurred.
- No procurement conversion has completed.

An approved requisition requires an authorized cancellation workflow.

Cancellation must:

- Record the reason.
- Release budget reservations.
- Cancel pending workflow steps.
- Publish a cancellation event.
- Preserve audit history.

---

# 7. Direct Expense Workflow

## 7.1 Purpose

The Direct Expense workflow manages expenses incurred without a prior advance.

Examples include:

- Rent
- Utilities
- Internet
- Bank charges
- Repairs
- Subscriptions
- Fuel
- Cleaning
- Statutory fees
- Emergency purchases

---

## 7.2 Direct Expense Capture

```text
User Creates Direct Expense

↓

Select Payee Type:
- Employee
- Supplier
- Customer Refund Recipient
- Other Payee

↓

Capture:
- Expense Date
- Payee
- Expense Category
- Description
- Invoice or Receipt Number
- Amount
- Tax
- Currency
- Payment Method
- Allocations

↓

Upload Supporting Documents

↓

Save Draft
```

---

## 7.3 Direct Expense Validation

The system validates:

- Payee exists or valid details are supplied.
- Invoice or receipt is not duplicated.
- Expense date is in an allowed period.
- Tax values are valid.
- Expense category is allowed.
- Allocations balance.
- Budget is available.
- Required documents exist.
- Payment has not already been processed.
- Expense policy limits are satisfied.

---

## 7.4 Approval and Payment

```text
Direct Expense Submitted

↓

Policy and Budget Validation

↓

Approval Required?

├── Yes
│     ↓
│ Workflow Approval
│
└── No
      ↓
Proceed to Payment Request

↓

Publish Payment Request to Finance Engine

↓

Finance Outcome

├── Payment Processed
│     ↓
│ Mark Paid
│ Publish Expense Paid Event
│
├── Payment Failed
│     ↓
│ Mark Payment Failed
│ Notify Finance User
│
└── Payment Pending
      ↓
Remain Pending Payment
```

---

## 7.5 Personally Paid Direct Expense

Where an employee paid personally:

```text
Direct Expense Captured

↓

Mark Personally Paid

↓

Validate Receipt and Policy

↓

Approval

↓

Create Employee Reimbursement

↓

Finance Processes Reimbursement

↓

Complete Direct Expense
```

---

# 8. Staff Advance Workflow

## 8.1 Purpose

The Staff Advance workflow manages funds issued to an employee before an activity or expense occurs.

---

## 8.2 Advance Initiation

An advance may originate from:

- An approved requisition
- A travel request
- A project activity
- A direct advance request
- An emergency expense request

```text
Advance Source Approved

↓

Create Staff Advance

↓

Copy:
- Employee
- Purpose
- Approved Amount
- Currency
- Allocations
- Due Date
- Supporting References

↓

Check Employee Eligibility
```

---

## 8.3 Outstanding Advance Check

```text
Check Employee Outstanding Advances

↓

Policy Result

├── No Outstanding Advance
│     ↓
│ Continue
│
├── Outstanding but Allowed
│     ↓
│ Continue with Warning or Extra Approval
│
├── Maximum Advances Reached
│     ↓
│ Block
│
└── Overdue Accountability Exists
      ↓
Block or Escalate
```

---

## 8.4 Advance Approval

The Workflow Engine may require:

- Supervisor approval
- Department approval
- Project manager approval
- Budget holder approval
- Finance approval
- Executive approval

Once approved, the advance becomes eligible for disbursement.

---

# 9. Advance Disbursement Workflow

## 9.1 Disbursement Preparation

```text
Approved Advance

↓

Finance or Cashier Opens Disbursement

↓

Select Payment Method:
- Bank Transfer
- Mobile Money
- Cash
- Cheque
- Petty Cash
- Other Approved Method

↓

Confirm:
- Employee
- Approved Amount
- Payment Account
- Currency
- Payment Reference
- Disbursement Date

↓

Validate Segregation of Duties

↓

Generate Idempotency Key

↓

Submit to Finance Engine
```

---

## 9.2 Disbursement Outcome

```text
Finance Processes Request

↓

Outcome

├── Successful
│     ↓
│ Record Disbursement Reference
│ Update Advance Balance
│ Set Accountability Due Date
│ Notify Employee
│
├── Partially Successful
│     ↓
│ Record Partial Disbursement
│ Leave Remaining Amount Pending
│
├── Failed
│     ↓
│ Store Failure Reason
│ Allow Retry
│
└── Cancelled
      ↓
Record Cancellation
```

An advance must not be marked as disbursed based solely on client confirmation.

---

## 9.3 Partial Disbursement

Where partial disbursement is allowed:

- Each payment must have a unique reference.
- Total disbursement must not exceed approved amount.
- Accountability may begin only after configured conditions are met.
- The due date may be based on the first or final disbursement according to policy.

---

# 10. Accountability Workflow

## 10.1 Purpose

The Accountability workflow records and verifies how advance funds were used.

---

## 10.2 Accountability Initiation

```text
Employee Opens Outstanding Advance

↓

System Displays:
- Advance Amount
- Disbursement History
- Purpose
- Approved Lines
- Due Date
- Outstanding Balance

↓

Employee Creates Accountability

↓

System Copies Advance Context
```

---

## 10.3 Accountability Capture

The employee records:

- Expense date
- Category
- Merchant or payee
- Description
- Amount
- Tax
- Receipt number
- Invoice number
- Payment reference
- Allocation
- Supporting receipt

Multiple lines are supported.

---

## 10.4 Accountability Validation

Before submission, validate:

- Expense dates fall within allowed period.
- Amounts are positive.
- Categories are permitted.
- Receipts exist where required.
- Documents are readable or verified where applicable.
- Duplicate receipts are not already used.
- Allocation totals balance.
- Total submitted amount is recalculated.
- Policy limits are satisfied.
- Activity reports are attached where required.
- Required attendance lists or deliverables are present.

---

## 10.5 Accountability Submission

```text
Accountability Submitted

↓

Lock Submitted Lines

↓

Run Duplicate Checks

↓

Run Policy Validation

↓

Submit to Workflow Engine

↓

Notify Reviewer
```

---

## 10.6 Accountability Review

The reviewer may assess each line independently.

Reviewer actions include:

- Approve full amount
- Approve reduced amount
- Reject line
- Request clarification
- Request replacement receipt
- Reclassify category where authorized
- Flag suspected fraud
- Escalate exception

The reviewer must not silently change submitted values without preserving the original amount.

---

## 10.7 Accountability Approval

```text
Review Complete

↓

Calculate:
- Submitted Amount
- Approved Amount
- Rejected Amount

↓

Compare Approved Amount with Disbursed Amount

↓

Settlement Result

├── Equal
│     ↓
│ Fully Accounted
│
├── Approved Amount Less Than Advance
│     ↓
│ Refund or Recovery Required
│
└── Approved Amount Greater Than Advance
      ↓
Reimbursement Required
```

---

## 10.8 Returned Accountability

```text
Reviewer Returns Accountability

↓

Specify:
- Lines Requiring Correction
- Missing Documents
- Clarification Required
- Due Date

↓

Employee Revises

↓

Resubmit

↓

Workflow Continues
```

Version history must preserve all prior submissions.

---

# 11. Refund Workflow

## 11.1 Purpose

A refund is required where an employee or beneficiary retains unspent or disallowed funds.

---

## 11.2 Refund Requirement Creation

```text
Accountability Approved

↓

Unspent or Rejected Amount Identified

↓

Create Refund Requirement

↓

Generate Refund Number

↓

Set Due Date

↓

Notify Employee
```

---

## 11.3 Refund Methods

Supported methods include:

- Cash deposit
- Bank deposit
- Mobile money
- Cashier receipt
- Advance offset
- Other approved method

---

## 11.4 Refund Confirmation

```text
Employee Makes Refund

↓

Finance or Cashier Captures:
- Amount
- Date
- Method
- Reference
- Evidence

↓

Finance Engine Confirms Receipt

↓

Expenses Engine Records Refund

↓

Update Outstanding Balance

↓

Balance Remaining?

├── Yes
│     ↓
│ Keep Refund Requirement Open
│
└── No
      ↓
Mark Refunded
Continue Settlement
```

A refund must not be confirmed solely by uploading payment evidence.

---

# 12. Payroll Recovery Workflow

## 12.1 Purpose

Payroll recovery applies where an employee fails to refund or account for an approved recoverable amount.

---

## 12.2 Recovery Initiation

```text
Outstanding Refund Overdue

↓

Evaluate Recovery Policy

↓

Recovery Eligible?

├── No
│     ↓
│ Escalate or Continue Reminder
│
└── Yes
      ↓
Create Payroll Recovery Request
```

---

## 12.3 Recovery Approval

Depending on tenant policy, recovery may require:

- Finance approval
- Human Resources approval
- Legal approval
- Employee acknowledgment
- Executive approval

---

## 12.4 Payroll Integration

```text
Approved Recovery Request

↓

Send Recovery Command to Payroll Engine

↓

Payroll Response

├── Accepted
│     ↓
│ Schedule Deduction
│
├── Partially Accepted
│     ↓
│ Record Installment Plan
│
└── Rejected
      ↓
Record Reason and Escalate
```

---

## 12.5 Recovery Completion

```text
Payroll Period Processed

↓

Payroll Publishes Recovery Confirmation

↓

Expenses Engine Updates Recovered Amount

↓

Outstanding Balance Recalculated

↓

Fully Recovered?

├── No
│     ↓
│ Continue Future Deductions
│
└── Yes
      ↓
Complete Recovery
Close Settlement
```

The Expenses Management Engine must never alter payroll calculations directly.

---

# 13. Expense Claim Workflow

## 13.1 Purpose

The Expense Claim workflow manages reimbursement requests for expenses paid personally by employees.

---

## 13.2 Claim Submission

```text
Employee Creates Claim

↓

Capture Claim Lines

↓

Upload Receipts

↓

Select Allocations

↓

System Validates:
- Policy
- Budget
- Duplicate Receipts
- Claim Period
- Employee Eligibility

↓

Submit for Approval
```

---

## 13.3 Claim Review

Approvers may:

- Approve all lines
- Partially approve
- Reject lines
- Return for revision
- Request clarification
- Require policy override

Approved amounts must be recalculated server-side.

---

## 13.4 Claim Outcome

```text
Claim Approved

↓

Create Reimbursement Request

↓

Finance Processes Payment

↓

Payment Confirmed

↓

Mark Claim Reimbursed

↓

Close Claim
```

Rejected claim lines remain preserved for audit.

---

# 14. Employee Reimbursement Workflow

## 14.1 Reimbursement Sources

A reimbursement may originate from:

- Expense Claim
- Accountability Overspend
- Personally Paid Direct Expense
- Mileage Claim
- Travel Expense
- Emergency Expense

---

## 14.2 Reimbursement Processing

```text
Approved Reimbursement Requirement

↓

Validate Employee Payment Details

↓

Create Finance Payment Request

↓

Generate Idempotency Key

↓

Finance Processes Payment

↓

Outcome

├── Paid
│     ↓
│ Record Payment Reference
│ Mark Reimbursed
│
├── Failed
│     ↓
│ Record Failure
│ Allow Retry
│
└── Cancelled
      ↓
Return for Review
```

A reimbursement must not exceed the approved reimbursement amount.

---

# 15. Petty Cash Fund Setup Workflow

## 15.1 Fund Creation

```text
Authorized User Creates Petty Cash Fund

↓

Capture:
- Company
- Branch
- Fund Name
- Fund Code
- Currency
- Custodian
- Authorized Float
- Minimum Balance
- Maximum Transaction Amount
- Cash Account Reference

↓

Submit for Approval

↓

Approval Completed

↓

Finance Funds the Float

↓

Record Opening Transaction

↓

Activate Fund
```

---

## 15.2 Custodian Assignment

Custodian assignment must validate:

- Employee is active.
- Employee has petty cash permissions.
- Employee is assigned to the relevant company or branch.
- No prohibited conflict exists.
- Handover is completed where replacing a previous custodian.

---

# 16. Petty Cash Voucher Workflow

## 16.1 Voucher Creation

```text
Requester Creates Petty Cash Voucher

↓

Select Fund

↓

Capture:
- Payee
- Purpose
- Category
- Amount
- Supporting Documents

↓

Validate:
- Fund Active
- Amount Within Limit
- Sufficient Balance
- Policy Compliance

↓

Approval Required?

├── Yes
│     ↓
│ Workflow Approval
│
└── No
      ↓
Proceed to Payment
```

---

## 16.2 Petty Cash Payment

```text
Approved Voucher

↓

Custodian Confirms Payee

↓

System Revalidates Balance

↓

Record Payment

↓

Reduce Fund Balance

↓

Generate Voucher or Receipt

↓

Notify Requester
```

A custodian must not pay beyond the available fund balance.

---

## 16.3 Petty Cash Accountability

Where a petty cash payment is issued before final evidence is available:

```text
Cash Issued

↓

Voucher Marked Pending Accountability

↓

Requester Uploads Receipt

↓

Custodian or Reviewer Verifies

↓

Voucher Closed
```

Unaccounted petty cash vouchers must remain visible in reconciliation.

---

# 17. Petty Cash Replenishment Workflow

```text
Fund Reaches Replenishment Threshold

↓

Custodian Creates Replenishment Request

↓

System Calculates:
- Approved Vouchers
- Accounted Payments
- Refunds
- Variances
- Required Replenishment

↓

Submit for Approval

↓

Finance Reviews Supporting Transactions

↓

Approved Amount Sent to Finance Engine

↓

Finance Funds Petty Cash

↓

Record Replenishment Transaction

↓

Update Fund Balance
```

Replenishment must not include unsupported or unapproved vouchers.

---

# 18. Petty Cash Reconciliation Workflow

## 18.1 Reconciliation Initiation

```text
Custodian Starts Reconciliation

↓

System Calculates Expected Cash

↓

Custodian Counts Actual Cash

↓

Capture Unaccounted Vouchers

↓

Calculate Variance
```

---

## 18.2 No-Variance Outcome

```text
Expected Cash = Actual Cash + Valid Vouchers

↓

Submit Reconciliation

↓

Independent Reviewer Approves

↓

Mark Reconciled
```

---

## 18.3 Variance Outcome

```text
Variance Detected

↓

Capture:
- Variance Type
- Amount
- Reason
- Supporting Evidence

↓

Create Expense Exception

↓

Submit for Investigation or Approval

↓

Outcome

├── Corrected
├── Custodian Refund Required
├── Payroll Recovery Required
├── Approved Adjustment
├── Write-Off Requested
└── Investigation Escalated
```

The custodian must not approve their own reconciliation.

---

# 19. Travel Request Workflow

## 19.1 Travel Request Creation

```text
Employee Creates Travel Request

↓

Capture:
- Travel Purpose
- Destination
- Departure Date
- Return Date
- Travel Type
- Project or Department
- Estimated Costs
- Advance Requirement

↓

System Validates Travel Eligibility

↓

Calculate Estimated Per Diem

↓

Validate Budget and Policy

↓

Submit for Approval
```

---

## 19.2 Travel Approval

Approval levels may include:

- Supervisor
- Department Head
- Project Manager
- Human Resources
- Finance
- Executive Approver

The workflow may also validate:

- Travel class
- Destination restrictions
- Accommodation limits
- Duration
- Security or duty-of-care requirements

---

## 19.3 Approved Travel Outcome

```text
Travel Approved

↓

Advance Required?

├── Yes
│     ↓
│ Create Staff Advance
│
└── No
      ↓
Travel Proceeds Without Advance

↓

After Travel

↓

Submit Travel Accountability or Claim

↓

Settle and Close Travel Request
```

---

# 20. Per Diem Workflow

## 20.1 Per Diem Calculation

```text
Approved Travel Request

↓

Resolve Employee Grade

↓

Resolve Destination Rate

↓

Determine Eligible Days

↓

Apply:
- Departure Rules
- Return Rules
- Meal Deductions
- Accommodation Provided
- Partial-Day Rules
- Currency Rules

↓

Calculate Gross Per Diem

↓

Apply Deductions

↓

Calculate Payable Amount
```

---

## 20.2 Per Diem Approval

The calculated amount may be:

- Automatically approved within policy
- Included in travel approval
- Sent for separate approval
- Overridden with justification

All manual overrides must be audited.

---

# 21. Mileage Claim Workflow

```text
Employee Creates Mileage Claim

↓

Capture:
- Journey Date
- Origin
- Destination
- Purpose
- Distance
- Vehicle Type
- Travel Request Reference

↓

Resolve Mileage Rate

↓

Calculate Claim Amount

↓

Validate Policy and Duplicate Journey

↓

Submit for Approval

↓

Approved Amount Determined

↓

Create Reimbursement Request

↓

Finance Pays

↓

Close Mileage Claim
```

Future GPS validation may supplement, but must not replace, approved business rules.

---

# 22. Budget Override Workflow

## 22.1 Trigger

A budget override may be triggered when:

- Budget is insufficient.
- No budget line exists.
- Amount exceeds budget threshold.
- Spending is outside the planned period.
- Emergency spending is requested.

---

## 22.2 Override Process

```text
Budget Validation Fails

↓

Create Budget Override Request

↓

Capture:
- Requested Amount
- Available Amount
- Variance
- Reason
- Business Impact
- Supporting Documents

↓

Submit to Workflow Engine

↓

Approvers May Include:
- Budget Holder
- Finance Manager
- Project Manager
- Executive Approver

↓

Outcome

├── Approved
│     ↓
│ Record Override
│ Continue Expense Workflow
│
├── Partially Approved
│     ↓
│ Reduce Expense Amount
│
└── Rejected
      ↓
Block Expense
```

Approval of an override does not directly update official budget balances.

---

# 23. Policy Exception Workflow

## 23.1 Trigger

Policy exceptions may include:

- Missing receipt
- Amount above category limit
- Late accountability
- Additional outstanding advance
- Prohibited expense category
- Travel class exception
- Per diem override
- Duplicate expense suspicion
- Emergency expenditure

---

## 23.2 Exception Process

```text
Policy Exception Detected

↓

Classify Severity

↓

Create Exception Record

↓

Require Justification

↓

Submit for Exception Approval

↓

Outcome

├── Approved
│     ↓
│ Record Approver and Reason
│ Continue Workflow
│
├── Approved with Conditions
│     ↓
│ Apply Additional Controls
│
└── Rejected
      ↓
Block or Return Transaction
```

---

# 24. Procurement Conversion Workflow

## 24.1 Trigger

A requisition should be converted to procurement where it involves:

- Supplier selection
- Competitive quotation
- Purchase order
- Goods receipt
- Inventory items
- Capital assets
- Formal vendor contract
- Procurement policy thresholds

---

## 24.2 Conversion Process

```text
Expense Requisition Approved

↓

Mark Procurement Required

↓

Validate Requisition Lines

↓

Create Procurement Conversion Request

↓

Send to Procurement Engine

↓

Procurement Engine Creates Purchase Requisition

↓

Return Procurement Reference

↓

Expenses Engine Records Conversion

↓

Release or Transfer Budget Reservation

↓

Set Requisition Status to Converted
```

---

## 24.3 Conversion Failure

```text
Procurement Conversion Fails

↓

Record Failure Reason

↓

Retry Automatically or Manually

↓

Do Not Create Duplicate Purchase Requisition

↓

Escalate After Maximum Retries
```

The originating expense requisition remains the source record.

---

# 25. Operational Fund Transfer Workflow

## 25.1 Purpose

Operational Fund Transfers manage controlled movement of operational cash or float between:

- Head office and branch
- Branches
- Field offices
- Projects
- Petty cash funds
- Custodians
- Activity coordinators

They do not replace bank transfers or official treasury operations owned by Finance.

---

## 25.2 Transfer Types

```text
branch_funding
field_office_funding
project_funding
custodian_transfer
petty_cash_transfer
activity_funding
fund_return
temporary_float
```

---

## 25.3 Transfer Request

```text
Authorized User Creates Transfer Request

↓

Select:
- Source Company or Branch
- Source Fund or Custodian
- Destination Branch or Custodian
- Amount
- Currency
- Purpose
- Required Date

↓

Validate:
- Available Operational Balance
- Transfer Limits
- Destination Eligibility
- Segregation of Duties
- Supporting Documents

↓

Submit for Approval
```

---

## 25.4 Transfer Approval and Release

```text
Transfer Approved

↓

Finance Validates Funding Source

↓

Source Custodian Releases Funds

↓

Record Release Reference

↓

Set Transfer Status to In Transit
```

---

## 25.5 Transfer Receipt

```text
Destination Receives Funds

↓

Recipient Confirms:
- Amount Received
- Date
- Method
- Reference
- Evidence

↓

Amount Matches?

├── Yes
│     ↓
│ Record Receipt
│ Complete Transfer
│
└── No
      ↓
Create Transfer Variance
Start Investigation
```

---

## 25.6 Custodian Handover

```text
Outgoing Custodian Initiates Handover

↓

System Calculates:
- Expected Cash
- Outstanding Vouchers
- Pending Refunds
- Unresolved Variances

↓

Joint Cash Count

↓

Incoming Custodian Confirms Receipt

↓

Independent Reviewer Approves

↓

Update Custodian Assignment

↓

Close Handover
```

A custodian assignment must not change without resolving or acknowledging outstanding balances.

---

# 26. Expense Adjustment Workflow

## 26.1 Purpose

Adjustments correct valid operational records without deleting financial history.

Examples include:

- Incorrect category
- Incorrect allocation
- Incorrect payee description
- Incorrect project
- Approved amount correction
- Duplicate record reversal
- Wrong currency reference

---

## 26.2 Adjustment Process

```text
User Requests Adjustment

↓

Capture:
- Original Record
- Error Description
- Proposed Correction
- Reason
- Supporting Evidence

↓

Check Record Status

├── Draft
│     ↓
│ Edit Directly
│
├── Submitted but Not Approved
│     ↓
│ Return for Revision
│
└── Approved or Financially Processed
      ↓
Start Adjustment Workflow
```

---

## 26.3 Financial Adjustment

Where Finance has already processed the expense:

```text
Adjustment Approved

↓

Publish Reversal or Correction Request to Finance Engine

↓

Finance Confirms Adjustment

↓

Create Linked Corrected Expense Record

↓

Preserve Original Record

↓

Close Adjustment
```

---

# 27. Reopening Workflow

## 27.1 Reopening Conditions

A closed transaction may be reopened only where:

- Audit correction is required.
- Supporting evidence was later found invalid.
- Settlement was incomplete.
- Finance posting failed or was reversed.
- Fraud investigation requires review.
- Authorized correction is approved.

---

## 27.2 Reopening Process

```text
Authorized User Requests Reopening

↓

Capture Reason and Evidence

↓

Submit to Reopening Workflow

↓

Approvers Review Impact

↓

Outcome

├── Approved
│     ↓
│ Set Status to Reopened
│ Restrict Editable Fields
│
└── Rejected
      ↓
Remain Closed
```

Reopening must generate a new audit event and preserve the previous closure details.

---

# 28. Reversal Workflow

A reversal is used where a completed operational transaction must be negated.

```text
Reversal Requested

↓

Validate:
- Original Transaction Exists
- No Previous Full Reversal
- User Has Permission
- Downstream Impact Identified

↓

Approval

↓

Send Reversal Request to Finance Engine if Applicable

↓

Create Reversal Record

↓

Update Original Record as Reversed

↓

Release Budget or Restore Balance Where Appropriate

↓

Publish Reversal Event
```

The original transaction remains immutable.

---

# 29. Overdue Advance Workflow

```text
Scheduled Job Finds Due Advance

↓

Accountability Not Submitted?

├── No
│     ↓
│ No Action
│
└── Yes
      ↓
Mark Accountability Due or Overdue

↓

Send Employee Reminder

↓

Notify Supervisor

↓

Escalation Threshold Reached?

├── No
│     ↓
│ Continue Reminders
│
└── Yes
      ↓
Block New Advances
Notify Finance and HR
Consider Recovery Workflow
```

---

# 30. Overdue Refund Workflow

```text
Refund Due Date Reached

↓

Outstanding Balance Exists

↓

Send Reminder

↓

Escalate to Supervisor and Finance

↓

Grace Period Expires

↓

Create Recovery Evaluation

↓

Outcome

├── Payroll Recovery
├── Advance Offset
├── Legal Recovery
├── Exception Approval
└── Extended Deadline
```

---

# 31. Duplicate Expense Review Workflow

```text
Duplicate Detector Finds Match

↓

Create Duplicate Check Record

↓

Set Transaction to Review Required

↓

Reviewer Compares:
- Employee
- Date
- Amount
- Merchant
- Receipt Number
- Document Hash
- Payment Reference

↓

Outcome

├── Not Duplicate
│     ↓
│ Continue
│
├── Duplicate
│     ↓
│ Reject or Reverse
│
├── Legitimate Repeat Expense
│     ↓
│ Approve with Reason
│
└── Suspected Fraud
      ↓
Create Investigation Exception
```

---

# 32. Missing Receipt Workflow

```text
Receipt Required but Missing

↓

Policy Determines Action

├── Block Submission
├── Allow Declaration
├── Require Supervisor Approval
├── Require Finance Exception
└── Allow Below Threshold

↓

User Provides Missing Receipt Declaration

↓

Exception Workflow

↓

Approved?

├── Yes
│     ↓
│ Continue
│
└── No
      ↓
Reject Expense Line
```

---

# 33. Emergency Expense Workflow

```text
User Creates Emergency Expense

↓

Select Emergency Reason

↓

Capture Immediate Business Impact

↓

Upload Available Evidence

↓

Emergency Approval Path

↓

Expense Allowed Before Full Documentation?

├── Yes
│     ↓
│ Process Conditional Disbursement
│ Set Documentation Deadline
│
└── No
      ↓
Wait for Full Approval

↓

Post-Expense Review

↓

Complete Documents and Accountability

↓

Close Emergency Expense
```

Emergency status must not bypass audit or later review.

---

# 34. Multi-Currency Workflow

```text
Expense Captured in Transaction Currency

↓

Retrieve Approved Exchange Rate

↓

Store:
- Transaction Currency
- Exchange Rate
- Base Currency Amount

↓

Finance Engine Validates Official Rate

↓

Settlement Occurs

↓

Exchange Difference?

├── No
│     ↓
│ Complete
│
└── Yes
      ↓
Finance Handles Exchange Difference
Expenses Engine Stores Reference
```

The Expenses Management Engine must not calculate official foreign exchange gains or losses.

---

# 35. Workflow Delegation

Where an approver is absent:

```text
Workflow Step Assigned

↓

Valid Delegation Exists?

├── Yes
│     ↓
│ Assign Delegate
│ Record Delegation
│
└── No
      ↓
Retain Original Approver
```

Delegation rules remain owned by the Workflow Engine.

---

# 36. Workflow Escalation

```text
Approval Step Remains Pending

↓

Escalation Time Reached

↓

Workflow Engine Executes:
- Reminder
- Supervisor Escalation
- Alternate Approver
- Executive Escalation

↓

Record Escalation History
```

The Expenses Management Engine consumes the resulting workflow state.

---

# 37. Segregation of Duties

The following controls should apply:

- Requesters should not approve their own requests.
- Employees should not approve their own accountabilities.
- Cashiers should not approve payment requests they process.
- Petty cash custodians should not approve their own reconciliations.
- Fund recipients should not independently confirm source release and destination receipt.
- Policy override approvers should be independent of the requester.
- Budget override approval should not be performed solely by the requester’s department where higher control is required.
- Reopening and reversals must require elevated permission.

Tenant policy may define limited exceptions, but all exceptions must be audited.

---

# 38. Workflow Notifications

Notifications should be issued for:

- Draft nearing expiry
- Requisition submitted
- Approval assigned
- Request returned
- Request approved
- Request rejected
- Disbursement completed
- Disbursement failed
- Accountability due
- Accountability overdue
- Refund required
- Refund overdue
- Reimbursement paid
- Payroll recovery initiated
- Petty cash low balance
- Replenishment approved
- Reconciliation variance
- Operational transfer dispatched
- Operational transfer received
- Policy exception
- Budget override
- Workflow escalation

The Notification Engine controls templates and channels.

---

# 39. Workflow Audit Trail

Every workflow action must record:

- Entity type
- Entity ID
- Workflow instance
- Workflow step
- Actor
- Action
- Previous status
- New status
- Amount before and after
- Comments
- Reason
- Timestamp
- Delegation information
- Escalation information
- Correlation ID
- Device or session context

---

# 40. Workflow Event Publishing

Major events include:

```text
expense.requisition.submitted
expense.requisition.approved
expense.requisition.rejected
expense.requisition.converted_to_procurement

expense.advance.approved
expense.advance.disbursement_requested
expense.advance.disbursed
expense.advance.overdue

expense.accountability.submitted
expense.accountability.approved
expense.accountability.settled

expense.refund.required
expense.refund.received

expense.reimbursement.requested
expense.reimbursement.paid

expense.recovery.requested
expense.recovery.completed

expense.petty_cash.payment_recorded
expense.petty_cash.replenished
expense.petty_cash.reconciled
expense.petty_cash.variance_recorded

expense.travel.approved
expense.mileage.approved

expense.fund_transfer.approved
expense.fund_transfer.dispatched
expense.fund_transfer.received
expense.fund_transfer.variance_recorded

expense.adjustment.approved
expense.record.reopened
expense.transaction.reversed
```

---

# 41. Workflow Failure Handling

Failures may occur during:

- Budget validation
- Workflow submission
- Finance integration
- Procurement conversion
- Payroll recovery
- Document verification
- Event publication
- Notification delivery

The system must:

- Preserve committed business state.
- Store the failure reason.
- Mark integrations as retryable where applicable.
- Avoid duplicate financial effects.
- Notify authorized users.
- Use dead-letter handling after retry exhaustion.
- Support manual replay.
- Maintain correlation IDs.

---

# 42. Idempotent Workflow Actions

Idempotency is required for:

- Workflow submission
- Advance disbursement
- Direct payment request
- Reimbursement request
- Refund receipt
- Payroll recovery request
- Petty cash replenishment
- Procurement conversion
- Operational fund transfer release
- Operational fund transfer receipt
- Reversal request

Repeated requests must return the original result.

---

# 43. Scheduled Workflow Jobs

Scheduled jobs may process:

- Accountability reminders
- Overdue advance detection
- Refund reminders
- Reimbursement follow-up
- Recovery follow-up
- Low petty cash balance
- Reconciliation deadlines
- Operational transfer receipt reminders
- Workflow escalations
- Expired budget reservations
- Failed integration retries
- Draft expiry
- Policy activation and expiry

---

# 44. Workflow Reporting

Workflow reporting should expose:

- Requests by status
- Approval turnaround time
- Approval bottlenecks
- Rejected requests
- Returned requests
- Partially approved requests
- Outstanding advances
- Overdue accountabilities
- Refunds outstanding
- Recoveries outstanding
- Reimbursements pending
- Petty cash variances
- Fund transfers in transit
- Budget overrides
- Policy exceptions
- Emergency expenses
- Reopened transactions
- Reversals

---

# 45. Workflow Performance Requirements

The engine should support:

- High-volume submissions
- Concurrent approvers
- Multi-company approval structures
- Multi-branch operations
- Complex approval thresholds
- Parallel approval steps
- Delegation and escalation
- Large supporting document collections
- Real-time status updates

Long-running actions should use asynchronous processing where appropriate.

---

# 46. Workflow Security Requirements

Every workflow action must validate:

- Tenant
- Company
- Branch
- User
- Employee
- Permission
- Workflow assignment
- Record status
- Version
- Amount authority
- Segregation of duties
- Required comments
- Required documents

A user must not act on a workflow step merely because they know the entity ID.

---

# 47. Workflow Acceptance Criteria

The workflows are accepted when:

- Requisitions support full, partial, returned, rejected, and cancelled outcomes.
- Approved requisitions can create advances, direct payments, petty cash vouchers, or procurement requests.
- Staff advances cannot be disbursed before approval.
- Disbursements are idempotent.
- Accountabilities support line-level approval and rejection.
- Settlement correctly identifies refunds, reimbursements, and recoveries.
- Refunds are confirmed through Finance.
- Payroll recovery is executed only through Payroll.
- Expense claims generate approved reimbursements.
- Petty cash balances reflect all fund transactions.
- Petty cash reconciliation identifies and handles variances.
- Travel, per diem, and mileage workflows enforce policies.
- Budget override workflows are fully auditable.
- Policy exception workflows preserve reasons and approvers.
- Procurement conversion does not duplicate procurement records.
- Operational fund transfers support dispatch, receipt, and variance handling.
- Adjustments, reopening, and reversals preserve original records.
- All workflows enforce tenant isolation and segregation of duties.
- All major state changes publish events.
- Workflow history remains immutable and reportable.

---

# 48. Workflow Summary

The Expenses Management Engine workflows provide complete operational control over:

- Internal fund requests
- Expense approvals
- Direct expenses
- Staff advances
- Disbursements
- Accountabilities
- Claims
- Reimbursements
- Refunds
- Recoveries
- Petty cash
- Travel
- Per diem
- Mileage
- Budget overrides
- Policy exceptions
- Procurement conversion
- Operational fund transfers
- Adjustments
- Reopening
- Reversals

The workflows preserve clear ownership boundaries and integrate with the Workflow Engine, Finance Engine, Human Resources Engine, Payroll Engine, Procurement Engine, Document Management Engine, Notification Engine, Reporting Engine, Audit Engine, Authorization Engine, and Platform Event Bus.

---

# 49. Next Document

The next document is:

**UI.md**

It will define:

- Expenses Dashboard
- Requisition Screens
- Direct Expense Screens
- Advance Screens
- Accountability Screens
- Claims and Reimbursement Screens
- Refund and Recovery Screens
- Petty Cash Screens
- Travel and Mileage Screens
- Operational Fund Transfer Screens
- Policy and Budget Controls
- Approval Workspaces
- Reports
- Navigation
- Forms
- Modals
- Validation
- Mobile and Responsive Behavior
