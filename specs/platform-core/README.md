# Platform Core Specification

Version: 1.0

Status: Draft

Owner: Business Suite Architecture Team

Last Updated: July 2026

---

# 1. Introduction

## 1.1 Purpose

Platform Core is the foundational module of the Business Suite platform.

It provides the common platform capabilities required by every business module, including authentication, tenant management, workspace management, user management, subscriptions, packages, roles, permissions, platform configuration, and administration.

Every module developed within Business Suite depends on Platform Core.

Platform Core is responsible for providing a secure, scalable, configurable, and extensible SaaS foundation upon which all future modules are built.

---

## 1.2 Vision

The Business Suite platform aims to become a modern cloud-native ERP platform for Small and Medium Enterprises (SMEs), allowing businesses to manage their operations through modular applications.

Platform Core provides the infrastructure that enables businesses to:

- Register online
- Create secure workspaces
- Invite users
- Manage company settings
- Control permissions
- Activate modules
- Subscribe to platform packages
- Scale without changing the platform architecture

---

## 1.3 Objectives

Platform Core has the following objectives.

### Platform

- Provide secure multi-tenant architecture.
- Support unlimited tenants.
- Ensure complete tenant isolation.
- Provide centralized authentication.
- Provide centralized authorization.
- Support multiple workspaces per user.
- Support configurable platform settings.

### Business

- Allow businesses to self-register.
- Allow businesses to invite team members.
- Allow businesses to configure company information.
- Support branch management.
- Support subscription management.
- Support configurable packages.

### Technical

- API-first architecture.
- Cloud-native deployment.
- Feature-based modular architecture.
- Service Layer architecture.
- React + TypeScript frontend.
- Supabase backend.
- PostgreSQL database.
- Future mobile support without backend redesign.

---

# 2. Scope

Platform Core includes the following functional areas.

## Public Platform

- Landing Page
- Product Information
- Pricing
- Features
- Contact
- Registration
- Login
- Forgot Password

---

## Authentication

Platform Core provides centralized authentication.

Supported authentication methods include:

- Email & Password
- Google OAuth
- Microsoft OAuth
- Magic Link Authentication
- Multi-Factor Authentication (MFA)

Authentication providers should be configurable by the Super Administrator.

Providers may be enabled or disabled without changing application code.

---

## Tenant Management

Platform Core manages:

- Tenant Registration
- Tenant Activation
- Tenant Status
- Workspace Creation
- Company Profile
- Branches
- Company Settings

---

## User Management

Platform Core manages:

- Platform Users
- Workspace Memberships
- Invitations
- Password Reset
- Account Status
- User Profiles

---

## Authorization

Platform Core manages:

- Roles
- Permission Groups
- User Permissions
- Module Permissions
- Branch Permissions

Authorization is evaluated within the currently active workspace.

---

## Subscription Management

Platform Core manages:

- Trial
- Packages
- Subscription Status
- Manual Subscription Activation
- Subscription Extensions
- Package Upgrades
- Package Downgrades

Online payments are outside the scope of Version 1.

---

## Notifications

Platform Core provides platform-wide notifications.

Supported notification channels:

- Email
- SMS

Notification templates should be configurable.

---

## Platform Administration

Platform Core provides:

- Tenant Administration
- Package Administration
- Module Administration
- Authentication Provider Configuration
- SMTP Configuration
- SMS Gateway Configuration
- Platform Branding
- Platform Settings

---

# 3. Core Concepts

## User

A User represents a person.

A user has exactly one platform account.

The user authenticates once and may belong to multiple workspaces.

A user is never duplicated.

Examples:

- Business Owner
- Accountant
- Sales Officer
- Auditor
- Consultant

---

## Tenant

A Tenant represents a registered business.

Every tenant owns:

- Company Profile
- Branches
- Users
- Roles
- Permissions
- Modules
- Reports
- Business Data
- Settings

Every tenant is isolated from every other tenant.

---

## Workspace

Workspace is the user-facing representation of a Tenant.

Users switch workspaces—not user accounts.

Examples:

- ABC Limited
- Smart Click Uganda
- XYZ Holdings

Changing the workspace changes:

- Active Company
- Active Branches
- Roles
- Permissions
- Modules
- Reports
- Business Data

---

## Tenant Membership

Users and Tenants have a many-to-many relationship.

A Tenant Membership links a user to a workspace.

A membership contains:

- Workspace
- Role
- Permission Set
- Status
- Default Workspace
- Date Joined

A user may have different permissions in different workspaces.

Example:

| Workspace    | Role            |
| ------------ | --------------- |
| ABC Ltd      | Administrator   |
| XYZ Holdings | Finance Manager |
| Smart Click  | Auditor         |

Platform authentication is global.

Authorization is always workspace-specific.

# 4. User Types

Platform Core supports different categories of users, each with specific responsibilities and permissions.

## 4.1 Public Visitor

A Public Visitor is any person accessing the public website without authentication.

Capabilities include:

- View the landing page
- View product information
- View available modules
- View pricing and packages
- Contact support
- Register a business
- Log in

Public visitors have no access to tenant data.

---

## 4.2 Platform User

A Platform User is a registered person on the Business Suite platform.

A Platform User has:

- One platform account
- One verified email address
- One authentication identity
- One profile

A Platform User may belong to:

- One workspace
- Multiple workspaces

The same Platform User should never be duplicated simply because they join another organization.

---

## 4.3 Workspace Owner

The Workspace Owner is automatically created when a new business registers.

The Workspace Owner has full control of the workspace.

Responsibilities include:

- Managing company information
- Managing branches
- Inviting users
- Managing subscriptions
- Managing roles
- Managing permissions
- Configuring workspace settings
- Activating modules available within the subscription

Each workspace has only one Workspace Owner.

Ownership may later be transferred to another user.

---

## 4.4 Workspace Administrator

Workspace Administrators assist the Workspace Owner.

Responsibilities include:

- Managing users
- Assigning roles
- Managing permissions
- Managing branches
- Managing operational settings

Workspace Administrators cannot perform platform-level administration.

---

## 4.5 Workspace User

Workspace Users are invited into the workspace.

Examples include:

- Accountant
- Cashier
- Sales Officer
- Procurement Officer
- HR Officer
- Inventory Officer

Permissions depend entirely on the assigned role.

The same user may have different roles across different workspaces.

---

## 4.6 Super Administrator

The Super Administrator manages the entire SaaS platform.

Responsibilities include:

- Tenant management
- Package management
- Trial management
- Subscription activation
- Subscription extensions
- Platform configuration
- Authentication providers
- SMTP settings
- SMS gateway configuration
- Platform branding
- Feature flags
- Module management
- Platform reports

The Super Administrator should not participate in normal tenant operations except where support is required.

---

# 5. Registration & Onboarding

Business Suite supports self-service registration.

Every registered business becomes a Tenant.

Every Tenant begins with a configurable trial period.

---

## Registration Workflow

```text
Landing Page
        │
        ▼
Choose Trial Package
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
Assign Workspace Owner
        │
        ▼
Activate Trial
        │
        ▼
Configure Company
        │
        ▼
Invite Team Members
        │
        ▼
Start Using Business Suite
```

---

## Business Registration

During registration, the following information is collected:

### Company Information

- Business Name
- Trading Name (Optional)
- Industry
- Country
- Currency
- Time Zone
- Phone Number
- Email Address

### Workspace Owner

- Full Name
- Email Address
- Password

The Workspace Owner automatically becomes the first member of the workspace.

---

## Email Verification

Platform users must verify their email before accessing the workspace.

Verification emails should be configurable.

The platform should support:

- Verification link
- Resend verification
- Verification expiry

---

# 6. Authentication

Authentication is global across the Business Suite platform.

Users authenticate once regardless of the number of workspaces they belong to.

---

## Supported Authentication Methods

Platform Core must support:

- Email & Password
- Google OAuth
- Microsoft OAuth
- Magic Link Authentication
- Multi-Factor Authentication (MFA)

The authentication architecture should be implemented from Version 1.

Individual providers may be enabled or disabled through Platform Configuration.

---

## Password Policies

The platform should support configurable password policies including:

- Minimum password length
- Password complexity
- Password expiry
- Password history
- Account lockout
- Failed login attempts

---

## Password Recovery

Users should be able to:

- Request password reset
- Receive reset email
- Set a new password
- Invalidate previous reset links

---

## Multi-Factor Authentication

Platform Core should support MFA using:

- Email OTP
- Authenticator Applications

MFA should be configurable at:

- Platform level
- Workspace level

---

# 7. Workspace Selection

After authentication, the platform loads all workspace memberships for the user.

## Single Workspace

If the user belongs to one workspace:

- Open the dashboard immediately.

---

## Multiple Workspaces

If the user belongs to multiple workspaces:

Display the Workspace Selector.

The user can:

- Select a workspace
- Switch workspaces
- Set a default workspace

The selected workspace becomes the Active Workspace.

---

## Active Workspace

The Active Workspace determines:

- Company Profile
- Branches
- Available Modules
- Permissions
- Dashboard
- Reports
- Business Data

Every request within the application executes using the Active Workspace.

Users must never access another workspace's data unless they switch to it and have an active membership.

# 8. Subscription Management

Platform Core manages the complete lifecycle of tenant subscriptions.

Every tenant must have exactly one active subscription at any given time.

A subscription determines:

- Package
- Status
- Start Date
- End Date
- Trial Status
- Active Modules
- Maximum Users
- Maximum Branches
- Storage Limits
- Feature Availability

Subscription management is centralized and controlled by Platform Core.

---

# 9. Subscription Lifecycle

A tenant subscription moves through the following lifecycle.

```text
Tenant Registration
        │
        ▼
30-Day Trial
        │
        ▼
Trial Expiry Reminder
        │
        ▼
Trial Expired
        │
        ▼
Awaiting Subscription Activation
        │
        ▼
Super Administrator Activation
        │
        ▼
Active Subscription
        │
        ▼
Subscription Renewal
        │
        ▼
Expired / Suspended
```

The platform should maintain a complete history of all subscription changes.

---

## Subscription Statuses

Supported statuses include:

### Trial

The tenant is using the free trial.

---

### Active

The tenant has an active subscription.

All subscribed modules are available.

---

### Expired

The subscription period has ended.

Access to restricted modules is disabled until renewed.

---

### Suspended

The tenant has been suspended by the Super Administrator.

Users cannot access the workspace.

---

### Cancelled

The subscription has been permanently cancelled.

Tenant data should remain preserved according to the platform retention policy.

---

# 10. Trial Management

Every newly registered tenant receives a configurable trial period.

Default:

- 30 Days

The trial period should be configurable from Platform Settings.

Platform Core automatically:

- Calculates trial start date
- Calculates trial expiry date
- Tracks remaining days
- Displays trial status
- Sends reminder notifications

Suggested reminders:

- 14 Days Remaining
- 7 Days Remaining
- 3 Days Remaining
- 1 Day Remaining
- Trial Expired

---

## Trial Limitations

The trial package determines:

- Enabled modules
- Maximum users
- Maximum branches
- Storage allocation

Trial limitations should be configurable.

Nothing should be hardcoded.

---

# 11. Subscription Activation

Version 1 of Business Suite does not support online payment.

Subscription activation is performed manually by the Super Administrator.

Workflow:

```text
Trial Expired
        │
        ▼
Tenant Requests Subscription
        │
        ▼
Super Administrator Reviews
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

The architecture should support future online payment integration without redesign.

---

# 12. Package Management

Packages define the capabilities available to each tenant.

Packages are fully configurable.

Nothing should be hardcoded.

Each package defines:

- Package Name
- Description
- Trial Availability
- Duration
- Enabled Modules
- Maximum Users
- Maximum Branches
- Storage Allocation
- Support Level
- Feature Availability

Only the Super Administrator manages packages.

---

## Example Packages

Examples include:

- Starter
- Professional
- Business
- Enterprise

Package names are configurable.

---

# 13. Module Management

Business Suite is modular.

Every business capability is implemented as an independent module.

Examples include:

- Platform Core
- CRM
- Sales
- Inventory
- Procurement
- Finance
- HR
- POS
- Reports
- Assets
- Projects

Platform Core is always enabled.

All other modules are enabled according to the tenant's subscription package.

---

## Module Rules

Modules should:

- Be independently deployable within the application.
- Be independently configurable.
- Respect tenant permissions.
- Respect subscription packages.
- Respect licensing rules.

The platform should prevent users from accessing modules that are not enabled for their workspace.

---

# 14. Company Management

Every workspace manages its own company information.

Workspace Owners and Workspace Administrators may configure:

- Business Name
- Trading Name
- Company Logo
- Company Registration Number
- Tax Identification Number
- Industry
- Business Address
- Country
- Currency
- Time Zone
- Language
- Financial Year
- Business Contact Information

Company settings belong exclusively to the active workspace.

---

# 15. Branch Management

A workspace may operate one or more branches.

The number of branches depends on the active subscription package.

Each branch maintains:

- Branch Name
- Branch Code
- Address
- Contact Details
- Status
- Manager
- Default Currency (where applicable)

Users may be assigned to one or more branches depending on their permissions.

Branch access should always be permission-driven.

# 16. User Management

Platform Core provides centralized user management across the entire platform.

A Platform User exists only once.

Users are associated with one or more workspaces through Tenant Memberships.

Users should never be duplicated because they join another organization.

---

## User Lifecycle

The user lifecycle follows the workflow below.

```text
Platform User Created
        │
        ▼
Email Verification
        │
        ▼
Workspace Membership Created
        │
        ▼
Role Assigned
        │
        ▼
Permissions Applied
        │
        ▼
Active User
        │
        ▼
Suspended / Removed
```

---

## User Profile

Each Platform User maintains a single profile.

Suggested profile information includes:

### Personal Information

- Profile Photo
- First Name
- Middle Name (Optional)
- Last Name
- Display Name
- Email Address
- Phone Number
- Preferred Language

### Security

- Password
- Authentication Method
- MFA Status
- Last Login
- Account Status

### Preferences

- Default Workspace
- Theme
- Time Zone
- Notification Preferences

---

## Workspace Membership

A Platform User may belong to one or many workspaces.

Each membership includes:

- Workspace
- Role
- Status
- Date Joined
- Invited By
- Default Workspace Flag

Membership statuses include:

- Pending
- Active
- Suspended
- Removed

---

## User Invitations

Workspace Owners and Workspace Administrators may invite users.

Invitation workflow:

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
Create           Create Platform
Membership          User
      │               │
      └───────┬───────┘
              ▼
      Send Invitation
              │
              ▼
      User Accepts
              │
              ▼
     Membership Activated
```

If the email address already exists:

- Do not create another Platform User.
- Create a Tenant Membership.
- Notify the existing user.
- Add the workspace to the user's available workspaces.

---

## User Administration

Workspace Owners and Workspace Administrators may:

- Invite Users
- Remove Users
- Suspend Users
- Reactivate Users
- Assign Roles
- Change Roles
- View User Activity
- Reset Passwords (optional based on policy)

---

# 17. Roles & Permissions

Business Suite uses Role-Based Access Control (RBAC).

Roles belong to a workspace.

Permissions belong to roles.

Users receive permissions through assigned roles.

Permissions should never be assigned directly to users unless the platform explicitly supports user-level overrides in the future.

---

## Default Roles

Suggested default roles:

- Workspace Owner
- Workspace Administrator
- Manager
- Finance Officer
- Sales Officer
- Procurement Officer
- Inventory Officer
- HR Officer
- Viewer

Workspaces may create additional custom roles.

---

## Permission Categories

Permissions should be grouped by module.

Examples:

### CRM

- View Customers
- Create Customers
- Edit Customers
- Delete Customers

### Sales

- View Sales
- Create Sales
- Approve Sales
- Cancel Sales

### Inventory

- View Stock
- Receive Stock
- Issue Stock
- Perform Stock Count

The same structure should be followed for every future module.

---

## Permission Rules

Permissions should support actions such as:

- View
- Create
- Edit
- Delete
- Approve
- Reject
- Export
- Print
- Configure

Permissions should be configurable.

Nothing should be hardcoded.

---

# 18. Platform Configuration

Platform Configuration allows the Super Administrator to configure global platform settings.

Configuration should be data-driven rather than hardcoded.

---

## General Settings

- Platform Name
- Platform Logo
- Company Information
- Support Email
- Support Phone
- Default Language
- Default Time Zone
- Maintenance Mode

---

## Authentication Settings

The Super Administrator should be able to enable or disable:

- Email & Password
- Google OAuth
- Microsoft OAuth
- Magic Link Authentication
- Multi-Factor Authentication

Authentication providers should be configurable without code changes.

---

## Email Configuration

The platform should support configurable SMTP settings.

Examples:

- SMTP Server
- Port
- Username
- Password
- Encryption
- Sender Name
- Sender Email

Email templates should also be configurable.

---

## SMS Configuration

SMS settings should support configurable providers.

Examples:

- Provider Name
- API URL
- API Key
- Sender ID
- Default Country Code

SMS templates should be configurable.

---

## Trial Configuration

The platform should allow configuration of:

- Trial Duration
- Trial Package
- Reminder Schedule
- Expiry Behaviour

---

## Security Configuration

Examples include:

- Password Policy
- Session Timeout
- Maximum Failed Login Attempts
- Account Lock Duration
- MFA Enforcement

---

# 19. Notifications

Platform Core is responsible for all platform notifications.

Business modules may consume the notification service but should not implement their own notification engines.

---

## Email Notifications

Platform Core should support email notifications for:

- Welcome Email
- Email Verification
- Password Reset
- Workspace Invitation
- Trial Started
- Trial Expiry Reminder
- Trial Expired
- Subscription Activated
- Subscription Expiry Reminder
- User Added
- Security Alerts

---

## SMS Notifications

Platform Core should support SMS notifications for:

- OTP Verification
- Trial Reminder
- Subscription Activation
- Password Reset (Optional)
- Security Alerts

SMS functionality should be provider-independent.

The provider should be selected through Platform Configuration.

---

## Notification Templates

Email and SMS templates should be editable by the Super Administrator.

Templates should support placeholders such as:

- User Name
- Company Name
- Workspace Name
- Trial End Date
- Subscription End Date
- Verification Link
- Reset Password Link

# 20. Tenant Data Isolation

Business Suite is a multi-tenant SaaS platform.

Tenant isolation is the most critical architectural requirement.

Every tenant must only access its own data.

No tenant should ever be able to access another tenant's information.

---

## Tenant Ownership

Every business record must belong to a tenant.

All tenant-owned tables must include:

```text
tenant_id
```

Examples include:

- Customers
- Suppliers
- Products
- Invoices
- Purchases
- Employees
- Assets
- Reports
- Settings
- Roles
- Branches

Platform-wide tables (such as authentication providers or system settings) may exist without a tenant reference.

---

## Active Workspace Context

Every authenticated request must execute within an Active Workspace.

The Active Workspace determines:

- Current Tenant
- Current Company
- Current Branch
- Available Modules
- Available Permissions
- Accessible Data

Changing the Active Workspace immediately changes the application context.

---

## Tenant Isolation Rules

The platform must enforce tenant isolation through:

- Authentication
- Authorization
- Service Layer
- Database Queries
- PostgreSQL Row Level Security (RLS)

No module may bypass tenant isolation.

Every service must automatically apply the active `tenant_id` when querying or writing data.

---

# 21. Audit & Activity Logging

Platform Core must maintain a complete audit trail for all critical platform activities.

Audit logs support:

- Security
- Compliance
- Troubleshooting
- User accountability

Audit logging should not be optional for critical operations.

---

## Activities to Log

Examples include:

### Authentication

- Login
- Logout
- Failed Login
- Password Reset
- Password Change
- MFA Enabled
- MFA Disabled

### Tenant Management

- Tenant Created
- Tenant Updated
- Tenant Suspended
- Tenant Reactivated

### User Management

- User Created
- User Invited
- User Removed
- User Suspended
- Role Changed
- Permission Changed

### Subscription

- Trial Started
- Trial Expired
- Subscription Activated
- Subscription Extended
- Subscription Suspended

### Platform Configuration

- SMTP Updated
- SMS Provider Updated
- Authentication Provider Changed
- Feature Flag Changed

---

## Audit Log Information

Each audit record should include:

- Timestamp
- User
- Workspace
- Tenant
- Action
- Module
- Record Identifier
- Previous Value (where applicable)
- New Value (where applicable)
- IP Address
- Device Information (where available)

Audit logs should be immutable.

Users should never be able to edit or delete audit records.

---

# 22. Super Administration

The Super Administrator manages the Business Suite platform.

The Super Administrator does not belong to a tenant and operates at the platform level.

---

## Responsibilities

The Super Administrator can manage:

### Platform

- Platform Settings
- Branding
- Maintenance Mode
- Feature Flags

### Tenants

- View Tenants
- Activate Tenants
- Suspend Tenants
- Extend Trials
- Activate Subscriptions

### Packages

- Create Packages
- Edit Packages
- Enable Modules
- Configure Limits

### Authentication

- Authentication Providers
- Password Policies
- Session Settings
- MFA Policies

### Notifications

- Email Templates
- SMTP
- SMS Providers
- SMS Templates

### Reporting

- Platform Statistics
- Tenant Statistics
- Subscription Reports
- User Reports
- Audit Reports

---

# 23. Platform Principles

Every future module must respect the following principles.

## Modular

Modules must be independently developed and maintained.

---

## Tenant Aware

Every business module must respect tenant isolation.

---

## Permission Driven

No functionality should be accessible without the appropriate permission.

---

## Package Driven

Modules and features must respect the active subscription package.

---

## Configuration Driven

Business rules should be configurable whenever practical.

Avoid hardcoded values.

---

## API First

Business logic should be exposed through reusable service classes and APIs.

Future mobile applications should consume the same business logic as the web application.

---

# 24. Success Criteria

Platform Core is considered complete when all of the following are achieved.

## Registration

- Businesses can register online.
- A Platform User is created.
- A Tenant is created.
- A Workspace is created.
- The Workspace Owner is assigned.
- Email verification is completed.

---

## Authentication

- Users can log in.
- Users can reset passwords.
- Google OAuth works.
- Microsoft OAuth works.
- Magic Link authentication works.
- MFA can be enabled.

---

## Workspace

- Users can belong to multiple workspaces.
- Users can switch between workspaces.
- Default workspaces are supported.

---

## Administration

- Workspace Owners can invite users.
- Workspace Administrators can manage users.
- Roles can be created.
- Permissions can be assigned.

---

## Subscription

- Trial activates automatically.
- Trial reminders are sent.
- Trial expires correctly.
- Super Administrator can activate subscriptions.
- Packages control available modules.

---

## Notifications

- Email notifications function correctly.
- SMS notifications function correctly.
- Notification templates are configurable.

---

## Security

- Tenant isolation is enforced.
- Permissions are respected.
- Audit logs are generated.
- Row Level Security is implemented.

---

# 25. Implementation Rules

The implementation must follow the following architecture documents.

- docs/architecture/Architecture.md
- docs/architecture/TechStack.md
- docs/architecture/FolderStructure.md
- docs/architecture/TenantArchitecture.md
- docs/architecture/CodingStandards.md
- docs/architecture/Security.md

---

## Technology Stack

Implementation must use:

- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui
- React Hook Form
- Zod
- React Context API
- Supabase
- PostgreSQL

---

## Development Rules

Lovable, Cursor, ChatGPT, and future AI development tools must:

- Follow the approved architecture documentation.
- Follow the feature-based folder structure.
- Use the Service Layer pattern.
- Avoid direct database calls from UI components.
- Avoid hardcoded business rules.
- Respect tenant isolation.
- Respect package limitations.
- Respect permissions.
- Produce reusable and maintainable code.

---

# 26. Acceptance Criteria

Platform Core Version 1.0 is accepted when:

- All functional requirements in this specification are implemented.
- All architecture documents are followed.
- Tenant isolation is verified.
- Multi-workspace support is operational.
- Authentication providers function correctly.
- Trial and subscription lifecycle is operational.
- Email and SMS notifications are operational.
- Role-Based Access Control is fully implemented.
- Platform configuration is data-driven.
- Audit logging is operational.
- The platform is ready to support future business modules without architectural redesign.

---

# 27. Conclusion

Platform Core is the foundation of the Business Suite platform.

It establishes the architectural, security, tenancy, authentication, authorization, subscription, and administration standards that every future module must follow.

By implementing Platform Core according to this specification, Business Suite will provide a secure, scalable, configurable, and maintainable SaaS foundation capable of supporting multiple businesses, multiple workspaces, and future modules without requiring significant architectural changes.
