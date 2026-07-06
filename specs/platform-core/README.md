# 1. Introduction

## 1.1 Purpose

Platform Core is the foundational layer of the Business Suite platform.

It provides the shared platform infrastructure required by every platform engine and business module.

Rather than implementing specialized business capabilities, Platform Core establishes the common services, standards, and runtime context upon which the entire Business Suite ecosystem operates.

Platform Core is responsible for providing:

- Authentication
- Tenant Management
- Workspace Management
- User Identity
- Organization Management
- Module Registry
- Configuration Management
- Feature Management
- Shared Entity Standards
- Request Lifecycle
- Service Discovery
- Platform Administration

Specialized platform capabilities such as authorization, workflows, notifications, document management, reporting, search, auditing, and document numbering are implemented through dedicated Platform Engines.

Every Platform Engine and every Business Module depends on Platform Core.

Platform Core provides a secure, scalable, configurable, and extensible SaaS foundation that enables Business Suite to grow without architectural redesign.

---

## 1.2 Vision

Business Suite is a modern cloud-native, API-first, multi-tenant enterprise SaaS platform designed to help Small and Medium Enterprises (SMEs) manage their entire business through integrated modular applications.

Platform Core provides the foundational services that enable organizations to:

- Register online
- Create secure workspaces
- Manage organizational structures
- Invite and manage users
- Configure company settings
- Activate licensed modules
- Manage subscriptions
- Enforce tenant isolation
- Scale seamlessly as the platform evolves

Platform Core establishes the operational foundation upon which all Platform Engines and Business Modules execute.

---

## 1.3 Objectives

Platform Core has the following objectives.

### Platform

- Provide secure multi-tenant architecture.
- Support unlimited tenants.
- Ensure complete tenant isolation.
- Provide centralized authentication.
- Provide centralized identity management.
- Support multiple workspaces per user.
- Provide centralized configuration management.
- Establish shared platform standards.

### Business

- Allow businesses to self-register.
- Allow businesses to invite team members.
- Allow businesses to manage organizational information.
- Support branch management.
- Support subscription management.
- Support configurable platform packages.
- Provide a reusable platform foundation for all business modules.

### Technical

- API-first architecture.
- Event-driven architecture.
- Cloud-native deployment.
- Feature-based modular architecture.
- Service Layer Architecture.
- React + TypeScript frontend.
- Supabase backend.
- PostgreSQL database.
- Row Level Security (RLS).
- UUID-based entity identities.
- Future mobile support without backend redesign.

---

## 1.4 Platform Core Responsibilities

Platform Core owns the foundational infrastructure of the Business Suite platform.

Its responsibilities include:

- Authentication
- User Identity
- Tenant Management
- Workspace Management
- Organization Management
- Module Registry
- Configuration Registry
- Feature Management
- Shared Entity Standards
- Request Lifecycle
- Service Discovery
- Health Monitoring
- Platform Administration

Platform Core does **not** implement specialized business capabilities.

Instead, it integrates with dedicated Platform Engines, each responsible for a specific enterprise capability.

### Platform Engines

Platform Core integrates with:

- Authorization Engine
- Workflow Engine
- Platform Event Bus
- Reference Data Engine
- Document Numbering Engine
- Document Management Engine
- Notification Engine
- Reporting Engine
- Search & Indexing Engine
- Platform Activity & Audit Engine

Each engine exposes standardized APIs, publishes platform events, and follows the shared contracts defined by Platform Core.

This separation of responsibilities keeps Platform Core lightweight, reusable, and focused on providing the enterprise foundation required by the entire Business Suite platform.

# 2. Scope

Platform Core provides the foundational capabilities required by every Platform Engine and Business Module within Business Suite.

It establishes the shared infrastructure, runtime context, identity services, tenant management, configuration, and administration that enable the rest of the platform to operate consistently.

Platform Core intentionally delegates specialized enterprise capabilities to dedicated Platform Engines.

---

## Public Platform

Platform Core provides the public-facing services of Business Suite.

These include:

- Landing Page
- Product Information
- Features
- Pricing
- Contact
- Business Registration
- Login
- Forgot Password
- Privacy Policy
- Terms of Service

The public platform serves as the entry point for organizations onboarding to Business Suite.

---

## Authentication

Platform Core owns platform authentication and user identity.

Responsibilities include:

- User Authentication
- Identity Management
- Login
- Logout
- Password Recovery
- Email Verification
- Multi-Factor Authentication (MFA)
- OAuth Provider Integration
- Session Management
- Active Workspace Resolution

Supported authentication providers include:

- Email & Password
- Google OAuth
- Microsoft OAuth
- Magic Link Authentication
- Multi-Factor Authentication

Authentication providers are configurable through Platform Configuration.

Platform Core delegates authorization decisions to the Authorization Engine.

---

## Tenant Management

Platform Core manages the lifecycle of tenants within Business Suite.

Responsibilities include:

- Tenant Registration
- Tenant Activation
- Tenant Status
- Workspace Creation
- Organization Management
- Branch Management
- Tenant Configuration
- Tenant Branding

Platform Core establishes tenant boundaries that are enforced throughout the platform.

---

## User Management

Platform Core provides centralized identity management.

Responsibilities include:

- Platform Users
- User Profiles
- Workspace Memberships
- User Invitations
- Password Management
- Account Status
- Default Workspace
- Session Management

A Platform User exists only once and may belong to multiple workspaces.

---

## Module Management

Platform Core maintains the Platform Module Registry.

Responsibilities include:

- Module Registration
- Module Discovery
- Module Installation
- Module Activation
- Module Configuration
- Module Dependencies
- Feature Availability
- Module Version Information

Business functionality remains within the individual business modules.

---

## Configuration Management

Platform Core provides centralized configuration management.

Configuration categories include:

- Platform Settings
- Tenant Settings
- Environment Settings
- Feature Flags
- Branding
- Authentication Providers
- Localization
- Regional Settings

Individual Platform Engines may expose additional configuration specific to their own responsibilities.

---

## Subscription Management

Platform Core manages tenant subscriptions.

Responsibilities include:

- Trial Management
- Package Management
- Subscription Lifecycle
- Subscription Status
- Package Assignment
- Licensing
- Module Availability
- Usage Limits

Online payment processing is outside the scope of Version 1.

The architecture should support future billing integration without requiring architectural redesign.

---

## Platform Administration

Platform Core provides centralized administration of the Business Suite platform.

Responsibilities include:

- Tenant Administration
- Organization Administration
- User Administration
- Package Administration
- Module Administration
- Platform Configuration
- Feature Management
- Authentication Providers
- Branding
- Environment Configuration
- Health Monitoring

Platform administration operates independently of tenant business operations.

---

## Platform Engine Integration

Platform Core integrates with the following Platform Engines.

### Authorization Engine

Provides:

- Roles
- Permissions
- Policies
- Authorization Decisions
- Resource Security

---

### Workflow Engine

Provides:

- Approval Workflows
- Business Processes
- Task Routing
- Escalations
- Workflow Execution

---

### Platform Event Bus

Provides:

- Event Publishing
- Event Subscription
- Cross-Module Communication
- Asynchronous Processing

---

### Reference Data Engine

Provides:

- Lookup Data
- Shared Reference Values
- Code Lists
- Localized Reference Data

---

### Document Numbering Engine

Provides:

- Business Document Numbers
- Number Series
- Sequence Management

---

### Document Management Engine

Provides:

- File Storage
- Attachments
- Document Versioning
- Metadata
- Secure Document Access

---

### Notification Engine

Provides:

- Email Notifications
- SMS Notifications
- Push Notifications
- In-App Notifications
- Notification Templates
- Delivery Tracking

---

### Reporting Engine

Provides:

- Operational Reports
- Analytical Reports
- Scheduled Reports
- Dashboards
- Data Exports

---

### Search & Indexing Engine

Provides:

- Enterprise Search
- Full-Text Search
- Search Suggestions
- Cross-Module Search
- Metadata Indexing

---

### Platform Activity & Audit Engine

Provides:

- Audit Logging
- Activity Tracking
- Change History
- Compliance Records
- Security Auditing

---

## Out of Scope

The following capabilities are intentionally implemented by dedicated Platform Engines rather than Platform Core.

- Authorization Logic
- Workflow Execution
- Notification Delivery
- Document Storage
- Document Number Generation
- Report Generation
- Enterprise Search
- Audit Processing
- Reference Data Management

Platform Core consumes these capabilities through standardized contracts, APIs, and platform events rather than implementing them directly.

# 3. Core Concepts

Platform Core is built around several foundational concepts that establish the identity, tenancy, and execution context for the entire Business Suite platform.

These concepts are shared by every Platform Engine and every Business Module.

---

## User

A User represents a person who can authenticate and access the Business Suite platform.

A User has exactly one Platform Identity.

The User authenticates once and may belong to multiple workspaces.

A User is never duplicated across the platform.

Examples include:

- Business Owner
- Accountant
- Sales Officer
- Procurement Officer
- HR Officer
- Auditor
- Consultant
- System Administrator

Platform Core owns:

- Identity
- Authentication
- User Profile
- Workspace Membership

Platform Core does not own authorization.

Authorization decisions are delegated to the Authorization Engine.

---

## Tenant

A Tenant represents an independent organization registered on the Business Suite platform.

Every Tenant owns its own:

- Organization
- Branches
- Users
- Business Data
- Configuration
- Subscriptions
- Enabled Modules
- Platform Engine Data

Each Tenant operates independently and is completely isolated from every other Tenant.

Tenant isolation is enforced throughout the platform using:

- Tenant Context
- Row Level Security (RLS)
- Authorization Engine
- Service Layer
- Platform Event Bus

---

## Workspace

A Workspace is the operational environment through which users interact with a Tenant.

From a user's perspective, the Workspace represents the company they are currently working in.

Examples:

- ABC Limited
- Smart Click Uganda
- XYZ Holdings

Changing the active Workspace changes the execution context of the application, including:

- Active Tenant
- Active Organization
- Active Branch
- Available Modules
- User Permissions
- Business Data
- Dashboards
- Reports

Every request executed within Business Suite is scoped to the Active Workspace.

---

## Tenant Membership

A Tenant Membership establishes the relationship between a User and a Workspace.

This relationship allows a single User to participate in multiple organizations without creating multiple accounts.

Each membership contains:

- Workspace
- Membership Status
- Assigned Roles
- Assigned Permission Policies
- Default Workspace
- Date Joined

Example

| Workspace    | Role            |
| ------------ | --------------- |
| ABC Ltd      | Administrator   |
| XYZ Holdings | Finance Manager |
| Smart Click  | Auditor         |

Platform authentication is global.

Authorization is always evaluated within the current Workspace by the Authorization Engine.

---

## Organization

An Organization represents the legal business entity associated with a Tenant.

An Organization manages:

- Company Information
- Registration Details
- Branding
- Financial Settings
- Regional Settings
- Contact Information

An Organization may operate one or more Branches.

---

## Branch

A Branch represents a physical or operational location within an Organization.

Examples include:

- Head Office
- Kampala Branch
- Mbarara Branch
- Nairobi Office

Branches are shared across all Business Modules.

Access to Branches is governed by the Authorization Engine.

---

## Module

A Module is an independently deployable business capability within Business Suite.

Examples include:

- CRM
- Sales
- Procurement
- Inventory
- Finance
- Human Resources
- Payroll
- Assets
- Projects
- Help Desk

Platform Core maintains the Module Registry.

Business logic remains within the individual modules.

---

## Platform Engine

A Platform Engine is a reusable enterprise service that provides standardized capabilities across the platform.

Examples include:

- Authorization Engine
- Workflow Engine
- Notification Engine
- Reporting Engine
- Search & Indexing Engine
- Document Management Engine
- Document Numbering Engine
- Reference Data Engine
- Platform Activity & Audit Engine
- Platform Event Bus

Platform Engines expose reusable services consumed by Business Modules.

Platform Core provides the infrastructure required for these engines to operate consistently.

---

## Active Context

Every authenticated request executes within an Active Context.

The Active Context consists of:

- Authenticated User
- Active Tenant
- Active Workspace
- Active Organization
- Active Branch
- Enabled Modules
- Subscription
- Feature Flags
- User Permissions

Platform Core resolves the Active Context before processing any request.

This context is then used by Platform Engines and Business Modules to enforce security, tenancy, configuration, and business rules consistently across the platform.

# 4. User Types

Business Suite supports multiple categories of users, each operating within a specific platform context and security boundary.

Platform Core manages user identities, authentication, and workspace memberships.

User permissions, roles, policies, and authorization decisions are managed by the Authorization Engine.

---

## 4.1 Public Visitor

A Public Visitor is any person accessing the Business Suite public website without authentication.

Capabilities include:

- View the Landing Page
- View Product Information
- View Platform Features
- View Pricing and Packages
- Contact Sales
- Register a Business
- Sign In
- Request Password Reset

Public Visitors have no authenticated identity and cannot access tenant resources.

---

## 4.2 Platform User

A Platform User is an authenticated individual within Business Suite.

Every Platform User has:

- One Platform Identity
- One Verified Email Address
- One Authentication Account
- One User Profile

A Platform User may belong to:

- One Workspace
- Multiple Workspaces

The same Platform User should never be duplicated simply because they belong to multiple organizations.

Platform Core manages:

- Identity
- Authentication
- Profile
- Workspace Memberships

Authorization is performed by the Authorization Engine after successful authentication.

---

## 4.3 Workspace Owner

The Workspace Owner is automatically created during tenant registration.

The Workspace Owner is the primary administrator of the workspace.

Typical responsibilities include:

- Managing Company Information
- Managing Branches
- Inviting Users
- Managing Workspace Configuration
- Managing Subscription Information
- Activating Licensed Modules
- Delegating Administrative Responsibilities

Each workspace has exactly one Workspace Owner.

Ownership may be transferred to another Platform User.

Permissions available to the Workspace Owner are determined by the Authorization Engine.

---

## 4.4 Workspace Administrator

Workspace Administrators assist the Workspace Owner in managing day-to-day workspace operations.

Typical responsibilities include:

- Managing Users
- Managing Branches
- Managing Organizational Settings
- Managing Workspace Configuration
- Assigning Operational Responsibilities

Workspace Administrators cannot perform platform-level administration.

Their available permissions are determined by the Authorization Engine.

---

## 4.5 Workspace User

Workspace Users perform normal business operations within one or more business modules.

Examples include:

- Accountant
- Cashier
- Sales Officer
- Procurement Officer
- HR Officer
- Inventory Officer
- Project Manager
- Customer Support Officer

Workspace Users only have access to the modules, resources, and actions explicitly granted through the Authorization Engine.

The same Platform User may perform different roles across different workspaces.

---

## 4.6 Super Administrator

The Super Administrator operates at the Business Suite platform level.

Unlike Workspace Users, the Super Administrator is not part of tenant business operations.

The Super Administrator manages the Business Suite platform itself.

Responsibilities include:

- Platform Configuration
- Tenant Administration
- Package Administration
- Subscription Administration
- Module Registry
- Platform Branding
- Authentication Providers
- Feature Flags
- Platform Monitoring
- Platform Maintenance

The Super Administrator does not participate in tenant workflows or day-to-day business operations except where platform support is required.

---

## 4.7 System Services

System Services are non-human identities used internally by the platform.

Examples include:

- Platform Event Bus
- Scheduled Jobs
- Background Workers
- Edge Functions
- Integration Services
- Notification Processors
- Report Generation Services

System Services authenticate using service identities rather than interactive user accounts.

Access to platform resources is controlled through the Authorization Engine using service-specific permissions and policies.

---

## 4.8 User Identity Principles

Business Suite follows these identity principles.

### Single Identity

A person has only one Platform Identity regardless of the number of organizations they belong to.

---

### Multiple Workspace Memberships

A Platform User may belong to multiple independent workspaces without requiring additional accounts.

---

### Authentication Before Authorization

Authentication is always performed by Platform Core.

Authorization is always performed by the Authorization Engine.

---

### Tenant Isolation

Every authenticated session executes within the currently selected Active Workspace.

Tenant boundaries are enforced across all Platform Engines and Business Modules.

---

### Least Privilege

Users receive only the permissions required to perform their responsibilities.

Permission evaluation is delegated entirely to the Authorization Engine.

# 5. Registration & Onboarding

Business Suite supports a fully self-service registration and onboarding process.

Every successfully registered business becomes a Tenant within the Business Suite platform.

The registration process establishes the foundational platform resources required for a new organization to begin using Business Suite.

Platform Core orchestrates the onboarding process while leveraging Platform Engines for specialized capabilities such as notifications, auditing, authorization, and event processing.

---

## Registration Workflow

```text
Landing Page
        │
        ▼
Select Subscription Package
        │
        ▼
Business Registration
        │
        ▼
Create Platform User
        │
        ▼
Verify Email Address
        │
        ▼
Create Tenant
        │
        ▼
Create Workspace
        │
        ▼
Create Organization
        │
        ▼
Assign Workspace Owner
        │
        ▼
Initialize Platform Configuration
        │
        ▼
Activate Trial Subscription
        │
        ▼
Publish Platform Events
        │
        ▼
Send Welcome Notification
        │
        ▼
Begin Workspace Setup
```

The onboarding workflow is executed by Platform Core and coordinated through the Platform Event Bus.

---

## Business Registration

During registration, Platform Core collects the information required to establish a new Tenant.

### Organization Information

- Business Name
- Trading Name (Optional)
- Industry
- Country
- Currency
- Time Zone
- Language
- Business Email
- Business Phone Number

---

### Workspace Owner

The first Platform User created during registration becomes the Workspace Owner.

Required information includes:

- First Name
- Last Name
- Email Address
- Password

The Workspace Owner receives administrative access after successful onboarding.

Role assignment is performed through the Authorization Engine.

---

## Email Verification

Platform Users must verify their email address before accessing Business Suite.

Platform Core manages the verification process.

Supported capabilities include:

- Verification Email
- Verification Link
- Verification Expiry
- Resend Verification
- Email Verification Status

Email delivery is delegated to the Notification Engine.

---

## Workspace Initialization

After successful registration, Platform Core automatically provisions the Workspace.

Initialization includes:

- Tenant Creation
- Workspace Creation
- Organization Creation
- Default Branch Creation
- Default Configuration
- Default Language
- Default Currency
- Trial Subscription
- Default Module Registration

Platform Core initializes only the platform foundation.

Business modules are activated according to the assigned subscription package.

---

## Default Workspace Resources

The following resources are automatically created during onboarding.

### Organization

The registered business entity.

---

### Default Branch

The organization's primary operating branch.

---

### Workspace Owner

The initial administrator of the Workspace.

---

### Subscription

The default Trial or Licensed Subscription.

---

### Platform Configuration

Default configuration values inherited from Platform Settings.

---

### Module Registry

Available modules based on the active subscription.

---

## Platform Events

Platform Core publishes standardized events during onboarding.

Examples include:

```text
PlatformUserCreated

EmailVerificationRequested

TenantCreated

WorkspaceCreated

OrganizationCreated

WorkspaceOwnerAssigned

TrialActivated

WorkspaceInitialized
```

These events are published through the Platform Event Bus and may be consumed by other Platform Engines.

Examples include:

- Notification Engine sending welcome emails.
- Activity & Audit Engine recording onboarding activities.
- Reporting Engine updating platform statistics.
- Search & Indexing Engine indexing new tenant information.

---

## Onboarding Notifications

Platform Core requests notifications through the Notification Engine.

Typical notifications include:

- Welcome Email
- Email Verification
- Workspace Ready
- Trial Activated
- Trial Reminder
- Subscription Activated

Notification templates are managed exclusively by the Notification Engine.

---

## Default Authorization

During onboarding, Platform Core requests the Authorization Engine to provision the default security model.

This includes:

- Workspace Owner Role
- Default Administrative Permissions
- Initial Role Assignments
- Default Permission Policies

Platform Core does not create or evaluate permissions directly.

---

## Activity Logging

All onboarding activities are recorded by the Platform Activity & Audit Engine.

Examples include:

- Registration Started
- User Created
- Email Verified
- Tenant Created
- Workspace Created
- Trial Activated
- Initial Login

These records provide complete traceability of the onboarding lifecycle.

---

## Registration Design Principles

The registration and onboarding process follows these principles.

- Self-Service Registration
- API First
- Event Driven
- Tenant Aware
- Secure by Default
- Fully Auditable
- Configuration Driven
- Extensible
- Idempotent
- Observable

These principles ensure that onboarding remains scalable, reliable, and capable of supporting future enhancements without requiring architectural redesign.

# 6. Authentication

Authentication is the responsibility of Platform Core.

Platform Core provides a centralized identity service that authenticates users across the entire Business Suite platform.

Users authenticate once using their Platform Identity, regardless of the number of workspaces they belong to.

After successful authentication, Platform Core establishes the user's Active Context and delegates authorization decisions to the Authorization Engine.

Authentication is completely separated from authorization.

---

## Authentication Principles

Business Suite authentication follows these principles.

- Single Sign-On (Platform Identity)
- Secure by Default
- Multi-Tenant Aware
- Event Driven
- API First
- Session Based
- Token Based
- Extensible Authentication Providers
- Zero Trust Security

---

## Authentication Workflow

```text
User Login
      │
      ▼
Validate Credentials
      │
      ▼
Authenticate User
      │
      ▼
Create User Session
      │
      ▼
Resolve Workspace Memberships
      │
      ▼
Select Active Workspace
      │
      ▼
Build Active Context
      │
      ▼
Generate Access Token
      │
      ▼
Publish Authentication Events
      │
      ▼
Return Authenticated Session
```

Platform Core authenticates the user.

The Authorization Engine evaluates permissions after authentication has completed.

---

## Supported Authentication Methods

Platform Core supports multiple authentication providers.

Supported providers include:

- Email & Password
- Google OAuth
- Microsoft OAuth
- Magic Link Authentication
- Multi-Factor Authentication (MFA)

Authentication providers are configurable through Platform Configuration.

Individual providers may be enabled or disabled without requiring application code changes.

Future providers may include:

- Apple Sign-In
- GitHub OAuth
- LinkedIn OAuth
- SAML
- OpenID Connect (OIDC)
- Enterprise Identity Providers

---

## Identity Management

Every authenticated user has one Platform Identity.

A Platform Identity contains:

- User Identifier
- Verified Email Address
- Authentication Method
- Profile Information
- Session Information
- Workspace Memberships

A Platform Identity is global across Business Suite.

Users should never have duplicate accounts because they belong to multiple organizations.

---

## Session Management

Platform Core manages authenticated user sessions.

Each session includes:

- Session Identifier
- User Identifier
- Tenant Context
- Active Workspace
- Device Information
- Browser Information
- IP Address
- Login Time
- Last Activity
- Expiration Time

Platform Core supports:

- Session Timeout
- Session Refresh
- Manual Logout
- Automatic Logout
- Session Revocation
- Multiple Active Sessions

---

## Access Tokens

Platform Core issues secure access tokens after successful authentication.

Access tokens contain:

- User Identifier
- Tenant Context
- Workspace Context
- Session Identifier
- Token Expiry
- Token Version

Tokens should never contain permission information.

Permissions are resolved dynamically through the Authorization Engine.

---

## Password Policies

Platform Core supports configurable password policies.

Examples include:

- Minimum Password Length
- Password Complexity
- Password History
- Password Expiry
- Account Lockout
- Maximum Failed Login Attempts
- Password Reuse Prevention

Password policies are configurable through Platform Configuration.

---

## Password Recovery

Platform Users can securely recover their accounts.

Supported capabilities include:

- Request Password Reset
- Email Verification
- Reset Link Generation
- Reset Link Expiration
- Password Reset Confirmation
- Previous Token Invalidation

Password reset notifications are delivered through the Notification Engine.

---

## Multi-Factor Authentication

Platform Core supports configurable Multi-Factor Authentication (MFA).

Supported methods include:

- Email One-Time Password (OTP)
- Authenticator Applications (TOTP)

Future versions may support:

- SMS OTP
- Hardware Security Keys
- Passkeys
- Biometric Authentication

MFA can be configured at:

- Platform Level
- Tenant Level

---

## Authentication Events

Platform Core publishes authentication events through the Platform Event Bus.

Examples include:

```text
UserAuthenticated

AuthenticationFailed

UserLoggedOut

PasswordResetRequested

PasswordResetCompleted

EmailVerified

SessionCreated

SessionExpired

MFAEnabled

MFADisabled
```

These events are consumed by various Platform Engines.

Examples include:

- Activity & Audit Engine
- Notification Engine
- Reporting Engine
- Search & Indexing Engine

---

## Authentication Security

Platform Core enforces enterprise authentication security.

Security measures include:

- Secure Password Hashing
- HTTPS Only
- Secure Cookies
- Token Expiration
- CSRF Protection
- Brute Force Protection
- Session Revocation
- Device Validation
- Rate Limiting

Authentication operates independently of authorization.

After successful authentication, every request is passed to the Authorization Engine for permission evaluation.

---

## Authentication Design Principles

Authentication within Business Suite follows these principles.

- Authentication before Authorization
- Single Platform Identity
- Multi-Tenant Aware
- Secure by Default
- Event Driven
- Fully Auditable
- Extensible
- Stateless APIs
- Session Aware
- Standards Based

Platform Core is responsible for proving **who the user is**.

The Authorization Engine is responsible for determining **what the user is allowed to do**.

# 7. Workspace Selection

After successful authentication, Platform Core resolves the authenticated user's available Workspace Memberships.

A Platform User may belong to one or multiple Workspaces.

Before accessing any business functionality, an Active Workspace must be established.

The Active Workspace becomes part of the user's Active Context and is used throughout the platform to enforce tenant isolation, authorization, configuration, and business rules.

---

## Workspace Resolution Workflow

```text
User Authenticated
        │
        ▼
Load Workspace Memberships
        │
        ▼
Single Workspace?
      ┌───────────────┐
      │               │
     YES             NO
      │               │
      ▼               ▼
Open Workspace   Display Workspace
Automatically      Selector
      │               │
      └───────┬───────┘
              ▼
Select Active Workspace
              │
              ▼
Build Active Context
              │
              ▼
Initialize Platform Engines
              │
              ▼
Open Dashboard
```

Platform Core is responsible for resolving the Active Workspace.

Platform Engines consume the Active Context during request processing.

---

## Single Workspace

If the authenticated user belongs to only one Workspace:

- Automatically select the Workspace.
- Build the Active Context.
- Initialize the user's session.
- Open the appropriate dashboard.

No Workspace selection screen is required.

---

## Multiple Workspaces

If the authenticated user belongs to multiple Workspaces, Platform Core displays the Workspace Selector.

Users may:

- Select a Workspace
- Search Workspaces
- Switch Workspaces
- Set a Default Workspace
- View Workspace Information

The selected Workspace becomes the Active Workspace for the current session.

---

## Active Workspace

The Active Workspace establishes the operational context for every request executed within Business Suite.

Changing the Active Workspace changes:

- Active Tenant
- Active Organization
- Active Branch
- Available Modules
- Subscription
- Feature Flags
- Business Data
- Reports
- Dashboards

Platform Core automatically rebuilds the Active Context whenever the Workspace changes.

---

## Active Context

Platform Core creates an Active Context after Workspace selection.

The Active Context includes:

- Authenticated User
- Active Tenant
- Active Workspace
- Active Organization
- Active Branch
- Subscription
- Enabled Modules
- Configuration
- Localization Settings
- Correlation ID

The Authorization Engine extends this context with:

- Roles
- Permissions
- Policies
- Resource Access Rules

All Platform Engines consume the Active Context during request processing.

---

## Workspace Membership

Every Workspace Membership defines the relationship between a Platform User and a Workspace.

Each membership includes:

- Workspace
- Membership Status
- Assigned Roles
- Date Joined
- Default Workspace
- Invitation Status

Membership statuses include:

- Pending
- Active
- Suspended
- Removed

Workspace Memberships are managed by Platform Core.

Role assignments and permission evaluation are managed by the Authorization Engine.

---

## Workspace Switching

Platform Users may switch between authorized Workspaces without re-authenticating.

Workspace switching performs the following operations:

- Validate Workspace Membership
- Load Tenant Context
- Load Organization Context
- Load Branch Context
- Load Subscription
- Load Enabled Modules
- Refresh Active Context
- Publish Workspace Changed Event

Switching Workspaces never changes the user's Platform Identity.

Only the execution context changes.

---

## Platform Events

Platform Core publishes Workspace events through the Platform Event Bus.

Examples include:

```text
WorkspaceSelected

WorkspaceChanged

ActiveContextCreated

WorkspaceMembershipLoaded

DefaultWorkspaceChanged
```

These events may be consumed by:

- Authorization Engine
- Activity & Audit Engine
- Reporting Engine
- Notification Engine
- Search & Indexing Engine

---

## Workspace Security

Workspace selection follows these security principles.

- Users may only select Workspaces where they have an active membership.
- Every request must execute within an Active Workspace.
- Workspace switching requires membership validation.
- Tenant isolation must always be enforced.
- Active Context must be rebuilt after every Workspace change.
- Authorization decisions are recalculated by the Authorization Engine whenever the Active Workspace changes.

---

## Design Principles

Workspace selection follows these architectural principles.

- Single Platform Identity
- Multiple Workspace Memberships
- Tenant Isolation
- Active Context Driven
- Event Driven
- Secure by Default
- API First
- Fully Auditable
- Extensible

Platform Core establishes the execution context.

Platform Engines execute within that context to provide secure, tenant-aware enterprise services.

# 8. Subscription Management

Platform Core manages the complete lifecycle of tenant subscriptions.

Every Tenant must have exactly one active subscription at any given time.

A subscription determines the commercial and operational capabilities available to a Tenant.

Platform Core is responsible for managing subscription information, licensing, module availability, and platform limits.

Business modules and Platform Engines consume subscription information through the Active Context.

---

## Subscription Responsibilities

Platform Core manages:

- Trial Management
- Subscription Lifecycle
- Package Assignment
- License Validation
- Module Availability
- Tenant Limits
- Storage Allocation
- User Limits
- Branch Limits
- Feature Availability

Subscription information is evaluated during every authenticated session.

---

## Subscription Information

Every subscription contains:

- Subscription Identifier
- Tenant
- Package
- Status
- Trial Status
- Start Date
- End Date
- Renewal Date
- Enabled Modules
- Enabled Features
- Maximum Users
- Maximum Branches
- Storage Allocation
- License Status

---

## Subscription Validation

Before allowing access to business functionality, Platform Core validates:

- Subscription Status
- Package Status
- Trial Expiry
- Module Availability
- License Limits
- Feature Availability

If validation fails, access is restricted according to platform policies.

---

## Platform Engine Integration

Subscription information is consumed by:

- Authorization Engine
- Workflow Engine
- Reporting Engine
- Notification Engine
- Platform Event Bus
- Business Modules

Platform Core remains the single source of truth for subscription information.

---

# 9. Subscription Lifecycle

Every Tenant subscription follows a standardized lifecycle.

```text
Tenant Registration
        │
        ▼
Trial Activated
        │
        ▼
Active Trial
        │
        ▼
Trial Reminder
        │
        ▼
Trial Expired
        │
        ▼
Pending Subscription
        │
        ▼
Subscription Activated
        │
        ▼
Active Subscription
        │
        ▼
Renewal
        │
        ▼
Expired / Suspended / Cancelled
```

The Platform Activity & Audit Engine records every subscription state change.

The Platform Event Bus publishes lifecycle events.

---

## Subscription Statuses

Supported statuses include:

### Trial

The Tenant is using a trial subscription.

---

### Active

The Tenant has an active paid subscription.

---

### Expired

The subscription period has ended.

Licensed modules become unavailable according to platform policy.

---

### Suspended

The subscription has been temporarily suspended.

Tenant users cannot access restricted functionality until the subscription is reactivated.

---

### Cancelled

The subscription has been permanently cancelled.

Tenant data is retained according to the Information Governance Framework.

---

## Subscription Events

Platform Core publishes subscription events.

Examples include:

```text
TrialActivated

TrialReminderSent

TrialExpired

SubscriptionActivated

SubscriptionRenewed

SubscriptionSuspended

SubscriptionCancelled
```

These events may be consumed by:

- Notification Engine
- Reporting Engine
- Platform Activity & Audit Engine
- Business Modules

---

# 10. Trial Management

Every newly registered Tenant receives a configurable trial subscription.

Default trial configuration is managed through Platform Configuration.

Platform Core automatically manages:

- Trial Start Date
- Trial End Date
- Remaining Days
- Trial Status
- Trial Validation

---

## Trial Notifications

Reminder notifications are delegated to the Notification Engine.

Suggested reminders include:

- 14 Days Remaining
- 7 Days Remaining
- 3 Days Remaining
- 1 Day Remaining
- Trial Expired

Notification templates are managed by the Notification Engine.

---

## Trial Limitations

Trial packages determine:

- Available Modules
- Enabled Features
- Maximum Users
- Maximum Branches
- Storage Allocation

Nothing should be hardcoded.

All limits are configuration-driven.

---

# 11. Subscription Activation

Version 1 of Business Suite supports manual subscription activation.

Subscription activation is performed by the Super Administrator.

Workflow

```text
Trial Expired
        │
        ▼
Subscription Requested
        │
        ▼
Package Selected
        │
        ▼
Subscription Activated
        │
        ▼
Tenant Continues Using Platform
```

Platform Core updates the subscription.

The Platform Event Bus publishes the activation event.

The Notification Engine informs the Tenant.

The Platform Activity & Audit Engine records the activation.

The architecture supports future online payment providers without redesign.

---

# 12. Package Management

Packages define the commercial offerings available within Business Suite.

Platform Core manages package definitions.

Packages are fully configuration-driven.

Nothing should be hardcoded.

---

## Package Definition

Each package defines:

- Package Name
- Description
- Trial Availability
- Subscription Duration
- Enabled Modules
- Enabled Features
- Maximum Users
- Maximum Branches
- Storage Allocation
- API Limits
- Support Level

---

## Example Packages

Examples include:

- Starter
- Professional
- Business
- Enterprise

Package names and capabilities are completely configurable.

---

## Package Assignment

Each Tenant may have only one active package at a time.

Changing a package immediately updates:

- Module Availability
- Feature Availability
- User Limits
- Branch Limits
- Storage Limits

Affected Platform Engines automatically consume the updated subscription information through the Active Context.

---

## Package Events

Platform Core publishes package events.

Examples include:

```text
PackageCreated

PackageUpdated

PackageAssigned

PackageChanged

PackageRetired
```

These events are consumed by:

- Notification Engine
- Reporting Engine
- Platform Activity & Audit Engine
- Business Modules

---

## Design Principles

Package Management follows these principles.

- Configuration Driven
- Subscription Aware
- Tenant Aware
- Event Driven
- Fully Auditable
- Extensible
- API First

Platform Core remains the authoritative source for package and subscription management across the Business Suite platform.

# 13. Module Management

Business Suite is built using a modular, plug-and-play architecture.

Every business capability is implemented as an independent Business Module that integrates with Platform Core and the Platform Engines through standardized contracts.

Platform Core provides the Module Registry, module discovery, licensing, and activation mechanisms.

Business functionality remains entirely within the individual modules.

---

## Module Architecture

Every Business Module is an independently developed and deployable capability.

Examples include:

- CRM
- Sales
- Procurement
- Inventory
- Finance
- Human Resources
- Payroll
- Assets
- Projects
- Help Desk
- Point of Sale (POS)

Platform Core itself is also implemented as a platform module but serves as the mandatory foundation for the entire platform.

---

## Module Registry

Platform Core maintains the Platform Module Registry.

The Module Registry provides:

- Module Registration
- Module Discovery
- Module Metadata
- Module Status
- Module Version
- Module Dependencies
- Module Configuration
- Module Licensing

The Module Registry acts as the authoritative catalog of all modules available within Business Suite.

---

## Module Metadata

Every registered module contains standardized metadata.

Examples include:

- Module Identifier
- Module Name
- Display Name
- Description
- Version
- Publisher
- Category
- Status
- Dependencies
- License Requirement
- Installation Date
- Last Updated

Standardized metadata enables module lifecycle management and future marketplace support.

---

## Module Activation

Modules become available according to the Tenant's active subscription package.

Platform Core determines:

- Whether the module is licensed.
- Whether the module is enabled.
- Whether module dependencies are satisfied.

If all validation checks succeed, the module becomes available to the Tenant.

---

## Module Dependencies

Some modules depend on other modules.

Example

```text
Sales
    │
    ├── CRM
    ├── Inventory
    └── Finance
```

Platform Core validates module dependencies before activation.

Modules cannot be enabled if their required dependencies are unavailable.

---

## Module Lifecycle

Each module follows a standardized lifecycle.

```text
Registered
        │
        ▼
Installed
        │
        ▼
Configured
        │
        ▼
Activated
        │
        ▼
Suspended
        │
        ▼
Retired
```

Lifecycle changes are fully auditable and event-driven.

---

## Module Configuration

Each module may expose its own configuration.

Platform Core provides the configuration framework while individual modules manage their own configuration settings.

Examples include:

- Module Settings
- Feature Flags
- Localization
- Default Values
- Business Preferences
- Integration Settings

Configuration is always data-driven.

---

## Module Permissions

Platform Core does not manage module permissions.

Access to module functionality is evaluated by the Authorization Engine.

The Authorization Engine determines:

- Module Access
- Menu Visibility
- Actions
- Resources
- Policies

Platform Core simply exposes the registered modules available to the current Tenant.

---

## Module Events

Platform Core publishes module lifecycle events through the Platform Event Bus.

Examples include:

```text
ModuleRegistered

ModuleInstalled

ModuleConfigured

ModuleActivated

ModuleDeactivated

ModuleUpdated

ModuleRemoved
```

These events may be consumed by:

- Authorization Engine
- Notification Engine
- Reporting Engine
- Search & Indexing Engine
- Platform Activity & Audit Engine
- Business Modules

---

## Module Integration

Every Business Module integrates with Platform Core and the Platform Engines using standardized contracts.

Typical integrations include:

| Component                        | Purpose                                           |
| -------------------------------- | ------------------------------------------------- |
| Platform Core                    | Identity, tenancy, configuration, module registry |
| Authorization Engine             | Access control                                    |
| Workflow Engine                  | Business workflows                                |
| Platform Event Bus               | Event communication                               |
| Reference Data Engine            | Shared lookup data                                |
| Document Numbering Engine        | Business document numbering                       |
| Document Management Engine       | File management                                   |
| Notification Engine              | User notifications                                |
| Reporting Engine                 | Operational and analytical reporting              |
| Search & Indexing Engine         | Enterprise search                                 |
| Platform Activity & Audit Engine | Activity tracking and audit logging               |

This standardized integration model ensures consistency across all Business Modules.

---

## Design Principles

Module Management follows these principles.

- Modular by Design
- API First
- Event Driven
- Tenant Aware
- Configuration Driven
- License Aware
- Dependency Managed
- Extensible
- Fully Auditable
- Standards Based

Platform Core provides the infrastructure for module discovery, registration, licensing, and activation.

Business Modules remain responsible for implementing their own business capabilities while consuming shared services provided by Platform Core and the Platform Engines.

# 14. Organization Management

Every Tenant within Business Suite represents an Organization.

Platform Core is responsible for managing the organizational structure and profile information for every Tenant.

The Organization serves as the highest business entity within a Tenant and provides the default context for all Business Modules and Platform Engines.

Organization information is shared across the entire platform.

---

## Organization Responsibilities

Platform Core manages:

- Organization Profile
- Legal Information
- Registration Details
- Branding
- Regional Settings
- Financial Defaults
- Contact Information
- Default Business Preferences

Business-specific data is managed by the individual Business Modules.

---

## Organization Profile

Each Organization maintains the following information.

### General Information

- Organization Name
- Trading Name
- Organization Code
- Business Type
- Industry
- Description
- Logo

---

### Registration Information

- Registration Number
- Tax Identification Number (TIN)
- VAT Number
- Business License Number

---

### Contact Information

- Email Address
- Telephone Number
- Mobile Number
- Website
- Physical Address
- Postal Address

---

### Regional Settings

Platform Core manages organization-wide defaults including:

- Country
- Currency
- Language
- Time Zone
- Date Format
- Number Format
- Financial Year

These settings are consumed throughout the platform by all Platform Engines and Business Modules.

---

## Branding

Organizations may customize their workspace branding.

Supported branding includes:

- Company Logo
- Favicon
- Primary Color
- Secondary Color
- Email Branding
- Report Branding

Branding is automatically used by:

- Notification Engine
- Reporting Engine
- Document Management Engine

---

## Organization Lifecycle

Every Organization follows a standardized lifecycle.

```text
Registered
        │
        ▼
Active
        │
        ▼
Suspended
        │
        ▼
Archived
```

Lifecycle events are published through the Platform Event Bus.

---

## Organization Events

Platform Core publishes organization events.

Examples include:

```text
OrganizationCreated

OrganizationUpdated

OrganizationActivated

OrganizationSuspended

OrganizationArchived
```

These events may be consumed by:

- Notification Engine
- Reporting Engine
- Search & Indexing Engine
- Platform Activity & Audit Engine

---

## Organization Design Principles

Organization Management follows these principles.

- Single Organization per Tenant
- Shared Across All Modules
- Tenant Aware
- Event Driven
- Fully Auditable
- Configuration Driven
- Extensible

Platform Core remains the authoritative source of organization information across Business Suite.

---

# 15. Branch Management

Organizations may operate one or more Branches.

Platform Core manages the organizational branch structure shared across all Business Modules.

Branches represent operational, geographical, or legal business locations.

Every Business Module uses the same branch structure.

---

## Branch Responsibilities

Platform Core manages:

- Branch Registration
- Branch Information
- Branch Status
- Branch Hierarchy
- Default Branch
- Branch Configuration

Access to Branches is managed by the Authorization Engine.

---

## Branch Information

Each Branch maintains standardized information.

### General Information

- Branch Name
- Branch Code
- Description
- Branch Type

---

### Contact Information

- Email Address
- Telephone Number
- Physical Address

---

### Operational Information

- Manager
- Status
- Default Currency
- Default Warehouse (Optional)
- Operating Hours

---

### Regional Information

- Country
- Region
- District
- City
- Time Zone

---

## Branch Hierarchy

Platform Core supports hierarchical branch structures.

Example

```text
Head Office
│
├── Kampala Branch
│
├── Mbarara Branch
│
├── Gulu Branch
│
└── Arua Branch
```

Branch hierarchies enable future regional reporting and delegated administration.

---

## Branch Assignment

Platform Users may be assigned to:

- One Branch
- Multiple Branches
- All Branches

Branch assignments are evaluated by the Authorization Engine.

Platform Core only maintains the branch structure.

---

## Branch Lifecycle

Each Branch follows a standardized lifecycle.

```text
Created
        │
        ▼
Active
        │
        ▼
Inactive
        │
        ▼
Archived
```

Lifecycle changes are fully auditable.

---

## Branch Events

Platform Core publishes branch events.

Examples include:

```text
BranchCreated

BranchUpdated

BranchActivated

BranchDeactivated

BranchArchived
```

These events may be consumed by:

- Authorization Engine
- Reporting Engine
- Search & Indexing Engine
- Platform Activity & Audit Engine
- Notification Engine

---

## Branch Design Principles

Branch Management follows these principles.

- Shared Organizational Structure
- Tenant Aware
- Event Driven
- Fully Auditable
- Extensible
- API First

Platform Core provides the organizational branch structure consumed by all Platform Engines and Business Modules.

# 16. User Management

Platform Core provides centralized identity and user management across the entire Business Suite platform.

A Platform User represents a single authenticated identity that may belong to one or more Workspaces.

Platform Core is responsible for user identity, profiles, memberships, invitations, authentication, and account lifecycle.

Authorization, roles, permissions, and access policies are managed by the Authorization Engine.

---

## User Management Responsibilities

Platform Core manages:

- Platform Users
- User Profiles
- User Authentication
- Workspace Memberships
- User Invitations
- User Sessions
- Account Status
- Password Management
- Email Verification
- Multi-Factor Authentication
- User Preferences

Platform Core does not manage:

- Roles
- Permissions
- Policies
- Resource Authorization
- Approval Authority

These capabilities are provided by the Authorization Engine.

---

## Platform User

A Platform User exists only once within Business Suite.

A Platform User contains:

### Identity

- User ID
- Display Name
- First Name
- Middle Name
- Last Name
- Email Address
- Phone Number
- Profile Photo

---

### Authentication

- Authentication Provider
- Password
- MFA Status
- Email Verification
- Last Login
- Last Password Change

---

### Preferences

- Default Workspace
- Preferred Language
- Time Zone
- Theme
- Notification Preferences

---

### Status

- Pending Verification
- Active
- Suspended
- Locked
- Disabled

---

## User Lifecycle

Every Platform User follows the same lifecycle.

```text
User Registered
        │
        ▼
Email Verification
        │
        ▼
User Activated
        │
        ▼
Workspace Membership Created
        │
        ▼
User Active
        │
        ▼
Suspended
        │
        ▼
Archived
```

Every lifecycle transition is fully audited.

---

## Workspace Membership

A Platform User may belong to one or many Workspaces.

Workspace Membership establishes the relationship between a Platform User and a Tenant.

Each membership includes:

- Workspace
- Membership Status
- Date Joined
- Invited By
- Default Workspace
- Invitation Status

Roles and Permissions are not stored within the membership.

They are resolved dynamically by the Authorization Engine.

---

## User Invitations

Workspace Owners and authorized Workspace Administrators may invite users.

Invitation workflow

```text
Invite User
        │
        ▼
User Exists?
      ┌───────────────┐
      │               │
     YES             NO
      │               │
      ▼               ▼
Create Membership   Create Platform User
      │               │
      └───────┬───────┘
              ▼
Generate Invitation
              │
              ▼
Send Notification
              │
              ▼
User Accepts
              │
              ▼
Membership Activated
```

Platform Core creates the invitation.

The Notification Engine delivers the invitation.

The Authorization Engine provisions the user's initial authorization.

---

## User Profile Management

Platform Users may manage their own profile.

Editable information includes:

- Profile Photo
- Name
- Phone Number
- Language
- Time Zone
- Theme
- Notification Preferences

Security-related settings are managed through Platform Authentication.

---

## User Administration

Authorized users may perform the following operations:

- Create Users
- Invite Users
- Suspend Users
- Reactivate Users
- Remove Workspace Memberships
- View User Profiles
- Reset Passwords
- Unlock Accounts

Administrative permissions are evaluated by the Authorization Engine.

---

## Session Management

Platform Core manages authenticated user sessions.

Each session includes:

- Session Identifier
- User Identifier
- Tenant Context
- Active Workspace
- Device Information
- Browser Information
- IP Address
- Login Timestamp
- Last Activity Timestamp
- Session Expiry

Platform Core supports:

- Multiple Active Sessions
- Session Revocation
- Automatic Timeout
- Remember Me
- Device Recognition

---

## User Events

Platform Core publishes user lifecycle events through the Platform Event Bus.

Examples include:

```text
UserRegistered

UserActivated

UserUpdated

UserSuspended

UserUnlocked

WorkspaceMembershipCreated

WorkspaceMembershipRemoved

InvitationCreated

InvitationAccepted

InvitationExpired
```

These events are consumed by:

- Authorization Engine
- Notification Engine
- Reporting Engine
- Search & Indexing Engine
- Platform Activity & Audit Engine

---

## Platform Engine Integration

User Management integrates with the following Platform Engines.

### Authorization Engine

Provides:

- Role Assignment
- Permission Evaluation
- Access Policies
- Resource Authorization

---

### Notification Engine

Provides:

- Invitation Emails
- Welcome Emails
- Password Reset Notifications
- Security Alerts
- Verification Emails

---

### Platform Activity & Audit Engine

Provides:

- User Activity History
- Login History
- Security Audit
- Account Changes

---

### Reporting Engine

Provides:

- User Reports
- Login Statistics
- Membership Reports
- Platform Analytics

---

### Search & Indexing Engine

Provides:

- User Search
- Workspace Search
- Membership Search

---

## Design Principles

User Management follows these principles.

- Single Platform Identity
- Multiple Workspace Memberships
- Authentication before Authorization
- Tenant Aware
- Event Driven
- API First
- Secure by Default
- Fully Auditable
- Extensible
- Standards Based

Platform Core is responsible for user identity and lifecycle management.

Authorization decisions are delegated entirely to the Authorization Engine.

# 17. Authorization

Business Suite separates **Authentication** from **Authorization**.

Platform Core is responsible for authenticating users and establishing the Active Context.

The Authorization Engine is responsible for determining what authenticated users are allowed to access and perform within the platform.

This separation provides a scalable, reusable, and enterprise-grade security architecture.

---

## Authorization Responsibilities

Authorization is fully delegated to the Authorization Engine.

The Authorization Engine is responsible for:

- Role Management
- Permission Management
- Permission Groups
- Resource Authorization
- Policy Management
- Branch Access
- Organization Access
- Module Access
- Feature Access
- Action Authorization
- Dynamic Authorization Rules
- Authorization Decisions

Platform Core never evaluates permissions directly.

---

## Platform Core Responsibilities

Platform Core integrates with the Authorization Engine.

Responsibilities include:

- Authenticate Users
- Resolve Active Workspace
- Resolve Active Tenant
- Build Active Context
- Pass Active Context to the Authorization Engine
- Consume Authorization Decisions

Platform Core acts as the identity provider.

The Authorization Engine acts as the policy decision point.

---

## Authorization Workflow

```text
User Request
        │
        ▼
Platform Authentication
        │
        ▼
Resolve Active Context
        │
        ▼
Authorization Engine
        │
        ▼
Evaluate Policies
        │
        ▼
Access Granted?
      ┌───────────────┐
      │               │
     YES             NO
      │               │
      ▼               ▼
Continue        Return Forbidden
      │
      ▼
Execute Request
```

Platform Core never bypasses the Authorization Engine.

Every protected request follows this workflow.

---

## Active Context

Platform Core constructs the Active Context before authorization is evaluated.

The Active Context contains:

- Authenticated User
- Active Tenant
- Active Workspace
- Active Organization
- Active Branch
- Subscription
- Enabled Modules
- Feature Flags
- Correlation ID

The Authorization Engine enriches the Active Context with:

- Assigned Roles
- Effective Permissions
- Security Policies
- Resource Access Rules
- Branch Access Rules
- Organization Access Rules

---

## Authorization Integration

Platform Core integrates with the Authorization Engine through standardized contracts.

Examples include:

### Authentication

Platform Core authenticates the user.

---

### Authorization

Authorization Engine evaluates access.

---

### Policy Evaluation

Authorization Engine evaluates:

- Roles
- Permissions
- Policies
- Conditions
- Ownership Rules

---

### Resource Access

Authorization Engine determines access to:

- Modules
- Pages
- Menus
- APIs
- Resources
- Reports
- Documents
- Branches

---

## Authorization Events

Platform Core consumes authorization events published through the Platform Event Bus.

Examples include:

```text
RoleAssigned

RoleRemoved

PermissionGranted

PermissionRevoked

PolicyUpdated

AuthorizationDecisionGenerated
```

These events may trigger:

- UI Refresh
- Cache Invalidation
- Session Refresh
- Audit Logging

---

## Platform Engine Integration

Platform Core integrates with:

### Authorization Engine

Provides:

- Role Management
- Permission Evaluation
- Policy Enforcement
- Resource Authorization

---

### Platform Event Bus

Provides:

- Authorization Events
- Cache Synchronization
- Cross-Engine Communication

---

### Platform Activity & Audit Engine

Records:

- Authorization Decisions
- Access Attempts
- Permission Changes
- Security Events

---

### Reporting Engine

Provides:

- Authorization Reports
- Permission Reports
- Role Reports
- Security Analytics

---

## Design Principles

Authorization follows these principles.

- Authentication before Authorization
- Policy Based Access Control
- Least Privilege
- Tenant Aware
- Resource Based Authorization
- Event Driven
- API First
- Secure by Default
- Fully Auditable
- Extensible

Platform Core authenticates users and establishes the execution context.

The Authorization Engine is solely responsible for making authorization decisions throughout the Business Suite platform.

# 18. Platform Configuration

Platform Configuration provides the centralized configuration framework for the entire Business Suite platform.

Platform Core manages global platform configuration, tenant defaults, environment settings, feature flags, and shared platform preferences.

Platform Engines and Business Modules consume configuration through standardized configuration services rather than maintaining independent configuration stores.

All configuration is data-driven.

Nothing should be hardcoded.

---

## Configuration Principles

Platform Configuration follows these principles.

- Centralized Configuration
- Configuration over Customization
- Tenant Aware
- Environment Aware
- API First
- Event Driven
- Version Controlled
- Fully Auditable

Configuration changes become effective without requiring application redeployment whenever possible.

---

## Configuration Categories

Platform Core manages the following configuration categories.

### Platform Configuration

Global settings shared by the entire platform.

Examples include:

- Platform Name
- Platform Branding
- Default Language
- Default Time Zone
- Maintenance Mode
- Platform URLs
- Support Information

---

### Tenant Configuration

Default settings inherited by each Tenant.

Examples include:

- Regional Settings
- Financial Year
- Currency
- Language
- Date Format
- Number Format

---

### Environment Configuration

Environment-specific settings.

Examples include:

- Development
- Testing
- Staging
- Production

---

### Feature Flags

Feature Flags control platform functionality.

Examples include:

- Beta Features
- Experimental Features
- Early Access Modules
- Feature Rollout
- Tenant-specific Features

---

### Localization

Localization settings include:

- Language
- Region
- Time Zone
- Currency
- Date Format
- Number Format

These settings are consumed throughout the platform.

---

## Platform Engine Configuration

Each Platform Engine manages its own specialized configuration while Platform Core provides the configuration framework.

Examples include:

| Platform Engine            | Configuration          |
| -------------------------- | ---------------------- |
| Authorization Engine       | Authorization Policies |
| Workflow Engine            | Workflow Defaults      |
| Notification Engine        | Notification Providers |
| Reporting Engine           | Report Defaults        |
| Search Engine              | Search Configuration   |
| Document Management Engine | Storage Configuration  |
| Reference Data Engine      | Reference Data Sources |

Platform Core stores configuration references while individual Platform Engines own their specialized configuration.

---

## Configuration Events

Platform Core publishes configuration events through the Platform Event Bus.

Examples include:

```text
PlatformConfigurationUpdated

TenantConfigurationUpdated

FeatureFlagChanged

EnvironmentChanged

LocalizationUpdated
```

Platform Engines subscribe to these events to refresh cached configuration.

---

## Configuration Security

Configuration changes require appropriate authorization.

The Authorization Engine determines:

- Who may view configuration.
- Who may modify configuration.
- Who may approve configuration changes.

All configuration changes are recorded by the Platform Activity & Audit Engine.

---

## Configuration Design Principles

Platform Configuration follows these principles.

- Data Driven
- API First
- Tenant Aware
- Event Driven
- Secure by Default
- Fully Auditable
- Extensible

Platform Core provides the configuration framework used consistently across every Platform Engine and Business Module.

---

# 19. Notifications

Platform Core does not deliver notifications directly.

Notification delivery is provided by the Notification Engine.

Platform Core integrates with the Notification Engine by publishing business events and requesting notification delivery through standardized service contracts.

---

## Platform Core Notification Responsibilities

Platform Core requests notifications for platform activities including:

- Welcome Messages
- Email Verification
- Password Reset
- User Invitations
- Trial Started
- Trial Reminder
- Trial Expired
- Subscription Activated
- Subscription Renewed
- Security Alerts

The Notification Engine determines:

- Delivery Channel
- Message Template
- Scheduling
- Retry Policy
- Delivery Status

---

## Notification Workflow

```text
Platform Event
        │
        ▼
Platform Event Bus
        │
        ▼
Notification Engine
        │
        ▼
Resolve Template
        │
        ▼
Resolve Recipients
        │
        ▼
Send Notification
        │
        ▼
Track Delivery
```

Platform Core never sends emails or SMS directly.

---

## Notification Channels

Notification delivery is managed by the Notification Engine.

Supported channels include:

- Email
- SMS
- In-App Notifications
- Push Notifications
- Webhooks

Future channels may be added without modifying Platform Core.

---

## Notification Templates

Notification templates are managed by the Notification Engine.

Examples include:

- Welcome Email
- Verification Email
- Password Reset
- Workspace Invitation
- Trial Reminder
- Subscription Activated
- Security Alert

Platform Core references notification types only.

---

## Notification Events

Platform Core publishes events that may trigger notifications.

Examples include:

```text
PlatformUserRegistered

WorkspaceCreated

InvitationCreated

PasswordResetRequested

TrialActivated

TrialExpired

SubscriptionActivated
```

The Notification Engine subscribes to these events and determines whether notifications should be delivered.

---

## Notification Auditing

Notification delivery history is maintained by the Notification Engine.

Platform Core relies on the Platform Activity & Audit Engine for platform-level activity history.

---

## Design Principles

Platform Core never implements notification delivery.

Instead, it publishes business events and delegates all messaging responsibilities to the Notification Engine.

This separation ensures:

- Loose Coupling
- Event Driven Communication
- Provider Independence
- High Scalability
- Enterprise Maintainability

# 20. Tenant Data Isolation

Business Suite is a true multi-tenant SaaS platform.

Tenant isolation is one of the most critical architectural responsibilities of Platform Core.

Every Tenant operates within its own isolated execution context, ensuring that business data, configuration, users, subscriptions, and platform resources remain completely separated from every other Tenant.

Platform Core establishes the Tenant Context that is consumed by every Platform Engine and Business Module.

---

## Tenant Isolation Principles

Business Suite follows these core isolation principles.

- Complete Tenant Isolation
- Shared Application
- Shared Platform Services
- Isolated Business Data
- Row Level Security (RLS)
- Active Context Driven
- Secure by Default
- API First

No Platform Engine or Business Module may bypass tenant isolation.

---

## Tenant Ownership

Every tenant-owned business record must belong to exactly one Tenant.

All tenant-owned entities include:

```text
tenant_id
```

Examples include:

- Customers
- Suppliers
- Products
- Sales
- Purchases
- Employees
- Assets
- Projects
- Workflows
- Documents
- Reports
- Branches
- Configuration

Platform-wide entities such as authentication providers, platform settings, and module definitions are managed separately from tenant-owned resources.

---

## Active Tenant Context

Every authenticated request executes within an Active Tenant Context.

Platform Core resolves:

- Authenticated User
- Active Tenant
- Active Workspace
- Active Organization
- Active Branch
- Subscription
- Enabled Modules
- Feature Flags
- Localization Settings

This Active Context becomes the execution context shared by all Platform Engines.

---

## Tenant Resolution

Platform Core resolves the Tenant Context using:

- Authenticated Identity
- Workspace Membership
- Active Workspace
- Session Information

Tenant Context is established before any business logic executes.

---

## Row Level Security (RLS)

Business Suite uses PostgreSQL Row Level Security (RLS) as the final enforcement layer for tenant isolation.

Platform Core establishes the tenant context.

PostgreSQL enforces:

- Data Visibility
- Data Updates
- Data Inserts
- Data Deletes

Every tenant query is automatically scoped to the current Tenant.

---

## Platform Engine Integration

Every Platform Engine consumes the Tenant Context provided by Platform Core.

Examples include:

| Platform Engine                  | Tenant Usage            |
| -------------------------------- | ----------------------- |
| Authorization Engine             | Workspace authorization |
| Workflow Engine                  | Tenant workflows        |
| Notification Engine              | Tenant notifications    |
| Reporting Engine                 | Tenant reports          |
| Search & Indexing Engine         | Tenant search indexes   |
| Document Management Engine       | Tenant documents        |
| Platform Activity & Audit Engine | Tenant audit records    |

No Platform Engine maintains its own tenant resolution logic.

---

## Tenant Context Lifecycle

```text
User Authenticated
        │
        ▼
Workspace Selected
        │
        ▼
Tenant Resolved
        │
        ▼
Active Context Created
        │
        ▼
Platform Engines Initialized
        │
        ▼
Business Request Executed
```

Platform Core owns the complete lifecycle of Tenant Context creation and management.

---

## Tenant Events

Platform Core publishes tenant lifecycle events through the Platform Event Bus.

Examples include:

```text
TenantCreated

TenantActivated

TenantSuspended

TenantArchived

ActiveTenantChanged
```

These events enable Platform Engines to synchronize their internal state.

---

## Design Principles

Tenant Isolation follows these principles.

- Shared Platform
- Isolated Data
- Active Context Driven
- Row Level Security
- Event Driven
- Secure by Default
- Fully Auditable
- Extensible

Platform Core provides the Tenant Context that enables secure multi-tenant execution across the Business Suite platform.

---

# 21. Activity & Audit Logging

Platform Core does not implement audit logging directly.

Audit logging is provided by the Platform Activity & Audit Engine.

Platform Core publishes platform events and exposes operational context that enables the Platform Activity & Audit Engine to produce a complete audit trail for the Business Suite platform.

---

## Platform Core Responsibilities

Platform Core provides audit context including:

- Authenticated User
- Active Tenant
- Active Workspace
- Active Organization
- Active Branch
- Correlation ID
- Session Information
- Request Metadata

The Platform Activity & Audit Engine records and manages all audit information.

---

## Auditable Activities

Platform Core publishes events for activities such as:

### Authentication

- User Login
- User Logout
- Failed Login
- Password Reset
- Password Changed
- MFA Enabled
- MFA Disabled

---

### Tenant Management

- Tenant Created
- Tenant Activated
- Tenant Suspended
- Tenant Updated

---

### Workspace Management

- Workspace Created
- Workspace Selected
- Workspace Changed

---

### User Management

- User Registered
- User Activated
- Membership Created
- Membership Removed
- Invitation Accepted

---

### Subscription Management

- Trial Activated
- Trial Expired
- Subscription Activated
- Subscription Renewed
- Subscription Suspended

---

### Platform Configuration

- Configuration Updated
- Feature Flag Changed
- Authentication Provider Updated
- Localization Updated

---

## Audit Workflow

```text
Platform Action
        │
        ▼
Platform Event Bus
        │
        ▼
Platform Activity & Audit Engine
        │
        ▼
Record Audit Entry
        │
        ▼
Index Activity
        │
        ▼
Generate Audit Analytics
```

Platform Core never writes audit records directly.

---

## Correlation IDs

Every request processed by Platform Core is assigned a Correlation ID.

The Correlation ID is propagated across:

- Platform Engines
- Business Modules
- API Requests
- Background Jobs
- Notifications
- Workflows

This enables complete end-to-end request tracing throughout Business Suite.

---

## Audit Events

Platform Core publishes events that are consumed by the Platform Activity & Audit Engine.

Examples include:

```text
UserAuthenticated

WorkspaceSelected

ConfigurationChanged

SubscriptionActivated

FeatureFlagChanged

TenantCreated
```

The Platform Activity & Audit Engine transforms these events into immutable audit records.

---

## Design Principles

Platform Core provides operational context.

The Platform Activity & Audit Engine provides:

- Audit Logging
- Activity History
- Change Tracking
- Security Auditing
- Compliance Reporting

This separation keeps Platform Core lightweight while ensuring complete traceability and enterprise-grade auditing across the Business Suite platform.

# 22. Super Administration

The Super Administrator manages the Business Suite platform at the platform level.

Unlike Workspace Users, the Super Administrator does not participate in normal tenant business operations.

The Super Administrator is responsible for managing the platform infrastructure, platform configuration, tenant lifecycle, subscriptions, modules, and overall platform health.

Specialized administrative functions provided by Platform Engines remain within their respective engines.

---

## Responsibilities

The Super Administrator manages:

### Platform

- Platform Configuration
- Platform Branding
- Feature Flags
- Environment Settings
- Maintenance Mode
- Platform Health
- Platform Monitoring

---

### Tenants

- View Tenants
- Activate Tenants
- Suspend Tenants
- Archive Tenants
- Restore Tenants

---

### Subscriptions

- Trial Management
- Subscription Activation
- Subscription Renewal
- Subscription Suspension
- Package Assignment

---

### Module Registry

- Register Modules
- Activate Modules
- Deactivate Modules
- Configure Modules
- Manage Module Versions

---

### Platform Configuration

- Authentication Providers
- Localization
- Feature Flags
- Global Settings
- Environment Configuration

---

## Platform Engine Administration

The Super Administrator may access administrative capabilities exposed by Platform Engines.

Examples include:

| Platform Engine                  | Administration               |
| -------------------------------- | ---------------------------- |
| Authorization Engine             | Roles, Permissions, Policies |
| Workflow Engine                  | Workflow Templates           |
| Notification Engine              | Providers, Templates         |
| Reporting Engine                 | Report Definitions           |
| Search & Indexing Engine         | Search Indexes               |
| Document Management Engine       | Storage Configuration        |
| Platform Activity & Audit Engine | Audit Review                 |
| Reference Data Engine            | Shared Reference Data        |

Each Platform Engine owns its own administration.

Platform Core provides centralized access.

---

## Administrative Principles

Super Administration follows these principles.

- Platform Wide
- Tenant Independent
- Secure by Default
- Fully Auditable
- Event Driven
- API First

Platform Core coordinates platform administration while Platform Engines manage their own specialized capabilities.

---

# 23. Platform Principles

Every Platform Engine and Business Module must follow the architectural principles established by Platform Core.

---

## Platform First

Platform Core provides the common infrastructure required by every Platform Engine and Business Module.

Business logic should never duplicate platform functionality.

---

## Engine Based Architecture

Business capabilities should be implemented through reusable Platform Engines.

Business Modules consume Platform Engines rather than implementing duplicate infrastructure.

---

## Multi-Tenant

Every component must respect Tenant Context.

No Platform Engine or Business Module may bypass tenant isolation.

---

## API First

All platform capabilities should be exposed through standardized APIs.

Business Modules, mobile applications, integrations, and future clients consume the same APIs.

---

## Event Driven

Cross-module communication must occur through the Platform Event Bus whenever possible.

Modules should avoid direct dependencies.

---

## Configuration Driven

Business rules should be configurable whenever practical.

Avoid hardcoded values.

---

## Secure by Default

Authentication, authorization, auditing, and tenant isolation must be enforced throughout the platform.

---

## Observability

All Platform Engines and Business Modules must support:

- Logging
- Metrics
- Tracing
- Correlation IDs
- Health Monitoring

Platform Observability standards must be followed consistently.

---

## Extensibility

The platform should support future Platform Engines, Business Modules, integrations, and mobile applications without architectural redesign.

---

# 24. Success Criteria

Platform Core is considered complete when the following objectives have been achieved.

---

## Identity

- Platform Users authenticate successfully.
- Multiple authentication providers are supported.
- Multi-Factor Authentication functions correctly.
- Session management is operational.

---

## Multi-Tenancy

- Tenant isolation is fully enforced.
- Active Context is correctly established.
- Workspace switching functions correctly.
- PostgreSQL Row Level Security is operational.

---

## Platform Foundation

- Module Registry is operational.
- Configuration Registry is operational.
- Organization Management functions correctly.
- Branch Management functions correctly.
- Subscription Management functions correctly.

---

## Platform Integration

Platform Core successfully integrates with:

- Authorization Engine
- Workflow Engine
- Platform Event Bus
- Reference Data Engine
- Document Numbering Engine
- Document Management Engine
- Notification Engine
- Reporting Engine
- Search & Indexing Engine
- Platform Activity & Audit Engine

---

## Security

- Authentication functions correctly.
- Authorization Engine integration is operational.
- Correlation IDs are generated.
- Active Context is maintained.
- Audit events are published.

---

## Extensibility

New Platform Engines and Business Modules can be added without modifying Platform Core.

---

# 25. Implementation Rules

Business Suite implementation must follow the approved architecture documentation.

Platform Core serves as the enterprise foundation for every Platform Engine and Business Module.

---

## Technology Stack

Implementation uses:

- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui
- React Router
- React Hook Form
- Zod
- Supabase
- PostgreSQL
- Edge Functions
- Storage
- Realtime
- Row Level Security (RLS)

---

## Development Standards

All development must:

- Follow the Platform Architecture.
- Follow the Feature-Based Folder Structure.
- Use the Service Layer Architecture.
- Publish Platform Events.
- Respect Tenant Isolation.
- Consume Platform Engines through standardized contracts.
- Avoid hardcoded business rules.
- Use UUID Primary Keys.
- Implement Observability.
- Generate Correlation IDs.
- Follow Information Governance standards.

---

# 26. Acceptance Criteria

Platform Core Version 2.0 is accepted when:

- Platform authentication is operational.
- Active Context is correctly established.
- Tenant isolation is verified.
- Workspace management functions correctly.
- Organization and Branch Management are operational.
- Subscription Management functions correctly.
- Module Registry is operational.
- Platform Configuration is data-driven.
- Platform Events are published correctly.
- Platform Engines integrate successfully.
- PostgreSQL Row Level Security is enforced.
- Correlation IDs propagate across requests.
- Platform Core is ready to support all current and future Platform Engines without architectural redesign.

---

# 27. Conclusion

Platform Core is the enterprise foundation of Business Suite.

It establishes the identity, tenancy, configuration, organization, subscription, module registry, and execution context upon which every Platform Engine and Business Module depends.

Rather than implementing specialized business capabilities, Platform Core provides the common infrastructure that enables Platform Engines to deliver reusable enterprise services.

By separating foundational platform responsibilities from specialized engine responsibilities, Business Suite achieves a scalable, loosely coupled, event-driven architecture that is secure, maintainable, extensible, and capable of supporting future business growth without significant architectural redesign.
