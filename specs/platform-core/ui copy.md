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
