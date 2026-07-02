# Platform Core Workflow Specification

Version: 1.0  
Status: Draft  
Module: Platform Core

---

# 1. Purpose

This document defines the workflows for Platform Core only.

It covers the business processes required for tenant onboarding, authentication, workspace management, subscriptions, users, roles, permissions, notifications, and platform administration.

Business module workflows such as CRM, Sales, Inventory, Finance, HR, Procurement, and POS are outside the scope of this document and will be defined in their own module specifications.

---

# 2. Scope

This document covers:

- Business Registration
- Email Verification
- Login
- Password Reset
- Magic Link Login
- Google Login
- Microsoft Login
- MFA Verification
- Workspace Selection
- Workspace Switching
- Company Profile Setup
- Branch Management
- User Invitation
- Existing User Invitation
- Role Management
- Permission Assignment
- Trial Activation
- Trial Expiry
- Manual Subscription Activation
- Package Management
- Module Access
- Email Notification
- SMS Notification
- Audit Logging
- Super Admin Tenant Management

---

# 3. Workflow Rules

All workflows must follow these rules:

- Every authenticated workflow must have an active user.
- Every tenant workflow must have an active workspace.
- Every tenant-owned operation must include `tenant_id`.
- Every restricted action must check permissions.
- Every module access request must check subscription/package access.
- Every critical action must create an audit log.
- Every async action must show loading feedback in the UI.
- Every failed action must return a clear error message.
- Workflows must not bypass the service layer.
- Workflows must follow the Platform Core database specification.

---

# 4. Business Registration & Trial Activation

## Purpose

Allow a business to register itself, create a workspace, create the first user, and start a trial subscription.

---

## Workflow

````text
Public User
    ↓
Open Landing Page
    ↓
Select Package / Start Trial
    ↓
Open Registration Form
    ↓
Enter Business Details
    ↓
Enter Workspace Owner Details
    ↓
Validate Form
    ↓
Create Platform User
    ↓
Send Email Verification
    ↓
Verify Email
    ↓
Create Tenant
    ↓
Create Company
    ↓
Create Default Branch
    ↓
Create Workspace Owner Membership
    ↓
Create Trial Subscription
    ↓
Seed Default Roles
    ↓
Seed Default Permissions
    ↓
Redirect to Workspace Dashboard



---

# 5. Authentication Workflows

Authentication is managed by Platform Core.

All business modules rely on Platform Core for authentication.

---

# 5.1 Email & Password Login

## Purpose

Authenticate an existing Platform User using email and password.

---

## Workflow

```text
Open Login Page
        │
        ▼
Enter Email
        │
        ▼
Enter Password
        │
        ▼
Validate Credentials
        │
        ▼
Authentication Successful?
        │
   ┌────┴─────┐
   │          │
  No         Yes
   │          │
Display     Load Tenant Memberships
Error        │
             ▼
      Single Workspace?
             │
      ┌──────┴──────┐
      │             │
     Yes           No
      │             │
      ▼             ▼
Open Dashboard   Workspace Selector
````

---

## Business Rules

- Email must exist.
- Password must be correct.
- User account must be active.
- Email must be verified.
- Suspended users cannot log in.
- Disabled users cannot log in.

---

## Notifications

Optional:

- Login Alert Email
- Login Alert SMS

---

## Audit Logs

Create:

- Login Successful
- Login Failed

---

# 5.2 Google Login

## Purpose

Authenticate using Google OAuth.

---

## Workflow

```text
Click Google Login

↓

Google Authentication

↓

User Exists?

↓

Yes

↓

Authenticate

↓

Load Memberships

↓

Workspace Selection

↓

Dashboard
```

If user does not exist:

```text
Google Authentication

↓

Create Platform User

↓

Create Membership (if invited)

↓

Dashboard
```

---

## Rules

- Email becomes the unique identifier.
- Duplicate accounts must never be created.
- Existing users should automatically link to Google.

---

# 5.3 Microsoft Login

Workflow is identical to Google Login.

Authentication provider changes.

All business rules remain the same.

---

# 5.4 Magic Link Login

## Workflow

```text
Enter Email

↓

Validate Email

↓

Generate Secure Token

↓

Email Magic Link

↓

User Opens Link

↓

Validate Token

↓

Authenticate User

↓

Load Memberships

↓

Dashboard
```

---

## Rules

- Links expire.
- Links are single-use.
- Tokens should never be reusable.

---

# 5.5 Multi-Factor Authentication

## Purpose

Provide additional account security.

---

## Workflow

```text
Email & Password

↓

Authentication Successful

↓

MFA Enabled?

↓

No

↓

Dashboard

↓

Yes

↓

Send Verification Code

↓

Enter Code

↓

Validate

↓

Dashboard
```

---

## Rules

- MFA is optional in Version 1.
- Platform Administrator may enforce MFA in future.
- Failed attempts should be limited.

---

# 5.6 Forgot Password

## Workflow

```text
Forgot Password

↓

Enter Email

↓

Validate User

↓

Generate Reset Token

↓

Email Reset Link

↓

Open Reset Link

↓

Enter New Password

↓

Validate Password

↓

Update Password

↓

Success
```

---

## Rules

- Reset links expire.
- Tokens are single-use.
- Password must satisfy password policy.

---

# 5.7 Password Change

Authenticated users may change passwords.

Workflow:

```text
Current Password

↓

Validate

↓

Enter New Password

↓

Confirm Password

↓

Save

↓

Success
```

---

# 5.8 Logout

## Workflow

```text
User

↓

Logout

↓

Clear Session

↓

Clear Workspace Context

↓

Redirect Login
```

---

## Audit Logs

Create:

- Logout

---

# 5.9 Session Management

Sessions should support:

- Automatic Refresh
- Session Timeout
- Remember Me
- Multiple Devices (Future)

Expired sessions should redirect users to Login.

---

# 5.10 Authentication Failure

Authentication may fail because of:

- Invalid Password
- Invalid Email
- Suspended Account
- Disabled Account
- Email Not Verified
- Expired Token
- Invalid Token
- Locked Account

Users should receive clear, non-technical error messages.

Avoid exposing sensitive security information.

---

# 6. Workspace & User Management Workflows

Workspace Management controls how users access one or more tenant workspaces.

A Platform User may belong to one or more workspaces.

Each workspace represents one tenant.

---

# 6.1 Workspace Selection

## Purpose

Allow users belonging to multiple workspaces to choose the workspace they wish to access.

---

## Workflow

```text
Authentication Successful
        │
        ▼
Load Tenant Memberships
        │
        ▼
Only One Workspace?
        │
   ┌────┴────┐
   │         │
  Yes       No
   │         │
   ▼         ▼
Open      Display Workspace Selector
Dashboard        │
                 ▼
        User Selects Workspace
                 │
                 ▼
        Load Workspace Context
                 │
                 ▼
           Open Dashboard
```

---

## Business Rules

- Workspace list should display:
  - Company Logo
  - Company Name
  - User Role
  - Subscription Status
- Users may set a default workspace.
- Users may switch workspaces at any time.

---

## Audit Logs

Create:

- Workspace Selected

---

# 6.2 Workspace Switching

## Purpose

Allow authenticated users to switch between workspaces without logging out.

---

## Workflow

```text
Workspace Switcher

↓

Select Workspace

↓

Validate Membership

↓

Load Tenant

↓

Load Company

↓

Load Branches

↓

Load Permissions

↓

Load Modules

↓

Refresh Application Context

↓

Dashboard
```

---

## Business Rules

Workspace switching must refresh:

- Active Tenant
- Company
- Branches
- Permissions
- Available Modules
- Dashboard
- Navigation

No browser refresh should occur.

---

## Audit Logs

Create:

- Workspace Switched

---

# 6.3 Invite New User

## Purpose

Invite someone who has never used Business Suite.

---

## Workflow

```text
Workspace Administrator

↓

Invite User

↓

Enter Email

↓

Validate Email

↓

User Exists?

↓

No

↓

Create Invitation

↓

Send Invitation Email

↓

User Opens Invitation

↓

Create Platform Account

↓

Verify Email

↓

Create Tenant Membership

↓

Join Workspace

↓

Dashboard
```

---

## Business Rules

- Invitations expire.
- Invitations are single-use.
- Email must be unique.
- Membership remains pending until accepted.

---

## Notifications

Send:

- Invitation Email
- Invitation Reminder (Future)

---

## Audit Logs

Create:

- Invitation Sent
- Invitation Accepted

---

# 6.4 Invite Existing Platform User

## Purpose

Invite an existing Platform User to another workspace.

---

## Workflow

```text
Workspace Administrator

↓

Invite User

↓

Enter Existing Email

↓

User Exists

↓

Create Tenant Membership

↓

Notify User

↓

User Logs In

↓

Workspace Appears

↓

User Selects Workspace

↓

Dashboard
```

---

## Business Rules

- Never create duplicate platform users.
- Only create a new tenant membership.
- Existing profile information is reused.

---

## Notifications

Send:

- Workspace Invitation
- Workspace Added Notification

---

## Audit Logs

Create:

- Membership Created

---

# 6.5 Accept Invitation

## Purpose

Allow invited users to join a workspace.

---

## Workflow

```text
Open Invitation

↓

Validate Token

↓

Invitation Valid?

↓

Yes

↓

Existing User?

↓

No

↓

Create Account

↓

Verify Email

↓

Accept Membership

↓

Dashboard

↓

Yes

↓

Accept Membership

↓

Dashboard
```

---

## Business Rules

- Invitation tokens expire.
- Invitations cannot be reused.
- Accepted invitations become Active memberships.

---

## Audit Logs

Create:

- Invitation Accepted

---

# 6.6 Remove User

## Workflow

```text
Administrator

↓

Remove User

↓

Confirmation

↓

Deactivate Membership

↓

Refresh Workspace
```

---

## Rules

Removing a user only removes the membership.

The Platform User account must remain.

---

## Audit Logs

Create:

- Membership Removed

---

# 6.7 Suspend User

## Workflow

```text
Administrator

↓

Suspend Membership

↓

Membership Disabled

↓

User Loses Access
```

---

## Rules

Suspension affects only the current workspace.

The user may continue accessing other workspaces where they have active memberships.

---

## Audit Logs

Create:

- Membership Suspended

---

# 6.8 Workspace Owner Transfer (Future)

A Workspace Owner may transfer ownership to another Workspace Administrator.

Workflow:

```text
Current Owner

↓

Select New Owner

↓

Confirmation

↓

Transfer Ownership

↓

Update Memberships

↓

Success
```

Only one active Workspace Owner should exist per workspace.

---

# 7. Organization Management Workflows

Organization Management allows each tenant to configure its business structure.

Every tenant owns its own organization hierarchy.

Version 1 includes:

- Company
- Branches

Future versions may include:

- Departments
- Cost Centers
- Warehouses
- Business Units
- Projects

---

# 7.1 Company Setup

## Purpose

Create and maintain the company profile.

The company profile is automatically created during registration.

---

## Workflow

```text
Tenant Registration

↓

Create Company

↓

Assign Default Settings

↓

Create Default Branch

↓

Company Ready
```

---

## Company Information

The company profile includes:

- Company Name
- Registration Number
- Tax Identification Number
- Email
- Phone
- Website
- Address
- Country
- Currency
- Time Zone
- Logo
- Business Type
- Industry

---

## Business Rules

- Every tenant owns exactly one company.
- Company information may be updated.
- Company deletion is not permitted.
- Logo uploads should support preview.

---

## Audit Logs

Create:

- Company Created
- Company Updated

---

# 7.2 Branch Management

## Purpose

Allow businesses to manage branches.

---

## Workflow

```text
Branch List

↓

Create Branch

↓

Validate

↓

Save

↓

Success
```

---

## Branch Information

Fields include:

- Branch Name
- Branch Code
- Address
- Phone
- Email
- Manager
- Status

---

## Business Rules

- Every tenant must have at least one branch.
- Branch codes must be unique within a tenant.
- Branches may be activated or deactivated.
- Historical data remains attached to inactive branches.

---

## Branch Status

Supported statuses:

- Active
- Inactive

Statuses should come from Reference Data.

---

## Audit Logs

Create:

- Branch Created
- Branch Updated
- Branch Activated
- Branch Deactivated

---

# 7.3 Default Branch

Every tenant has one default branch.

Rules:

- Created automatically during registration.
- Cannot be deleted while it is the only branch.
- May be changed by an administrator.

---

# 7.4 Branch Assignment

Users may belong to one or more branches.

Workflow:

```text
Administrator

↓

Select User

↓

Assign Branches

↓

Save

↓

Permissions Updated
```

---

## Business Rules

- Users may belong to multiple branches.
- One branch may be marked as the default branch.
- Branch access controls business data visibility.

---

# 7.5 Company Settings

Workspace Administrators manage:

- Business Information
- Branding
- Currency
- Time Zone
- Date Format
- Number Format
- Language (Future)
- Business Preferences

Updates take effect immediately unless otherwise specified.

---

# 7.6 Organization Preferences

Organization preferences define tenant-specific behavior.

Examples:

- Default Currency
- Fiscal Year Start
- Financial Period
- Decimal Precision
- Default Tax
- Working Days
- Default Branch

Preferences should be configurable and stored in the database.

---

# 7.7 Future Organization Structure

The architecture should support expansion to include:

- Departments
- Cost Centers
- Warehouses
- Business Units
- Projects

These entities should integrate with the existing organization model without requiring architectural redesign.
