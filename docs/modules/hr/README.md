# Business Suite Enterprise Platform

# Human Resources Engine

## README.md

---

## 1. Document Control

| Field              | Value                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------ |
| Document Name      | Human Resources Engine – README                                                                              |
| Platform           | Business Suite Enterprise Platform                                                                           |
| Engine             | Human Resources Engine                                                                                       |
| Bounded Context    | Human Capital Management                                                                                     |
| Document Type      | Engine Overview and Functional Scope                                                                         |
| Architecture Style | Domain-Driven, Event-Driven, Modular, Multi-Tenant                                                           |
| Status             | Architecture Specification                                                                                   |
| Version            | 1.0                                                                                                          |
| Primary Owner      | Human Resources Engine                                                                                       |
| Related Engine     | Payroll Engine                                                                                               |
| Technology Stack   | React, TypeScript, Supabase, PostgreSQL                                                                      |
| Intended Audience  | Product Owners, Architects, Developers, HR Specialists, Security Teams, QA Teams, Implementation Consultants |

---

## 2. Introduction

The **Human Resources Engine** is the authoritative human capital management bounded context within the Business Suite Enterprise Platform.

It manages the complete lifecycle of employees, workers, applicants, contractors, interns, volunteers, consultants, temporary staff, and other workforce participants from initial workforce planning and recruitment through onboarding, active employment, development, movement, performance, and eventual separation.

The Human Resources Engine is designed as a reusable enterprise platform capability rather than a collection of disconnected HR screens.

It provides the authoritative source for:

- Employee identity within the organization
- Employment relationships
- Organizational assignments
- Reporting relationships
- Employment contracts
- Employee lifecycle status
- Workforce qualifications
- Skills and competencies
- Leave eligibility
- Attendance participation
- Performance participation
- Training participation
- Employee movements
- Employee separation
- Employee self-service
- Manager self-service

The Human Resources Engine owns people and employment lifecycle information.

It does not own payroll calculations, inventory items, customer records, accounting journals, expense transactions, authentication credentials, workflow definitions, notification delivery, document binaries, or reporting infrastructure.

Those responsibilities remain with their respective engines.

---

## 3. Purpose

The purpose of the Human Resources Engine is to provide a complete, enterprise-grade foundation for managing people, employment relationships, workforce structures, and HR operations across multiple tenants, companies, branches, countries, projects, departments, and legal entities.

The engine must support organizations ranging from small businesses to complex institutions such as:

- Multi-company enterprises
- Government agencies
- Ministries and public authorities
- NGOs
- International development organizations
- Donor-funded programs
- Universities
- Schools
- Hospitals
- Financial institutions
- Retail organizations
- Manufacturing organizations
- Professional service firms
- Construction companies
- Distributed field operations
- Multi-country organizations

The engine must accommodate differences in:

- Employment law
- Labor practices
- Contract structures
- Leave policies
- Working calendars
- Organization structures
- Approval hierarchies
- Reporting structures
- Data retention rules
- Employee numbering conventions
- Public-sector grading structures
- Donor-funded project assignments
- Country-specific HR requirements

---

## 4. Engine Mission

The mission of the Human Resources Engine is to provide a secure, auditable, configurable, and extensible system of record for the organization’s workforce.

The engine shall:

1. Maintain accurate employee and employment records.
2. Manage the complete employee lifecycle.
3. Support configurable organizational structures.
4. Support dynamic reporting hierarchies.
5. Automate HR approvals using the Workflow Engine.
6. provide employee and manager self-service capabilities.
7. Manage recruitment and applicant tracking.
8. Manage onboarding and offboarding.
9. Manage employee contracts and contract renewals.
10. Manage leave, attendance, timesheets, overtime, shifts, and absences.
11. Manage performance, goals, competencies, and reviews.
12. Manage training, skills, qualifications, and development.
13. Support workforce mobility across companies, branches, departments, and projects.
14. Publish authoritative employee events through the Platform Event Bus.
15. Supply validated employee information to the Payroll Engine.
16. Maintain strict ownership boundaries with other Business Suite engines.
17. Support statutory, governance, donor, and institutional reporting requirements.
18. Preserve historical workforce records for audit and reporting.
19. Support high-volume HR operations.
20. Provide extensible foundations for future workforce intelligence capabilities.

---

## 5. Engine Position in the Platform

The Human Resources Engine is a business engine operating on top of the completed Business Suite Platform Engines.

```text
┌───────────────────────────────────────────────────────────────────────┐
│                      Business Suite Platform                         │
├───────────────────────────────────────────────────────────────────────┤
│ Platform Core                                                        │
│ Authorization Engine                                                 │
│ Workflow Engine                                                      │
│ Reference Data Engine                                                │
│ Document Numbering Engine                                            │
│ Document Management Engine                                           │
│ Notification Engine                                                  │
│ Reporting Engine                                                     │
│ Search & Indexing Engine                                              │
│ Platform Activity & Audit Engine                                      │
│ Platform Event Bus                                                    │
├───────────────────────────────────────────────────────────────────────┤
│ Business Engines                                                     │
│                                                                       │
│ Finance Engine          CRM Module             Sales Module           │
│ Inventory Engine        POS Module             Expenses Engine        │
│                                                                       │
│ ┌─────────────────────────────┐    ┌───────────────────────────────┐  │
│ │ Human Resources Engine      │───▶│ Payroll Engine                │  │
│ │                             │    │                               │  │
│ │ Owns people and employment  │    │ Owns compensation and payroll│  │
│ └─────────────────────────────┘    └───────────────────────────────┘  │
└───────────────────────────────────────────────────────────────────────┘
```

The Human Resources Engine is the workforce system of record.

The Payroll Engine consumes approved and effective-dated HR information but remains an independently owned bounded context.

---

## 6. Bounded Context Definition

The Human Resources Engine is responsible for workforce and employment management.

Its domain boundary begins when a person becomes:

- An applicant
- A prospective worker
- A candidate
- An employee
- A consultant
- A contractor
- An intern
- A volunteer
- A temporary worker
- A secondee
- An external workforce participant

Its active lifecycle continues through:

- Recruitment
- Selection
- Offer
- Pre-employment
- Onboarding
- Employment
- Assignment
- Development
- Movement
- Performance
- Leave
- Attendance
- Disciplinary management
- Separation
- Exit clearance
- Alumni or former employee status

The bounded context ends where compensation processing begins.

The Human Resources Engine may define an employee’s:

- Employment status
- Contract
- Position
- Job grade
- Department
- Branch
- Work location
- Supervisor
- Leave eligibility
- Standard work schedule
- Approved attendance
- Approved overtime
- Approved unpaid leave
- Payroll eligibility
- Compensation reference assignment

The Human Resources Engine does not calculate:

- Gross salary
- Net salary
- PAYE
- Pension deductions
- NSSF
- SACCO deductions
- Payroll liabilities
- Payroll journals
- Payroll payment files
- Employee deduction balances
- Final payroll values

These remain under the Payroll Engine.

---

## 7. Domain Ownership Statement

The Human Resources Engine owns the authoritative records for:

- Applicants
- Candidates
- Employees
- Employment relationships
- Employee profiles
- Employment contracts
- Employment assignments
- Departments
- Positions
- Jobs
- Job grades
- Designations
- Reporting lines
- Organization charts
- Employee movements
- Employee qualifications
- Employee skills
- Employee competencies
- Employee certifications
- Employee documents metadata
- Employee dependants
- Emergency contacts
- Work history
- Education history
- Leave records
- Attendance records
- Timesheets
- Shift assignments
- Overtime requests
- Performance records
- Goals
- KPIs
- Training participation
- Disciplinary cases
- Grievances
- Onboarding cases
- Offboarding cases
- Employee lifecycle status

The Human Resources Engine is the only engine permitted to create or change authoritative employee master and employment lifecycle data.

Other engines may reference HR identifiers but must not duplicate HR ownership.

---

## 8. Non-Ownership Boundaries

The Human Resources Engine does not own the following domains.

### 8.1 Authentication and User Accounts

Platform Core owns:

- Login credentials
- Supabase Auth identities
- Authentication sessions
- Passwords
- Multi-factor authentication
- OAuth identities
- User invitations
- Tenant membership
- Active tenant context

An employee may exist without a user account.

A user account may exist without an employee record.

The Human Resources Engine links employees to Platform Core users through controlled references.

---

### 8.2 Payroll and Compensation Processing

The Payroll Engine owns:

- Salary structures
- Payroll earnings
- Payroll deductions
- Payroll calculations
- Statutory calculations
- Payroll periods
- Payroll runs
- Payslips
- Payroll journals
- Payroll payment files
- Payroll posting
- Payroll audit calculations

HR may maintain employment-related compensation references, but Payroll remains the authoritative owner of processed compensation.

---

### 8.3 Finance and Accounting

The Finance Engine owns:

- General ledger accounts
- Financial journals
- Accounts payable
- Accounts receivable
- Bank accounts
- Financial periods
- Financial postings
- Cost centers where Finance is the authoritative owner
- Financial statements

HR may reference Finance dimensions such as cost centers, projects, and account structures.

---

### 8.4 Expenses and Staff Advances

The Expenses Management Engine owns:

- Expense requisitions
- Staff advances
- Travel advances
- Accountability
- Expense claims
- Reimbursements
- Per diem transactions
- Mileage claims
- Expense settlement
- Expense recoveries

HR owns the employee receiving or submitting the expense.

---

### 8.5 Inventory and Assets

The Inventory Engine owns:

- Inventory item master
- Stock quantities
- Warehouses
- Stock movements
- Equipment items
- Uniform items
- Tools
- Consumables
- Item valuation

HR owns the employee assignment relationship where an inventory item is issued to an employee.

The Inventory Engine remains authoritative for the item and stock movement.

---

### 8.6 Customers and Sales Territories

The CRM Module owns:

- Customers
- Accounts
- Contacts
- Leads
- Opportunities
- Customer territories
- Account relationships

HR owns the employee assigned as an account manager, salesperson, or territory owner.

---

### 8.7 POS Operations

The POS Module owns:

- Tills
- Registers
- POS shifts
- Cash sessions
- POS transactions
- Cashier sales
- Cash variances

HR owns the employee who is authorized or assigned to operate the POS.

---

### 8.8 Platform Workflows

The Workflow Engine owns:

- Workflow definitions
- Workflow versions
- Workflow steps
- Approval routing
- Approver resolution
- Escalations
- Delegations
- Workflow execution
- Approval decisions

HR owns the business document being approved.

---

### 8.9 Documents and Files

The Document Management Engine owns:

- File storage
- File versions
- Document access controls
- Document retention
- File hashes
- Document links
- File metadata infrastructure

HR owns the business meaning and classification of HR documents.

---

### 8.10 Notifications

The Notification Engine owns:

- Notification delivery
- Templates
- Channels
- Delivery status
- Retry handling
- Email delivery
- SMS delivery
- Push delivery
- In-app delivery

HR publishes notification requests and business events.

---

## 9. Core Architectural Principles

The Human Resources Engine shall follow the established Business Suite architecture principles.

### 9.1 Multi-Tenant by Design

Every HR business record must belong to a tenant.

Tenant isolation must be enforced using PostgreSQL Row Level Security.

No query, function, report, event, document, or workflow may expose data across tenant boundaries.

Every HR table must contain or inherit a valid tenant context.

---

### 9.2 Multi-Company Support

A tenant may operate multiple companies or legal entities.

Employees may:

- Work for one company
- Transfer between companies
- Hold concurrent assignments
- Be seconded between companies
- Provide shared services across companies
- Have historical employment under multiple companies

Company assignments must be effective-dated.

---

### 9.3 Multi-Branch Support

Employees may be assigned to:

- Head office
- Regional offices
- Branches
- Field offices
- Stores
- Warehouses
- Project sites
- Client sites
- Remote work locations

Branch changes must be handled as employee movements rather than destructive updates.

---

### 9.4 Effective-Dated Records

Employment information that changes over time must support effective dating.

Examples include:

- Employment contracts
- Positions
- Job grades
- Departments
- Supervisors
- Branch assignments
- Work locations
- Employment status
- Working schedules
- Leave policies
- Project assignments
- Payroll eligibility
- Probation periods

Historical records must remain available for reporting and audit.

---

### 9.5 Event-Driven Integration

The Human Resources Engine must publish domain events through the Platform Event Bus.

Other engines must consume HR events instead of directly coupling themselves to HR database tables.

Examples include:

- EmployeeCreated
- EmployeeActivated
- EmployeeUpdated
- EmployeeTransferred
- EmployeePromoted
- EmployeeTerminated
- EmployeeResigned
- EmployeeRetired
- EmployeePayrollEligibilityChanged
- EmployeeContractCreated
- EmployeeContractRenewed
- EmployeeContractExpired
- LeaveApproved
- LeaveCancelled
- OvertimeApproved
- AttendanceFinalized
- TimesheetApproved
- EmployeeAssetAssignmentRequested
- EmployeeManagerChanged
- EmployeeCompanyChanged
- EmployeeBranchChanged
- EmployeePositionChanged
- EmployeeOnboardingCompleted
- EmployeeOffboardingCompleted

---

### 9.6 Engine Ownership

Only the Human Resources Engine may authoritatively change HR-owned records.

External engines may:

- Read permitted HR projections
- Reference employee identifiers
- Subscribe to HR events
- Request HR actions through APIs
- Submit integration commands

External engines must not directly mutate HR-owned tables.

---

### 9.7 Auditability

Every sensitive HR action must be auditable.

Audit records must capture:

- Tenant
- Company
- Branch
- Actor
- Acting role
- Action
- Previous value
- New value
- Timestamp
- Correlation identifier
- Source
- IP or session context where available
- Workflow reference
- Reason for change
- Approval reference
- Effective date

---

### 9.8 Configurability

Tenant-specific HR policies must be configurable without modifying source code.

Configurable areas include:

- Employee numbering
- Employment types
- Contract types
- Probation rules
- Leave policies
- Leave accrual rules
- Working calendars
- Attendance tolerances
- Overtime rules
- Shift rules
- Performance review cycles
- Recruitment stages
- Onboarding checklists
- Offboarding checklists
- Promotion rules
- Transfer rules
- Disciplinary procedures
- Document requirements
- Employee profile fields
- Notification preferences
- Approval workflows

---

### 9.9 Separation of Person, Employee, and User

The architecture must distinguish between:

- Person
- Applicant
- Candidate
- Employee
- Employment
- Assignment
- Platform user

A person may:

- Apply for multiple jobs
- Become a candidate
- Receive an offer
- Become an employee
- Hold multiple employment assignments
- Leave and later be rehired
- Have or not have a user account

This separation prevents duplicate records and supports long-term workforce history.

---

### 9.10 Privacy by Design

HR records contain sensitive personal information.

Access must be granted on a need-to-know basis.

The engine must support:

- Field-level access restrictions
- Record-level access restrictions
- Company-level restrictions
- Branch-level restrictions
- Department-level restrictions
- Manager hierarchy restrictions
- Self-service restrictions
- Payroll-sensitive field isolation
- Confidential case restrictions
- Document classification
- Data retention policies
- Legal hold
- Data anonymization where permitted

---

## 10. Human Resources Engine Capability Map

The Human Resources Engine consists of the following capability domains.

```text
Human Resources Engine
│
├── Workforce Foundation
│   ├── Person Master
│   ├── Employee Master
│   ├── Employment
│   ├── Employment Assignment
│   ├── Employee Numbering
│   ├── Employee Status
│   └── Employee Profiles
│
├── Organization Management
│   ├── Organization Structures
│   ├── Departments
│   ├── Units
│   ├── Divisions
│   ├── Sections
│   ├── Positions
│   ├── Jobs
│   ├── Designations
│   ├── Job Grades
│   ├── Reporting Hierarchies
│   └── Organizational Charts
│
├── Recruitment
│   ├── Workforce Requests
│   ├── Job Requisitions
│   ├── Vacancies
│   ├── Job Advertisements
│   ├── Applicant Tracking
│   ├── Screening
│   ├── Shortlisting
│   ├── Interviews
│   ├── Assessments
│   ├── References
│   ├── Offers
│   └── Candidate Conversion
│
├── Onboarding
│   ├── Preboarding
│   ├── Document Collection
│   ├── Checklists
│   ├── Orientation
│   ├── Account Requests
│   ├── Asset Requests
│   ├── Policy Acknowledgements
│   └── Onboarding Completion
│
├── Employment Administration
│   ├── Contracts
│   ├── Probation
│   ├── Confirmation
│   ├── Renewals
│   ├── Promotions
│   ├── Transfers
│   ├── Secondments
│   ├── Acting Appointments
│   ├── Demotions
│   ├── Reassignments
│   └── Employee History
│
├── Employee Information
│   ├── Personal Details
│   ├── Contact Details
│   ├── Emergency Contacts
│   ├── Dependants
│   ├── Education
│   ├── Work History
│   ├── Skills
│   ├── Certifications
│   ├── Licenses
│   ├── Memberships
│   └── Documents
│
├── Leave and Absence
│   ├── Leave Types
│   ├── Leave Policies
│   ├── Leave Eligibility
│   ├── Accrual
│   ├── Carry Forward
│   ├── Leave Requests
│   ├── Leave Approval
│   ├── Leave Calendar
│   ├── Leave Encashment
│   ├── Leave Without Pay
│   └── Absence Management
│
├── Time and Attendance
│   ├── Working Calendars
│   ├── Shifts
│   ├── Rosters
│   ├── Clock Events
│   ├── Attendance
│   ├── Lateness
│   ├── Early Departure
│   ├── Overtime
│   ├── Timesheets
│   ├── Exceptions
│   └── Attendance Finalization
│
├── Performance Management
│   ├── Review Cycles
│   ├── Goals
│   ├── OKRs
│   ├── KPIs
│   ├── Competencies
│   ├── Appraisals
│   ├── Self Assessments
│   ├── Manager Assessments
│   ├── Calibration
│   ├── Development Plans
│   └── Performance Improvement Plans
│
├── Learning and Development
│   ├── Training Needs
│   ├── Training Plans
│   ├── Courses
│   ├── Sessions
│   ├── Attendance
│   ├── Assessments
│   ├── Certifications
│   ├── Training Costs
│   ├── Skills Development
│   └── Learning History
│
├── Employee Relations
│   ├── Disciplinary Cases
│   ├── Warning Letters
│   ├── Investigations
│   ├── Hearings
│   ├── Sanctions
│   ├── Appeals
│   ├── Grievances
│   ├── Complaints
│   └── Confidential Cases
│
├── Separation and Exit
│   ├── Resignation
│   ├── Termination
│   ├── Retirement
│   ├── Contract Expiry
│   ├── Redundancy
│   ├── Death in Service
│   ├── Exit Clearance
│   ├── Asset Return
│   ├── Exit Interview
│   ├── Final Dues Trigger
│   └── Former Employee Records
│
├── Employee Self-Service
│   ├── Profile
│   ├── Personal Updates
│   ├── Leave
│   ├── Attendance
│   ├── Timesheets
│   ├── Goals
│   ├── Reviews
│   ├── Training
│   ├── Documents
│   └── Requests
│
└── Manager Self-Service
    ├── Team Directory
    ├── Team Calendar
    ├── Approvals
    ├── Attendance Review
    ├── Leave Review
    ├── Timesheet Review
    ├── Performance Reviews
    ├── Team Development
    ├── Workforce Movements
    └── Team Analytics
```

---

## 11. Workforce Foundation

### 11.1 Person Master

The Person Master represents a natural person independently from their relationship with the organization.

It supports:

- Legal names
- Preferred names
- Previous names
- Date of birth
- Gender reference
- Nationality
- Marital status
- National identification
- Passport information
- Tax identification
- Social security identification
- Contact details
- Addresses
- Biographical information
- Photograph
- Communication preferences
- Accessibility requirements
- Privacy classifications

Person information must not be duplicated for each employment assignment.

---

### 11.2 Employee Master

The Employee Master represents an individual recognized as part of the tenant’s workforce.

It includes:

- Employee number
- Person reference
- Employee category
- Current employee status
- Original hire date
- Most recent hire date
- Seniority date
- Service date
- Payroll eligibility
- Self-service eligibility
- Manager self-service eligibility
- Primary company
- Primary branch
- Primary assignment
- Employee classification
- Confidentiality classification

Employee records must support rehire without losing historical service information.

---

### 11.3 Employment Relationship

An Employment Relationship represents a formal engagement between an employee and a legal entity.

It supports:

- Permanent employment
- Fixed-term employment
- Temporary employment
- Casual employment
- Part-time employment
- Internship
- Apprenticeship
- Consultancy
- Contract work
- Volunteer engagement
- Secondment
- Visiting appointment
- Project-funded employment

An employee may have multiple historical or concurrent employment relationships where organizational policy permits.

---

### 11.4 Employment Assignment

An Employment Assignment defines where and how the employee works.

It includes:

- Company
- Branch
- Department
- Organizational unit
- Position
- Job
- Designation
- Grade
- Supervisor
- Work location
- Project
- Cost center reference
- Employment percentage
- Working calendar
- Shift pattern
- Assignment start date
- Assignment end date
- Primary assignment indicator
- Payroll assignment eligibility

Assignments must be effective-dated.

---

### 11.5 Employee Numbering

Employee numbers must be generated through the Document Numbering Engine.

Numbering may be configured by:

- Tenant
- Company
- Branch
- Country
- Employee type
- Employment category
- Year
- Sequence

Example formats include:

```text
EMP-000001
UG-HQ-2026-0001
MSC-FIELD-00458
HR-CONS-2026-0012
```

The Human Resources Engine must not implement an independent numbering mechanism.

---

## 12. Organization Management

The Organization Management domain defines how the workforce is structurally organized.

It supports:

- Companies
- Divisions
- Directorates
- Departments
- Units
- Sections
- Teams
- Programs
- Projects
- Cost center references
- Branches
- Work locations
- Reporting hierarchies
- Matrix structures
- Dotted-line reporting
- Acting reporting relationships

Platform Core remains authoritative for tenant, organization, company, and branch identity.

The Human Resources Engine owns HR-specific organizational structures below or across those entities.

---

### 12.1 Departments

Departments must support:

- Department code
- Department name
- Parent department
- Company
- Branch applicability
- Department head
- Effective dates
- Active status
- Cost center reference
- Description
- Hierarchy level
- Reporting sequence

Department heads must reference employees or positions rather than duplicating user details.

---

### 12.2 Positions

A position represents an approved organizational seat that may be vacant, occupied, frozen, abolished, or temporarily filled.

Positions support:

- Position code
- Position title
- Job reference
- Grade
- Department
- Company
- Branch
- Location
- Reports-to position
- Authorized headcount
- Full-time equivalent value
- Position status
- Funding source
- Project reference
- Budget reference
- Effective dates
- Vacancy state
- Incumbent history

The architecture must distinguish positions from employees.

An employee occupies a position.

The employee is not the position.

---

### 12.3 Jobs and Designations

A job defines the reusable nature of work.

A designation represents the employee-facing title where required.

Jobs support:

- Job family
- Job function
- Job category
- Standard duties
- Minimum qualifications
- Required skills
- Required competencies
- Grade range
- Career path
- Occupational classification

Designations support tenant-specific naming conventions without changing the underlying job definition.

---

### 12.4 Job Grades

Job grades support:

- Grade code
- Grade name
- Grade level
- Grade sequence
- Grade band
- Public service scale
- Salary structure reference
- Leave policy eligibility
- Benefit eligibility
- Position eligibility
- Promotion progression
- Minimum service requirements

The HR Engine owns employee grade assignment.

The Payroll Engine owns monetary salary values and pay calculations associated with payroll structures.

---

### 12.5 Reporting Hierarchy

The engine must support:

- Position-based supervision
- Employee-based supervision
- Department head supervision
- Project manager supervision
- Functional manager relationships
- Administrative manager relationships
- Dotted-line relationships
- Acting supervisors
- Delegated supervisors
- Temporary reporting assignments

Reporting relationships must be effective-dated and cycle-validated.

The engine must prevent invalid hierarchy loops.

---

## 13. Recruitment and Applicant Tracking

The Recruitment domain manages the process from workforce need to candidate hire.

### 13.1 Workforce Requests

Workforce requests may originate from:

- New position requirements
- Replacement needs
- Project staffing requirements
- Seasonal staffing
- Temporary staffing
- Intern recruitment
- Consultant recruitment
- Approved workforce plans

Requests may include:

- Requested job
- Position
- Department
- Branch
- Company
- Number of vacancies
- Employment type
- Justification
- Funding source
- Target start date
- Required qualifications
- Required skills
- Budget confirmation reference

Workforce requests may require Workflow Engine approval.

---

### 13.2 Job Requisitions

An approved workforce request may generate a job requisition.

Job requisitions support:

- Requisition number
- Vacancy information
- Recruitment owner
- Hiring manager
- Recruiter
- Recruitment team
- Screening criteria
- Interview plan
- Target dates
- Publication channels
- Diversity or compliance requirements
- Approval status
- Vacancy status

---

### 13.3 Job Advertisements

Advertisements may be published to:

- Internal career portal
- External career portal
- Public website
- Partner sites
- Recruitment agencies
- Social channels
- Manual channels

Advertisements support:

- Publication date
- Closing date
- Application instructions
- Job description
- Qualifications
- Experience requirements
- Required documents
- Screening questions
- Location
- Employment type
- Terms of engagement
- Equal opportunity statements

---

### 13.4 Applicant Tracking

The engine must support:

- Applicant profiles
- Multiple applications per applicant
- Application forms
- CV uploads
- Cover letters
- Academic records
- Certificates
- Screening responses
- Application source
- Referral source
- Applicant consent
- Application status
- Candidate communication
- Duplicate applicant detection

Applicant records must remain separate from employee records until conversion.

---

### 13.5 Screening and Shortlisting

Screening must support:

- Mandatory criteria
- Weighted criteria
- Qualification requirements
- Experience requirements
- Skills requirements
- Location requirements
- Document completeness
- Automated eligibility checks
- Manual screening
- Screening scores
- Reviewer comments
- Conflict-of-interest declarations

Shortlisting decisions must be auditable.

---

### 13.6 Interviews and Assessments

The engine must support:

- Interview panels
- Interview batches
- Interview schedules
- Physical interviews
- Virtual interviews
- Assessment tests
- Practical tests
- Technical tests
- Written tests
- Competency interviews
- Structured scorecards
- Panel scoring
- Consolidated scoring
- Interview recommendations
- Interview attendance
- Candidate notifications

Interview scheduling notifications must be sent through the Notification Engine.

---

### 13.7 References and Background Checks

The recruitment process may include:

- Reference checks
- Employment verification
- Qualification verification
- Identity verification
- Criminal record checks where legally permitted
- Professional license verification
- Medical clearance where legally permitted
- Conflict-of-interest checks
- Sanctions screening where required

Sensitive verification documents must be stored through the Document Management Engine.

---

### 13.8 Job Offers

Job offers support:

- Offer number
- Candidate
- Proposed position
- Company
- Branch
- Department
- Grade
- Employment type
- Proposed start date
- Probation terms
- Contract duration
- Payroll structure reference
- Conditions
- Offer expiry
- Approval workflow
- Acceptance
- Rejection
- Withdrawal
- Renegotiation
- Digital acknowledgement

The HR Engine owns the offer.

The Payroll Engine may validate compensation structure references without owning the offer process.

---

### 13.9 Candidate Conversion

An accepted candidate may be converted into:

- Person
- Employee
- Employment relationship
- Employment assignment
- Onboarding case

Candidate conversion must preserve the recruitment history.

The process must avoid duplicate person and employee records.

---

## 14. Onboarding and Preboarding

The Onboarding domain manages the transition from accepted candidate to productive employee.

It supports:

- Preboarding
- Employee record preparation
- Contract generation
- Document collection
- Identity verification
- Medical clearance
- Bank detail collection
- Tax detail collection
- Emergency contact collection
- Policy acknowledgement
- Code of conduct acknowledgement
- Confidentiality agreements
- Orientation schedules
- Training assignments
- Workspace requests
- Equipment requests
- System access requests
- Email account requests
- Payroll enrollment request
- Benefits enrollment request
- Supervisor introduction
- Probation goals
- Onboarding checklist
- Completion review

Onboarding tasks may be assigned to:

- HR
- Hiring manager
- Employee
- IT
- Finance
- Payroll
- Administration
- Security
- Inventory or stores
- Facilities
- Project management

The HR Engine owns the onboarding case.

Other engines own the actual fulfillment of their assigned tasks.

---

## 15. Employment Contracts

The Employment Contract domain manages the legal and administrative terms of employment.

Contracts support:

- Contract number
- Employee
- Employment relationship
- Company
- Contract type
- Start date
- End date
- Probation period
- Notice period
- Working hours
- Work location
- Position
- Grade
- Terms and conditions
- Renewal eligibility
- Contract status
- Signed date
- Employer signatory
- Employee acknowledgement
- Supporting documents
- Version history
- Amendment history

Contract documents must be stored through the Document Management Engine.

Contract numbers must be generated through the Document Numbering Engine.

---

### 15.1 Contract Lifecycle

Contract statuses may include:

```text
Draft
Pending Review
Pending Approval
Approved
Issued
Accepted
Active
Amended
Suspended
Expiring
Expired
Renewed
Terminated
Cancelled
```

Contract activation must not occur before required approvals and acceptance.

---

### 15.2 Contract Expiry Management

The engine must support:

- Contract expiry alerts
- Configurable reminder periods
- Renewal requests
- Manager recommendations
- HR review
- Renewal approvals
- Non-renewal decisions
- Contract extension
- Contract conversion
- Separation initiation

Contract expiry reminders must use the Notification Engine.

---

## 16. Probation and Confirmation

The engine must support probation management for eligible employees.

Probation functionality includes:

- Probation start date
- Planned end date
- Review schedule
- Probation objectives
- Supervisor assessment
- Employee assessment
- HR review
- Confirmation recommendation
- Extension recommendation
- Termination recommendation
- Confirmation approval
- Confirmation letter
- Probation extension
- Confirmation effective date

Probation expiry notifications must be configurable.

Confirmation must create a permanent and auditable employment lifecycle event.

---

## 17. Employee Movements

Employee movements must be managed as controlled business transactions rather than direct field changes.

Supported movements include:

- Promotion
- Transfer
- Reassignment
- Demotion
- Acting appointment
- Secondment
- Temporary assignment
- Location change
- Department change
- Branch change
- Company change
- Position change
- Grade change
- Supervisor change
- Project assignment
- Return from secondment
- Return from acting appointment

Every movement must include:

- Employee
- Current assignment
- Proposed assignment
- Movement type
- Effective date
- Reason
- Initiator
- Supporting documents
- Workflow status
- Approval history
- Payroll impact indicator
- Handover requirements
- Notification requirements

Approved movements must create effective-dated assignment history.

---

## 18. Employee Information Management

The Employee Information domain provides a complete workforce profile.

### 18.1 Personal Information

The engine supports:

- Legal name
- Preferred name
- Date of birth
- Gender reference
- Nationality
- Marital status
- National identification
- Passport details
- Tax identification
- Social security identification
- Photograph
- Personal contact information
- Residential address
- Postal address
- Disability or accessibility information where legally permitted
- Communication preferences

Sensitive information must be protected using field-level authorization.

---

### 18.2 Emergency Contacts

Emergency contact records support:

- Contact name
- Relationship
- Phone
- Email
- Address
- Primary contact indicator
- Effective dates
- Employee consent

---

### 18.3 Dependants and Beneficiaries

Dependant records support:

- Name
- Relationship
- Date of birth
- Identification details
- Eligibility dates
- Benefit eligibility
- Supporting documents
- Beneficiary allocation where applicable

Payroll or Benefits modules may consume eligible dependant information but must not own it.

---

### 18.4 Education History

Education records support:

- Institution
- Country
- Qualification
- Education level
- Field of study
- Start date
- Completion date
- Grade or classification
- Verification status
- Supporting documents
- Expiry where applicable

Education levels must come from the Reference Data Engine.

---

### 18.5 Work History

Work history supports:

- Employer
- Job title
- Industry
- Start date
- End date
- Responsibilities
- Reason for leaving
- Reference contact
- Verification status

---

### 18.6 Skills and Competencies

Skills management supports:

- Skill catalog reference
- Skill category
- Proficiency level
- Years of experience
- Last used date
- Verification source
- Manager validation
- Certification link
- Expiry date
- Development priority

The future AI Skills Matching capability will consume this information.

---

### 18.7 Certifications and Licenses

Certification and license records support:

- Certification type
- Issuing body
- Certificate number
- Issue date
- Expiry date
- Verification status
- Renewal requirements
- Supporting document
- Reminder schedule

Expiry reminders must be generated through the Notification Engine.

---

### 18.8 Professional Memberships

Professional memberships support:

- Professional body
- Membership number
- Membership type
- Start date
- Renewal date
- Status
- Employer sponsorship
- Supporting document

---

## 19. Employee Documents

The HR Engine must classify and relate employee documents while the Document Management Engine stores the actual files.

Supported document categories include:

- Curriculum vitae
- National identification
- Passport
- Employment contract
- Appointment letter
- Academic certificate
- Transcript
- Professional certificate
- Professional license
- Medical clearance
- Tax form
- Social security form
- Bank form
- Reference letter
- Confirmation letter
- Promotion letter
- Transfer letter
- Warning letter
- Disciplinary decision
- Resignation letter
- Termination letter
- Exit clearance
- Policy acknowledgement
- Confidentiality agreement
- Photograph
- Other HR documents

Document requirements may be configured by:

- Country
- Employment type
- Job
- Grade
- Company
- Employee category
- Contract type

The engine must support document expiry and renewal reminders.

---

## 20. Leave Management

The Leave Management domain provides configurable leave administration.

### 20.1 Leave Types

Leave types may include:

- Annual leave
- Sick leave
- Maternity leave
- Paternity leave
- Adoption leave
- Compassionate leave
- Study leave
- Examination leave
- Marriage leave
- Unpaid leave
- Sabbatical leave
- Official duty
- Time off in lieu
- Public service leave
- Special leave
- Project-specific leave
- Custom tenant-defined leave

Leave Types must be sourced from or synchronized with the Reference Data Engine where they are shared reference definitions.

---

### 20.2 Leave Policies

Leave policies support:

- Eligibility rules
- Accrual frequency
- Accrual rate
- Maximum balance
- Minimum service
- Carry-forward limit
- Carry-forward expiry
- Maximum consecutive days
- Minimum request notice
- Backdated request rules
- Supporting document requirements
- Gender applicability where legally valid
- Employment type applicability
- Grade applicability
- Company applicability
- Country applicability
- Encashment eligibility
- Negative balance rules
- Probation restrictions
- Holiday treatment
- Weekend treatment
- Half-day support
- Approval workflow
- Delegation requirements

---

### 20.3 Leave Accrual

Accrual must support:

- Monthly accrual
- Daily accrual
- Annual allocation
- Anniversary allocation
- Prorated allocation
- Service-based allocation
- Grade-based allocation
- Contract-based allocation
- Carry-forward
- Expiry
- Manual adjustment
- Opening balance migration

Every leave balance change must be auditable.

---

### 20.4 Leave Requests

Leave requests support:

- Leave type
- Start date
- End date
- Partial day
- Number of days or hours
- Reason
- Supporting document
- Contact while away
- Delegated employee
- Handover notes
- Workflow
- Approval comments
- Cancellation
- Recall
- Extension
- Return-to-work confirmation

The engine must prevent invalid overlaps and policy violations.

---

### 20.5 Leave Calendar

The leave calendar must support:

- Employee view
- Team view
- Department view
- Branch view
- Company view
- Manager view
- HR view
- Public holiday overlays
- Shift overlays
- Confidential leave masking

Access to leave reasons and medical details must be restricted.

---

### 20.6 Leave Encashment

HR may determine leave encashment eligibility and approved days.

The Payroll Engine owns the monetary calculation and payment.

The integration flow must be:

```text
HR Leave Encashment Approval
        │
        ▼
LeaveEncashmentApproved Event
        │
        ▼
Payroll Engine Calculates Monetary Value
        │
        ▼
Payroll Processing and Posting
```

---

## 21. Holidays and Working Calendars

The engine must support:

- Country calendars
- Company calendars
- Branch calendars
- Location calendars
- Public holidays
- Regional holidays
- Religious holidays
- Tenant-defined holidays
- One-time holidays
- Recurring holidays
- Working weekends
- Special working days
- Emergency closures

Holiday definitions should reuse the Reference Data Engine where applicable.

Employees may be assigned different working calendars based on:

- Country
- Branch
- Work location
- Department
- Shift
- Employment assignment
- Project

---

## 22. Time and Attendance

The Time and Attendance domain manages employee presence, working time, and attendance exceptions.

### 22.1 Attendance Sources

Attendance may originate from:

- Manual attendance entry
- Employee self-service
- Manager confirmation
- Biometric devices
- Access control devices
- Mobile applications
- GPS-enabled field attendance
- Web clock-in
- POS login sessions
- Shift schedules
- Imported attendance files
- External attendance APIs

Attendance integrations must use controlled APIs or event ingestion.

---

### 22.2 Attendance Records

Attendance records support:

- Employee
- Assignment
- Work date
- Scheduled shift
- Clock-in
- Clock-out
- Breaks
- Worked hours
- Late minutes
- Early departure
- Overtime hours
- Absence status
- Attendance source
- Location
- Device
- Exception status
- Approval status
- Finalization status

Raw clock events must be distinguishable from calculated attendance results.

---

### 22.3 Attendance Exceptions

Exceptions include:

- Missing clock-in
- Missing clock-out
- Late arrival
- Early departure
- Unscheduled absence
- Unauthorized overtime
- Duplicate clock event
- Invalid location
- Shift mismatch
- Attendance during approved leave
- Attendance during holiday
- Excessive hours

Exceptions may require employee explanation and manager approval.

---

### 22.4 Attendance Finalization

Attendance must be reviewed and finalized before payroll consumption.

Finalized attendance may publish:

- Approved worked hours
- Approved absence days
- Approved late deductions basis
- Approved unpaid absence
- Approved overtime
- Approved shift participation

Payroll consumes finalized attendance outputs but must not independently alter attendance records.

---

## 23. Shift Scheduling and Rosters

The engine must support:

- Fixed shifts
- Rotating shifts
- Split shifts
- Night shifts
- Flexible shifts
- On-call shifts
- Field shifts
- Weekend shifts
- Public holiday shifts
- Tenant-defined shift types

Shift definitions include:

- Start time
- End time
- Break rules
- Grace period
- Minimum hours
- Maximum hours
- Overtime threshold
- Night classification
- Cross-midnight handling
- Applicable locations
- Applicable jobs
- Applicable departments

Rosters must support:

- Employee assignment
- Team assignment
- Bulk scheduling
- Rotation templates
- Schedule publication
- Shift swaps
- Manager approval
- Employee acknowledgement
- Conflict detection

---

## 24. Timesheets

Timesheets support time reporting against:

- Projects
- Grants
- Donor programs
- Customers
- Activities
- Tasks
- Cost centers
- Departments
- Work orders
- Internal initiatives

Timesheets must support:

- Daily entry
- Weekly entry
- Monthly entry
- Hour-based entry
- Day-based entry
- Project allocation
- Activity allocation
- Billable indicator
- Non-billable indicator
- Notes
- Attachments
- Submission
- Approval
- Rejection
- Reopening
- Finalization

The engine must prevent:

- Duplicate time
- Overlapping time
- Excessive hours
- Time outside employment dates
- Time against inactive projects
- Submission against closed periods

Approved timesheets may be consumed by:

- Payroll Engine
- Finance Engine
- Expenses Management Engine
- Project accounting capabilities
- Donor reporting
- Customer billing processes

---

## 25. Overtime Management

Overtime may be:

- Pre-authorized
- Attendance-derived
- Shift-derived
- Timesheet-derived
- Emergency overtime
- Public holiday overtime
- Weekend overtime
- Night overtime

Overtime requests support:

- Employee
- Work date
- Start time
- End time
- Hours
- Overtime category
- Reason
- Project
- Cost center
- Supervisor
- Supporting evidence
- Approval workflow
- Compensation method

Compensation methods may include:

- Payroll payment
- Time off in lieu
- No compensation
- Fixed allowance
- Tenant-defined treatment

HR owns overtime approval.

Payroll owns monetary overtime calculation.

---

## 26. Absence Management

Absence management covers unplanned and planned non-attendance.

Absence categories include:

- Approved leave
- Unapproved absence
- Sick absence
- Work injury absence
- Suspension
- Official duty
- Training
- Field assignment
- Travel
- Strike
- Lockout
- Emergency closure
- Other tenant-defined absence

The engine must track:

- Absence period
- Absence category
- Reason
- Supporting documentation
- Certification
- Return-to-work date
- Return-to-work interview
- Payroll impact
- Disciplinary impact
- Confidentiality classification

---

## 27. Performance Management

The Performance Management domain supports structured employee performance planning and review.

### 27.1 Performance Cycles

Performance cycles may be:

- Annual
- Semiannual
- Quarterly
- Monthly
- Probationary
- Project-based
- Contract-based
- Ad hoc

Cycles support:

- Planning period
- Goal-setting period
- Mid-cycle review
- Self-assessment
- Manager assessment
- Calibration
- Finalization
- Appeal period
- Development planning

---

### 27.2 Goals and OKRs

Goals support:

- Goal title
- Description
- Goal type
- Objective
- Key results
- Weight
- Target
- Measurement unit
- Start date
- Due date
- Progress
- Status
- Alignment
- Parent goal
- Organizational goal
- Department goal
- Team goal
- Employee goal
- Evidence
- Manager comments

Goals may cascade through the organizational hierarchy.

---

### 27.3 KPIs

KPI definitions support:

- KPI name
- Description
- Category
- Calculation method
- Target
- Minimum threshold
- Maximum threshold
- Weight
- Measurement frequency
- Data source
- Evidence requirements
- Rating scale

KPI results may be entered manually or supplied by integrated systems.

---

### 27.4 Competencies

Competency management supports:

- Competency framework
- Competency categories
- Behavioral indicators
- Proficiency levels
- Job competency requirements
- Position competency requirements
- Employee competency assessments
- Competency gaps
- Development recommendations

---

### 27.5 Performance Reviews

Performance reviews support:

- Employee self-assessment
- Manager assessment
- Reviewer assessment
- Peer feedback
- 360-degree feedback
- Calibration
- Moderation
- Final rating
- Employee acknowledgement
- Appeal
- Development plan
- Performance improvement plan

Confidential review data must have strict access controls.

---

## 28. Learning and Development

The Learning and Development domain manages employee capability building.

It supports:

- Training needs assessments
- Individual development plans
- Department training plans
- Annual training plans
- Course catalog
- Internal courses
- External courses
- Training providers
- Training sessions
- Facilitators
- Venues
- Virtual sessions
- Training nominations
- Training approvals
- Attendance
- Assessments
- Results
- Certificates
- Training evaluations
- Training costs
- Training agreements
- Bond periods
- Skills updates
- Learning history

Training costs remain financial transactions owned by Finance or Expenses.

HR owns the learning event and employee participation record.

---

## 29. Career Development and Succession Planning

The architecture must support:

- Career paths
- Job families
- Job progression
- Talent pools
- High-potential employees
- Readiness levels
- Successor nominations
- Critical positions
- Succession risk
- Development actions
- Mobility preferences
- Career interests
- Replacement planning

These capabilities may initially be delivered in phases but must fit cleanly within the HR bounded context.

---

## 30. Employee Relations

### 30.1 Disciplinary Cases

Disciplinary case management supports:

- Case number
- Employee
- Allegation
- Incident date
- Reporting party
- Confidentiality level
- Investigation
- Evidence
- Hearing
- Panel
- Employee response
- Findings
- Recommendation
- Sanction
- Appeal
- Final decision
- Supporting documents
- Workflow
- Closure

Potential sanctions may include:

- Verbal warning
- Written warning
- Final warning
- Suspension
- Demotion
- Transfer
- Termination
- Other tenant-defined action

The engine must not expose confidential disciplinary details through general employee search or standard manager access.

---

### 30.2 Grievances

Grievance management supports:

- Grievance submission
- Confidential reporting
- Category
- Description
- Parties involved
- Investigation
- Mediation
- Hearing
- Resolution
- Appeal
- Closure
- Retaliation protection controls
- Supporting evidence

Access must be restricted to explicitly authorized roles and case participants.

---

## 31. Separation and Exit Management

The Separation domain manages the end of an employment relationship.

Supported separation types include:

- Resignation
- Termination
- Retirement
- Contract expiry
- Non-renewal
- Redundancy
- Dismissal
- Medical separation
- Death in service
- Abandonment
- Mutual separation
- End of internship
- End of consultancy
- Transfer outside the tenant
- Other tenant-defined separation

---

### 31.1 Separation Request

A separation request includes:

- Employee
- Employment
- Separation type
- Proposed last working date
- Notice date
- Notice period
- Reason
- Initiator
- Supporting documents
- Workflow
- Handover requirements
- Exit clearance
- Final dues requirement
- Rehire eligibility
- Confidential notes

---

### 31.2 Exit Clearance

Exit clearance may include:

- Manager clearance
- HR clearance
- Payroll clearance
- Finance clearance
- Inventory clearance
- IT clearance
- Security clearance
- Administration clearance
- Project clearance
- Document handover
- Asset return
- Account deactivation
- Access card return
- Loan balance verification
- Expense accountability verification

Each responsible engine owns its clearance result.

HR owns the overall offboarding case.

---

### 31.3 Final Dues Trigger

After HR separation approval and relevant clearance, the HR Engine publishes a final dues request event to Payroll.

```text
Separation Approved
        │
        ▼
Exit Clearance Completed
        │
        ▼
EmployeeFinalDuesRequested
        │
        ▼
Payroll Engine Calculates Final Dues
        │
        ▼
Finance and Payment Processing
```

HR does not calculate final payroll amounts.

---

### 31.4 Former Employee Records

After separation:

- Employment becomes inactive.
- Historical records remain preserved.
- User access may be revoked through Platform Core.
- Payroll eligibility ends.
- Self-service access may be retained where configured.
- Payslip and tax document access may continue through restricted alumni access.
- Rehire eligibility is recorded.
- Rehire must reuse the person record where valid.

---

## 32. Employee Self-Service

Employee Self-Service provides employees with controlled access to their own HR information.

Capabilities include:

- View personal profile
- Update permitted personal information
- Submit profile changes
- View employment details
- View organization assignment
- View reporting manager
- View contracts
- View HR documents
- Upload required documents
- Submit leave requests
- View leave balances
- View leave calendar
- Submit attendance corrections
- Submit timesheets
- Submit overtime requests
- View shift schedules
- Request shift swaps
- View performance goals
- Complete self-assessments
- View development plans
- Register for training
- View training history
- Update qualifications
- Update skills
- Update emergency contacts
- Submit HR requests
- Track request status

Sensitive changes may require workflow approval before becoming authoritative.

---

## 33. Manager Self-Service

Manager Self-Service provides supervisors with controlled access to their reporting teams.

Capabilities include:

- View direct reports
- View authorized indirect reports
- View team directory
- View team leave calendar
- Approve leave
- Approve attendance corrections
- Approve timesheets
- Approve overtime
- Review shift schedules
- Initiate recruitment requests
- Initiate employee movements
- Review probation
- Conduct performance reviews
- Assign goals
- Review training needs
- Nominate employees for training
- Initiate disciplinary actions
- Initiate separation requests
- View team analytics
- View contract expiry alerts
- View certification expiry alerts

Manager access must be derived from effective reporting relationships.

---

## 34. Organizational Charts

The engine must provide organizational charts based on:

- Company hierarchy
- Department hierarchy
- Position hierarchy
- Employee reporting hierarchy
- Project hierarchy
- Matrix relationships

Organizational charts must support:

- Vacant positions
- Acting appointments
- Dotted-line reporting
- Position details
- Employee summaries
- Filtering
- Effective-date viewing
- Historical hierarchy viewing
- Export
- Print
- Access restrictions

Confidential employee fields must not be displayed in organization charts.

---

## 35. Payroll Engine Integration

The Human Resources Engine and Payroll Engine are tightly integrated but independently owned.

### 35.1 HR Provides Payroll With

HR may provide:

- Employee identifier
- Employment identifier
- Assignment identifier
- Company
- Branch
- Position
- Job
- Grade
- Department
- Cost center reference
- Project reference
- Employment type
- Employment status
- Hire date
- Termination date
- Payroll eligibility
- Working calendar
- Approved attendance
- Approved overtime
- Approved unpaid leave
- Approved leave encashment
- Approved employee movements
- Approved separation
- Bank details where architecture assigns their master ownership to HR
- Tax and statutory identity references

---

### 35.2 Payroll Provides HR With

Payroll may provide:

- Payroll enrollment status
- Payroll processing status
- Payslip availability
- Compensation summary projections where permitted
- Final dues processing status
- Payroll recovery status
- Payroll validation errors
- Payroll posting status

HR must not store duplicate payroll calculation details.

---

### 35.3 Integration Pattern

```text
Human Resources Engine
        │
        │ Employee and Employment Events
        ▼
Platform Event Bus
        │
        ▼
Payroll Engine
        │
        │ Payroll Status Events
        ▼
Platform Event Bus
        │
        ▼
Human Resources Engine Projections
```

Direct cross-engine database writes are prohibited.

---

## 36. Finance Engine Integration

The Human Resources Engine integrates with Finance for:

- Cost center references
- Project references
- Department financial dimensions
- Training cost references
- Recruitment cost references
- Employee loan references
- Travel cost references
- Budget validation
- Position funding references
- Donor or grant funding references

HR does not create accounting journals for standard HR transactions.

Payroll-related accounting remains between Payroll and Finance.

---

## 37. Expenses Management Engine Integration

Integration supports:

- Employee validation
- Staff advance eligibility
- Travel advance requests
- Expense claim ownership
- Per diem eligibility references
- Mileage eligibility
- Accountability status
- Outstanding advance status
- Payroll recovery requests
- Separation clearance

The integration must preserve the ownership model:

```text
HR Engine
Owns employee and employment eligibility

Expenses Management Engine
Owns operational expenditure and staff advances

Payroll Engine
Owns approved payroll deductions and recoveries
```

An expense recovery must not automatically become a payroll deduction without the required approval and Payroll Engine acceptance.

---

## 38. Inventory Engine Integration

Employee asset assignment requires coordination between HR and Inventory.

Supported cases include:

- Laptop assignment
- Phone assignment
- Uniform issuance
- Tool assignment
- Equipment assignment
- Access card assignment
- Consumable issuance
- Safety equipment issuance
- Asset return
- Damaged asset reporting
- Lost asset reporting
- Exit clearance

Inventory owns:

- Item
- Stock
- Serial number
- Warehouse
- Stock movement
- Item condition
- Inventory valuation

HR owns:

- Employee
- Employment assignment
- Employee responsibility acknowledgement
- Onboarding requirement
- Offboarding clearance requirement

---

## 39. CRM Module Integration

The CRM integration supports:

- Account manager assignment
- Salesperson assignment
- Territory ownership
- Customer service representative assignment
- Opportunity team membership
- Customer portfolio ownership
- Employee availability status
- Employee transfer impact
- Employee termination impact

CRM must reference HR employee identifiers rather than duplicate employee profiles.

---

## 40. Sales Module Integration

The Sales Module may consume:

- Sales representative assignment
- Employee active status
- Employee company and branch
- Commission eligibility reference
- Territory assignment
- Approval authority
- Sales team membership

Sales commissions are business results supplied to Payroll.

HR owns the salesperson’s employment identity.

Payroll owns commission payment calculation.

---

## 41. POS Module Integration

The POS integration supports:

- Cashier assignment
- Till eligibility
- Register eligibility
- POS role authorization
- Branch assignment
- Shift assignment
- Employee active status
- Cashier performance references
- Suspension or termination deactivation events

POS owns the operational till session.

HR owns the employee and HR shift information.

Authorization Engine owns permissions.

---

## 42. Platform Core Integration

The Human Resources Engine integrates with Platform Core for:

- Tenant identity
- Organization identity
- Company identity
- Branch identity
- User identity
- Authentication
- Tenant membership
- Active tenant context
- Active company context
- Active branch context
- Subscription entitlements
- Module activation
- Correlation identifiers

Employee records must not be used as a substitute for Platform Core user accounts.

---

## 43. Authorization Engine Integration

The Authorization Engine controls access to HR capabilities.

Permission areas include:

- Employee master
- Personal information
- Employment information
- Contracts
- Recruitment
- Applicants
- Leave
- Attendance
- Timesheets
- Overtime
- Performance
- Training
- Disciplinary cases
- Grievances
- Separation
- Organization structures
- Reports
- Configuration
- Employee self-service
- Manager self-service

Authorization decisions may consider:

- Tenant
- Company
- Branch
- Department
- Employee
- Reporting hierarchy
- HR role
- Workflow assignment
- Confidentiality classification
- Record ownership
- Employment status
- Effective dates

---

## 44. Workflow Engine Integration

HR business processes must use the Workflow Engine.

Supported workflows include:

- Workforce request approval
- Job requisition approval
- Job offer approval
- Recruitment recommendation approval
- Contract approval
- Contract renewal
- Leave approval
- Leave cancellation
- Overtime approval
- Attendance correction approval
- Timesheet approval
- Shift swap approval
- Employee profile change approval
- Promotion approval
- Transfer approval
- Secondment approval
- Acting appointment approval
- Confirmation approval
- Training approval
- Disciplinary workflow
- Grievance workflow
- Separation approval
- Exit clearance
- Rehire approval

The HR Engine owns the business document and lifecycle state.

The Workflow Engine owns routing and approval execution.

---

## 45. Reference Data Engine Integration

The Human Resources Engine must reuse shared reference data.

Examples include:

- Countries
- Nationalities
- Districts
- Regions
- Cities
- Religions
- Marital statuses
- Gender references
- Identification types
- Education levels
- Qualification types
- Employment types
- Contract types
- Job categories
- Leave types
- Absence types
- Banks
- Currencies
- Languages
- Professional bodies
- Relationship types
- Payment methods
- Statutory schemes
- Disability classifications where legally applicable

Tenant-specific HR configuration may extend reference data through controlled extension mechanisms.

---

## 46. Document Numbering Engine Integration

The Document Numbering Engine must generate numbers for:

- Employee records
- Employment contracts
- Workforce requests
- Job requisitions
- Vacancies
- Job offers
- Onboarding cases
- Movement requests
- Leave encashment requests
- Disciplinary cases
- Grievances
- Separation cases
- Training requests
- Performance cycles where numbering is required

HR must not maintain standalone sequence counters.

---

## 47. Document Management Engine Integration

All HR file storage must use the Document Management Engine.

Required capabilities include:

- Secure upload
- Versioning
- Document classification
- Employee linking
- Applicant linking
- Contract linking
- Case linking
- Retention policy
- Expiry date
- Access control
- Download audit
- Preview
- Virus scanning integration
- File integrity
- Legal hold
- Archiving

HR stores references to documents rather than unmanaged storage paths.

---

## 48. Notification Engine Integration

The Notification Engine handles all HR communication delivery.

HR notification cases include:

- Application received
- Candidate status update
- Interview invitation
- Interview reminder
- Offer issued
- Offer expiry
- Onboarding task assigned
- Contract expiry
- Probation expiry
- Confirmation decision
- Leave submitted
- Leave approved
- Leave rejected
- Leave cancelled
- Shift published
- Shift changed
- Overtime approved
- Timesheet due
- Timesheet rejected
- Performance review due
- Goal update due
- Training reminder
- Certification expiry
- Birthday reminder
- Work anniversary
- Disciplinary hearing
- Exit clearance task
- Separation completion

Templates must be configurable by tenant, language, company, and channel.

---

## 49. Reporting Engine Integration

The Reporting Engine provides HR dashboards, operational reports, statutory reports, and analytical outputs.

HR reports may include:

- Employee register
- Workforce headcount
- Headcount by company
- Headcount by branch
- Headcount by department
- Headcount by grade
- Headcount by employment type
- Workforce movement
- New hires
- Separations
- Turnover
- Contract expiry
- Probation status
- Vacancy report
- Recruitment pipeline
- Time-to-hire
- Leave balances
- Leave utilization
- Absenteeism
- Attendance
- Lateness
- Overtime
- Timesheet compliance
- Shift coverage
- Performance completion
- Performance ratings
- Training participation
- Skills inventory
- Qualification profile
- Certification expiry
- Disciplinary cases
- Grievance cases
- Diversity reports where legally permitted
- Donor project staffing
- Project workforce allocation
- Employee asset assignment
- Exit clearance status

The HR Engine supplies governed datasets and projections.

The Reporting Engine owns report execution infrastructure.

---

## 50. Search and Indexing Engine Integration

Search must support authorized discovery of:

- Employees
- Applicants
- Candidates
- Departments
- Positions
- Jobs
- Contracts
- Vacancies
- Leave requests
- Attendance records
- Timesheets
- Training records
- Performance reviews
- Disciplinary cases
- Separation cases

Search results must respect:

- Tenant isolation
- Company access
- Branch access
- Department access
- Confidential record restrictions
- Employee self-service restrictions
- Manager hierarchy
- Document restrictions

Highly sensitive fields must not be placed in general-purpose search indexes.

---

## 51. Platform Activity and Audit Engine Integration

The Human Resources Engine must emit audit records for:

- Employee creation
- Employee updates
- Contract changes
- Assignment changes
- Supervisor changes
- Personal information changes
- Bank detail changes
- Identification changes
- Document access
- Leave balance adjustments
- Attendance changes
- Timesheet changes
- Performance rating changes
- Disciplinary actions
- Grievance actions
- Separation actions
- Configuration changes
- Data exports
- Bulk imports
- Approval actions
- Administrative overrides

Audit records must be immutable from normal HR application functions.

---

## 52. Platform Event Bus Integration

The Platform Event Bus is the required mechanism for cross-engine integration.

### 52.1 HR Domain Event Categories

The Human Resources Engine publishes events in the following categories:

- Person events
- Applicant events
- Recruitment events
- Employee events
- Employment events
- Assignment events
- Contract events
- Organization structure events
- Leave events
- Attendance events
- Timesheet events
- Overtime events
- Performance events
- Training events
- Disciplinary events
- Grievance events
- Separation events
- Onboarding events
- Offboarding events

---

### 52.2 Event Envelope

Every HR event must include:

```text
event_id
event_type
event_version
occurred_at
published_at
tenant_id
organization_id
company_id
branch_id
correlation_id
causation_id
aggregate_type
aggregate_id
actor_id
source_engine
payload
metadata
```

Events must be versioned and backward compatible.

---

### 52.3 Event Reliability

The implementation must support:

- Transactional outbox
- Idempotent consumers
- Retry handling
- Dead-letter handling
- Event replay
- Correlation tracking
- Event versioning
- Duplicate detection
- Failure monitoring

---

## 53. Multi-Country Architecture

The HR Engine must support multiple countries without embedding one country’s employment rules into the core domain.

Country-specific configuration may include:

- Identification requirements
- Employment classifications
- Contract requirements
- Leave entitlements
- Public holidays
- Working hours
- Probation limits
- Notice periods
- Statutory reporting fields
- Data retention rules
- Document requirements
- Labor reporting categories
- Privacy requirements

Country-specific payroll calculations remain under the Payroll Engine.

---

## 54. Government and Public-Sector Support

The architecture must support public-sector requirements such as:

- Establishment structures
- Authorized positions
- Public service grades
- Salary scales references
- Acting appointments
- Secondments
- Transfers
- Promotions
- Confirmation in appointment
- Pensionable service dates
- Retirement dates
- Disciplinary procedures
- Service commissions
- Appointment instruments
- Duty stations
- Cadres
- Ministries
- Departments
- Agencies
- Local government structures

The core engine must remain configurable rather than government-specific.

---

## 55. NGO and Donor-Funded Program Support

The architecture must support:

- Project-funded positions
- Grant-funded employees
- Funding source history
- Donor project assignments
- Timesheet allocation
- Level-of-effort reporting
- Project contract dates
- Cost-sharing assignments
- Multiple project allocations
- Restricted funding periods
- Donor-required qualifications
- Training compliance
- Project closeout
- Staff transition between grants

Finance and Expenses remain authoritative for financial transactions.

---

## 56. Universities and Education Institutions

The engine must accommodate:

- Academic staff
- Administrative staff
- Visiting lecturers
- Part-time lecturers
- Research assistants
- Teaching assistants
- Contract faculty
- Sabbatical leave
- Academic ranks
- Faculties
- Schools
- Departments
- Research projects
- Tenure-related extensions
- Semester-based assignments

These must be modeled using configurable jobs, positions, grades, contracts, and assignment types.

---

## 57. High-Volume Enterprise Support

The engine must support large tenants with:

- Hundreds of companies
- Thousands of branches
- Hundreds of thousands of employees
- High-volume attendance events
- High-volume timesheets
- Large recruitment campaigns
- Bulk employee imports
- Bulk movements
- Bulk shift scheduling
- Bulk leave allocation
- Bulk notifications
- Large organization hierarchies

Scalability requirements include:

- Indexed tenant-aware queries
- Partitioning strategies where justified
- Background processing
- Batch processing
- Pagination
- Cursor-based retrieval
- Materialized reporting projections
- Asynchronous event publication
- Controlled bulk operations
- Import validation
- Failure recovery

---

## 58. API-First Architecture

All HR functionality must be accessible through governed application services and APIs.

API categories include:

- Employee APIs
- Person APIs
- Employment APIs
- Assignment APIs
- Organization APIs
- Recruitment APIs
- Onboarding APIs
- Contract APIs
- Leave APIs
- Attendance APIs
- Shift APIs
- Timesheet APIs
- Overtime APIs
- Performance APIs
- Training APIs
- Employee relations APIs
- Separation APIs
- Employee self-service APIs
- Manager self-service APIs
- Integration APIs

APIs must enforce:

- Authentication
- Tenant context
- Authorization
- Validation
- Idempotency where necessary
- Audit
- Correlation identifiers
- Versioning
- Rate limits
- Error standards

---

## 59. Frontend Architecture

The Human Resources Engine frontend must use:

- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui
- React Router
- React Hook Form
- Zod
- React Context API

TanStack Query must not be used.

---

### 59.1 State Management

State must be managed using:

- React Context
- `useState`
- `useReducer`
- `useEffect`
- Purpose-specific hooks
- Service abstractions
- Supabase subscriptions where realtime behavior is justified

Global state must be limited to shared concerns such as:

- Tenant context
- Company context
- Branch context
- HR permissions
- Employee self-service identity
- Manager hierarchy context
- UI preferences

---

### 59.2 Form Standards

Forms must use:

- React Hook Form
- Zod validation
- Typed schemas
- Server-side validation
- Inline validation messages
- Unsaved-change warnings
- Loading indicators
- Button spinners
- Disabled duplicate submission
- Consistent modal and page patterns

Simple create and edit forms should use modals where appropriate.

Complex workflows should use dedicated pages, steppers, or workspaces.

---

### 59.3 Navigation Standards

Navigation must use React Router.

HTML anchor-based internal navigation must not be used.

The HR workspace should provide navigation for:

- Dashboard
- People
- Organization
- Recruitment
- Onboarding
- Contracts
- Leave
- Attendance
- Timesheets
- Shifts
- Performance
- Learning
- Employee Relations
- Separations
- Reports
- Configuration

Employee Self-Service and Manager Self-Service should provide role-appropriate simplified navigation.

---

## 60. Backend Architecture

The backend uses:

- Supabase
- PostgreSQL
- Supabase Auth
- Supabase Storage
- Supabase Edge Functions
- Supabase Realtime
- Row Level Security

The HR Engine must use a controlled database namespace or naming convention to preserve bounded-context clarity.

Example logical grouping:

```text
hr_people
hr_employees
hr_employments
hr_assignments
hr_departments
hr_positions
hr_jobs
hr_contracts
hr_recruitment_*
hr_leave_*
hr_attendance_*
hr_timesheets_*
hr_performance_*
hr_learning_*
hr_employee_relations_*
hr_separations_*
```

Final table definitions will be specified in `DATABASE.md`.

---

## 61. Row Level Security

RLS must protect every tenant-owned HR table.

Policies must enforce:

- Tenant isolation
- Company restrictions
- Branch restrictions
- Employee self-access
- Manager team access
- HR administrative access
- Confidential case access
- Workflow participant access
- Service-role restrictions
- Integration-role restrictions

Frontend filtering must never be treated as a security control.

---

## 62. Supabase Auth Relationship

Supabase Auth users are owned by Platform Core.

The HR Engine may maintain a link such as:

```text
employee_id → platform_user_id
```

The relationship must support:

- Employee without user
- User without employee
- Delayed user provisioning
- User deactivation after separation
- Rehire
- Multiple tenant memberships
- Employee self-service
- Manager self-service
- Delegated access

---

## 63. Supabase Storage

HR documents must be stored through the Document Management Engine using Supabase Storage.

Storage architecture must support:

- Tenant-separated paths
- Private buckets
- Signed access
- Expiring download links
- Document classification
- Access audit
- Retention
- Legal hold
- Versioning metadata
- Malware scanning integration

Public buckets must not be used for confidential HR documents.

---

## 64. Supabase Edge Functions

Edge Functions may be used for:

- Complex HR transactions
- Secure bulk imports
- Event publication
- Scheduled contract expiry checks
- Scheduled probation expiry checks
- Leave accrual processing
- Carry-forward processing
- Attendance calculation
- Timesheet period closure
- Notification orchestration
- Search indexing requests
- Integration callbacks
- Document generation
- High-privilege administrative actions

Business rules must remain organized within the HR domain rather than scattered across frontend components.

---

## 65. Supabase Realtime

Realtime may be used for:

- Workflow status updates
- Notification indicators
- Interview schedule updates
- Attendance exceptions
- Team leave calendar updates
- Shift publication
- Onboarding task updates
- Manager approval queues
- HR operational dashboards

Realtime subscriptions must remain tenant-scoped and authorization-aware.

---

## 66. Scheduled Processing

The HR Engine requires scheduled jobs for:

- Leave accrual
- Leave carry-forward
- Leave expiry
- Contract expiry alerts
- Probation expiry alerts
- Certification expiry alerts
- License expiry alerts
- Birthday reminders
- Work anniversary reminders
- Performance review reminders
- Training reminders
- Timesheet due reminders
- Shift publication reminders
- Attendance finalization
- Scheduled employment changes
- Scheduled terminations
- Scheduled transfers
- Effective-dated assignment activation

Scheduled jobs must be idempotent.

---

## 67. Import and Migration

The engine must support controlled import of:

- Employees
- Employment history
- Contracts
- Departments
- Positions
- Jobs
- Grades
- Reporting relationships
- Qualifications
- Skills
- Leave opening balances
- Attendance history
- Timesheet history
- Training history
- Performance history

Import processing must include:

- Template validation
- Tenant validation
- Reference validation
- Duplicate detection
- Preview
- Error report
- Partial or full rollback strategy
- Import batch identifier
- Audit trail
- Idempotency
- Reconciliation

---

## 68. Data Quality

The engine must enforce data quality for:

- Duplicate persons
- Duplicate employee numbers
- Invalid employment dates
- Overlapping primary assignments
- Invalid supervisor loops
- Invalid position occupancy
- Invalid contract dates
- Missing mandatory documents
- Invalid leave balances
- Overlapping leave
- Overlapping shifts
- Excessive timesheet hours
- Invalid qualification dates
- Expired licenses
- Missing separation clearances
- Invalid company or branch references

Data quality issues should be exposed through operational dashboards.

---

## 69. Localization and Internationalization

The engine must support:

- Multiple languages
- Localized date formats
- Localized time formats
- Localized names
- Localized addresses
- Country-specific identifiers
- Localized document templates
- Localized notifications
- Time zones
- Multi-currency references
- Right-to-left layouts where required in the future

Dates must be stored consistently and displayed according to user and tenant settings.

---

## 70. Accessibility

HR interfaces must support accessible interaction.

Requirements include:

- Keyboard navigation
- Screen-reader labels
- Focus management
- Accessible forms
- Accessible tables
- Accessible dialogs
- Clear validation messages
- Adequate contrast
- Semantic HTML
- Responsive layouts
- Non-color-only status indicators

---

## 71. Mobile Responsiveness

Employee and manager self-service functions must be mobile responsive.

Priority mobile capabilities include:

- Leave requests
- Leave approvals
- Attendance clocking
- Attendance correction
- Timesheets
- Overtime requests
- Shift schedules
- Training schedules
- Performance updates
- Onboarding tasks
- HR notifications
- Employee directory

---

## 72. Security Classification

HR information should support classifications such as:

```text
Public
Internal
Restricted
Confidential
Highly Confidential
Legal Hold
```

Examples:

- Organization chart: Internal
- General employee directory: Internal
- Personal identification: Confidential
- Medical information: Highly Confidential
- Disciplinary case: Highly Confidential
- Grievance case: Highly Confidential
- Contract: Confidential
- Performance review: Confidential
- Emergency contacts: Confidential

Classification influences authorization, document access, search indexing, export, and audit.

---

## 73. Employee Data Privacy

The engine must support privacy controls for:

- Personal data access
- Consent
- Data correction
- Data export
- Data retention
- Data minimization
- Anonymization
- Former employee retention
- Applicant retention
- Sensitive document access
- Access logging
- Legal hold
- Country-specific privacy requirements

Deletion must not be used where legal, statutory, audit, or employment retention requirements require preservation.

---

## 74. HR Configuration

Tenant HR administrators must be able to configure:

- Employee numbering
- Employee categories
- Employment types
- Contract types
- Probation policies
- Organization structures
- Jobs
- Positions
- Grades
- Working calendars
- Shift definitions
- Leave types
- Leave policies
- Attendance rules
- Overtime rules
- Timesheet rules
- Performance cycles
- Rating scales
- Competency frameworks
- Training categories
- Disciplinary categories
- Grievance categories
- Separation types
- Onboarding templates
- Offboarding templates
- Document requirements
- Notification rules
- Workflow associations

Configuration changes must be effective-dated where they affect existing employees.

---

## 75. HR Operational Dashboards

The HR workspace should provide dashboards for:

- Workforce headcount
- Active employees
- New hires
- Separations
- Upcoming contract expiries
- Upcoming probation expiries
- Pending onboarding
- Pending approvals
- Employees on leave
- Attendance exceptions
- Unsubmitted timesheets
- Overtime trends
- Open vacancies
- Recruitment pipeline
- Performance review completion
- Training participation
- Certification expiries
- Open disciplinary cases
- Open grievances
- Exit clearance status
- Workforce distribution

Dashboards must respect user authorization.

---

## 76. Employee 360 Profile

The Employee 360 profile provides a unified, authorized view of an employee.

It may include:

- Personal summary
- Employment summary
- Current assignment
- Reporting manager
- Organization path
- Contract
- Grade
- Position
- Work location
- Contact information
- Dependants
- Emergency contacts
- Qualifications
- Skills
- Certifications
- Documents
- Leave summary
- Attendance summary
- Timesheet summary
- Performance summary
- Training history
- Asset assignments
- Movement history
- Disciplinary summary where authorized
- Separation information
- Audit timeline

The Employee 360 profile is a read model composed from HR-owned information and authorized projections from other engines.

It must not transfer ownership of external data into HR.

---

## 77. HR Timeline

The employee timeline must display significant lifecycle events such as:

- Application submitted
- Candidate shortlisted
- Interview completed
- Offer issued
- Employee hired
- Onboarding completed
- Contract signed
- Probation completed
- Employee confirmed
- Employee transferred
- Employee promoted
- Acting appointment started
- Secondment started
- Leave approved
- Training completed
- Performance review finalized
- Warning issued
- Resignation submitted
- Separation approved
- Exit clearance completed
- Employee rehired

Timeline access must respect confidentiality.

---

## 78. Future Extensibility

The architecture must support future expansion without changing the HR ownership model.

Future capabilities include:

- Workforce planning
- Workforce analytics
- Talent management
- Compensation planning
- Benefits administration
- Medical insurance
- Occupational health and safety
- Fleet driver management
- Visitor management
- Advanced workforce scheduling
- Employee engagement
- Pulse surveys
- Recognition programs
- AI-powered performance reviews
- AI recruitment screening
- AI candidate matching
- AI skills matching
- AI workforce forecasting
- AI attrition risk analysis
- AI learning recommendations
- Digital employee assistant

These capabilities must integrate through governed APIs, events, and read models.

---

## 79. Artificial Intelligence Guardrails

Future AI capabilities must not bypass HR controls.

AI outputs must:

- Be explainable where decisions affect people
- Be reviewable by authorized humans
- Avoid autonomous termination or disciplinary decisions
- Avoid autonomous hiring rejection without approved governance
- Preserve audit history
- Identify source data
- Support bias monitoring
- Respect tenant isolation
- Respect privacy classifications
- Support opt-out where legally required
- Avoid exposing sensitive data
- Be configurable by tenant and jurisdiction

AI recommendations must remain recommendations unless an approved workflow explicitly permits automation.

---

## 80. Key Aggregate Roots

The detailed aggregate design will be defined in `ARCHITECTURE.md`.

Primary HR aggregate candidates include:

- Person
- Employee
- Employment
- EmploymentAssignment
- OrganizationUnit
- Position
- Job
- EmploymentContract
- WorkforceRequest
- JobRequisition
- Vacancy
- Application
- Candidate
- JobOffer
- OnboardingCase
- EmployeeMovement
- LeaveAccount
- LeaveRequest
- WorkSchedule
- ShiftRoster
- AttendancePeriod
- Timesheet
- OvertimeRequest
- PerformanceCycle
- PerformanceReview
- TrainingPlan
- TrainingSession
- DisciplinaryCase
- GrievanceCase
- SeparationCase
- ExitClearanceCase

Aggregate boundaries must prevent uncontrolled cross-domain updates.

---

## 81. Key Business Invariants

The engine must enforce at least the following invariants:

1. Every HR record belongs to exactly one tenant.
2. Every employment relationship belongs to a valid employee.
3. Every employment relationship belongs to a valid company.
4. An employee may have only one primary active assignment at a time unless tenant policy explicitly supports another model.
5. Effective-dated assignments must not create invalid overlaps.
6. Reporting hierarchies must not contain cycles.
7. A position cannot exceed authorized occupancy without an approved override.
8. An employment contract cannot become active before approval.
9. A terminated employment cannot remain payroll eligible after the effective termination date.
10. Leave cannot exceed policy limits without an approved override.
11. Approved leave must not overlap incompatible leave or separation periods.
12. Attendance cannot be finalized for a closed or unauthorized period without privileged reopening.
13. Timesheets cannot exceed allowed working limits without validation or approval.
14. Payroll must consume only finalized or approved HR inputs.
15. Employee movements must preserve historical assignments.
16. Confidential employee relations cases must not appear in general search results.
17. Applicant conversion must not create duplicate person records.
18. Separation cannot be finalized before mandatory approvals.
19. Exit clearance must reflect responses from relevant owning engines.
20. Cross-engine updates must use APIs or events rather than direct table mutation.

---

## 82. Standard Status Management

Entities must use explicit lifecycle statuses.

Statuses must not be inferred only from nullable dates.

Examples include:

### Employee Status

```text
Pre-Hire
Active
On Leave
Suspended
Seconded
Inactive
Separated
Retired
Deceased
```

### Recruitment Status

```text
Draft
Pending Approval
Approved
Published
Open
Closed
Screening
Interviewing
Selection
Offer
Filled
Cancelled
```

### Contract Status

```text
Draft
Pending Approval
Approved
Issued
Accepted
Active
Expiring
Expired
Renewed
Terminated
Cancelled
```

### Leave Request Status

```text
Draft
Submitted
Pending Approval
Approved
Rejected
Cancelled
Recalled
Completed
```

### Separation Status

```text
Draft
Submitted
Pending Approval
Approved
Clearance in Progress
Final Dues Pending
Completed
Cancelled
```

Detailed state machines will be defined in `WORKFLOWS.md`.

---

## 83. Error Handling

The engine must use standardized platform error responses.

Error categories include:

- Validation error
- Authorization error
- Tenant context error
- Record not found
- Duplicate record
- Invalid state transition
- Workflow error
- Reference data error
- Integration error
- Concurrency error
- Effective-date conflict
- Document error
- Event publication error
- Configuration error

Errors must include correlation identifiers.

Sensitive data must not be exposed in error messages.

---

## 84. Concurrency and Data Integrity

The engine must prevent accidental overwrites through:

- Optimistic concurrency
- Record versioning
- Effective-date conflict validation
- Transaction boundaries
- Idempotency keys
- Unique constraints
- Foreign key constraints
- State transition checks
- Workflow locking where necessary
- Period closure controls

High-risk updates such as contract changes, employee movements, leave balance adjustments, and separation completion must be transactionally consistent.

---

## 85. Observability

The engine must expose operational telemetry for:

- API performance
- Edge Function execution
- Event publication
- Event consumption
- Failed workflows
- Failed notifications
- Scheduled job status
- Leave accrual failures
- Attendance processing failures
- Import failures
- Search indexing failures
- Document failures
- Authorization denials
- RLS policy violations
- Integration latency

Logs must use correlation identifiers and must not expose unnecessary personal data.

---

## 86. Testing Strategy

The Human Resources Engine requires:

- Unit tests
- Domain rule tests
- Integration tests
- API tests
- RLS tests
- Authorization tests
- Workflow tests
- Event tests
- Contract tests
- Effective-date tests
- Leave calculation tests
- Attendance calculation tests
- Timesheet validation tests
- Import tests
- Document access tests
- Security tests
- Performance tests
- Accessibility tests
- End-to-end tests
- Multi-tenant isolation tests

Detailed acceptance criteria will be defined in `ACCEPTANCE.md`.

---

## 87. Performance Expectations

The engine must support responsive enterprise operations.

Performance objectives include:

- Paginated employee lists
- Indexed employee search
- Efficient hierarchy traversal
- Efficient manager team resolution
- Background bulk processing
- Asynchronous document generation
- Asynchronous notifications
- Asynchronous search indexing
- Batched attendance processing
- Batched leave accrual
- Materialized analytical projections
- Tenant-aware caching where secure
- Controlled realtime subscriptions

Large reports must be processed through the Reporting Engine rather than synchronous frontend queries.

---

## 88. Deployment and Module Activation

The Human Resources Engine must be activated through the Platform Core module registry and subscription entitlement framework.

Activation may depend on:

- Tenant package
- Company configuration
- Country setup
- User limits
- Employee limits
- Feature flags
- Payroll module availability
- Attendance module availability
- Recruitment module availability
- Performance module availability

Sub-capabilities may be independently enabled where the product packaging supports modular deployment.

---

## 89. Recommended Feature Packaging

The HR Engine may be packaged into editions without changing the underlying bounded context.

### 89.1 HR Core

- Employee Master
- Organization Structure
- Employment Contracts
- Employee Documents
- Employee Movements
- Employee Self-Service
- Manager Self-Service
- Basic Reporting

### 89.2 HR Operations

- Leave
- Attendance
- Timesheets
- Overtime
- Shifts
- Rosters
- Holidays
- Absence Management

### 89.3 Talent Acquisition

- Workforce Requests
- Recruitment
- Applicant Tracking
- Interviews
- Offers
- Onboarding

### 89.4 Talent and Performance

- Goals
- KPIs
- Performance Reviews
- Competencies
- Learning
- Career Development
- Succession Planning

### 89.5 Employee Relations

- Disciplinary Cases
- Grievances
- Investigations
- Exit Management

Feature packaging must not create duplicated models or logic.

---

## 90. Suggested Source Structure

A proposed frontend structure is:

```text
src/
└── modules/
    └── human-resources/
        ├── components/
        ├── contexts/
        ├── hooks/
        ├── pages/
        ├── routes/
        ├── schemas/
        ├── services/
        ├── types/
        ├── utils/
        ├── features/
        │   ├── people/
        │   ├── employees/
        │   ├── organization/
        │   ├── recruitment/
        │   ├── onboarding/
        │   ├── contracts/
        │   ├── movements/
        │   ├── leave/
        │   ├── attendance/
        │   ├── shifts/
        │   ├── timesheets/
        │   ├── overtime/
        │   ├── performance/
        │   ├── learning/
        │   ├── employee-relations/
        │   ├── separations/
        │   ├── employee-self-service/
        │   ├── manager-self-service/
        │   └── reports/
        └── index.ts
```

The final implementation structure must follow the broader Business Suite conventions already established.

---

## 91. Documentation Structure

The Human Resources Engine documentation consists of:

```text
human-resources/
├── README.md
├── ARCHITECTURE.md
├── DATABASE.md
├── WORKFLOWS.md
├── UI.md
├── SECURITY.md
└── ACCEPTANCE.md
```

---

### 91.1 README.md

Defines:

- Purpose
- Scope
- Ownership
- Capability map
- Integrations
- Platform alignment
- Functional overview
- Architectural constraints

---

### 91.2 ARCHITECTURE.md

Will define:

- Bounded contexts
- Domain model
- Aggregate roots
- Entities
- Value objects
- Domain services
- Application services
- Event architecture
- Integration patterns
- Effective-dated architecture
- Multi-country architecture
- Scalability architecture
- Module dependencies

---

### 91.3 DATABASE.md

Will define:

- PostgreSQL schemas
- Tables
- Columns
- Keys
- Constraints
- Indexes
- Relationships
- Effective-dated structures
- Audit fields
- RLS requirements
- Database functions
- Views
- Materialized views
- Migration strategy

---

### 91.4 WORKFLOWS.md

Will define:

- Recruitment workflows
- Contract workflows
- Onboarding workflows
- Leave workflows
- Attendance correction workflows
- Timesheet workflows
- Overtime workflows
- Movement workflows
- Performance workflows
- Training workflows
- Disciplinary workflows
- Grievance workflows
- Separation workflows
- Exit clearance workflows
- State transitions
- Workflow events

---

### 91.5 UI.md

Will define:

- HR application shell
- Navigation
- Dashboards
- Employee 360
- Organization charts
- Recruitment workspaces
- Leave interfaces
- Attendance interfaces
- Timesheet interfaces
- Performance interfaces
- Employee Self-Service
- Manager Self-Service
- Forms
- Tables
- Modals
- Steppers
- Mobile behavior
- Accessibility

---

### 91.6 SECURITY.md

Will define:

- HR roles
- Permissions
- RLS policies
- Field-level security
- Employee self-access
- Manager hierarchy access
- Confidential case security
- Document security
- Audit requirements
- Privacy controls
- Data retention
- Security testing

---

### 91.7 ACCEPTANCE.md

Will define:

- Functional acceptance criteria
- Security acceptance criteria
- Workflow acceptance criteria
- Integration acceptance criteria
- Performance acceptance criteria
- Multi-tenant acceptance criteria
- UI acceptance criteria
- Reporting acceptance criteria
- Data migration acceptance criteria
- Production readiness criteria

---

## 92. Human Resources and Payroll Documentation Sequence

The required documentation sequence is:

### Human Resources Engine

1. `README.md`
2. `ARCHITECTURE.md`
3. `DATABASE.md`
4. `WORKFLOWS.md`
5. `UI.md`
6. `SECURITY.md`
7. `ACCEPTANCE.md`

### Payroll Engine

After the Human Resources Engine documentation is complete:

1. `README.md`
2. `ARCHITECTURE.md`
3. `DATABASE.md`
4. `WORKFLOWS.md`
5. `UI.md`
6. `SECURITY.md`
7. `ACCEPTANCE.md`

The Payroll Engine documentation must not begin before completion of the Human Resources Engine documentation.

---

## 93. Final Ownership Summary

The Human Resources Engine owns people and employment.

The Payroll Engine owns compensation processing.

The Platform Core owns identity, tenancy, companies, branches, authentication, and user access foundations.

The Authorization Engine owns permission evaluation.

The Workflow Engine owns approval routing and workflow execution.

The Reference Data Engine owns shared reference information.

The Document Numbering Engine owns numbering sequences.

The Document Management Engine owns file storage and document infrastructure.

The Notification Engine owns communication delivery.

The Reporting Engine owns reporting infrastructure.

The Search and Indexing Engine owns enterprise search infrastructure.

The Platform Activity and Audit Engine owns immutable platform audit infrastructure.

The Platform Event Bus owns reliable cross-engine event distribution.

The Finance Engine owns accounting.

The Expenses Management Engine owns operational employee expenditure and advances.

The Inventory Engine owns inventory items and stock.

The CRM Module owns customers and customer relationships.

The Sales Module owns sales transactions.

The POS Module owns point-of-sale operations.

These boundaries must remain explicit in every future design and implementation decision.

---

## 94. Conclusion

The Human Resources Engine provides the authoritative workforce foundation for the Business Suite Enterprise Platform.

It is designed to support the complete employee lifecycle while maintaining strict separation from payroll, finance, expenses, inventory, CRM, sales, POS, and platform infrastructure.

The engine is:

- Multi-tenant
- Multi-company
- Multi-branch
- Multi-country
- Effective-dated
- Event-driven
- API-first
- Workflow-enabled
- Permission-controlled
- Auditable
- Searchable
- Reportable
- Configurable
- Extensible
- Enterprise-grade

The Human Resources Engine shall serve as the trusted workforce system of record upon which Payroll and other workforce-dependent Business Suite capabilities operate.

No other engine may duplicate or independently maintain authoritative employee lifecycle information.

The next document in the required sequence is:

```text
ARCHITECTURE.md
```
