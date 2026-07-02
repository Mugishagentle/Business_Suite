# Business Suite Development Standards

Version: 1.0

Status: Approved

Owner: Business Suite Engineering Team

Last Updated: July 2026

---

# 1. Purpose

This document defines the official development standards for the Business Suite platform.

It establishes the architectural, coding, UI, UX, performance, security, and development conventions that every developer and AI-assisted development tool must follow.

The objective is to ensure that all modules within the Business Suite are built consistently, remain maintainable, and provide a unified user experience.

This document applies to:

- Platform Core
- CRM
- Sales
- Inventory
- Procurement
- Finance
- HR
- POS
- Reports
- Dashboard
- Mobile Applications
- Future Modules

---

# 2. Development Philosophy

Business Suite follows a modern enterprise software architecture.

The platform must be:

- Modular
- Scalable
- Secure
- Maintainable
- Configurable
- Multi-Tenant
- API-First
- Mobile Ready
- AI Friendly

Every feature should be developed with future expansion in mind.

Avoid temporary solutions that require redesign later.

---

# 3. General Development Principles

Every implementation should follow these principles.

## Single Responsibility Principle

Every component, service, hook, and utility should have one clear responsibility.

Avoid components that perform multiple unrelated tasks.

---

## Reusability

Whenever functionality is likely to be reused, it should be extracted into reusable components, hooks, services, or utilities.

Avoid duplicate code.

---

## Separation of Concerns

Business logic must never reside inside UI components.

Business logic belongs inside services.

Validation belongs inside validators.

State belongs inside hooks or context providers.

UI components should primarily focus on rendering.

---

## Configuration over Hardcoding

Business rules should be configurable whenever practical.

Examples include:

- Status values
- Dropdown values
- Notification templates
- Authentication providers
- Package limits
- Platform settings

Avoid hardcoded values.

---

## Consistency

Every feature should follow the same folder structure.

Every screen should behave consistently.

Every form should behave consistently.

Every table should behave consistently.

Users should never have to relearn how different modules work.

---

# 4. Technology Stack

The Business Suite platform uses the following technologies.

## Frontend

- React
- TypeScript
- Vite

---

## Styling

- Tailwind CSS
- shadcn/ui

---

## Forms

- React Hook Form
- Zod

---

## State Management

- React Context API
- React Hooks

Avoid unnecessary global state.

---

## Backend

- Supabase

---

## Database

- PostgreSQL

---

## Authentication

- Supabase Authentication

Supported providers:

- Email & Password
- Google OAuth
- Microsoft OAuth
- Magic Links
- Multi-Factor Authentication

---

## Version Control

- Git
- GitHub

Git Flow:

- main
- develop
- feature/\*
- hotfix/\*
- release/\*

---

# 5. Project Structure

The application follows a feature-based architecture.

```text
src/

features/

shared/

components/

hooks/

services/

layouts/

contexts/

types/

utils/

validators/

routes/
```

Each feature should remain independent.

Future modules should integrate without changing the overall project architecture.

---

# 6. Folder Standards

Every feature should follow this structure.

```text
feature-name/

components/

pages/

hooks/

services/

validators/

types/

contexts/

utils/

routes/

constants/
```

Optional folders:

```text
assets/

tests/

```

Each folder should have a single responsibility.

---

# 7. Naming Conventions

## Components

Use PascalCase.

Examples:

```text
UserTable.tsx

CompanyCard.tsx

BranchModal.tsx
```

---

## Hooks

Prefix with use.

Examples:

```text
useUsers.ts

useBranches.ts

usePermissions.ts
```

---

## Services

Suffix with Service.

Examples:

```text
UserService.ts

TenantService.ts

SubscriptionService.ts
```

---

## Validators

Suffix with Schema.

Examples:

```text
userSchema.ts

branchSchema.ts

companySchema.ts
```

---

## Types

Suffix with Type or Interface where appropriate.

Examples:

```text
User.ts

Branch.ts

Tenant.ts
```

---

## Constants

Use UPPER_SNAKE_CASE.

Examples:

```text
DEFAULT_PAGE_SIZE

MAX_UPLOAD_SIZE

DEFAULT_LANGUAGE
```

---

# 8. Routing Standards

Business Suite is a Single Page Application.

Internal navigation must always use React Router.

---

## Navigation Rules

Use:

- Link
- NavLink
- useNavigate()

Do not use:

```html
<a href="/users"></a>
```

except for:

- External websites
- File downloads
- mailto:
- tel:

---

## Route Structure

Routes should be grouped by feature.

Examples:

```text
/platform/dashboard

/platform/company

/platform/users

/platform/branches

/platform/subscription

/admin/dashboard

/admin/tenants

/admin/packages

/admin/settings
```

---

## Route Protection

Protected routes must verify:

- Authentication
- Active Workspace
- Tenant Membership
- Subscription Status
- Module Access
- User Permissions

Unauthorized users should be redirected appropriately.

---

## Navigation Performance

Navigation should never reload the browser.

React Router should provide seamless client-side navigation.

Use lazy loading for large feature modules.

Preserve:

- User Session
- Active Workspace
- Navigation State
- Filters
- Search Parameters where appropriate.

---

# 9. React Component Standards

Business Suite follows a component-driven architecture.

Components should be:

- Small
- Reusable
- Focused
- Easy to test
- Easy to maintain

---

## Single Responsibility

Every component should have one responsibility.

Examples:

Good

```text
UserTable
BranchCard
CompanyHeader
RoleSelector
```

Avoid components that perform multiple unrelated tasks.

---

## Component Size

As a general guideline:

- Small Components: 20–100 lines
- Medium Components: 100–250 lines
- Large Components: 250–400 lines

If a component becomes too large:

- Extract child components
- Move business logic into hooks
- Move API logic into services

---

## Component Structure

Recommended order:

```text
Imports

Types

Constants

Hooks

State

Effects

Functions

Render
```

---

## Component Naming

Use PascalCase.

Examples:

```text
UserList.tsx

BranchModal.tsx

CompanyProfile.tsx

DashboardCard.tsx
```

---

# 10. Layout Standards

Business Suite uses consistent layouts.

Supported layouts:

- Public Layout
- Authentication Layout
- Workspace Layout
- Super Admin Layout

Every page should inherit from one of these layouts.

Do not create custom layouts unless necessary.

---

## Page Structure

Every page should contain:

- Page Title
- Breadcrumb
- Primary Action
- Search (where applicable)
- Filters (where applicable)
- Content Area
- Pagination (where applicable)

Example:

```text
Users

Dashboard > Administration > Users

[Search]

[Filters]

[Invite User]

---------------------------------

User Table

---------------------------------

Pagination
```

---

# 11. Navigation Standards

Navigation must always use React Router.

Use:

- Link
- NavLink
- useNavigate()

Do not use HTML `<a href="">` for internal navigation.

Use `<a>` only for:

- External websites
- Downloads
- Email links
- Telephone links

---

## Breadcrumbs

Every feature page should display breadcrumbs.

Example:

```text
Dashboard

>

Administration

>

Users
```

---

## Active Navigation

The current navigation item should always be highlighted.

---

# 12. Form Standards

Forms are one of the most important parts of the application.

Every form should follow identical behaviour.

---

## Form Library

Every form must use:

- React Hook Form
- Zod

Do not rely on HTML validation.

Avoid:

```html
required maxlength minlength
```

Validation belongs inside Zod schemas.

---

## Form Layout

Forms should use consistent spacing.

Fields should be grouped logically.

Examples:

Company Information

↓

Contact Information

↓

Address

↓

Settings

---

## Form Submission

Every form must:

- Validate before submit
- Disable submit button
- Display loading spinner
- Prevent duplicate submissions
- Display success message
- Display validation errors

---

## Form Reset

Forms should support:

- Reset
- Cancel
- Close

Where appropriate.

---

# 13. Modal Standards

Create and Edit operations should use modals by default.

Examples:

Use Modal

- Add User
- Edit User
- Add Branch
- Edit Branch
- Add Role
- Edit Role
- Invite User

Use Full Page

- Registration
- Company Profile
- Platform Settings
- Multi-step Wizards

---

## Modal Behaviour

Every modal should include:

- Title
- Description (Optional)
- Close Button
- Cancel Button
- Save Button

---

## Modal Size

Suggested sizes:

Small

Confirmation Dialogs

Medium

Simple Forms

Large

Complex Forms

Extra Large

Multi-section Forms

---

# 14. Validation Standards

Every feature should define reusable validation schemas.

Store validators inside:

```text
validators/
```

Example:

```text
userSchema.ts

branchSchema.ts

companySchema.ts
```

---

Validation rules should include:

- Required
- Length
- Format
- Numeric
- Date
- Business Rules

Business rules should not exist inside components.

---

# 15. Button Standards

Buttons should clearly communicate their purpose.

Types:

- Primary
- Secondary
- Outline
- Ghost
- Destructive

---

## Loading Buttons

Every asynchronous button must:

- Show loading spinner
- Disable itself
- Prevent duplicate clicks

Examples:

- Save
- Submit
- Invite
- Delete
- Approve
- Reject
- Upload
- Activate Subscription

---

## Icons

Buttons may include icons.

Examples:

＋ Add

✏ Edit

🗑 Delete

✓ Approve

⬇ Export

Icons should improve usability, not replace labels.

---

# 16. Tables & Lists

All list screens should behave consistently.

---

## Features

Every table should support:

- Search
- Sorting
- Pagination
- Responsive layout

Where applicable:

- Column filtering
- Export
- Bulk actions
- Column visibility

---

## Empty State

Example:

"No users have been added yet."

Display:

- Illustration (optional)
- Friendly message
- Primary action button

---

## Loading State

Use skeleton loaders instead of blank screens.

Avoid flashing content.

---

## Error State

Display:

- Error message
- Retry button

Do not expose technical errors to users.

---

# 17. Feedback Standards

Every action should provide immediate feedback.

---

## Success

Use toast notifications.

Examples:

User created successfully.

Branch updated successfully.

Subscription activated.

---

## Error

Use:

- Toast
- Inline validation
- Dialog

depending on severity.

---

## Confirmation

Destructive actions require confirmation.

Examples:

Delete User

Suspend Tenant

Deactivate Package

Cancel Subscription

---

# 18. Empty States

Every feature should have a meaningful empty state.

Instead of:

"No records"

Use:

"No users have been invited yet."

Provide an action:

"Invite User"

---

# 19. Loading Standards

Every page should have a loading state.

Every table should have a loading state.

Every form should have a loading state.

Every button should have a loading state.

Use:

- Skeletons
- Loading Spinners

Avoid blocking the interface unnecessarily.

---

# 20. Error Handling

The application should fail gracefully.

Common scenarios:

- Network failure
- Server error
- Validation failure
- Permission denied
- Subscription expired
- Session expired

Provide clear, user-friendly messages and recovery actions where possible.

---

# 21. Service Layer Standards

Business logic must never be implemented inside React components.

Every feature communicates with the backend through a dedicated Service Layer.

UI Components

↓

Hooks

↓

Services

↓

Supabase / APIs

This separation improves maintainability, testing, and code reuse.

---

## Service Responsibilities

Services are responsible for:

- CRUD Operations
- Business Logic
- Data Transformation
- Error Handling
- Logging
- API Communication

Services should not render UI.

---

## Service Structure

Each feature should contain its own services.

Example:

```text
features/platform/

services/

UserService.ts

TenantService.ts

BranchService.ts

SubscriptionService.ts

RoleService.ts
```

Avoid creating one large service for multiple unrelated features.

---

# 22. API Standards

Business Suite follows an API-First architecture.

Every business operation should be implemented as a reusable service that can be consumed by:

- Web Application
- Mobile Application
- Desktop Application (Future)
- Third-Party Integrations
- AI Services

---

## Response Structure

Every service should return a consistent response object.

Example:

```typescript
{
    success: true,
    message: "User created successfully.",
    data: {...},
    errors: null
}
```

Errors:

```typescript
{
    success: false,
    message: "Validation failed.",
    data: null,
    errors: [...]
}
```

---

## Error Handling

Services should handle:

- Validation Errors
- Authentication Errors
- Permission Errors
- Network Errors
- Unexpected Exceptions

UI components should display user-friendly messages.

---

# 23. React Hooks Standards

Business logic shared between components should be extracted into custom hooks.

Examples:

```text
useUsers()

useBranches()

usePermissions()

useSubscription()

useWorkspace()

useNotifications()
```

---

## Hook Responsibilities

Hooks may manage:

- State
- API Calls
- Loading
- Errors
- Refresh Logic

Hooks should never render UI.

---

# 24. State Management

Business Suite primarily uses React's built-in capabilities.

Use:

- React Context
- useState
- useReducer
- useMemo
- useCallback

Avoid introducing additional global state libraries unless a justified need arises.

---

## Global State

Global Contexts may include:

- Authentication
- Active Workspace
- Theme
- Notifications
- User Preferences

Everything else should remain feature-local.

---

# 25. Authentication Standards

Authentication is managed centrally.

Authentication providers include:

- Email & Password
- Google
- Microsoft
- Magic Link
- Multi-Factor Authentication

Authentication should never be duplicated inside modules.

Modules consume authentication from Platform Core.

---

## Session Handling

Sessions should support:

- Automatic Refresh
- Automatic Logout
- Session Timeout
- Remember Me

Expired sessions should redirect users to Login.

---

# 26. Authorization Standards

Business Suite uses Role-Based Access Control (RBAC).

Permissions are evaluated within the active workspace.

Every protected action must verify:

- Authentication
- Active Tenant Membership
- Active Subscription
- Module Access
- Permission

Never rely on hiding buttons alone.

Authorization must also be enforced in the backend.

---

# 27. Multi-Tenant Standards

Business Suite is tenant-aware.

Every tenant-owned operation must include the active tenant context.

Examples:

- Users
- Customers
- Inventory
- Finance
- Procurement
- Sales

The active tenant should never be selected manually by developers.

It should be obtained from the authenticated workspace context.

---

## Workspace Switching

Switching workspaces must update:

- Active Tenant
- Active Company
- Active Branch
- Available Modules
- Permissions
- Navigation
- Dashboard Data

No browser refresh should be required.

---

# 28. Data Fetching Standards

Data fetching should occur through services and hooks.

Do not fetch data directly inside JSX.

Preferred flow:

```text
Component

↓

Hook

↓

Service

↓

Supabase
```

---

## Loading Behaviour

Data loading should use:

- Skeleton Loaders
- Progressive Loading
- Pagination

Avoid blocking the UI while loading.

---

## Refresh Behaviour

Features should support:

- Manual Refresh
- Automatic Refresh (where applicable)

without requiring a full page reload.

---

# 29. File Upload Standards

Uploads should support:

- Images
- Documents
- PDFs
- Excel
- CSV

Future support:

- Videos
- Audio

---

## Upload Rules

- Validate file size.
- Validate file type.
- Display upload progress.
- Show success or failure messages.
- Store metadata in the database.
- Support secure file access.

---

# 30. Logging Standards

Critical operations should be logged.

Examples:

- Login
- Logout
- Password Reset
- User Invitation
- Subscription Activation
- Role Assignment
- Workspace Switching

Logs should include:

- User
- Tenant
- Timestamp
- Action
- IP Address (where available)

---

# 31. Notification Standards

Notifications should be centralized.

Supported channels:

- Email
- SMS
- In-App

Notification templates should be configurable.

Avoid hardcoded notification content.

---

# 32. Search Standards

Every list should support search where applicable.

Search should be:

- Fast
- Debounced
- Case-insensitive
- Responsive

Search should work together with:

- Filters
- Sorting
- Pagination

---

# 33. Export Standards

Where applicable, lists should support:

- PDF
- Excel
- CSV

Exports should respect:

- Active Filters
- Active Sorting
- User Permissions

Only authorized users should export data.

---

# 34. Performance Standards

Performance should be considered during development, not after deployment.

## General Rules

- Avoid unnecessary component re-renders.
- Lazy load feature modules where appropriate.
- Memoize expensive calculations.
- Use pagination for large datasets.
- Avoid loading unnecessary data.
- Prefer server-side filtering over client-side filtering for large datasets.
- Keep API payloads as small as practical.

---

## React Performance

Use React optimization features where appropriate.

Examples:

- useMemo
- useCallback
- React.memo
- Lazy Loading
- Suspense

Do not optimize prematurely.

Only optimize where measurable improvements are needed.

---

## Images

Images should:

- Be compressed.
- Be responsive.
- Support lazy loading.
- Use modern formats where appropriate.

---

## Lists

Large tables should support:

- Pagination
- Virtual scrolling (future)
- Server-side filtering
- Server-side searching

---

# 35. Security Standards

Security is everyone's responsibility.

Every module must follow Platform Core security rules.

---

## Authentication

Never implement authentication inside feature modules.

Authentication is managed by Platform Core.

---

## Authorization

Never rely on hidden buttons for security.

Permissions must be validated:

- Frontend
- Backend
- Database (RLS)

---

## Sensitive Data

Never expose:

- Passwords
- Tokens
- API Keys
- SMTP Passwords
- SMS Credentials

Sensitive values should always be encrypted.

---

## Input Validation

Validate all user input.

Validation should exist:

- Client Side
- Server Side

Never trust client-side validation alone.

---

## SQL Security

Prevent:

- SQL Injection
- Broken Access Control
- Unauthorized Data Access

Use parameterized queries and Row Level Security.

---

# 36. Database Standards

Business Suite uses PostgreSQL.

---

## Primary Keys

All application tables should use UUID primary keys.

---

## Foreign Keys

Always enforce foreign key relationships.

Avoid orphaned records.

---

## Soft Deletes

Prefer soft deletes using:

```text
deleted_at
```

unless permanent deletion is explicitly required.

---

## Audit Logging

Critical actions should always create audit records.

---

## Tenant Isolation

Every tenant-owned table must contain:

```text
tenant_id
```

Tenant isolation is mandatory.

---

# 37. Git Standards

Business Suite follows Git Flow.

Main branches:

```text
main

develop
```

Supporting branches:

```text
feature/*

release/*

hotfix/*
```

---

## Commit Messages

Use meaningful commit messages.

Examples:

```text
Add tenant registration workflow

Implement workspace switching

Fix role permission validation

Update company management UI
```

Avoid messages such as:

```text
Update

Fix

Changes

Testing
```

---

## Pull Requests

Every Pull Request should:

- Have a clear description.
- Reference the related specification.
- Be reviewed before merging.
- Pass automated checks.

---

# 38. AI Development Standards

Business Suite embraces AI-assisted development.

Supported tools include:

- Lovable
- Cursor
- ChatGPT
- GitHub Copilot
- Claude
- Future AI coding assistants

---

## AI Rules

AI-generated code must:

- Follow the architecture documents.
- Follow this coding standards document.
- Follow the feature folder structure.
- Respect tenant isolation.
- Respect package restrictions.
- Respect permissions.
- Use service classes.
- Use React Hook Form.
- Use Zod.
- Use React Router.
- Use reusable components.
- Avoid duplicate code.
- Produce readable TypeScript.

AI should not invent architecture that contradicts project documentation.

---

# 39. Things You Must Never Do

The following practices are prohibited.

## Navigation

❌ Do not use:

```html
<a href="/users"></a>
```

for internal navigation.

Use:

- Link
- NavLink
- useNavigate()

---

## Forms

❌ Do not rely on HTML validation.

Examples:

```html
required maxlength minlength
```

Use:

- React Hook Form
- Zod

---

## API Calls

❌ Never call Supabase directly inside UI components.

Always use:

Component

↓

Hook

↓

Service

↓

Supabase

---

## Business Logic

❌ Never place business logic inside components.

Move business logic into services.

---

## Hardcoded Values

❌ Never hardcode:

- Statuses
- Dropdown values
- Package names
- Authentication providers
- Notification templates

Use:

- User Codes
- Platform Settings
- Reference Data

---

## Permissions

❌ Never hide buttons instead of enforcing permissions.

Permissions must be enforced throughout the application.

---

## TypeScript

❌ Avoid using:

```typescript
any;
```

Use strongly typed interfaces wherever possible.

---

## Console Logging

❌ Do not leave debugging statements in production code.

Examples:

```javascript
console.log();

console.error();

debugger;
```

Use the application's logging strategy instead.

---

# 40. Code Review Checklist

Before code is merged, verify:

- Folder structure is correct.
- Naming conventions are followed.
- No duplicate code exists.
- Services are used correctly.
- Forms use React Hook Form.
- Validation uses Zod.
- Navigation uses React Router.
- Permissions are enforced.
- Loading states exist.
- Error handling exists.
- Audit logging is implemented where required.
- Tenant isolation is respected.
- UI follows the design standards.

---

# 41. Testing Standards

Every feature should be tested before it is considered complete.

Testing should cover:

- Functional behaviour
- Validation
- Permissions
- Tenant isolation
- Error handling
- Performance
- User experience

---

## Functional Testing

Verify:

- Create
- Read
- Update
- Delete
- Search
- Filtering
- Sorting
- Pagination
- Export
- Printing

where applicable.

---

## Validation Testing

Verify:

- Required fields
- Invalid data
- Boundary values
- Business rules
- Duplicate values
- Permission restrictions

---

## Security Testing

Verify:

- Authentication
- Authorization
- Tenant isolation
- Session expiration
- Protected routes
- File access
- Module access

---

## User Experience Testing

Verify:

- Responsive layouts
- Loading states
- Empty states
- Error states
- Navigation
- Accessibility

---

# 42. Accessibility Standards

Business Suite should be usable by as many users as possible.

Recommended practices include:

- Keyboard navigation
- Visible focus indicators
- Accessible form labels
- Proper heading hierarchy
- Sufficient color contrast
- Meaningful button labels
- Screen reader friendly components where practical

Avoid using color alone to communicate important information.

---

# 43. Documentation Standards

Every feature should include appropriate documentation.

Documentation may include:

- README updates
- Database changes
- Workflow changes
- API changes
- UI changes
- Configuration changes

Documentation should always be updated alongside code changes.

---

# 44. Future Module Standards

Every new module should follow the same project structure.

Example:

```text
specs/

crm/
    README.md
    database.md
    workflows.md
    ui.md
    security.md
    acceptance.md

sales/
inventory/
finance/
procurement/
hr/
reports/
```

Every module must inherit the standards defined in this document.

---

# 45. Definition of Done (DoD)

A feature is considered complete only when all of the following have been satisfied.

## Architecture

- Follows project architecture.
- Uses the approved folder structure.
- Uses the approved technology stack.

---

## Functionality

- Feature works as specified.
- Validation works.
- Business rules work.
- Permissions work.

---

## User Interface

- Responsive
- Accessible
- Loading states implemented
- Empty states implemented
- Error states implemented
- Confirmation dialogs implemented
- Toast notifications implemented

---

## Code Quality

- No duplicate code
- Reusable components
- Service Layer used
- Proper typing
- No unnecessary complexity

---

## Security

- Tenant isolation verified
- Permission checks verified
- Sensitive data protected

---

## Database

- Proper relationships
- Foreign keys
- Indexes where appropriate
- Audit logging implemented
- Soft deletes where required

---

## Documentation

- Specifications updated
- Documentation updated
- Changelog updated (where applicable)

---

# 46. Engineering Principles

Business Suite should always prioritize:

1. Simplicity over complexity.
2. Configuration over hardcoding.
3. Reusability over duplication.
4. Security by default.
5. Performance by design.
6. User experience first.
7. Consistency across all modules.
8. Scalability without redesign.
9. Clear separation of concerns.
10. Long-term maintainability.

When in doubt, choose the solution that is easier to understand, easier to maintain, and easier to extend.

---

# 47. AI Development Manifesto

Business Suite is designed to embrace AI-assisted software development.

AI tools are expected to accelerate development while following the project's architecture and engineering standards.

Every AI-generated contribution must:

- Respect the documented architecture.
- Respect tenant isolation.
- Respect package and permission rules.
- Produce clean, readable TypeScript.
- Prefer reusable solutions over one-off implementations.
- Avoid introducing conflicting patterns.
- Update documentation when architecture or behaviour changes.

AI should assist engineering—not replace engineering judgment.

---

# 48. Conclusion

This document establishes the engineering standards for the Business Suite platform.

Together with the architecture documents and module specifications, it forms the single source of truth for development.

All developers, contributors, and AI tools must follow these standards to ensure that Business Suite remains:

- Consistent
- Scalable
- Secure
- Performant
- Maintainable
- Extensible

These standards apply to every current and future module of the platform.
