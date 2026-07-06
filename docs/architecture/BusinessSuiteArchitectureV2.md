# Business Suite Enterprise Platform Architecture

Version: 2.0

Status: Approved

Document Type: Master Architecture

---

# 1. Purpose

This document defines the enterprise architecture of Business Suite.

It serves as the authoritative reference for all platform services, business modules, development standards, integration patterns, and cross-cutting concerns.

Every engine, module, API, and user interface shall comply with this architecture.

---

# 2. Vision

Business Suite is a world-class enterprise SaaS platform for organizations of all sizes.

The platform is designed to provide a modular, cloud-native, API-first, event-driven foundation capable of supporting ERP, CRM, HR, Finance, Procurement, Inventory, Manufacturing, Healthcare, Education, Government, and industry-specific solutions.

The architecture emphasizes:

- Scalability
- Maintainability
- Security
- Extensibility
- Multi-tenancy
- Enterprise Governance

---

# 3. Architectural Principles

Business Suite follows these architectural principles.

## API First

Every platform capability shall be exposed through well-defined APIs.

---

## Event Driven

Business events are published through the Platform Event Bus.

---

## Modular

Every capability is implemented as an independent engine or business module.

---

## Cloud Native

The platform is designed for horizontal scalability and cloud deployment.

---

## Multi-Tenant

Every business operation is tenant-aware unless explicitly designated as a platform operation.

---

## Security by Design

Authentication, authorization, auditing, and governance are built into the platform.

---

## Configuration over Customization

Business behavior should be configured rather than modified in code wherever possible.

---

## Shared Platform Services

Cross-cutting concerns shall be implemented once and reused throughout the platform.

---

# 4. Technology Stack

## Frontend

- React
- TypeScript
- Tailwind CSS
- shadcn/ui
- React Router
- React Hook Form
- Zod

---

## Backend

- Supabase
- PostgreSQL
- Edge Functions
- Realtime
- Storage

---

## Platform Standards

- UUID Primary Keys
- Row Level Security
- Service Layer Architecture
- API-First Design
- Event-Driven Processing
- JSON-Based Integration
- Correlation IDs
- Immutable Audit History

---

# 5. Enterprise Architecture Goals

The architecture shall support:

- High Availability
- Horizontal Scalability
- Low Operational Complexity
- Enterprise Security
- Regulatory Compliance
- Observability
- Information Governance
- Cross-Module Integration
- Future AI Capabilities
- Mobile and Web Clients through shared APIs

---

# 6. Platform Foundation

The Business Suite Platform Foundation consists of the core services that every engine and business module depends on.

```text
Business Suite Platform Foundation

├── Platform Core
├── Platform Framework
├── Platform Event Bus
├── Authorization Engine
├── Information Governance Framework
├── Platform Observability Framework
└── Decision Framework
```

These services provide the foundation for identity, tenancy, authorization, user experience, communication, governance, monitoring, and rule evaluation.

---

# 7. Platform Core

Platform Core provides the base runtime services required by Business Suite.

Responsibilities include:

- Authentication
- Session Management
- Tenant Context
- User Management
- Organization Management
- Platform Configuration
- Environment Configuration

Platform Core does not own every platform capability.

Instead, it delegates specialized responsibilities to dedicated engines.

Examples:

```text
Authorization

↓

Authorization Engine
```

```text
Events

↓

Platform Event Bus
```

```text
Notifications

↓

Notification Engine
```

```text
Documents

↓

Document Management Engine
```

---

# 8. Platform Framework

Platform Framework provides the shared frontend and client-side foundation.

Responsibilities include:

- Design Language
- Layout System
- Navigation
- Shared UI Components
- Form Components
- Data Tables
- Modal System
- Notification Bell
- Command Palette
- Timeline Component
- History Viewer
- Rule Builder
- Permission Gate
- Client Services

Every business module must use Platform Framework components unless a justified exception is approved.

---

# 9. Platform Event Bus

The Platform Event Bus is the communication backbone of Business Suite.

Responsibilities include:

- Event Publishing
- Event Routing
- Event Subscriptions
- Event Deliveries
- Retry Handling
- Dead Letter Queue
- Event History
- Correlation Tracing

Business modules and platform services publish events to the Event Bus instead of directly calling other engines for event-driven operations.

Example:

```text
InvoiceCreated

↓

Platform Event Bus

├── Notification Engine
├── Search & Indexing Engine
├── Activity & Audit Engine
└── Reporting Engine
```

---

# 10. Authorization Engine

The Authorization Engine is the centralized authorization decision service.

Responsibilities include:

- Roles
- Permissions
- Actions
- Resources
- Policies
- Assignments
- Authorization Decisions
- Authorization History
- Authorization Simulation

Business modules must not implement authorization logic independently.

They must ask:

```text
Can this subject perform this action on this resource?
```

They must not ask:

```text
Does this user have this role?
```

This keeps business modules independent of the authorization model.

---

# 11. Information Governance Framework

The Information Governance Framework defines platform-wide rules for protecting and managing information.

Responsibilities include:

- Classification
- Evidence Level
- Search Visibility
- Data Masking
- Retention Policy
- Legal Hold
- Export Policy
- Audit Policy
- Encryption Policy

This framework ensures consistent governance across:

- Documents
- Reports
- Search Results
- Audit Records
- Events
- Notifications
- Workflows

---

# 12. Platform Observability Framework

The Platform Observability Framework provides operational visibility across Business Suite.

Responsibilities include:

- Metrics
- Logs
- Correlation Tracing
- Event Monitoring
- Search Monitoring
- Audit Monitoring
- Notification Monitoring
- Workflow Monitoring
- Performance Statistics
- Platform Health

Observability is not owned by one engine.

It is a cross-cutting platform capability supported by multiple engines.

---

# 13. Decision Framework

The Decision Framework provides reusable rule and decision evaluation capabilities.

Used by:

- Authorization Engine
- Workflow Engine
- Notification Engine
- Search & Indexing Engine
- Reporting Engine
- Future Business Rule Engine

Common capabilities include:

- Rule Evaluation
- Decision Tracing
- Explainable Outcomes
- Simulation
- Rule Versioning
- Decision History
- Performance Monitoring

The Authorization Engine is the first major consumer of this framework.

# 14. Platform Engines

## 14.1 Overview

The Platform Engines are reusable enterprise services that provide standardized capabilities across the entire Business Suite platform.

Rather than allowing every business module to implement its own infrastructure logic, the platform exposes these capabilities through dedicated engines that are shared by all modules.

This approach provides:

- Standardization
- Reusability
- Maintainability
- Scalability
- Security
- Governance
- Consistency
- Extensibility

Every business module consumes these engines through well-defined contracts.

---

## 14.2 Design Principles

Every platform engine follows the same architectural principles.

- Independent
- API First
- Event Driven
- Tenant Aware
- Observable
- Secure
- Versioned
- Extensible
- Loosely Coupled
- Highly Configurable

No engine should contain business-specific logic.

Business rules remain inside business modules while engines provide generic platform capabilities.

---

## 14.3 Engine Categories

The platform groups engines into logical categories.

### Platform Foundation Engines

Provide the core infrastructure of the platform.

Examples

- Platform Framework
- Authorization Engine
- Workflow Engine
- Event Bus
- Activity Engine
- Search Engine
- Reporting Engine

---

### Governance Engines

Provide compliance, governance, auditing, and information management.

Examples

- Information Governance Framework
- Audit Engine
- Retention Engine
- Document Management Engine
- Policy Engine

---

### Data Management Engines

Provide standardized data services.

Examples

- Reference Data Engine
- Master Data Engine
- Search & Indexing Engine
- Document Numbering Engine
- File Storage Engine

---

### Communication Engines

Provide communication services.

Examples

- Notification Engine
- Email Engine
- SMS Engine
- Push Notification Engine
- Real-time Messaging Engine

---

### Intelligence Engines

Provide analytics and decision support.

Examples

- Reporting Engine
- Dashboard Engine
- KPI Engine
- AI Engine
- Recommendation Engine

---

### Integration Engines

Provide connectivity with external systems.

Examples

- Integration Engine
- API Gateway
- Webhook Engine
- Import Engine
- Export Engine

---

## 14.4 Engine Characteristics

Every engine must satisfy the following characteristics.

### Stateless

Platform engines should remain stateless whenever possible.

Application state is stored within PostgreSQL or other managed services.

---

### Event Driven

Every significant action should publish platform events.

Example

```
InvoiceCreated

WorkflowApproved

CustomerUpdated

PaymentReceived

DocumentArchived
```

---

### Observable

Every engine emits telemetry including:

- Logs
- Metrics
- Traces
- Correlation IDs
- Execution Duration
- Errors
- Performance Statistics

---

### Multi-Tenant

Every engine must operate within tenant boundaries.

All operations enforce:

- Tenant Isolation
- Tenant Security
- Tenant Configuration
- Tenant Policies

---

### Secure

Every engine integrates with:

- Authorization Engine
- Policy Framework
- Information Governance Framework
- Audit Engine

---

### Extensible

Every engine exposes extension points.

Extension methods include:

- Events
- Webhooks
- Plugins
- Custom Policies
- Configuration
- API Extensions

---

## 14.5 Platform Engine Dependencies

Platform engines are designed to minimize direct dependencies.

Instead of tightly coupling engines together, communication occurs through:

- Platform Event Bus
- Shared Contracts
- Platform APIs
- Resource Contracts
- Authorization Contracts

This architecture reduces coupling while improving scalability and maintainability.

---

## 14.6 Standard Engine Architecture

Every engine follows a standardized internal architecture.

```
Presentation Layer
        │
        ▼
API Layer
        │
        ▼
Service Layer
        │
        ▼
Validation Layer
        │
        ▼
Business Rules
        │
        ▼
Repository Layer
        │
        ▼
PostgreSQL / Supabase
```

Cross-cutting concerns are applied throughout the request pipeline.

Examples include:

- Authorization
- Validation
- Logging
- Auditing
- Event Publishing
- Observability
- Correlation IDs
- Error Handling

---

## 14.7 Standard Engine Lifecycle

Every engine processes requests using the same lifecycle.

1. Receive Request

2. Authenticate User

3. Authorize Request

4. Validate Input

5. Resolve Tenant Context

6. Apply Business Policies

7. Execute Service Logic

8. Persist Changes

9. Publish Platform Events

10. Create Audit Record

11. Generate Observability Metrics

12. Return Standardized Response

This lifecycle ensures consistent behavior across every platform engine.

---

## 14.8 Engine Communication Model

Platform engines communicate using three primary mechanisms.

### Direct Service Calls

Used when an immediate response is required.

Examples

- Authorization Checks
- Reference Data Retrieval
- Document Number Generation

---

### Event Bus

Used for asynchronous communication.

Examples

- Invoice Created
- Customer Updated
- Payment Posted
- User Registered

---

### Scheduled Processing

Used for delayed or recurring operations.

Examples

- Notifications
- Reports
- Data Synchronization
- Cleanup Jobs
- Archive Operations

---

## 14.9 Engine Versioning

Platform engines evolve independently.

Versioning follows semantic versioning principles.

Example

```
v1

v1.1

v1.2

v2
```

Breaking changes require:

- New Contracts
- Migration Strategy
- Compatibility Layer
- Deprecation Policy

---

## 14.10 Engine Development Standards

Every new platform engine must adhere to the following standards.

- API First
- Event Driven
- UUID Primary Keys
- Row Level Security
- Service Layer Architecture
- Standard Response Contracts
- Standard Error Contracts
- Correlation IDs
- Observability
- Authorization Integration
- Audit Integration
- Information Governance Compliance
- Comprehensive Documentation
- Automated Testing
- Backward Compatibility

These standards ensure that every engine behaves consistently, integrates seamlessly with the platform ecosystem, and maintains enterprise-grade quality throughout its lifecycle.

---

# 16. Reference Data Engine

## 16.1 Overview

The Reference Data Engine is the centralized platform service responsible for managing all reusable reference and lookup data across the Business Suite platform.

Instead of allowing each business module to maintain its own lookup tables, all shared reference data is managed through a single standardized engine.

This ensures:

- Consistency
- Data Integrity
- Reusability
- Governance
- Centralized Management
- Multi-Tenant Support
- API Accessibility
- Version Control

All business modules consume reference data through the Reference Data Engine.

---

## 16.2 Objectives

The Reference Data Engine aims to:

- Eliminate duplicate lookup tables
- Standardize master reference values
- Improve data quality
- Simplify maintenance
- Support localization
- Enable tenant customization
- Provide centralized governance
- Improve reporting consistency

---

## 16.3 Types of Reference Data

The engine manages multiple categories of reference data.

### Global Reference Data

Shared across the entire platform.

Examples

- Countries
- Currencies
- Time Zones
- Languages
- Continents
- Measurement Units
- Tax Types

---

### Platform Reference Data

Shared across all tenants.

Examples

- Workflow Statuses
- Approval Actions
- Notification Types
- Document Types
- Activity Types
- Audit Categories
- Permission Categories

---

### Tenant Reference Data

Specific to an individual tenant.

Examples

- Departments
- Branches
- Job Titles
- Cost Centers
- Payment Terms
- Sales Territories
- Business Units

---

### Module Reference Data

Specific to individual business modules.

Examples

- Invoice Status
- Purchase Types
- Customer Categories
- Vendor Types
- Asset Categories
- Inventory Adjustment Reasons
- Leave Types

---

## 16.4 Core Concepts

The engine is built around the following concepts.

### Reference Category

A logical grouping of related reference values.

Examples

```
Country

Currency

Department

Tax Type

Customer Category

Gender

Payment Method
```

---

### Reference Value

A single selectable item within a category.

Example

Category

```
Currency
```

Values

```
UGX

USD

EUR

KES
```

---

### Reference Set

A collection of related categories managed together.

Example

```
Human Resources

Finance

Inventory

CRM
```

---

### Reference Hierarchy

Reference values may be hierarchical.

Example

```
Africa
    Uganda
        Kampala
            Central Division
```

Hierarchies support parent-child relationships and recursive navigation.

---

## 16.5 Standard Reference Attributes

Every reference value contains standardized metadata.

Examples include:

- UUID
- Code
- Name
- Description
- Category
- Parent Reference
- Display Order
- Status
- Effective Date
- Expiry Date
- Tenant Identifier
- Localization Metadata
- Version
- Created By
- Modified By
- Created Date
- Modified Date

These attributes provide consistency across all reference data.

---

## 16.6 Reference Data Lifecycle

Reference data follows a standardized lifecycle.

```
Draft

Pending Review

Approved

Active

Inactive

Deprecated

Archived
```

Lifecycle transitions are governed by platform policies and are fully auditable.

---

## 16.7 Engine Architecture

```
Business Module
        │
        ▼
Reference Data API
        │
        ▼
Reference Service
        │
        ▼
Validation Layer
        │
        ▼
Caching Layer
        │
        ▼
Repository Layer
        │
        ▼
PostgreSQL
```

The engine provides optimized read performance through intelligent caching while maintaining strong consistency for write operations.

---

## 16.8 Validation Rules

The Reference Data Engine enforces standardized validation.

Validation includes:

- Unique Codes
- Required Fields
- Duplicate Prevention
- Parent Validation
- Circular Hierarchy Detection
- Effective Date Validation
- Tenant Ownership Validation
- Authorization Checks

Invalid reference data cannot be published.

---

## 16.9 Localization Support

Reference values support localization to enable multilingual user experiences.

Localized attributes may include:

- Display Name
- Short Name
- Description
- Help Text

Localization is resolved automatically based on:

- User Preferences
- Tenant Settings
- Browser Locale
- Platform Configuration

This allows business modules to display culturally appropriate values without duplicating data.

---

## 16.10 Version Management

Reference categories and values are version controlled.

Versioning supports:

- Historical Tracking
- Change Comparison
- Rollback
- Effective Dating
- Future Scheduling

Breaking changes are managed through controlled publishing workflows to minimize disruption across dependent modules.

---

## 16.11 Reference Data Caching

Frequently accessed reference data is cached to improve performance.

Caching strategies include:

- In-Memory Cache
- Client Cache
- API Response Cache
- Edge Cache

Cache invalidation occurs automatically when reference data changes, ensuring consumers receive the latest approved values.

---

## 16.12 Reference Data Events

The Reference Data Engine publishes standardized events through the Platform Event Bus.

Examples include:

```
ReferenceCategoryCreated

ReferenceCategoryUpdated

ReferenceCategoryArchived

ReferenceValueCreated

ReferenceValueUpdated

ReferenceValueActivated

ReferenceValueDeactivated

ReferenceValueDeleted
```

Dependent services subscribe to these events to refresh caches, synchronize data, or trigger downstream processes.

---

## 16.13 Engine Integration

The Reference Data Engine integrates with multiple platform services.

| Engine                           | Purpose                         |
| -------------------------------- | ------------------------------- |
| Authorization Engine             | Permission enforcement          |
| Workflow Engine                  | Approval of reference changes   |
| Search & Indexing Engine         | Searchable lookup values        |
| Reporting Engine                 | Consistent reporting dimensions |
| Activity & Audit Engine          | Change history                  |
| Notification Engine              | Change notifications            |
| Platform Event Bus               | Event publication               |
| Information Governance Framework | Data governance and retention   |

---

## 16.14 Design Principles

The Reference Data Engine follows these architectural principles.

- Single Source of Truth
- Configuration over Hardcoding
- API First
- Event Driven
- Tenant Aware
- Version Controlled
- Fully Auditable
- Localizable
- Cache Optimized
- Secure by Default
- Highly Reusable
- Extensible

By centralizing all reusable lookup information within a dedicated platform service, the Reference Data Engine promotes consistency, reduces duplication, and provides a reliable foundation for every business module across the Business Suite platform.

---

# 17. Document Numbering Engine

## 17.1 Overview

The Document Numbering Engine is the centralized platform service responsible for generating unique, standardized, configurable, and auditable document numbers across the entire Business Suite platform.

Rather than allowing individual modules to generate their own identifiers, every business document obtains its official business number through this engine.

Examples include:

- Customer Numbers
- Supplier Numbers
- Invoice Numbers
- Quotation Numbers
- Sales Order Numbers
- Purchase Order Numbers
- Goods Receipt Numbers
- Payment Numbers
- Journal Numbers
- Asset Numbers
- Employee Numbers
- Project Numbers

The engine guarantees uniqueness, consistency, and traceability across the platform.

---

## 17.2 Objectives

The Document Numbering Engine aims to:

- Standardize document numbering
- Prevent duplicate document numbers
- Support configurable numbering schemes
- Enable tenant-specific formats
- Improve document traceability
- Support regulatory compliance
- Simplify auditing
- Eliminate manual numbering

---

## 17.3 Core Concepts

The engine is built around the following concepts.

### Number Series

A number series defines how document numbers are generated.

Examples

```
INV

PO

SO

GRN

PAY

EMP

CUS
```

---

### Number Format

A format defines the structure of generated numbers.

Examples

```
INV-2026-000001

PO-UG-000254

EMP-HR-001245

CUS-KLA-000012

AST-2026-001540
```

Formats may include:

- Prefixes
- Suffixes
- Date Components
- Fiscal Year
- Tenant Codes
- Branch Codes
- Department Codes
- Sequential Values
- Custom Tokens

---

### Number Sequence

A sequence maintains the current running value for a number series.

Each sequence tracks:

- Current Number
- Next Number
- Increment Step
- Reset Rules
- Last Generated Number
- Status

---

### Number Reservation

A document number may be reserved before a document is finalized.

Reserved numbers help support long-running business processes while preventing duplicate assignments.

Reservations can:

- Expire
- Be Released
- Be Confirmed
- Be Reused (if allowed by policy)

---

## 17.4 Number Generation Strategies

The engine supports multiple numbering strategies.

### Sequential

Numbers increase incrementally.

Example

```
INV-000001

INV-000002

INV-000003
```

---

### Date-Based

Numbers include date components.

Example

```
INV-20260701-00001

INV-20260701-00002
```

---

### Fiscal Year

Numbers reset according to fiscal periods.

Example

```
INV-2026-000001

INV-2026-000002
```

---

### Branch-Based

Independent sequences exist for each branch.

Example

```
KLA-INV-000001

MBR-INV-000001

GUL-INV-000001
```

---

### Tenant-Based

Each tenant maintains isolated numbering sequences.

This guarantees uniqueness within tenant boundaries while supporting multi-tenant scalability.

---

### Custom Strategy

Organizations may define custom numbering logic using configurable tokens and business rules without modifying application code.

---

## 17.5 Number Format Tokens

Formats are constructed using reusable tokens.

Examples include:

| Token           | Description             |
| --------------- | ----------------------- |
| `{PREFIX}`      | Document prefix         |
| `{YEAR}`        | Four-digit year         |
| `{YY}`          | Two-digit year          |
| `{MONTH}`       | Two-digit month         |
| `{DAY}`         | Two-digit day           |
| `{FISCAL_YEAR}` | Fiscal year             |
| `{TENANT}`      | Tenant code             |
| `{BRANCH}`      | Branch code             |
| `{DEPARTMENT}`  | Department code         |
| `{SEQUENCE}`    | Running sequence number |

Example format:

```
{PREFIX}-{YEAR}-{BRANCH}-{SEQUENCE}
```

Produces:

```
INV-2026-KLA-000245
```

---

## 17.6 Sequence Reset Rules

Number sequences may reset automatically.

Supported reset intervals include:

- Never
- Daily
- Monthly
- Quarterly
- Semi-Annually
- Annually
- Fiscal Year
- Custom Schedule

Reset operations are fully audited and controlled through platform policies.

---

## 17.7 Concurrency Management

The engine guarantees uniqueness under high transaction volumes.

Concurrency protection includes:

- Database Transactions
- Row-Level Locking
- Atomic Operations
- Retry Policies
- Conflict Detection
- Optimistic Concurrency Controls

These mechanisms ensure duplicate document numbers can never be generated, even under heavy concurrent workloads.

---

## 17.8 Number Lifecycle

Generated document numbers follow a managed lifecycle.

```
Available

Reserved

Assigned

Confirmed

Cancelled

Expired

Archived
```

Lifecycle events are recorded for complete traceability.

---

## 17.9 Engine Architecture

```
Business Module
        │
        ▼
Document Numbering API
        │
        ▼
Number Generation Service
        │
        ▼
Format Resolver
        │
        ▼
Sequence Manager
        │
        ▼
Validation Layer
        │
        ▼
Repository Layer
        │
        ▼
PostgreSQL
```

The engine ensures consistent generation regardless of which module requests a document number.

---

## 17.10 Validation Rules

The engine validates every numbering request.

Validation includes:

- Active Number Series
- Authorized Access
- Tenant Ownership
- Valid Format
- Sequence Availability
- Duplicate Prevention
- Reserved Number Validation
- Reset Policy Validation

Requests failing validation are rejected before a number is issued.

---

## 17.11 Number Reservations

Some business processes require document numbers before final approval.

Examples include:

- Draft Invoices
- Purchase Orders
- Contracts
- Project Registration

The engine supports configurable reservation policies including:

- Reservation Duration
- Automatic Expiration
- Confirmation on Save
- Release on Cancellation
- Reservation Auditing

---

## 17.12 Numbering Events

The Document Numbering Engine publishes standardized events to the Platform Event Bus.

Examples include:

```
NumberSeriesCreated

NumberSeriesUpdated

NumberSeriesActivated

NumberSeriesDeactivated

NumberReserved

NumberReleased

NumberGenerated

NumberAssigned

NumberSequenceReset
```

These events enable downstream services to synchronize state and maintain observability.

---

## 17.13 Engine Integration

The Document Numbering Engine integrates with multiple platform services.

| Engine                           | Purpose                          |
| -------------------------------- | -------------------------------- |
| Authorization Engine             | Access control                   |
| Workflow Engine                  | Approval-based number assignment |
| Activity & Audit Engine          | Number generation history        |
| Reporting Engine                 | Sequence reporting and analytics |
| Search & Indexing Engine         | Document lookup                  |
| Notification Engine              | Administrative alerts            |
| Platform Event Bus               | Event publication                |
| Information Governance Framework | Compliance and retention         |

---

## 17.14 Design Principles

The Document Numbering Engine follows these architectural principles.

- Centralized Number Generation
- Guaranteed Uniqueness
- Tenant Isolation
- Configuration over Customization
- API First
- Event Driven
- Fully Auditable
- High Availability
- Concurrency Safe
- Extensible
- Version Controlled
- Secure by Default

By centralizing document numbering within a dedicated platform engine, Business Suite ensures that every business document is uniquely identifiable, traceable, and compliant with organizational and regulatory requirements while remaining flexible enough to support diverse business numbering conventions.

---

# 18. Document Management Engine

## 18.1 Overview

The Document Management Engine is the centralized enterprise service responsible for storing, organizing, securing, versioning, retrieving, and governing all digital documents across the Business Suite platform.

Instead of allowing individual business modules to manage files independently, every document is managed through a single platform engine.

The engine supports all forms of enterprise content, including:

- Business Documents
- Contracts
- Images
- PDFs
- Office Documents
- Spreadsheets
- Presentations
- Scanned Records
- Supporting Attachments
- Certificates
- Media Files

This ensures consistency, compliance, and secure access throughout the platform.

---

## 18.2 Objectives

The Document Management Engine aims to:

- Centralize document storage
- Standardize document handling
- Support secure file management
- Enable document versioning
- Improve collaboration
- Ensure regulatory compliance
- Simplify document retrieval
- Protect enterprise information

---

## 18.3 Core Responsibilities

The engine is responsible for:

- Document Upload
- Document Download
- Secure Storage
- Document Versioning
- Metadata Management
- File Preview
- File Conversion
- Document Classification
- Document Linking
- Access Control
- Document Retention
- Archiving
- Document Search
- Audit Logging

---

## 18.4 Supported Document Types

The engine supports a wide range of document formats.

### Office Documents

- DOC
- DOCX
- XLS
- XLSX
- PPT
- PPTX

---

### Portable Documents

- PDF

---

### Images

- PNG
- JPG
- JPEG
- GIF
- SVG
- WebP

---

### Text Documents

- TXT
- CSV
- JSON
- XML

---

### Media Files

- MP3
- WAV
- MP4
- WEBM

---

### Archive Files

- ZIP
- TAR
- GZIP

Support for additional formats may be enabled through configurable platform extensions.

---

## 18.5 Document Classification

Every document is assigned a classification.

Examples include:

- Public
- Internal
- Confidential
- Restricted
- Highly Confidential

Document classification influences:

- Access Policies
- Encryption Requirements
- Sharing Rules
- Retention Policies
- Audit Requirements

---

## 18.6 Document Metadata

Each document contains standardized metadata.

Examples include:

- UUID
- File Name
- Original File Name
- Display Name
- File Extension
- MIME Type
- File Size
- Storage Location
- Checksum
- Version
- Classification
- Status
- Owner
- Tenant Identifier
- Related Entity
- Related Module
- Created By
- Modified By
- Created Date
- Modified Date

Metadata enables efficient governance and search capabilities.

---

## 18.7 Document Versioning

The engine supports complete document version management.

Versioning capabilities include:

- Major Versions
- Minor Versions
- Draft Versions
- Published Versions
- Version Comparison
- Version History
- Rollback
- Version Labels

Example

```
Contract

Version 1.0

Version 1.1

Version 2.0
```

Previous versions remain available according to retention policies.

---

## 18.8 Storage Architecture

Business Suite uses secure object storage for binary content while maintaining metadata within PostgreSQL.

```
Business Module
        │
        ▼
Document API
        │
        ▼
Document Service
        │
        ▼
Metadata Repository
        │
        ├───────────────► PostgreSQL
        │
        ▼
Storage Provider
        │
        ▼
Supabase Storage
```

This separation allows scalable storage while preserving transactional integrity for metadata.

---

## 18.9 Document Lifecycle

Every document follows a managed lifecycle.

```
Draft

Uploaded

Validated

Approved

Published

Archived

Deleted
```

Lifecycle transitions are governed by workflow policies and are fully audited.

---

## 18.10 Document Linking

Documents may be associated with one or more business entities.

Examples include:

- Customer
- Supplier
- Employee
- Invoice
- Purchase Order
- Sales Order
- Asset
- Project
- Workflow
- Support Ticket

A document may be referenced by multiple entities without duplicating the underlying file.

---

## 18.11 Access Control

Document access is enforced through the Authorization Engine.

Access permissions include:

- View
- Download
- Upload
- Replace
- Update Metadata
- Delete
- Restore
- Share
- Archive

Additional controls include:

- Tenant Isolation
- Role-Based Permissions
- Policy-Based Restrictions
- Time-Limited Access
- Document Classification Rules

Every access attempt is recorded for audit purposes.

---

## 18.12 File Validation

The engine validates every uploaded document before storage.

Validation includes:

- File Type
- MIME Type
- File Size
- File Extension
- Duplicate Detection
- Malware Scanning
- Checksum Verification
- Storage Quota Validation

Invalid or unsafe files are rejected before being persisted.

---

## 18.13 Search Integration

Every uploaded document is indexed for enterprise search.

Indexed attributes include:

- File Name
- Tags
- Metadata
- Related Entity
- Module
- Classification
- Version
- Owner

Where supported, document content may also be indexed to enable full-text search.

---

## 18.14 Retention and Archiving

Document retention is governed by the Information Governance Framework.

Retention capabilities include:

- Retention Policies
- Legal Hold
- Scheduled Archiving
- Secure Deletion
- Automatic Expiration
- Recovery Windows

Retention rules may differ by:

- Document Type
- Module
- Tenant
- Regulatory Requirements

---

## 18.15 Document Events

The Document Management Engine publishes standardized events to the Platform Event Bus.

Examples include:

```
DocumentUploaded

DocumentValidated

DocumentUpdated

DocumentVersionCreated

DocumentPublished

DocumentArchived

DocumentRestored

DocumentDeleted

DocumentDownloaded
```

These events enable downstream services such as auditing, notifications, workflows, and analytics.

---

## 18.16 Engine Integration

The Document Management Engine integrates with multiple platform services.

| Engine                           | Purpose                                   |
| -------------------------------- | ----------------------------------------- |
| Authorization Engine             | Secure document access                    |
| Workflow Engine                  | Approval of controlled documents          |
| Search & Indexing Engine         | Metadata and content indexing             |
| Notification Engine              | Document-related notifications            |
| Reporting Engine                 | Storage and usage analytics               |
| Activity & Audit Engine          | Complete document audit trail             |
| Platform Event Bus               | Event publication                         |
| Information Governance Framework | Classification, retention, and compliance |

---

## 18.17 Design Principles

The Document Management Engine follows these architectural principles.

- Centralized Document Repository
- Metadata-Driven Architecture
- API First
- Event Driven
- Tenant Aware
- Version Controlled
- Secure by Default
- Fully Auditable
- Scalable Storage
- Information Governance Compliant
- Extensible
- High Availability

By providing a unified document management capability, the Document Management Engine enables every Business Suite module to securely store, manage, retrieve, and govern enterprise content while maintaining consistency, compliance, and long-term scalability across the platform.

---

# 19. Notification Engine

## 19.1 Overview

The Notification Engine is the centralized communication service responsible for delivering system-generated notifications across all Business Suite modules.

Rather than allowing individual modules to implement their own messaging logic, every notification is created, managed, and delivered through a single enterprise engine.

The Notification Engine supports:

- In-App Notifications
- Email Notifications
- SMS Notifications
- Push Notifications
- Real-Time Notifications
- Webhook Notifications
- Future Communication Channels

This centralized approach ensures consistent communication, reliable delivery, configurable notification policies, and complete auditability.

---

## 19.2 Objectives

The Notification Engine aims to:

- Standardize system communications
- Decouple messaging from business modules
- Improve notification reliability
- Support multiple delivery channels
- Enable user notification preferences
- Reduce duplicated communication logic
- Improve operational visibility
- Support enterprise scalability

---

## 19.3 Core Responsibilities

The Notification Engine is responsible for:

- Notification Creation
- Template Management
- Channel Selection
- Recipient Resolution
- Message Personalization
- Scheduling
- Delivery
- Retry Processing
- Delivery Tracking
- User Preferences
- Notification History
- Notification Analytics

---

## 19.4 Notification Types

Business Suite supports multiple notification categories.

### Informational

Provides general updates.

Examples

- Report Generated
- Backup Completed
- Profile Updated
- Password Changed

---

### Action Required

Requests user intervention.

Examples

- Approval Required
- Task Assigned
- Review Required
- Document Pending Signature

---

### Transactional

Communicates business events.

Examples

- Invoice Created
- Payment Received
- Purchase Order Approved
- Customer Registered

---

### System

Communicates platform events.

Examples

- Maintenance Scheduled
- Service Restarted
- Security Alert
- Storage Limit Reached

---

### Reminder

Notifies users of upcoming deadlines.

Examples

- Contract Expiry
- License Renewal
- Meeting Reminder
- Workflow SLA Due

---

## 19.5 Notification Channels

Notifications may be delivered through one or more channels.

### In-App

Displayed within the Business Suite user interface.

Supports:

- Notification Center
- Badges
- Toast Messages
- Activity Feed

---

### Email

Supports:

- HTML Emails
- Plain Text Emails
- Attachments
- Rich Templates

---

### SMS

Designed for concise and time-sensitive communication.

Typical use cases include:

- One-Time Passwords (OTP)
- Security Alerts
- Payment Confirmations
- Critical Workflow Updates

---

### Push Notifications

Supports browser and mobile application notifications.

Examples include:

- Task Assigned
- Approval Completed
- New Message
- Urgent Alert

---

### Webhooks

Delivers notifications to external systems using standardized HTTP callbacks.

Typical integrations include:

- ERP Systems
- CRM Systems
- Third-Party Platforms
- Integration Services

---

## 19.6 Notification Templates

All notifications are generated using reusable templates.

Templates define:

- Subject
- Title
- Message Body
- Variables
- Supported Channels
- Language
- Formatting
- Priority

Example

```
Hello {{UserName}}

Your purchase order {{DocumentNumber}} has been approved.
```

Templates eliminate duplication while ensuring consistent messaging.

---

## 19.7 Notification Priorities

Notifications are prioritized to determine delivery urgency.

Supported priorities include:

```
Low

Normal

High

Critical
```

Priority influences:

- Delivery Timing
- Retry Policy
- Escalation
- User Presentation

---

## 19.8 Recipient Resolution

Recipients may be resolved dynamically.

Supported recipient types include:

- User
- Role
- Team
- Department
- Position
- Workflow Participant
- External Contact
- Distribution List

Recipient resolution occurs during notification processing.

---

## 19.9 User Notification Preferences

Each user may configure personal notification preferences.

Examples include:

- Preferred Channels
- Quiet Hours
- Language
- Time Zone
- Digest Frequency
- Notification Categories
- Device Preferences

Tenant administrators may define organization-wide defaults and mandatory notifications.

---

## 19.10 Notification Lifecycle

Every notification follows a standardized lifecycle.

```
Created

Queued

Scheduled

Sending

Delivered

Read

Failed

Cancelled

Archived
```

Lifecycle events are retained for auditing and reporting.

---

## 19.11 Delivery Processing

Notifications are delivered asynchronously.

Processing pipeline:

```
Business Event
        │
        ▼
Platform Event Bus
        │
        ▼
Notification Engine
        │
        ▼
Template Resolution
        │
        ▼
Recipient Resolution
        │
        ▼
Channel Selection
        │
        ▼
Delivery Queue
        │
        ▼
Delivery Provider
```

Asynchronous delivery prevents business operations from being blocked by communication delays.

---

## 19.12 Retry Policies

Failed deliveries are retried automatically.

Retry policies support:

- Configurable Retry Count
- Retry Intervals
- Exponential Backoff
- Permanent Failure Detection
- Dead Letter Queue Processing

Failures remain fully observable and auditable.

---

## 19.13 Delivery Providers

The Notification Engine abstracts communication providers behind standardized interfaces.

Examples include:

- Email Service Providers
- SMS Gateways
- Push Notification Services
- Webhook Endpoints

Providers can be replaced without affecting business modules.

---

## 19.14 Notification Events

The Notification Engine publishes standardized events to the Platform Event Bus.

Examples include:

```
NotificationCreated

NotificationQueued

NotificationSent

NotificationDelivered

NotificationRead

NotificationFailed

NotificationCancelled
```

These events support analytics, auditing, monitoring, and downstream automation.

---

## 19.15 Engine Integration

The Notification Engine integrates with multiple platform services.

| Engine                           | Purpose                             |
| -------------------------------- | ----------------------------------- |
| Workflow Engine                  | Workflow notifications              |
| Authorization Engine             | Permission-aware delivery           |
| Activity & Audit Engine          | Notification history                |
| Reporting Engine                 | Delivery analytics                  |
| Search & Indexing Engine         | Searchable notifications            |
| Platform Event Bus               | Event consumption and publication   |
| Information Governance Framework | Retention and compliance            |
| Platform Observability Framework | Delivery monitoring and diagnostics |

---

## 19.16 Design Principles

The Notification Engine follows these architectural principles.

- Centralized Communication
- API First
- Event Driven
- Multi-Channel Delivery
- Tenant Aware
- Template Driven
- Asynchronous Processing
- Reliable Delivery
- Fully Auditable
- Extensible
- Observable
- Secure by Default

By centralizing all communication within a dedicated Notification Engine, Business Suite provides a scalable, reliable, and consistent messaging infrastructure that ensures users receive timely, relevant, and secure notifications across every module and communication channel.

---

# 20. Reporting Engine

## 20.1 Overview

The Reporting Engine is the centralized enterprise service responsible for generating, managing, scheduling, securing, and distributing analytical and operational reports across the Business Suite platform.

Rather than allowing each business module to build its own reporting infrastructure, all reporting capabilities are provided through a standardized platform engine.

The Reporting Engine enables organizations to transform operational data into meaningful business insights while maintaining consistency, security, governance, and performance.

---

## 20.2 Objectives

The Reporting Engine aims to:

- Standardize reporting across the platform
- Eliminate duplicate reporting logic
- Support operational and analytical reporting
- Enable self-service reporting
- Improve decision making
- Ensure report security
- Simplify report distribution
- Support enterprise scalability

---

## 20.3 Core Responsibilities

The Reporting Engine is responsible for:

- Report Definition
- Report Generation
- Data Aggregation
- Visualization
- Report Scheduling
- Report Export
- Report Distribution
- Report Versioning
- Report Security
- Report Auditing
- Performance Monitoring

---

## 20.4 Report Categories

Business Suite supports multiple report categories.

### Operational Reports

Generated from day-to-day transactions.

Examples

- Sales Reports
- Purchase Reports
- Inventory Reports
- Customer Reports
- Employee Reports
- Financial Transactions

---

### Analytical Reports

Provide trends and business intelligence.

Examples

- Revenue Trends
- Customer Growth
- Sales Performance
- Inventory Turnover
- Employee Productivity
- Financial Analysis

---

### Compliance Reports

Support regulatory and governance requirements.

Examples

- Audit Reports
- Tax Reports
- Compliance Reports
- Security Reports
- Access Reports

---

### Executive Reports

Provide summarized management information.

Examples

- Executive Dashboard
- KPI Summary
- Department Performance
- Business Health Score
- Monthly Performance Review

---

### Ad Hoc Reports

User-defined reports created on demand using configurable filters and layouts.

---

## 20.5 Report Components

Every report consists of standardized components.

- Data Source
- Dataset
- Filters
- Parameters
- Calculations
- Grouping
- Sorting
- Charts
- Tables
- Totals
- Formatting
- Export Options

These components allow reports to remain reusable and configurable.

---

## 20.6 Data Sources

Reports may retrieve data from multiple platform sources.

Examples include:

- PostgreSQL Tables
- Database Views
- Materialized Views
- Search Indexes
- Aggregated Datasets
- External APIs
- Data Warehouses (Future)

Data access always respects authorization and tenant isolation.

---

## 20.7 Report Parameters

Reports support configurable runtime parameters.

Examples include:

- Date Range
- Branch
- Department
- Customer
- Supplier
- Employee
- Project
- Currency
- Workflow Status
- Document Number

Parameters allow users to generate highly targeted reports without modifying report definitions.

---

## 20.8 Report Output Formats

The Reporting Engine supports multiple output formats.

- PDF
- Excel (XLSX)
- CSV
- JSON
- HTML
- Print-Friendly View

Additional export formats may be added through platform extensions.

---

## 20.9 Visualization Components

Reports may include rich visualization elements.

Supported components include:

- Tables
- Bar Charts
- Line Charts
- Pie Charts
- Area Charts
- KPI Cards
- Gauges
- Trend Indicators
- Heat Maps
- Pivot Tables

Visualization standards are shared across the platform to provide a consistent user experience.

---

## 20.10 Report Scheduling

Reports may be generated automatically according to configurable schedules.

Supported schedules include:

- Hourly
- Daily
- Weekly
- Monthly
- Quarterly
- Annually
- Custom Cron Expressions

Scheduled reports may be delivered automatically through the Notification Engine.

---

## 20.11 Report Security

Every report is protected through the Authorization Engine.

Security controls include:

- Role-Based Access
- Resource Permissions
- Tenant Isolation
- Branch Restrictions
- Department Restrictions
- Data-Level Security
- Row Level Security (RLS)

Users only see data they are authorized to access.

---

## 20.12 Report Execution Architecture

```
Business Module
        │
        ▼
Reporting API
        │
        ▼
Report Service
        │
        ▼
Parameter Validation
        │
        ▼
Authorization Check
        │
        ▼
Query Builder
        │
        ▼
Data Source
        │
        ▼
Formatter
        │
        ▼
Output Generator
```

The reporting pipeline separates data retrieval, formatting, and output generation to maximize maintainability and extensibility.

---

## 20.13 Report Lifecycle

Every report follows a standardized lifecycle.

```
Draft

Published

Scheduled

Executing

Completed

Failed

Archived
```

Report executions are tracked independently from report definitions to provide complete operational history.

---

## 20.14 Performance Optimization

The Reporting Engine incorporates multiple optimization strategies.

Examples include:

- Query Optimization
- Pagination
- Materialized Views
- Cached Datasets
- Incremental Aggregation
- Background Processing
- Parallel Execution
- Lazy Loading

These strategies enable the platform to support large datasets while maintaining acceptable response times.

---

## 20.15 Report Events

The Reporting Engine publishes standardized events to the Platform Event Bus.

Examples include:

```
ReportCreated

ReportPublished

ReportScheduled

ReportGenerationStarted

ReportGenerated

ReportExported

ReportDelivered

ReportFailed
```

These events enable auditing, monitoring, workflow automation, and notification delivery.

---

## 20.16 Engine Integration

The Reporting Engine integrates with multiple platform services.

| Engine                           | Purpose                   |
| -------------------------------- | ------------------------- |
| Authorization Engine             | Report access control     |
| Search & Indexing Engine         | Searchable report catalog |
| Notification Engine              | Scheduled report delivery |
| Activity & Audit Engine          | Report execution history  |
| Workflow Engine                  | Workflow-based reporting  |
| Platform Event Bus               | Event publication         |
| Information Governance Framework | Retention and compliance  |
| Platform Observability Framework | Performance monitoring    |

---

## 20.17 Design Principles

The Reporting Engine follows these architectural principles.

- API First
- Event Driven
- Tenant Aware
- Secure by Default
- Data-Driven
- Configuration over Customization
- Fully Auditable
- High Performance
- Scalable
- Extensible
- Observable
- Standards-Based

By providing a centralized reporting capability, the Reporting Engine enables every Business Suite module to produce secure, accurate, and actionable information while ensuring consistent reporting standards, governance, and enterprise-scale performance across the entire platform.

---

# 21. Search & Indexing Engine

## 21.1 Overview

The Search & Indexing Engine is the centralized enterprise service responsible for providing fast, intelligent, secure, and consistent search capabilities across the entire Business Suite platform.

Instead of every module implementing its own search functionality, all searchable data is indexed and queried through a unified platform engine.

The engine enables users to locate information quickly regardless of the originating business module while ensuring authorization, tenant isolation, and information governance are consistently enforced.

---

## 21.2 Objectives

The Search & Indexing Engine aims to:

- Provide enterprise-wide search
- Deliver fast query performance
- Support full-text search
- Enable cross-module discovery
- Improve user productivity
- Standardize indexing
- Support secure data access
- Enable intelligent search experiences

---

## 21.3 Core Responsibilities

The Search & Indexing Engine is responsible for:

- Data Indexing
- Search Query Processing
- Full-Text Search
- Metadata Indexing
- Relevance Scoring
- Search Suggestions
- Auto Complete
- Faceted Search
- Result Ranking
- Search Analytics
- Index Maintenance
- Search Security

---

## 21.4 Search Scope

The engine provides unified search across all Business Suite modules.

Examples include:

- Customers
- Suppliers
- Employees
- Products
- Inventory
- Invoices
- Purchase Orders
- Sales Orders
- Projects
- Assets
- Workflows
- Reports
- Documents
- Activities
- Notifications

Each module contributes searchable resources through standardized indexing contracts.

---

## 21.5 Searchable Content

The engine indexes multiple categories of information.

### Structured Data

Examples

- Customer Records
- Invoice Data
- Employee Profiles
- Product Catalogs
- Financial Transactions

---

### Unstructured Data

Examples

- Documents
- PDF Files
- Notes
- Comments
- Descriptions
- Attachments

---

### Metadata

Examples

- Tags
- Categories
- Status
- Owners
- Departments
- Branches
- Workflow States

---

### Activity Data

Examples

- Audit Logs
- Workflow History
- User Activities
- Notifications

---

## 21.6 Search Features

The Search & Indexing Engine provides rich search capabilities.

### Keyword Search

Supports standard keyword matching across indexed content.

---

### Full-Text Search

Allows searching within document contents, descriptions, notes, and other textual fields.

---

### Auto Complete

Provides real-time search suggestions as users type.

---

### Search Suggestions

Suggests relevant entities, frequently searched terms, and recent searches.

---

### Faceted Search

Users may refine results using filters such as:

- Module
- Status
- Branch
- Department
- Date
- Owner
- Category
- Tags

---

### Advanced Search

Supports complex queries using multiple conditions and operators.

Examples include:

- Exact Match
- Partial Match
- Date Range
- Numeric Range
- Multiple Filters
- Boolean Logic

---

## 21.7 Search Architecture

```
Business Module
        │
        ▼
Platform Event Bus
        │
        ▼
Indexing Service
        │
        ▼
Search Index
        │
        ▼
Search API
        │
        ▼
Business Suite UI
```

Business modules publish events whenever searchable data changes, allowing indexes to remain synchronized with operational data.

---

## 21.8 Indexing Process

Indexing follows an event-driven pipeline.

```
Entity Created

Entity Updated

Entity Deleted
        │
        ▼
Platform Event Bus
        │
        ▼
Index Builder
        │
        ▼
Search Index Updated
```

This architecture minimizes coupling while ensuring near real-time search accuracy.

---

## 21.9 Search Security

Search results always respect platform security.

Security controls include:

- Authentication
- Authorization
- Row Level Security (RLS)
- Tenant Isolation
- Resource Permissions
- Data Classification Policies

Users cannot discover or retrieve information they are not authorized to access.

---

## 21.10 Search Ranking

Search results are ranked using multiple relevance factors.

Examples include:

- Keyword Relevance
- Exact Match Priority
- Field Weighting
- Entity Popularity
- Recent Activity
- Creation Date
- User Context
- Business Priority

Ranking algorithms may evolve independently without affecting consuming modules.

---

## 21.11 Search Index Lifecycle

Indexed resources follow a standardized lifecycle.

```
Queued

Indexing

Indexed

Updated

Reindexed

Archived

Removed
```

Index maintenance operations are fully automated and observable.

---

## 21.12 Search Analytics

The engine collects analytics to improve search quality.

Examples include:

- Search Frequency
- Popular Queries
- Zero-Result Searches
- Average Response Time
- Click-Through Rate
- Search Success Rate
- Index Size
- Index Freshness

Analytics support continuous optimization of the search experience.

---

## 21.13 Performance Optimization

The Search & Indexing Engine incorporates several optimization techniques.

Examples include:

- Incremental Indexing
- Background Indexing
- Parallel Processing
- Cached Results
- Pagination
- Result Highlighting
- Query Optimization
- Lazy Loading

These optimizations ensure consistent performance as platform data grows.

---

## 21.14 Search Events

The Search & Indexing Engine publishes standardized events to the Platform Event Bus.

Examples include:

```
IndexCreated

IndexUpdated

IndexRebuilt

EntityIndexed

EntityRemovedFromIndex

SearchExecuted

SearchCompleted

SearchFailed
```

These events support monitoring, diagnostics, auditing, and platform analytics.

---

## 21.15 Engine Integration

The Search & Indexing Engine integrates with multiple platform services.

| Engine                           | Purpose                                 |
| -------------------------------- | --------------------------------------- |
| Authorization Engine             | Secure search results                   |
| Document Management Engine       | Document indexing                       |
| Reporting Engine                 | Searchable report catalog               |
| Workflow Engine                  | Workflow discovery                      |
| Activity & Audit Engine          | Search history and diagnostics          |
| Platform Event Bus               | Index synchronization                   |
| Information Governance Framework | Classification and retention compliance |
| Platform Observability Framework | Search performance monitoring           |

---

## 21.16 Design Principles

The Search & Indexing Engine follows these architectural principles.

- Enterprise-Wide Search
- API First
- Event Driven
- Tenant Aware
- Secure by Default
- Fully Auditable
- Highly Performant
- Scalable
- Extensible
- Observable
- Near Real-Time Indexing
- Standards-Based

By centralizing all search capabilities within a dedicated Search & Indexing Engine, Business Suite delivers a fast, secure, and intelligent search experience that enables users to efficiently discover information across every module while maintaining consistent governance, authorization, and enterprise-scale performance.

---
