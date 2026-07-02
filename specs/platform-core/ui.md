# Platform Core UI Specification

Version: 1.0  
Status: Draft  
Module: Platform Core

---

# 1. Purpose

This document defines the user interface structure for Platform Core.

The UI is feature-driven, not page-driven. Each feature includes its screens, components, actions, permissions, validations, and navigation rules.

This document must guide Lovable and Cursor when generating Platform Core screens and components.

---

# 2. UI Principles

The Platform Core UI must be:

- Clean
- Simple
- Responsive
- Modular
- Tenant-aware
- Permission-driven
- Package-aware
- Easy to navigate
- Consistent with shadcn/ui and Tailwind CSS

---

# 3. UI Layouts

Platform Core uses the following layouts:

## Public Layout

Used for:

- Landing Page
- Features Page
- Pricing Page
- Contact Page
- Login
- Register
- Forgot Password
- Email Verification

## Auth Layout

Used for:

- Login
- Register
- Password Reset
- MFA Verification
- Magic Link Confirmation

## Workspace Layout

Used by authenticated workspace users.

Includes:

- Sidebar
- Top Navigation
- Workspace Switcher
- User Menu
- Notifications
- Module Navigation

## Super Admin Layout

Used by Super Administrators.

Includes:

- Platform Sidebar
- Platform Dashboard
- Tenant Management
- Package Management
- Platform Settings
- Audit Logs

---

# 4. Core Navigation

## Public Navigation

- Home
- Features
- Pricing
- Contact
- Login
- Register

## Workspace Navigation

- Dashboard
- Company
- Branches
- Users
- Roles & Permissions
- Subscription
- Notifications
- Profile
- Settings

## Super Admin Navigation

- Dashboard
- Tenants
- Packages
- Modules
- Subscriptions
- Platform Users
- Authentication Providers
- Email Providers
- SMS Providers
- Notification Templates
- Platform Settings
- Audit Logs
- Activity Logs

# 2.1 UI Interaction Standards

The following UI standards apply to all Platform Core screens.

## Forms

All create and edit forms should open in modals unless the form is too large or requires a multi-step workflow.

Examples:

- Add User → Modal
- Add Branch → Modal
- Add Role → Modal
- Add Package → Modal
- Edit Company Profile → Page or section form
- Register Business → Full page
- Configure Platform Settings → Full page

## Form Validation

Forms must use real validation through:

- React Hook Form
- Zod

HTML-only validation such as `required` attributes must not be relied on as the primary validation mechanism.

Validation rules must be defined in reusable schema files.

Example:

```text
src/features/platform/validators/userSchema.ts
src/features/platform/validators/branchSchema.ts



---

# 5. Public Platform Features

## 5.1 Landing Page

Purpose:

Introduce Business Suite and direct users to register or log in.

Sections:

- Hero section
- Product overview
- Key modules
- Benefits
- Industries served
- Pricing preview
- Call to action
- Footer

Actions:

- Register
- Login
- View Pricing
- Contact

---

## 5.2 Pricing Page

Purpose:

Show available packages and trial options.

Each package card should display:

- Package name
- Description
- Price or “Contact Sales”
- Trial availability
- Maximum users
- Maximum branches
- Included modules
- Call-to-action button

Actions:

- Start Trial
- Contact Support

---

## 5.3 Registration Page

Purpose:

Allow a business to register and create a workspace.

Fields:

Business:

- Business Name
- Trading Name
- Industry
- Country
- Currency
- Time Zone
- Phone
- Email

Workspace Owner:

- First Name
- Last Name
- Email
- Password
- Confirm Password

Rules:

- Use full page, not modal.
- Use React Hook Form and Zod validation.
- Show loading spinner on submit.
- Create user, tenant, company, workspace membership, and trial subscription.

---

## 5.4 Login Page

Purpose:

Authenticate platform users.

Supported login options:

- Email and Password
- Google
- Microsoft
- Magic Link

Actions:

- Login
- Login with Google
- Login with Microsoft
- Send Magic Link
- Forgot Password

---

## 5.5 Forgot Password Page

Purpose:

Allow users to reset passwords.

Fields:

- Email Address

Actions:

- Send Reset Link

---

## 5.6 Email Verification Page

Purpose:

Confirm user email verification.

States:

- Verification successful
- Verification expired
- Verification failed
- Resend verification email
```

---

# 6. Workspace Features

Workspace Features are available to authenticated users after selecting or entering a workspace.

Every feature must respect:

- Tenant Isolation
- Role-Based Permissions
- Subscription Package
- Active Workspace

---

# 6.1 Dashboard

## Purpose

The Dashboard provides an overview of the active workspace.

It should display only information available within the current workspace.

---

## Dashboard Widgets

Suggested widgets include:

### Company Summary

- Company Name
- Package
- Trial Status
- Subscription Status

---

### Statistics

Examples:

- Total Users
- Total Branches
- Active Modules
- Pending Invitations

---

### Quick Actions

Examples:

- Invite User
- Add Branch
- Edit Company
- View Subscription

---

### Notifications

Recent platform notifications.

---

### Recent Activity

Recent user activities.

Examples:

- User invited
- Branch created
- Subscription activated

---

## Actions

- Refresh Dashboard
- Navigate to feature pages

---

# 6.2 Company Management

## Purpose

Manage company information for the active workspace.

Each tenant owns one company in Version 1.

---

## Screen Components

- Company Information Card
- Contact Information
- Branding
- Business Settings

---

## Editable Fields

- Legal Name
- Trading Name
- Registration Number
- Tax Identification Number
- Industry
- Logo
- Email
- Phone
- Website
- Address
- Country
- Currency
- Time Zone
- Language
- Financial Year

---

## Actions

- Edit Company
- Upload Logo
- Save Changes

---

## UI Behaviour

- Display information in sections.
- Editing should occur within a modal where practical.
- Large forms may use a dedicated page.

---

# 6.3 Branch Management

## Purpose

Manage company branches.

---

## List View

Display:

- Branch Name
- Code
- Manager
- Status
- Phone
- Location

---

## Actions

- Add Branch
- Edit Branch
- View Branch
- Activate Branch
- Deactivate Branch

---

## Add Branch

Open using a modal.

Fields:

- Branch Name
- Code
- Address
- Country
- City
- Phone
- Email
- Branch Manager

---

## Validation

- Branch Name required.
- Branch limit should respect the subscription package.
- Prevent duplicate branch codes within a tenant.

---

# 6.4 Workspace Users

## Purpose

Manage users belonging to the active workspace.

---

## List View

Display:

- Name
- Email
- Role
- Status
- Last Login
- Date Joined

---

## Actions

- Invite User
- Edit User Role
- Suspend User
- Remove User
- Resend Invitation

---

## Invite User

Use a modal.

Fields:

- Email
- Role
- Default Branch (Optional)

Workflow:

If user exists:

- Create Tenant Membership.

If user does not exist:

- Create Platform User.
- Send Invitation.
- Create Membership after acceptance.

---

## Validation

- Email required.
- Valid email format.
- Prevent duplicate memberships.

---

# 6.5 Roles

## Purpose

Manage workspace roles.

---

## List View

Display:

- Role Name
- Description
- Number of Users

---

## Actions

- Add Role
- Edit Role
- Delete Role
- Assign Permissions

---

## Add Role

Use a modal.

Fields:

- Role Name
- Description

---

## Validation

- Role Name required.
- Role Name must be unique within the workspace.

---

# 6.6 Permissions

## Purpose

Assign permissions to workspace roles.

---

## Screen Layout

Permission Matrix.

Columns:

- View
- Create
- Edit
- Delete
- Approve
- Reject
- Export
- Print
- Configure

Rows:

Grouped by module.

Examples:

CRM

Sales

Inventory

Finance

Reports

Platform

---

## Actions

- Select All
- Clear All
- Save

---

## Validation

Permissions should only be editable by authorized users.

Changes should be audited.

---

# 6.7 User Profile

## Purpose

Allow users to manage their personal profile.

---

## Sections

- Personal Information
- Password
- Authentication
- Preferences

---

## Editable Fields

- Name
- Phone
- Profile Picture
- Language
- Time Zone

---

## Security

Users may:

- Change Password
- Enable MFA
- Disable MFA
- View Active Sessions (Future)

---

# 6.8 Workspace Switching

## Purpose

Allow users to switch between workspaces.

---

## Component

Workspace Switcher.

Displayed in the top navigation.

---

## Information Displayed

- Workspace Name
- Company Logo
- User Role
- Subscription Status

---

## Actions

- Switch Workspace
- Set Default Workspace

---

## Behaviour

Switching workspaces should immediately update:

- Sidebar
- Permissions
- Company Information
- Modules
- Dashboard
- Data Context

No page refresh should be required.
