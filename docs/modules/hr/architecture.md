# Business Suite Enterprise Platform

# Human Resources Engine

## ARCHITECTURE.md

---

## 1. Document Control

| Field              | Value                                                                                                          |
| ------------------ | -------------------------------------------------------------------------------------------------------------- |
| Document Name      | Human Resources Engine – Architecture                                                                          |
| Platform           | Business Suite Enterprise Platform                                                                             |
| Engine             | Human Resources Engine                                                                                         |
| Bounded Context    | Human Capital Management                                                                                       |
| Document Type      | Domain and Solution Architecture Specification                                                                 |
| Version            | 1.0                                                                                                            |
| Status             | Architecture Specification                                                                                     |
| Primary Owner      | Human Resources Engine                                                                                         |
| Related Engine     | Payroll Engine                                                                                                 |
| Architecture Style | Domain-Driven, Event-Driven, API-First, Multi-Tenant                                                           |
| Technology Stack   | React, TypeScript, Supabase, PostgreSQL                                                                        |
| Intended Audience  | Architects, Developers, Product Owners, HR Specialists, Security Engineers, QA Engineers, Implementation Teams |

---

## 2. Purpose

This document defines the enterprise architecture of the **Human Resources Engine** within the Business Suite Enterprise Platform.

It establishes:

- Domain boundaries
- Bounded contexts
- Aggregate roots
- Entities
- Value objects
- Domain services
- Application services
- Event architecture
- Integration patterns
- Effective-dated data principles
- Multi-tenant design
- Multi-company design
- Multi-branch design
- Multi-country extensibility
- Security boundaries
- Scalability principles
- Transaction boundaries
- Read-model architecture
- Background processing
- Cross-engine interaction rules

The architecture ensures that the Human Resources Engine remains the authoritative workforce system of record without duplicating functionality owned by other Business Suite engines.

---

## 3. Architectural Position

The Human Resources Engine is a business engine operating on top of the shared Business Suite platform capabilities.

```text
┌──────────────────────────────────────────────────────────────────────────┐
│                     Business Suite Platform Core                         │
├──────────────────────────────────────────────────────────────────────────┤
│ Tenant | Organization | Company | Branch | User | Subscription | Context │
└──────────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                         Platform Engines                                 │
├──────────────────────────────────────────────────────────────────────────┤
│ Authorization Engine                                                    │
│ Workflow Engine                                                         │
│ Reference Data Engine                                                   │
│ Document Numbering Engine                                               │
│ Document Management Engine                                              │
│ Notification Engine                                                     │
│ Reporting Engine                                                        │
│ Search & Indexing Engine                                                │
│ Platform Activity & Audit Engine                                        │
│ Platform Event Bus                                                      │
└──────────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                    Human Resources Engine                                │
├──────────────────────────────────────────────────────────────────────────┤
│ Workforce Foundation                                                    │
│ Organization Management                                                 │
│ Recruitment and Applicant Tracking                                      │
│ Onboarding and Employment Administration                                │
│ Leave and Absence                                                        │
│ Time and Attendance                                                      │
│ Performance Management                                                  │
│ Learning and Development                                                │
│ Employee Relations                                                      │
│ Separation and Exit                                                     │
│ Employee and Manager Self-Service                                        │
└──────────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                       Consuming Engines                                  │
├──────────────────────────────────────────────────────────────────────────┤
│ Payroll | Finance | Expenses | Inventory | CRM | Sales | POS             │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Core Architectural Decision

The Human Resources Engine is implemented as a single enterprise business engine containing multiple cohesive subdomains.

These subdomains are not independent platform engines.

They share:

- Employee identity
- Employment identity
- Organization structures
- Effective dating
- HR lifecycle rules
- HR security policies
- HR domain events
- HR audit requirements

The engine may be deployed modularly, but ownership remains unified within the Human Resources bounded context.

---

## 5. Primary Bounded Context

The primary bounded context is:

```text
Human Capital Management
```

It governs:

- People participating in the workforce
- Their employment relationships
- Their organizational placement
- Their lifecycle
- Their availability
- Their development
- Their performance
- Their workplace participation
- Their separation

The bounded context excludes:

- Authentication identity
- Payroll calculations
- General ledger accounting
- Customer ownership
- Inventory ownership
- Expense accounting
- Notification delivery
- Workflow execution infrastructure
- File storage infrastructure
- Enterprise reporting infrastructure

---

## 6. Domain-Driven Design Structure

The Human Resources Engine follows a layered Domain-Driven Design architecture.

```text
┌──────────────────────────────────────────────────────────────┐
│ Presentation Layer                                           │
│ React Pages, Forms, Tables, Dashboards, ESS, MSS              │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│ Application Layer                                            │
│ Commands, Queries, Use Cases, Orchestration, DTOs             │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│ Domain Layer                                                 │
│ Aggregates, Entities, Value Objects, Policies, Domain Events  │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│ Infrastructure Layer                                         │
│ PostgreSQL, Supabase, Event Bus, Storage, External Adapters   │
└──────────────────────────────────────────────────────────────┘
```

Each layer has distinct responsibilities.

---

## 7. Presentation Layer

The presentation layer handles user interaction.

It contains:

- React routes
- Pages
- Dashboards
- Forms
- Modals
- Steppers
- Tables
- Calendars
- Organization charts
- Timeline views
- Employee Self-Service
- Manager Self-Service
- Administrative workspaces

The presentation layer must not contain authoritative business rules.

It may perform:

- Input validation
- Required-field validation
- Basic format validation
- User experience state handling
- Permission-aware visibility
- Navigation
- Loading states
- Confirmation prompts

All critical validation must be repeated in the application and domain layers.

---

## 8. Application Layer

The application layer coordinates use cases.

It owns:

- Command handlers
- Query handlers
- Transaction orchestration
- Workflow initiation
- Authorization checks
- Domain aggregate loading
- Domain operation execution
- Repository coordination
- Domain event collection
- Audit emission
- External integration orchestration
- Idempotency handling
- Response mapping

Examples include:

- CreateEmployee
- HireCandidate
- ActivateEmployment
- TransferEmployee
- SubmitLeaveRequest
- ApproveAttendanceCorrection
- FinalizeAttendancePeriod
- SubmitTimesheet
- StartPerformanceReview
- ConfirmEmployee
- InitiateSeparation
- CompleteExitClearance

The application layer does not own the underlying business rules.

---

## 9. Domain Layer

The domain layer contains the authoritative HR business model.

It includes:

- Aggregate roots
- Entities
- Value objects
- Domain services
- Domain policies
- Specifications
- Domain events
- State machines
- Business invariants

The domain layer must remain independent of React, Supabase client libraries, and presentation concerns.

---

## 10. Infrastructure Layer

The infrastructure layer implements:

- PostgreSQL persistence
- Supabase database access
- Repository implementations
- Supabase Edge Functions
- Supabase Storage integration
- Platform Event Bus adapters
- Notification Engine adapters
- Workflow Engine adapters
- Document Management adapters
- Reporting projections
- Search indexing adapters
- Scheduled jobs
- External attendance device adapters
- Import and export processors

Infrastructure must implement domain interfaces without moving business rules out of the domain.

---

# PART I — DOMAIN DECOMPOSITION

## 11. HR Subdomains

The Human Resources Engine is divided into the following subdomains:

1. Workforce Foundation
2. Organization Management
3. Recruitment
4. Onboarding
5. Employment Administration
6. Employee Information
7. Leave and Absence
8. Time and Attendance
9. Shift and Roster Management
10. Timesheets
11. Performance Management
12. Learning and Development
13. Career and Succession
14. Employee Relations
15. Separation and Exit
16. Employee Self-Service
17. Manager Self-Service
18. HR Reporting Projections
19. HR Configuration

Each subdomain has clear ownership but shares the same engine boundary.

---

## 12. Workforce Foundation Subdomain

The Workforce Foundation subdomain provides the core identity and employment structures used by all other HR capabilities.

It owns:

- Person
- Employee
- Employment
- Employment Assignment
- Employee status
- Workforce category
- Service history
- Rehire history
- Employee numbering
- Payroll eligibility indicator
- Primary assignment

It is the foundational HR subdomain.

No other HR subdomain may duplicate employee or employment records.

---

## 13. Organization Management Subdomain

The Organization Management subdomain owns HR-specific workforce structures.

It owns:

- Departments
- Divisions
- Units
- Sections
- Teams
- Positions
- Jobs
- Designations
- Job families
- Job grades
- Reporting hierarchies
- Position occupancy
- Organization charts

Platform Core remains authoritative for:

- Tenant
- Organization
- Company
- Branch

HR references these entities and adds workforce structure beneath or across them.

---

## 14. Recruitment Subdomain

The Recruitment subdomain owns:

- Workforce requests
- Job requisitions
- Vacancies
- Advertisements
- Applicants
- Applications
- Screening criteria
- Shortlisting
- Interviews
- Assessments
- References
- Candidate selection
- Offers
- Candidate conversion

The Recruitment subdomain ends when the candidate is hired and converted into HR workforce entities.

---

## 15. Onboarding Subdomain

The Onboarding subdomain owns:

- Preboarding
- Onboarding cases
- Onboarding templates
- Onboarding tasks
- Required documents
- Policy acknowledgements
- Orientation
- Cross-engine fulfillment requests
- Onboarding completion

The onboarding case coordinates tasks performed by HR and other engines.

It does not own external fulfillment results such as inventory stock movements or user account provisioning.

---

## 16. Employment Administration Subdomain

Employment Administration owns:

- Employment contracts
- Contract amendments
- Contract renewals
- Probation
- Confirmation
- Promotions
- Transfers
- Secondments
- Acting appointments
- Reassignments
- Demotions
- Supervisor changes
- Branch changes
- Company changes
- Position changes
- Employee status transitions

All employee movements must preserve historical assignments.

---

## 17. Employee Information Subdomain

The Employee Information subdomain owns workforce-related personal and professional data.

It includes:

- Personal details
- Contact details
- Emergency contacts
- Dependants
- Beneficiaries
- Education
- Work history
- Skills
- Certifications
- Licenses
- Memberships
- Employee document classifications

Document binaries remain owned by the Document Management Engine.

---

## 18. Leave and Absence Subdomain

The Leave and Absence subdomain owns:

- Leave policy assignment
- Leave accounts
- Leave accrual
- Carry-forward
- Leave expiry
- Leave requests
- Leave cancellation
- Leave recall
- Leave extension
- Leave encashment approval
- Absence cases
- Return-to-work records

Payroll consumes approved leave outcomes where they affect compensation.

---

## 19. Time and Attendance Subdomain

The Time and Attendance subdomain owns:

- Clock events
- Attendance days
- Attendance calculations
- Attendance exceptions
- Attendance corrections
- Lateness
- Early departure
- Unauthorized absence
- Attendance periods
- Attendance finalization

Payroll consumes finalized attendance outputs.

---

## 20. Shift and Roster Subdomain

The Shift and Roster subdomain owns:

- Shift definitions
- Shift patterns
- Rosters
- Employee shift assignments
- Team shift assignments
- Shift swaps
- Schedule publication
- Shift acknowledgements

POS may consume cashier shift assignments.

Payroll may consume approved shift outcomes for allowances.

---

## 21. Timesheet Subdomain

The Timesheet subdomain owns:

- Timesheet periods
- Timesheet headers
- Timesheet entries
- Project allocation
- Activity allocation
- Submission
- Approval
- Rejection
- Reopening
- Finalization

Finance, Expenses, Payroll, and project accounting capabilities may consume approved timesheet outputs.

---

## 22. Performance Management Subdomain

The Performance Management subdomain owns:

- Performance cycles
- Goals
- Objectives
- Key results
- KPIs
- Competency reviews
- Self-assessments
- Manager reviews
- Calibration
- Final ratings
- Development plans
- Performance improvement plans

Performance data is confidential and requires restricted access.

---

## 23. Learning and Development Subdomain

The Learning and Development subdomain owns:

- Training needs
- Training plans
- Courses
- Programs
- Sessions
- Nominations
- Attendance
- Evaluations
- Results
- Certificates
- Learning history
- Skills development records

Finance and Expenses own the actual financial spending.

---

## 24. Career and Succession Subdomain

The Career and Succession subdomain owns:

- Career paths
- Talent pools
- Critical positions
- Successor nominations
- Readiness assessments
- Development actions
- Mobility preferences
- Career interests
- Replacement risks

This subdomain may be implemented in a later phase while remaining part of the same engine.

---

## 25. Employee Relations Subdomain

The Employee Relations subdomain owns:

- Disciplinary cases
- Grievances
- Investigations
- Hearings
- Evidence associations
- Findings
- Sanctions
- Appeals
- Confidential notes
- Case closure

It has stricter security than standard HR records.

---

## 26. Separation and Exit Subdomain

The Separation and Exit subdomain owns:

- Separation requests
- Resignations
- Terminations
- Retirements
- Contract expiries
- Redundancies
- Exit clearance cases
- Exit interviews
- Handover
- Rehire eligibility
- Final dues requests
- Former employee status

Payroll owns final dues calculations.

---

# PART II — UBIQUITOUS LANGUAGE

## 27. Core Domain Terms

The following terms must be used consistently throughout documentation, database design, code, APIs, and UI.

### 27.1 Person

A natural person whose identity may exist independently of any employment relationship.

---

### 27.2 Applicant

A person who has submitted or started an application for a vacancy.

---

### 27.3 Candidate

An applicant actively being considered in a recruitment process.

---

### 27.4 Employee

A workforce participant recognized by the tenant and represented by an employee record.

---

### 27.5 Employment

A legal or formal relationship between an employee and a company or legal entity.

---

### 27.6 Assignment

The placement of an employee into a company, branch, department, position, location, project, and reporting structure for a defined effective period.

---

### 27.7 Job

A reusable definition of work, responsibilities, qualifications, and competencies.

---

### 27.8 Position

An authorized seat within an organization structure that may be occupied or vacant.

---

### 27.9 Designation

The employee-facing title associated with a job or assignment.

---

### 27.10 Grade

A structured organizational level used for classification, eligibility, and progression.

---

### 27.11 Supervisor

The employee or position responsible for managing an employee’s assignment.

---

### 27.12 Effective Date

The date from which a business state or relationship becomes valid.

---

### 27.13 Event Date

The date on which a business event occurred.

---

### 27.14 Recorded Date

The date on which the platform stored the transaction.

These dates may differ.

---

### 27.15 Separation

The ending of an employment relationship.

---

### 27.16 Rehire

The creation of a new employment relationship for a person who previously worked for the organization.

---

# PART III — AGGREGATE MODEL

## 28. Aggregate Design Principles

Aggregates define consistency and transaction boundaries.

Each aggregate:

- Has one aggregate root
- Controls internal state changes
- Enforces invariants
- Emits domain events
- Is persisted transactionally
- References external aggregates by identifier
- Avoids loading unrelated large object graphs

Cross-aggregate processes use application services, events, and sagas.

---

## 29. Person Aggregate

### 29.1 Aggregate Root

```text
Person
```

### 29.2 Responsibilities

The Person aggregate manages:

- Legal identity
- Preferred identity
- Contact details
- Addresses
- Identification documents metadata
- Nationality references
- Biographical attributes
- Privacy classification
- Duplicate detection keys

### 29.3 Child Entities

- PersonName
- PersonContact
- PersonAddress
- PersonIdentification
- PersonEmergencyContact
- PersonDependant
- PersonConsent

### 29.4 Invariants

1. A person must belong to one tenant.
2. A person must have at least one recognized name.
3. Identification numbers must be unique within configured jurisdiction and type where required.
4. Sensitive identifiers must be protected.
5. Person records must not be duplicated solely because of rehire.
6. Person deletion must respect retention requirements.

### 29.5 Domain Events

- PersonCreated
- PersonUpdated
- PersonIdentificationAdded
- PersonContactChanged
- PersonDuplicateFlagged
- PersonMerged
- PersonPrivacyClassificationChanged

---

## 30. Employee Aggregate

### 30.1 Aggregate Root

```text
Employee
```

### 30.2 Responsibilities

The Employee aggregate manages:

- Employee number
- Person reference
- Workforce category
- Employee status
- Hire history
- Service dates
- Self-service eligibility
- Payroll eligibility
- Primary employment reference
- Primary assignment reference
- Rehire state

### 30.3 Invariants

1. Every employee references one valid person.
2. Every employee belongs to one tenant.
3. Employee number must be unique according to numbering scope.
4. An employee may not become active without an active employment.
5. A separated employee cannot remain payroll eligible after separation becomes effective.
6. Primary assignment must belong to the same employee.
7. Rehire must not overwrite former employment history.

### 30.4 Domain Events

- EmployeeCreated
- EmployeeActivated
- EmployeeStatusChanged
- EmployeePayrollEligibilityChanged
- EmployeeSelfServiceEnabled
- EmployeeSelfServiceDisabled
- EmployeeSeparated
- EmployeeRehired

---

## 31. Employment Aggregate

### 31.1 Aggregate Root

```text
Employment
```

### 31.2 Responsibilities

The Employment aggregate manages:

- Employee-company relationship
- Employment type
- Engagement category
- Employment start
- Employment end
- Status
- Seniority date
- Pensionable service date
- Notice terms
- Employment percentage
- Primary relationship indicator

### 31.3 Child Entities

- EmploymentStatusPeriod
- EmploymentServicePeriod
- EmploymentEligibility
- EmploymentConditionReference

### 31.4 Invariants

1. Employment must reference a valid employee.
2. Employment must reference a valid company.
3. Employment dates must be chronologically valid.
4. Active employment must have a start date.
5. Ended employment must have an end date and separation reason.
6. Concurrent employment must comply with tenant policy.
7. Primary employment overlap must be prevented unless explicitly allowed.

### 31.5 Domain Events

- EmploymentCreated
- EmploymentActivated
- EmploymentSuspended
- EmploymentResumed
- EmploymentEndScheduled
- EmploymentEnded
- EmploymentReactivated

---

## 32. Employment Assignment Aggregate

### 32.1 Aggregate Root

```text
EmploymentAssignment
```

### 32.2 Responsibilities

The assignment aggregate manages:

- Company
- Branch
- Department
- Position
- Job
- Designation
- Grade
- Supervisor
- Work location
- Project
- Cost center reference
- Working calendar
- Shift pattern
- Full-time equivalent
- Assignment dates
- Primary assignment status

### 32.3 Invariants

1. Assignment must belong to an active or scheduled employment.
2. Assignment dates must fall within employment dates.
3. Required organization references must be valid.
4. Primary assignment overlaps must be prevented.
5. Supervisor must not create hierarchy cycles.
6. Position occupancy must respect authorized capacity.
7. Company change may require a new employment relationship based on legal entity rules.
8. Effective changes must preserve history.

### 32.4 Domain Events

- AssignmentCreated
- AssignmentActivated
- AssignmentUpdated
- AssignmentTransferred
- AssignmentPromoted
- AssignmentSupervisorChanged
- AssignmentPositionChanged
- AssignmentDepartmentChanged
- AssignmentBranchChanged
- AssignmentEnded
- PrimaryAssignmentChanged

---

## 33. Organization Unit Aggregate

### 33.1 Aggregate Root

```text
OrganizationUnit
```

### 33.2 Unit Types

- Division
- Directorate
- Department
- Unit
- Section
- Team
- Program
- Faculty
- School
- Custom tenant-defined unit

### 33.3 Responsibilities

- Hierarchical structure
- Parent-child relationships
- Unit head assignment
- Effective dates
- Company scope
- Branch scope
- Cost center reference
- Status
- Structural path

### 33.4 Invariants

1. Organization unit hierarchy must not contain cycles.
2. Parent and child units must belong to compatible tenant and company scopes.
3. Effective dates must be valid.
4. Inactive units cannot receive new assignments unless overridden.
5. Unit heads must reference valid employees or positions.

### 33.5 Events

- OrganizationUnitCreated
- OrganizationUnitUpdated
- OrganizationUnitMoved
- OrganizationUnitHeadChanged
- OrganizationUnitActivated
- OrganizationUnitDeactivated

---

## 34. Position Aggregate

### 34.1 Aggregate Root

```text
Position
```

### 34.2 Responsibilities

- Authorized workforce seat
- Job association
- Grade association
- Organization placement
- Reporting position
- Headcount
- FTE capacity
- Funding reference
- Vacancy state
- Occupancy history

### 34.3 Child Entities

- PositionOccupancy
- PositionFundingReference
- PositionRequirement
- PositionResponsibility

### 34.4 Invariants

1. Position code must be unique in configured scope.
2. Position capacity cannot be negative.
3. Occupancy cannot exceed approved capacity without override.
4. Position reporting hierarchy must not contain cycles.
5. Abolished positions cannot receive new occupants.
6. Position effective dates must be respected.

### 34.5 Events

- PositionCreated
- PositionApproved
- PositionActivated
- PositionFrozen
- PositionVacated
- PositionOccupied
- PositionCapacityChanged
- PositionAbolished

---

## 35. Job Aggregate

### 35.1 Aggregate Root

```text
Job
```

### 35.2 Responsibilities

- Standard role definition
- Job family
- Job function
- Duties
- Qualifications
- Skills
- Competencies
- Grade range
- Career path references

### 35.3 Events

- JobCreated
- JobUpdated
- JobRequirementChanged
- JobActivated
- JobDeactivated

---

## 36. Employment Contract Aggregate

### 36.1 Aggregate Root

```text
EmploymentContract
```

### 36.2 Responsibilities

- Contract terms
- Contract versions
- Start and end dates
- Probation terms
- Notice period
- Working conditions
- Acceptance
- Approval state
- Renewal
- Amendment
- Termination

### 36.3 Child Entities

- ContractTerm
- ContractVersion
- ContractAmendment
- ContractSignatory
- ContractAcknowledgement

### 36.4 Invariants

1. Contract must belong to a valid employment.
2. Contract dates must be valid.
3. Contract cannot activate before approval.
4. Required signatures must exist before acceptance where configured.
5. Amendment must preserve prior version.
6. Renewal must reference the preceding contract.
7. Contract periods must comply with overlap rules.

### 36.5 Events

- ContractDrafted
- ContractSubmitted
- ContractApproved
- ContractIssued
- ContractAccepted
- ContractActivated
- ContractAmended
- ContractExpiryApproaching
- ContractExpired
- ContractRenewed
- ContractTerminated

---

## 37. Workforce Request Aggregate

### 37.1 Aggregate Root

```text
WorkforceRequest
```

### 37.2 Responsibilities

- Staffing need
- Requested headcount
- Justification
- Position or job requirement
- Funding reference
- Target date
- Approval state

### 37.3 Events

- WorkforceRequestCreated
- WorkforceRequestSubmitted
- WorkforceRequestApproved
- WorkforceRequestRejected
- WorkforceRequestCancelled
- WorkforceRequestConvertedToRequisition

---

## 38. Job Requisition Aggregate

### 38.1 Aggregate Root

```text
JobRequisition
```

### 38.2 Responsibilities

- Recruitment authorization
- Vacancy count
- Hiring manager
- Recruitment owner
- Screening plan
- Interview plan
- Recruitment timeline
- Recruitment status

### 38.3 Events

- JobRequisitionCreated
- JobRequisitionSubmitted
- JobRequisitionApproved
- JobRequisitionPublished
- JobRequisitionClosed
- JobRequisitionCancelled

---

## 39. Vacancy Aggregate

### 39.1 Aggregate Root

```text
Vacancy
```

### 39.2 Responsibilities

- Public or internal job opening
- Publication channels
- Application window
- Screening questions
- Application requirements
- Vacancy status
- Candidate pipeline

### 39.3 Events

- VacancyCreated
- VacancyPublished
- VacancyOpened
- VacancyClosed
- VacancyExtended
- VacancyFilled
- VacancyCancelled

---

## 40. Application Aggregate

### 40.1 Aggregate Root

```text
Application
```

### 40.2 Responsibilities

- Applicant-vacancy relationship
- Application form
- Submitted documents
- Screening responses
- Application state
- Candidate progression
- Applicant consent

### 40.3 Child Entities

- ApplicationResponse
- ApplicationDocumentReference
- ScreeningEvaluation
- ShortlistDecision
- CandidateStatusHistory

### 40.4 Invariants

1. Applicant must reference a valid person or applicant identity.
2. Application must reference an open or eligible vacancy.
3. Duplicate application rules must be enforced.
4. Submission must satisfy required fields.
5. Rejected applications cannot progress without authorized reopening.

### 40.5 Events

- ApplicationStarted
- ApplicationSubmitted
- ApplicationScreened
- ApplicationShortlisted
- ApplicationRejected
- ApplicationWithdrawn
- ApplicantPromotedToCandidate

---

## 41. Interview Aggregate

### 41.1 Aggregate Root

```text
Interview
```

### 41.2 Responsibilities

- Candidate scheduling
- Panel membership
- Interview type
- Venue or virtual link reference
- Scorecards
- Panel results
- Recommendations
- Attendance

### 41.3 Events

- InterviewScheduled
- InterviewRescheduled
- InterviewCancelled
- InterviewStarted
- InterviewCompleted
- InterviewRecommendationSubmitted

---

## 42. Job Offer Aggregate

### 42.1 Aggregate Root

```text
JobOffer
```

### 42.2 Responsibilities

- Candidate offer
- Proposed employment terms
- Position
- Grade
- Start date
- Contract reference
- Compensation structure reference
- Approval
- Issue
- Acceptance
- Rejection
- Expiry

### 42.3 Invariants

1. Offer must reference a selected candidate.
2. Offer cannot be issued before approval.
3. Offer expiry must be after issue date.
4. Accepted offer cannot be accepted twice.
5. Withdrawn or expired offer cannot be accepted without authorized reactivation.

### 42.4 Events

- JobOfferCreated
- JobOfferSubmitted
- JobOfferApproved
- JobOfferIssued
- JobOfferAccepted
- JobOfferRejected
- JobOfferExpired
- JobOfferWithdrawn

---

## 43. Onboarding Case Aggregate

### 43.1 Aggregate Root

```text
OnboardingCase
```

### 43.2 Responsibilities

- Employee onboarding lifecycle
- Template application
- Task orchestration
- Required documents
- Cross-engine requests
- Progress tracking
- Completion

### 43.3 Child Entities

- OnboardingTask
- OnboardingChecklistItem
- OnboardingAcknowledgement
- OnboardingDependency
- OnboardingFulfillmentReference

### 43.4 Invariants

1. Onboarding must reference an accepted offer or approved direct hire.
2. Required tasks must complete before final completion.
3. External task completion must be confirmed by owning engine.
4. Employee activation must comply with tenant onboarding policy.

### 43.5 Events

- OnboardingCaseCreated
- OnboardingStarted
- OnboardingTaskAssigned
- OnboardingTaskCompleted
- OnboardingBlocked
- OnboardingCompleted

---

## 44. Employee Movement Aggregate

### 44.1 Aggregate Root

```text
EmployeeMovement
```

### 44.2 Movement Types

- Promotion
- Transfer
- Demotion
- Reassignment
- Secondment
- Acting appointment
- Return from secondment
- Return from acting appointment
- Branch change
- Department change
- Position change
- Supervisor change
- Grade change
- Company movement
- Work location change

### 44.3 Responsibilities

- Current assignment snapshot
- Proposed assignment
- Effective date
- Reason
- Payroll impact flag
- Workflow status
- Supporting documents
- Completion status

### 44.4 Events

- EmployeeMovementCreated
- EmployeeMovementSubmitted
- EmployeeMovementApproved
- EmployeeMovementRejected
- EmployeeMovementScheduled
- EmployeeMovementApplied
- EmployeeMovementCancelled

---

## 45. Leave Account Aggregate

### 45.1 Aggregate Root

```text
LeaveAccount
```

### 45.2 Responsibilities

- Employee leave entitlement
- Leave policy assignment
- Accrued balance
- Used balance
- Reserved balance
- Carried balance
- Expiring balance
- Adjustments
- Balance history

### 45.3 Child Entities

- LeaveAccrual
- LeaveReservation
- LeaveAdjustment
- LeaveCarryForward
- LeaveExpiry

### 45.4 Invariants

1. Leave account belongs to one employee and leave type.
2. Balance cannot become invalid beyond policy limits.
3. Reservations must be released on rejection or cancellation.
4. Adjustments require reason and audit.
5. Accrual processing must be idempotent.
6. Closed service periods cannot accrue leave unless policy allows.

### 45.5 Events

- LeaveAccountCreated
- LeaveAccrued
- LeaveReserved
- LeaveConsumed
- LeaveReservationReleased
- LeaveBalanceAdjusted
- LeaveCarriedForward
- LeaveExpired

---

## 46. Leave Request Aggregate

### 46.1 Aggregate Root

```text
LeaveRequest
```

### 46.2 Responsibilities

- Requested leave dates
- Duration
- Leave type
- Reason
- Handover
- Supporting document references
- Approval state
- Cancellation
- Recall
- Extension
- Return-to-work

### 46.3 Invariants

1. Employee must be eligible.
2. Dates must be within employment.
3. Leave must not overlap incompatible absence.
4. Balance and policy must permit request.
5. Required documents must exist.
6. Approved leave must reserve or consume balance correctly.
7. Cancellation rules depend on leave state and dates.

### 46.4 Events

- LeaveRequestCreated
- LeaveRequestSubmitted
- LeaveRequestApproved
- LeaveRequestRejected
- LeaveRequestCancelled
- LeaveRequestRecalled
- LeaveRequestExtended
- EmployeeReturnedFromLeave
- LeaveWithoutPayApproved
- LeaveEncashmentApproved

---

## 47. Work Schedule Aggregate

### 47.1 Aggregate Root

```text
WorkSchedule
```

### 47.2 Responsibilities

- Working days
- Daily hours
- Break rules
- Calendar
- Shift pattern
- Effective assignment
- Overtime threshold
- Flexibility rules

### 47.3 Events

- WorkScheduleCreated
- WorkScheduleAssigned
- WorkScheduleChanged
- WorkScheduleRetired

---

## 48. Shift Roster Aggregate

### 48.1 Aggregate Root

```text
ShiftRoster
```

### 48.2 Responsibilities

- Scheduling period
- Employees
- Shift assignments
- Publication
- Changes
- Swaps
- Coverage validation

### 48.3 Events

- RosterCreated
- RosterPublished
- ShiftAssigned
- ShiftChanged
- ShiftSwapRequested
- ShiftSwapApproved
- RosterFinalized

---

## 49. Attendance Period Aggregate

### 49.1 Aggregate Root

```text
AttendancePeriod
```

### 49.2 Responsibilities

- Attendance processing window
- Raw clock ingestion status
- Attendance calculation
- Exception resolution
- Manager review
- Finalization
- Reopening

### 49.3 Child Entities

- AttendanceDay
- ClockEventReference
- AttendanceException
- AttendanceCorrection

### 49.4 Invariants

1. Attendance cannot finalize while blocking exceptions remain.
2. Finalized periods cannot change without reopening.
3. Reopening requires authorization and reason.
4. Calculations must use effective work schedules.
5. Approved leave and holidays must be respected.

### 49.5 Events

- AttendancePeriodOpened
- ClockEventsImported
- AttendanceCalculated
- AttendanceExceptionRaised
- AttendanceCorrectionSubmitted
- AttendanceCorrectionApproved
- AttendancePeriodFinalized
- AttendancePeriodReopened

---

## 50. Timesheet Aggregate

### 50.1 Aggregate Root

```text
Timesheet
```

### 50.2 Responsibilities

- Employee time reporting
- Reporting period
- Project or activity lines
- Hours
- Submission
- Approval
- Finalization

### 50.3 Child Entities

- TimesheetEntry
- TimesheetAllocation
- TimesheetComment
- TimesheetApprovalReference

### 50.4 Invariants

1. Timesheet belongs to one employee and period.
2. Entries must fall within the period.
3. Entries must not overlap where hour ranges are used.
4. Total hours must comply with policy.
5. Inactive projects cannot receive new entries.
6. Finalized timesheets cannot change without reopening.

### 50.5 Events

- TimesheetCreated
- TimesheetEntryAdded
- TimesheetSubmitted
- TimesheetApproved
- TimesheetRejected
- TimesheetReopened
- TimesheetFinalized

---

## 51. Overtime Request Aggregate

### 51.1 Aggregate Root

```text
OvertimeRequest
```

### 51.2 Responsibilities

- Overtime period
- Requested hours
- Approved hours
- Category
- Reason
- Project
- Cost center
- Compensation method
- Approval state

### 51.3 Events

- OvertimeRequestCreated
- OvertimeRequestSubmitted
- OvertimeRequestApproved
- OvertimeRequestRejected
- OvertimeHoursAdjusted
- OvertimeFinalized

---

## 52. Performance Cycle Aggregate

### 52.1 Aggregate Root

```text
PerformanceCycle
```

### 52.2 Responsibilities

- Review period
- Participants
- Templates
- Review stages
- Deadlines
- Calibration
- Completion

### 52.3 Events

- PerformanceCycleCreated
- PerformanceCycleLaunched
- PerformanceStageOpened
- PerformanceStageClosed
- PerformanceCycleCompleted

---

## 53. Performance Review Aggregate

### 53.1 Aggregate Root

```text
PerformanceReview
```

### 53.2 Responsibilities

- Employee review
- Goals
- KPIs
- Competencies
- Assessments
- Ratings
- Comments
- Acknowledgements
- Appeal
- Development plan

### 53.3 Child Entities

- ReviewGoal
- ReviewKPI
- CompetencyAssessment
- ReviewerAssessment
- ReviewRating
- ReviewAcknowledgement
- ReviewAppeal

### 53.4 Events

- PerformanceReviewCreated
- PerformanceGoalsSubmitted
- SelfAssessmentCompleted
- ManagerAssessmentCompleted
- ReviewCalibrated
- ReviewFinalized
- ReviewAcknowledged
- ReviewAppealed

---

## 54. Training Plan Aggregate

### 54.1 Aggregate Root

```text
TrainingPlan
```

### 54.2 Responsibilities

- Training needs
- Planned courses
- Target participants
- Planned dates
- Cost references
- Approval state

### 54.3 Events

- TrainingPlanCreated
- TrainingPlanSubmitted
- TrainingPlanApproved
- TrainingPlanRevised
- TrainingPlanClosed

---

## 55. Training Session Aggregate

### 55.1 Aggregate Root

```text
TrainingSession
```

### 55.2 Responsibilities

- Course delivery
- Schedule
- Facilitator
- Venue
- Participants
- Attendance
- Assessments
- Evaluations
- Completion

### 55.3 Events

- TrainingSessionScheduled
- EmployeeNominatedForTraining
- TrainingNominationApproved
- TrainingSessionStarted
- TrainingAttendanceRecorded
- TrainingSessionCompleted
- TrainingCertificateAwarded

---

## 56. Disciplinary Case Aggregate

### 56.1 Aggregate Root

```text
DisciplinaryCase
```

### 56.2 Responsibilities

- Allegation
- Incident
- Investigation
- Evidence references
- Employee response
- Hearing
- Findings
- Sanctions
- Appeal
- Closure

### 56.3 Invariants

1. Case access must be restricted.
2. Every action must be auditable.
3. Sanction must follow approved workflow.
4. Closed cases cannot change without authorized reopening.
5. Evidence must be immutable or versioned through Document Management.

### 56.4 Events

- DisciplinaryCaseOpened
- InvestigationStarted
- HearingScheduled
- EmployeeResponseRecorded
- DisciplinaryFindingRecorded
- DisciplinarySanctionApproved
- DisciplinaryAppealSubmitted
- DisciplinaryCaseClosed

---

## 57. Grievance Case Aggregate

### 57.1 Aggregate Root

```text
GrievanceCase
```

### 57.2 Responsibilities

- Grievance submission
- Confidentiality
- Investigation
- Mediation
- Findings
- Resolution
- Appeal
- Closure

### 57.3 Events

- GrievanceSubmitted
- GrievanceAssigned
- GrievanceInvestigationStarted
- GrievanceResolutionProposed
- GrievanceResolved
- GrievanceAppealed
- GrievanceClosed

---

## 58. Separation Case Aggregate

### 58.1 Aggregate Root

```text
SeparationCase
```

### 58.2 Responsibilities

- Separation reason
- Notice
- Last working date
- Workflow
- Handover
- Clearance
- Final dues request
- Rehire eligibility
- Completion

### 58.3 Child Entities

- SeparationNotice
- ExitClearanceRequirement
- ExitClearanceResponse
- ExitInterview
- HandoverItem
- FinalDuesReference

### 58.4 Invariants

1. Separation date must be valid.
2. Required workflow approvals must complete.
3. Mandatory clearance must complete before final closure.
4. Payroll final dues must remain external.
5. Employment status must update effective on separation date.
6. User deactivation requests must be sent to Platform Core.
7. Historical records must remain preserved.

### 58.5 Events

- SeparationCaseCreated
- ResignationSubmitted
- TerminationProposed
- SeparationApproved
- ExitClearanceStarted
- ExitClearanceCompleted
- EmployeeFinalDuesRequested
- EmploymentSeparationScheduled
- EmployeeSeparated
- SeparationCaseCompleted

---

# PART IV — VALUE OBJECTS

## 59. Value Object Principles

Value objects:

- Have no independent identity
- Are immutable where practical
- Validate their own state
- Are compared by value
- Encapsulate domain meaning

---

## 60. Core Value Objects

The HR Engine should use value objects such as:

- TenantContext
- OrganizationContext
- CompanyContext
- BranchContext
- EmployeeNumber
- PersonName
- DateRange
- EffectivePeriod
- EmploymentPercentage
- FullTimeEquivalent
- WorkDuration
- LeaveDuration
- AttendanceDuration
- TimesheetDuration
- PositionCapacity
- GradeCode
- JobCode
- DepartmentCode
- ContractNumber
- VacancyNumber
- ApplicationNumber
- CaseNumber
- ConfidentialityLevel
- EmploymentStatus
- AssignmentStatus
- ContractStatus
- LeaveStatus
- AttendanceStatus
- TimesheetStatus
- ReviewStatus
- SeparationStatus
- DocumentReference
- WorkflowReference
- UserReference
- MoneyReference
- CostCenterReference
- ProjectReference

---

## 61. EffectivePeriod Value Object

```text
EffectivePeriod
├── effective_from
└── effective_to
```

Rules:

1. `effective_from` is required.
2. `effective_to` may be open-ended.
3. `effective_to` cannot precede `effective_from`.
4. Period overlap rules depend on business context.
5. Open-ended periods represent current or indefinite validity.

---

## 62. PersonName Value Object

The PersonName value object may include:

- Title
- Given name
- Middle name
- Family name
- Previous name
- Preferred name
- Display name
- Local-script name

Formatting must be localization-aware.

---

## 63. WorkDuration Value Object

WorkDuration supports:

- Minutes
- Hours
- Days
- Partial days
- Scheduled duration
- Actual duration

Conversion rules must use the employee’s effective work schedule.

---

## 64. ConfidentialityLevel Value Object

Supported levels include:

```text
Public
Internal
Restricted
Confidential
Highly Confidential
Legal Hold
```

The value object influences:

- Authorization
- Search indexing
- Export rules
- Document access
- Audit intensity
- UI masking

---

# PART V — DOMAIN SERVICES

## 65. Domain Service Principles

Domain services are used where business logic:

- Does not naturally belong to one entity
- Requires multiple domain concepts
- Remains within the HR bounded context
- Must not depend directly on infrastructure

---

## 66. Person Matching Service

The Person Matching Service identifies potential duplicates using:

- National identification
- Passport
- Email
- Phone
- Name
- Date of birth
- Previous employment
- Applicant history

It returns matching confidence and duplicate warnings.

Final merge decisions require authorization and audit.

---

## 67. Assignment Validation Service

The Assignment Validation Service checks:

- Employment date compatibility
- Company compatibility
- Position capacity
- Grade eligibility
- Job eligibility
- Supervisor hierarchy cycles
- Branch validity
- Department validity
- Assignment overlaps
- Primary assignment rules

---

## 68. Position Occupancy Service

The Position Occupancy Service determines:

- Current incumbents
- Scheduled incumbents
- Vacancies
- Acting occupants
- Seconded occupants
- Capacity utilization
- Overcapacity
- Vacancy dates

---

## 69. Reporting Hierarchy Service

The Reporting Hierarchy Service resolves:

- Direct supervisor
- Functional supervisor
- Administrative supervisor
- Acting supervisor
- Delegated supervisor
- Indirect reports
- Manager access scope
- Approval hierarchy inputs

It must be effective-date aware.

---

## 70. Leave Eligibility Service

The Leave Eligibility Service determines:

- Applicable leave policy
- Employee eligibility
- Available balance
- Minimum service
- Maximum duration
- Required documents
- Notice requirements
- Overlap conflicts
- Holiday treatment
- Weekend treatment
- Payroll impact

---

## 71. Leave Accrual Service

The Leave Accrual Service calculates:

- Accrued entitlement
- Proration
- Carry-forward
- Expiry
- Service-based adjustments
- Contract-based adjustments
- Grade-based adjustments
- Suspension impact
- Unpaid leave impact

It must be deterministic and idempotent.

---

## 72. Attendance Calculation Service

The Attendance Calculation Service converts raw clock events into attendance outcomes.

It considers:

- Work schedule
- Shift
- Holiday calendar
- Approved leave
- Clock events
- Break rules
- Grace periods
- Overtime thresholds
- Cross-midnight shifts
- Attendance exceptions

---

## 73. Timesheet Validation Service

The Timesheet Validation Service checks:

- Employee eligibility
- Period status
- Project validity
- Activity validity
- Overlaps
- Maximum hours
- Employment dates
- Leave conflicts
- Attendance conflicts
- Closed project periods

---

## 74. Employee Movement Service

The Employee Movement Service coordinates:

- Current assignment closure
- Future assignment creation
- Position occupancy update
- Supervisor update
- Organization chart update
- Payroll impact event
- CRM impact event
- POS impact event
- Notification requests

---

## 75. Separation Coordination Service

The Separation Coordination Service coordinates:

- Separation approval
- Employment end scheduling
- Exit clearance
- Inventory clearance
- Expense clearance
- Payroll final dues request
- User access deactivation request
- Assignment closure
- Position vacancy
- Former employee status

This service coordinates but does not take ownership of external engine data.

---

# PART VI — APPLICATION SERVICES

## 76. Application Service Categories

Application services are grouped by business capability.

---

## 77. Workforce Application Services

Examples:

- CreatePersonService
- CreateEmployeeService
- ActivateEmployeeService
- RehireEmployeeService
- UpdateEmployeeProfileService
- LinkEmployeeToUserService
- ChangeEmployeeStatusService

---

## 78. Organization Application Services

Examples:

- CreateOrganizationUnitService
- MoveOrganizationUnitService
- AssignUnitHeadService
- CreatePositionService
- ApprovePositionService
- AssignEmployeeToPositionService
- VacatePositionService
- ChangeReportingLineService

---

## 79. Recruitment Application Services

Examples:

- CreateWorkforceRequestService
- ApproveWorkforceRequestService
- CreateJobRequisitionService
- PublishVacancyService
- SubmitApplicationService
- ScreenApplicationService
- ShortlistCandidateService
- ScheduleInterviewService
- RecordInterviewScoreService
- CreateJobOfferService
- AcceptJobOfferService
- ConvertCandidateToEmployeeService

---

## 80. Employment Administration Services

Examples:

- CreateEmploymentService
- CreateContractService
- ActivateContractService
- RenewContractService
- ConfirmEmployeeService
- PromoteEmployeeService
- TransferEmployeeService
- StartSecondmentService
- StartActingAppointmentService
- ApplyEmployeeMovementService

---

## 81. Leave Application Services

Examples:

- CreateLeaveAccountService
- ProcessLeaveAccrualService
- SubmitLeaveRequestService
- ApproveLeaveRequestService
- CancelLeaveRequestService
- RecallEmployeeFromLeaveService
- ProcessLeaveCarryForwardService
- ApproveLeaveEncashmentService

---

## 82. Attendance Application Services

Examples:

- ImportClockEventsService
- CalculateAttendanceService
- SubmitAttendanceCorrectionService
- ApproveAttendanceCorrectionService
- FinalizeAttendancePeriodService
- ReopenAttendancePeriodService

---

## 83. Timesheet Application Services

Examples:

- CreateTimesheetService
- AddTimesheetEntryService
- SubmitTimesheetService
- ApproveTimesheetService
- RejectTimesheetService
- FinalizeTimesheetService
- ReopenTimesheetService

---

## 84. Performance Application Services

Examples:

- CreatePerformanceCycleService
- LaunchPerformanceCycleService
- AssignGoalsService
- SubmitSelfAssessmentService
- SubmitManagerAssessmentService
- CalibratePerformanceService
- FinalizePerformanceReviewService
- CreateDevelopmentPlanService

---

## 85. Learning Application Services

Examples:

- CreateTrainingPlanService
- ScheduleTrainingSessionService
- NominateEmployeeService
- ApproveTrainingNominationService
- RecordTrainingAttendanceService
- CompleteTrainingSessionService
- AwardTrainingCertificateService

---

## 86. Employee Relations Services

Examples:

- OpenDisciplinaryCaseService
- StartInvestigationService
- ScheduleHearingService
- RecordFindingService
- ApproveSanctionService
- SubmitDisciplinaryAppealService
- SubmitGrievanceService
- ResolveGrievanceService

---

## 87. Separation Application Services

Examples:

- SubmitResignationService
- InitiateTerminationService
- ApproveSeparationService
- StartExitClearanceService
- RecordExitClearanceResponseService
- RequestFinalDuesService
- CompleteSeparationService

---

# PART VII — COMMAND AND QUERY ARCHITECTURE

## 88. Command Model

Commands represent requested state changes.

Examples:

```text
CreateEmployeeCommand
ActivateEmploymentCommand
TransferEmployeeCommand
SubmitLeaveRequestCommand
ApproveOvertimeCommand
FinalizeAttendanceCommand
SubmitTimesheetCommand
CompletePerformanceReviewCommand
InitiateSeparationCommand
```

Every command should include:

- Command identifier
- Tenant context
- Actor context
- Correlation identifier
- Idempotency key where applicable
- Target aggregate identifier
- Requested data
- Reason where required
- Expected record version where applicable

---

## 89. Query Model

Queries retrieve read-optimized information.

Examples:

```text
GetEmployeeProfileQuery
SearchEmployeesQuery
GetOrganizationChartQuery
GetManagerTeamQuery
GetLeaveBalanceQuery
GetAttendanceSummaryQuery
GetTimesheetStatusQuery
GetRecruitmentPipelineQuery
GetPerformanceDashboardQuery
GetExitClearanceStatusQuery
```

Queries must enforce the same authorization and tenant restrictions as commands.

---

## 90. CQRS Approach

The Human Resources Engine may use a pragmatic CQRS approach.

The write model:

- Uses aggregates
- Enforces invariants
- Preserves transaction boundaries
- Emits domain events

The read model:

- Uses optimized views
- Uses projections
- Supports dashboards
- Supports Employee 360
- Supports organization charts
- Supports reports
- Supports search

CQRS does not require separate physical databases.

---

## 91. Read Models

Recommended read models include:

- EmployeeDirectoryView
- Employee360View
- EmployeeCurrentAssignmentView
- ManagerTeamView
- OrganizationChartView
- PositionVacancyView
- RecruitmentPipelineView
- ContractExpiryView
- ProbationExpiryView
- LeaveBalanceView
- TeamLeaveCalendarView
- AttendanceSummaryView
- TimesheetComplianceView
- PerformanceCompletionView
- SkillsInventoryView
- TrainingHistoryView
- ExitClearanceView
- HRDashboardView

Read models may combine authorized projections from other engines.

---

# PART VIII — EFFECTIVE-DATED ARCHITECTURE

## 92. Effective Dating Requirement

HR data changes over time.

The architecture must preserve:

- Current state
- Historical state
- Future scheduled state
- Recorded transaction history

Direct destructive updates are prohibited for effective-dated workforce information.

---

## 93. Bi-Temporal Consideration

The architecture should distinguish:

1. Business effective time
2. System recorded time

Example:

```text
Promotion effective date: 2026-08-01
Promotion recorded date: 2026-07-21
```

This allows future-dated changes and historical correction tracking.

---

## 94. Effective-Dated Entities

The following must support effective dating:

- Employment
- Assignment
- Department structure
- Position
- Job grade assignment
- Supervisor assignment
- Work location
- Working calendar
- Shift pattern
- Contract
- Payroll eligibility
- Leave policy assignment
- Project assignment
- Cost center assignment
- Employee status
- Position occupancy

---

## 95. Effective-Dated Change Pattern

A change must:

1. Validate the proposed effective date.
2. Close or split the current effective period.
3. Create a new effective record.
4. Preserve the old record.
5. Emit an event.
6. Update current-state projections.
7. Notify dependent engines where required.

---

## 96. Future-Dated Changes

The engine must support scheduling:

- Promotions
- Transfers
- Contract activation
- Contract expiry
- Supervisor changes
- Branch changes
- Position changes
- Terminations
- Leave policy changes
- Shift changes

Scheduled changes must be applied by an idempotent effective-date processor.

---

## 97. Retroactive Changes

Retroactive changes require elevated authorization.

Examples:

- Correcting hire date
- Correcting assignment history
- Backdating a promotion
- Correcting termination date
- Correcting leave eligibility
- Correcting grade history

Retroactive changes may affect Payroll.

The HR Engine must publish retroactive change events with impacted periods.

---

# PART IX — EVENT-DRIVEN ARCHITECTURE

## 98. Event Types

The engine uses:

- Domain events
- Integration events
- Audit events
- Notification request events
- Projection update events

---

## 99. Domain Events

Domain events describe facts that occurred within an aggregate.

Example:

```text
EmployeePromoted
```

A domain event is raised by the aggregate and handled within the HR application boundary.

---

## 100. Integration Events

Integration events are published to the Platform Event Bus for consumption by other engines.

Examples:

- EmployeeActivated
- EmployeeTransferred
- EmployeePromoted
- EmployeeTerminated
- LeaveWithoutPayApproved
- OvertimeApproved
- AttendancePeriodFinalized
- TimesheetFinalized
- EmployeeFinalDuesRequested

Integration events must contain only required data.

Sensitive HR details must not be broadly published.

---

## 101. Event Envelope

Every integration event must use the platform event envelope.

```json
{
  "event_id": "uuid",
  "event_type": "hr.employee.promoted",
  "event_version": 1,
  "occurred_at": "timestamp",
  "published_at": "timestamp",
  "tenant_id": "uuid",
  "organization_id": "uuid",
  "company_id": "uuid",
  "branch_id": "uuid",
  "correlation_id": "uuid",
  "causation_id": "uuid",
  "aggregate_type": "employee_movement",
  "aggregate_id": "uuid",
  "actor_id": "uuid",
  "source_engine": "human_resources",
  "payload": {},
  "metadata": {}
}
```

---

## 102. Transactional Outbox

The HR Engine must use a transactional outbox pattern.

Within the same database transaction:

1. Aggregate state is persisted.
2. Domain changes are saved.
3. Outbox event is created.
4. Audit request is recorded or queued.

A background publisher sends the outbox event to the Platform Event Bus.

This prevents data changes without corresponding events.

---

## 103. Event Idempotency

Consumers must use:

- Event identifier
- Consumer name
- Processing status
- Processed timestamp
- Retry count

Duplicate event delivery must not create duplicate business effects.

---

## 104. Dead-Letter Handling

Failed events must be retained with:

- Event identifier
- Event type
- Consumer
- Failure reason
- Attempt count
- Last attempted time
- Correlation identifier
- Resolution status

Authorized administrators must be able to retry or resolve failures.

---

## 105. Event Versioning

Event schemas must be versioned.

Rules:

1. Do not remove required fields from an existing version.
2. Additive changes should remain backward compatible.
3. Breaking changes require a new event version.
4. Consumers must declare supported versions.
5. Historical events must remain interpretable.

---

# PART X — CROSS-ENGINE INTEGRATION

## 106. Integration Rules

Cross-engine interactions must follow these rules:

1. No direct writes to another engine’s tables.
2. Read access should use APIs, projections, or governed views.
3. State changes should use commands or events.
4. Every integration must be tenant-aware.
5. Every integration must be idempotent where repeated delivery is possible.
6. Sensitive fields must be minimized.
7. Failures must be observable.
8. Ownership boundaries must remain explicit.

---

## 107. Payroll Integration Architecture

Payroll consumes authoritative HR events and approved HR outputs.

```text
HR Employment and Time Data
           │
           ▼
Platform Event Bus
           │
           ▼
Payroll Workforce Projection
           │
           ▼
Payroll Calculation
```

Payroll should maintain a payroll-specific employee projection containing only required fields.

Payroll must not duplicate the full Employee Master.

---

## 108. Payroll Employee Projection

The Payroll Engine may maintain:

- Employee identifier
- Employment identifier
- Assignment identifier
- Company
- Branch
- Department
- Grade
- Payroll eligibility
- Effective dates
- Employment status
- Cost center reference
- Project reference
- Approved time inputs

This projection is not authoritative HR data.

---

## 109. Payroll-Relevant HR Events

Examples:

- EmployeeActivated
- EmployeePayrollEligibilityChanged
- EmploymentActivated
- EmploymentEnded
- AssignmentChanged
- EmployeePromoted
- EmployeeTransferred
- LeaveWithoutPayApproved
- LeaveEncashmentApproved
- OvertimeApproved
- AttendancePeriodFinalized
- TimesheetFinalized
- EmployeeFinalDuesRequested

---

## 110. Finance Integration Architecture

HR consumes Finance references such as:

- Cost centers
- Projects
- Grants
- Funding sources
- Budget references

HR publishes non-financial workforce events.

Finance should not derive payroll journals directly from HR.

Payroll remains the intermediary for payroll accounting.

---

## 111. Expenses Integration Architecture

```text
HR Employee Eligibility
        │
        ▼
Expenses Management Engine
        │
        │ Approved Recovery
        ▼
Payroll Engine
```

HR supplies employee status and assignment information.

Expenses owns the advance or claim.

Payroll owns the salary deduction.

---

## 112. Inventory Integration Architecture

```text
Onboarding or HR Asset Requirement
             │
             ▼
Inventory Assignment Request
             │
             ▼
Inventory Engine Issues Item
             │
             ▼
Asset Assignment Confirmation
             │
             ▼
HR Employee 360 Projection
```

Inventory owns the stock transaction.

HR may display the assignment through a read projection.

---

## 113. CRM Integration Architecture

CRM consumes:

- Active employee state
- Assignment
- Territory eligibility
- Account manager relationship inputs

HR consumes no customer master ownership.

CRM remains authoritative for customer assignment records.

---

## 114. POS Integration Architecture

POS may consume:

- Employee active status
- Branch assignment
- Cashier eligibility
- HR shift reference
- Termination or suspension events

Authorization Engine controls POS permissions.

POS owns till and cashier session records.

---

## 115. Platform Core Integration Architecture

Platform Core provides:

- Tenant context
- Company
- Branch
- User
- Authentication
- Subscription
- Module activation

HR links an employee to a platform user but does not create independent authentication records.

---

## 116. Workflow Integration Architecture

Workflow is initiated by HR application services.

```text
HR Business Document
        │
        ▼
Workflow Start Request
        │
        ▼
Workflow Engine
        │
        ▼
Approval Decision Event
        │
        ▼
HR Applies Business Transition
```

The Workflow Engine must not directly mutate HR business records.

---

## 117. Document Integration Architecture

```text
HR Business Record
        │
        ▼
Document Upload Request
        │
        ▼
Document Management Engine
        │
        ▼
Document Reference Returned
        │
        ▼
HR Stores Document Reference
```

HR stores business classification and relationship.

Document Management owns file infrastructure.

---

## 118. Notification Integration Architecture

HR emits notification requests containing:

- Template key
- Recipient reference
- Context
- Business entity reference
- Preferred channels
- Priority
- Schedule

Notification Engine handles delivery.

---

## 119. Reporting Integration Architecture

HR provides:

- Governed views
- Materialized projections
- Event-fed analytical datasets
- Authorized data contracts

Reporting Engine provides:

- Dashboards
- Report execution
- Scheduled reports
- Exports
- Visualization
- Distribution

---

## 120. Search Integration Architecture

HR publishes indexable search documents.

Example:

```text
EmployeeSearchDocument
├── employee_id
├── employee_number
├── display_name
├── company
├── branch
├── department
├── position
├── status
├── permitted_search_scope
└── classification
```

Sensitive fields must be excluded.

---

# PART XI — WORKFLOW ORCHESTRATION AND SAGAS

## 121. Saga Requirement

Long-running processes spanning multiple aggregates or engines must use saga-style orchestration.

Examples:

- Candidate hire
- Onboarding
- Employee transfer
- Separation
- Exit clearance
- Final dues initiation

---

## 122. Candidate Hire Saga

```text
Job Offer Accepted
        │
        ▼
Person Match or Creation
        │
        ▼
Employee Creation
        │
        ▼
Employment Creation
        │
        ▼
Assignment Creation
        │
        ▼
Contract Preparation
        │
        ▼
Onboarding Case Creation
        │
        ▼
Platform User Provisioning Request
        │
        ▼
Payroll Enrollment Request
```

Each step must be:

- Idempotent
- Auditable
- Recoverable
- Correlation-aware

---

## 123. Employee Transfer Saga

```text
Transfer Approved
        │
        ▼
Validate Effective Date
        │
        ▼
Close Current Assignment
        │
        ▼
Create New Assignment
        │
        ▼
Update Position Occupancy
        │
        ▼
Publish Assignment Events
        │
        ├──▶ Payroll
        ├──▶ CRM
        ├──▶ POS
        ├──▶ Authorization
        └──▶ Reporting
```

---

## 124. Separation Saga

```text
Separation Approved
        │
        ▼
Schedule Employment End
        │
        ▼
Start Exit Clearance
        │
        ├──▶ Inventory
        ├──▶ Expenses
        ├──▶ Finance
        ├──▶ IT or Platform Core
        └──▶ Payroll
        │
        ▼
Clearance Completed
        │
        ▼
Request Final Dues
        │
        ▼
End Employment
        │
        ▼
Deactivate Access
        │
        ▼
Vacate Position
        │
        ▼
Mark Former Employee
```

---

## 125. Saga Compensation

Where a multi-step process fails, compensating actions may include:

- Cancel scheduled assignment
- Release position reservation
- Cancel onboarding task
- Restore leave reservation
- Reopen clearance item
- Mark integration task failed
- Suspend finalization
- Require manual intervention

Not all HR actions are reversible.

Audit must preserve attempted and failed transitions.

---

# PART XII — MULTI-TENANT ARCHITECTURE

## 126. Tenant Isolation

Every HR-owned table must include:

```text
tenant_id
```

All queries must be tenant-scoped.

RLS must enforce tenant isolation at database level.

Service-role access must still explicitly validate tenant context.

---

## 127. Tenant Context Propagation

Tenant context must propagate through:

- Frontend route
- User session
- API call
- Edge Function
- Database transaction
- Event envelope
- Workflow request
- Document reference
- Audit record
- Notification request
- Report request
- Search document

---

## 128. Cross-Tenant Prohibition

The system must prevent:

- Cross-tenant employee search
- Cross-tenant reporting
- Cross-tenant document access
- Cross-tenant workflow routing
- Cross-tenant event consumption
- Cross-tenant manager hierarchy
- Cross-tenant employee numbering collision where tenant-scoped
- Cross-tenant imports
- Cross-tenant bulk actions

---

## 129. Shared Person Consideration

The same natural person may work for different tenants.

The platform must not create a global cross-tenant person identity visible to tenants.

Each tenant owns its own HR person record unless an explicit future consent-based identity service is introduced.

---

# PART XIII — MULTI-COMPANY ARCHITECTURE

## 130. Company Scope

Every employment belongs to a company or legal entity.

Assignments may also reference company.

The architecture must support:

- One employee with one company
- Sequential employment across companies
- Concurrent employment across companies
- Shared service arrangements
- Secondment
- Intercompany transfer

---

## 131. Intercompany Transfer

An intercompany transfer may require:

1. Ending current employment
2. Creating new employment
3. Preserving employee identity
4. Creating new assignment
5. Triggering payroll changes
6. Triggering benefit changes
7. Triggering leave transfer rules
8. Triggering new contract
9. Preserving service continuity where configured

Intercompany transfer rules vary by tenant and jurisdiction.

---

## 132. Company-Specific Configuration

Configuration may vary by company for:

- Employee numbering
- Contract templates
- Leave policies
- Probation rules
- Working calendars
- Shift definitions
- Organization structure
- Performance cycles
- Approval workflows
- Document requirements

---

# PART XIV — MULTI-BRANCH ARCHITECTURE

## 133. Branch Scope

Branches are owned by Platform Core.

HR uses branches for:

- Employee assignment
- Position placement
- Attendance location
- Holiday calendar
- Shift planning
- Manager access
- Reporting
- Recruitment
- Onboarding
- Separation clearance

---

## 134. Branch Transfers

A branch transfer must:

- Preserve assignment history
- Update manager hierarchy
- Update work schedule
- Update holiday calendar
- Notify Payroll
- Notify POS where relevant
- Update authorization scope
- Update reporting projections

---

# PART XV — MULTI-COUNTRY ARCHITECTURE

## 135. Country Rule Separation

Country-specific rules must be isolated from core HR logic.

The core domain defines extension points for:

- Employment validation
- Contract requirements
- Leave eligibility
- Probation rules
- Notice periods
- Document requirements
- Data retention
- Statutory identity fields
- Working hour restrictions

---

## 136. Country Policy Provider

The architecture may define:

```text
CountryHRPolicyProvider
```

Responsibilities include:

- Resolve applicable jurisdiction
- Validate country-specific employment data
- Return required documents
- Return leave minimums
- Return notice requirements
- Return privacy requirements
- Return retention requirements

Payroll statutory calculations remain outside this provider.

---

## 137. Jurisdiction Resolution

Jurisdiction may depend on:

- Employment company
- Work country
- Work location
- Contract jurisdiction
- Employee residence
- Assignment location

The applicable rule must be explicitly determined and auditable.

---

# PART XVI — SECURITY ARCHITECTURE

## 138. Security Layers

HR security uses multiple layers:

1. Authentication
2. Tenant isolation
3. Role authorization
4. Permission authorization
5. Company scope
6. Branch scope
7. Department scope
8. Reporting hierarchy scope
9. Record ownership
10. Field-level security
11. Confidentiality classification
12. Workflow participation
13. Document security
14. Audit monitoring

---

## 139. Employee Self-Access

Employees may access only authorized parts of their own record.

Examples:

- Personal profile
- Leave balance
- Attendance
- Timesheets
- Training
- Goals
- Documents
- Employment summary

Employees must not automatically access:

- Confidential HR notes
- Disciplinary investigation notes
- Manager-only review comments
- Internal recruitment assessments
- Payroll administration fields
- Other employees’ records

---

## 140. Manager Hierarchy Access

Manager access is derived from:

- Effective supervisor assignment
- Acting supervisor assignment
- Delegation
- Department leadership
- Workflow role
- Explicit HR authorization

Access must expire automatically when the reporting relationship ends.

---

## 141. Confidential Case Boundary

Disciplinary and grievance data must use separate authorization scopes.

General HR administrators may not automatically receive access.

Access may be restricted to:

- Assigned HR case officer
- Legal officer
- Investigation panel
- Authorized senior management
- Employee where permitted
- Appeal reviewer
- Auditor with approved scope

---

## 142. Field-Level Protection

Fields requiring elevated protection include:

- National identification
- Passport number
- Tax identification
- Social security number
- Bank details
- Medical details
- Disability information
- Emergency contacts
- Dependants
- Disciplinary details
- Grievance details
- Performance ratings
- Confidential documents

Detailed security rules will be defined in `SECURITY.md`.

---

# PART XVII — DATA ARCHITECTURE PRINCIPLES

## 143. Database Namespace

The HR Engine should use a dedicated logical schema or consistent prefix.

Preferred schema approach:

```text
hr
```

Example:

```text
hr.people
hr.employees
hr.employments
hr.assignments
hr.positions
hr.contracts
hr.leave_requests
hr.attendance_periods
hr.timesheets
```

Where Supabase schema limitations or application conventions require public schema usage, consistent prefixes must be used.

---

## 144. Standard Columns

Every HR aggregate table should include:

```text
id
tenant_id
created_at
created_by
updated_at
updated_by
record_version
```

Where applicable:

```text
organization_id
company_id
branch_id
status
effective_from
effective_to
workflow_instance_id
correlation_id
deleted_at
```

Soft deletion must be used cautiously.

Historical HR records should usually be inactivated rather than deleted.

---

## 145. Identity Strategy

Primary identifiers should use UUIDs.

Business identifiers such as employee numbers and contract numbers remain separate.

Example:

```text
id = internal UUID
employee_number = business identifier
```

---

## 146. Referential Integrity

Foreign keys must be used where ownership and deployment permit.

Cross-engine references may use:

- External UUID reference
- Cached projection
- Integration reference table

Cross-engine foreign keys should be avoided where they create deployment coupling.

---

## 147. JSON Usage

JSONB may be used for:

- Extensible metadata
- Integration payload snapshots
- Tenant-defined low-risk attributes
- Import diagnostics
- Workflow context
- Country-specific extension data

JSONB must not replace normalized core HR entities.

---

# PART XVIII — SCALABILITY ARCHITECTURE

## 148. Scalability Targets

The architecture must support:

- Large employee populations
- High-volume attendance events
- Large organization hierarchies
- High-volume recruitment
- Bulk leave accrual
- Bulk roster generation
- Mass notifications
- Large HR reports
- Multi-country operations

---

## 149. Partitioning Candidates

Potential partitioning candidates include:

- Clock events
- Attendance days
- Audit references
- Event outbox
- Timesheet entries
- Notification references
- Import rows

Partitioning may use:

- Tenant
- Date
- Company
- Hybrid strategies

Partitioning should be introduced based on measured scale.

---

## 150. Indexing Strategy

High-priority indexes include:

- Tenant and employee
- Tenant and employee number
- Tenant and status
- Tenant, company, and branch
- Effective date ranges
- Current assignment lookup
- Manager hierarchy lookup
- Position occupancy
- Contract expiry
- Leave account
- Leave date overlap
- Attendance employee-date
- Timesheet employee-period
- Recruitment vacancy-status
- Search projection keys

Detailed indexes will be defined in `DATABASE.md`.

---

## 151. Background Processing

Background jobs should handle:

- Leave accrual
- Contract expiry
- Probation expiry
- Attendance calculation
- Roster generation
- Timesheet reminders
- Event publishing
- Search indexing
- Document generation
- Bulk imports
- Report projection refresh
- Future-dated change activation

---

## 152. Bulk Operations

Bulk operations must support:

- Preview
- Validation
- Error isolation
- Batch identifier
- Progress tracking
- Cancellation where safe
- Partial failure reporting
- Audit
- Retry
- Reconciliation

Examples:

- Bulk employee import
- Bulk leave allocation
- Bulk shift assignment
- Bulk transfer
- Bulk contract renewal
- Bulk training nomination

---

# PART XIX — OBSERVABILITY ARCHITECTURE

## 153. Logging

Logs must include:

- Tenant
- Correlation identifier
- Operation
- Actor
- Aggregate
- Duration
- Outcome
- Error category

Sensitive personal data must be minimized.

---

## 154. Metrics

Recommended metrics include:

- Employee command latency
- HR API error rate
- Event publication lag
- Event consumption lag
- Leave accrual duration
- Attendance processing duration
- Import throughput
- Failed workflow count
- Failed integration count
- Search indexing lag
- Scheduled job success rate
- Realtime subscription count

---

## 155. Tracing

Distributed tracing should follow:

```text
User Action
   │
   ▼
HR Command
   │
   ▼
Workflow or Domain Change
   │
   ▼
Outbox Event
   │
   ▼
Platform Event Bus
   │
   ▼
Consuming Engine
```

Correlation identifiers must remain consistent across the flow.

---

# PART XX — FAILURE HANDLING

## 156. Failure Categories

Failures include:

- Validation failure
- Authorization failure
- Workflow failure
- Integration failure
- Event publication failure
- Document failure
- Notification failure
- Concurrency conflict
- Effective-date conflict
- External service failure
- Data quality failure
- Scheduled job failure

---

## 157. Retry Strategy

Retries are appropriate for:

- Event publication
- Notification request delivery
- Search indexing
- Report projection updates
- Temporary external integration errors
- Scheduled job transient failures

Retries are not appropriate for:

- Invalid business data
- Authorization denial
- Invalid state transition
- Duplicate business action
- Policy violation

---

## 158. Manual Intervention

Manual intervention queues should exist for:

- Failed employee hire saga
- Failed onboarding integration
- Failed payroll projection update
- Failed separation clearance
- Failed final dues request
- Failed attendance import
- Failed bulk migration
- Unresolved duplicate person
- Broken hierarchy relationship

---

# PART XXI — API ARCHITECTURE

## 159. API Categories

The HR API surface should include:

```text
/api/hr/people
/api/hr/employees
/api/hr/employments
/api/hr/assignments
/api/hr/organization-units
/api/hr/positions
/api/hr/jobs
/api/hr/contracts
/api/hr/recruitment
/api/hr/onboarding
/api/hr/movements
/api/hr/leave
/api/hr/attendance
/api/hr/shifts
/api/hr/timesheets
/api/hr/overtime
/api/hr/performance
/api/hr/learning
/api/hr/employee-relations
/api/hr/separations
/api/hr/self-service
/api/hr/manager-service
```

---

## 160. API Standards

APIs must support:

- Versioning
- Tenant context
- Correlation identifiers
- Idempotency
- Pagination
- Filtering
- Sorting
- Sparse field selection where appropriate
- Standard errors
- Authorization
- Audit
- Optimistic concurrency

---

## 161. Command Endpoint Pattern

Example:

```text
POST /api/hr/employees/{employeeId}/movements
```

The request should contain:

- Movement type
- Effective date
- Proposed assignment
- Reason
- Supporting document references
- Expected version

---

## 162. Query Endpoint Pattern

Example:

```text
GET /api/hr/employees/{employeeId}/profile
```

The response should be permission-filtered.

The API must not return inaccessible fields and depend only on frontend hiding.

---

# PART XXII — FRONTEND ARCHITECTURE

## 163. Frontend Module Boundary

The HR frontend should exist as one module with feature folders.

```text
src/modules/human-resources/
```

Each feature contains:

- Pages
- Components
- Hooks
- Schemas
- Services
- Types
- Contexts
- Routes

---

## 164. Context Architecture

Recommended contexts include:

- HRModuleContext
- EmployeeSelfServiceContext
- ManagerTeamContext
- HRPermissionContext
- OrganizationContext
- AttendanceContext
- RecruitmentContext

Contexts should not become global stores for all HR data.

---

## 165. Service Layer

Frontend services should:

- Call Supabase functions or governed APIs
- Attach tenant context
- Attach correlation identifiers
- Normalize errors
- Return typed responses
- Avoid embedding business rules

---

## 166. Validation Architecture

Validation occurs at:

1. Zod schema
2. Application service
3. Domain model
4. Database constraint
5. Workflow validation
6. Integration validation

No single layer is sufficient.

---

# PART XXIII — CONFIGURATION ARCHITECTURE

## 167. Configuration Layers

Configuration may exist at:

1. Platform default
2. Country default
3. Tenant
4. Company
5. Branch
6. Employee group
7. Employee override

The most specific valid configuration wins.

---

## 168. Configuration Resolution Service

The engine should provide:

```text
HRConfigurationResolver
```

It resolves:

- Leave policy
- Working calendar
- Probation policy
- Attendance rule
- Overtime rule
- Timesheet rule
- Contract template
- Document requirement
- Notification rule
- Performance template
- Onboarding template

---

## 169. Configuration Versioning

Configuration affecting employee outcomes must be versioned and effective-dated.

Examples:

- Leave policy
- Attendance tolerance
- Overtime eligibility
- Probation duration
- Performance rating scale
- Document requirement

Historical calculations must use the configuration effective at the relevant time.

---

# PART XXIV — ARCHITECTURAL DECISIONS

## 170. Decision: Separate Person and Employee

### Decision

Person and Employee are separate aggregates.

### Reason

This supports:

- Applicants before employment
- Rehire
- Multiple employments
- Reduced duplication
- Long-term workforce identity

---

## 171. Decision: Separate Employee and User

### Decision

Employee and Platform User remain separate.

### Reason

Not every employee requires system access, and not every platform user is an employee.

---

## 172. Decision: Separate Job and Position

### Decision

Job and Position remain distinct.

### Reason

A job defines work.

A position defines an authorized organizational seat.

---

## 173. Decision: Effective-Dated Assignments

### Decision

Employment assignments are effective-dated records.

### Reason

HR history must be preserved and future changes must be scheduled.

---

## 174. Decision: Payroll as Separate Engine

### Decision

Payroll is an independent bounded context.

### Reason

Payroll owns compensation calculations, statutory logic, payment outputs, and accounting integration.

HR only supplies authoritative workforce inputs.

---

## 175. Decision: Workflow Engine Owns Routing

### Decision

HR does not implement approval routing internally.

### Reason

Approval infrastructure is owned by the Workflow Engine.

---

## 176. Decision: Document Engine Owns Files

### Decision

HR stores document references, not unmanaged file paths.

### Reason

Document security, storage, versioning, and retention are platform responsibilities.

---

## 177. Decision: Event-Driven Cross-Engine Integration

### Decision

Cross-engine changes use Platform Event Bus events and governed commands.

### Reason

This preserves loose coupling and engine ownership.

---

## 178. Decision: Pragmatic CQRS

### Decision

Use aggregate-based write models and optimized read models.

### Reason

HR has complex writes and highly varied read requirements.

---

## 179. Decision: RLS as Mandatory Security Layer

### Decision

All tenant-owned HR tables must enforce RLS.

### Reason

Frontend and application filtering alone cannot guarantee tenant isolation.

---

## 180. Decision: No TanStack Query

### Decision

The HR frontend must use React Context, hooks, and service abstractions.

### Reason

This follows the established Business Suite frontend architecture.

---

# PART XXV — ARCHITECTURAL CONSTRAINTS

## 181. Mandatory Constraints

The Human Resources Engine must not:

- Duplicate Platform Core users
- Calculate payroll
- Own general ledger accounts
- Own expense transactions
- Own inventory stock
- Own customer records
- Own workflow definitions
- Store files independently
- Deliver notifications independently
- Implement separate audit infrastructure
- Implement separate search infrastructure
- Bypass RLS
- Perform cross-tenant queries
- Directly mutate another engine’s data
- Destroy effective-dated history
- Place authoritative rules only in the frontend
- Store unrestricted sensitive data in search indexes

---

## 182. Extensibility Constraints

Future modules must extend HR through:

- New subdomains
- New aggregates
- New event consumers
- New projections
- New policy providers
- New configuration
- New APIs

They must not fork or duplicate Employee Master ownership.

---

# PART XXVI — ARCHITECTURE VALIDATION

## 183. Architecture Validation Questions

Every HR feature must answer:

1. Which subdomain owns it?
2. Which aggregate controls the change?
3. What invariants apply?
4. Is the data effective-dated?
5. Which platform engine is reused?
6. Which events are emitted?
7. Which engines consume the event?
8. What authorization scope applies?
9. What audit data is required?
10. What failure recovery exists?
11. Is the feature multi-tenant?
12. Is the feature multi-company?
13. Is the feature multi-branch?
14. Is the feature multi-country ready?
15. Does it duplicate another engine’s ownership?

A feature should not proceed to implementation until these questions are answered.

---

## 184. Architecture Completion Criteria

The architecture is considered satisfied when:

- HR bounded context is explicit.
- Payroll ownership remains separate.
- Core aggregates are defined.
- Effective dating is mandatory.
- Event integration is defined.
- Cross-engine boundaries are protected.
- Multi-tenant isolation is enforced.
- Multi-company and multi-branch structures are supported.
- Multi-country extension points exist.
- Confidential HR security is recognized.
- Long-running processes use orchestration.
- Read models support enterprise use cases.
- Scalability considerations are documented.
- Failure handling is defined.
- Platform engines are reused.

---

## 185. Conclusion

The Human Resources Engine architecture establishes a complete enterprise foundation for workforce management.

It provides:

- A clear Human Capital Management bounded context
- Strong aggregate ownership
- Separation of person, employee, employment, and assignment
- Effective-dated workforce history
- Event-driven cross-engine integration
- Dedicated confidential case boundaries
- Multi-tenant isolation
- Multi-company and multi-branch support
- Multi-country extensibility
- Scalable read and write models
- Payroll separation
- Platform engine reuse

The architecture ensures that the Human Resources Engine remains the authoritative owner of people and employment while allowing Payroll, Finance, Expenses, Inventory, CRM, Sales, and POS to consume governed workforce information.

The next document in the required sequence is:

```text
DATABASE.md
```
