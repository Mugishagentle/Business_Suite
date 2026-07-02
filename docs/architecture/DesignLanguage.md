# Business Suite Design Language

Version: 1.0

Status: Approved

Owner: Business Suite Product Team

Last Updated: July 2026

---

# 1. Purpose

This document defines the official design language for Business Suite.

It establishes the visual, interaction, and user experience standards that every module must follow.

The objective is to create a professional, modern, scalable, and consistent experience across the entire platform.

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
- Dashboards
- Mobile Applications
- Future Modules

This document works together with:

- Architecture.md
- CodingStandards.md
- Platform Specifications

---

# 2. Design Philosophy

Business Suite is an enterprise productivity platform.

The interface should help users complete work quickly, confidently, and consistently.

The design should never distract users from their work.

Instead, the interface should become almost invisible.

Users should always understand:

- Where they are
- What they can do
- What happens next

The platform should feel:

- Professional
- Modern
- Calm
- Fast
- Predictable
- Trustworthy

---

# 3. Experience Principles

Every feature should follow these principles.

## Simplicity

Remove unnecessary visual clutter.

Every screen should focus only on what users need.

---

## Consistency

Buttons should behave consistently.

Forms should behave consistently.

Tables should behave consistently.

Dialogs should behave consistently.

Users should never need to relearn the interface.

---

## Predictability

The same action should always produce the same result.

Users should build confidence while using the system.

---

## Visibility

Important information should always be visible.

Avoid hiding important actions.

---

## Feedback

Every interaction should provide immediate feedback.

Examples:

- Button loading
- Success messages
- Validation
- Progress indicators

Users should never wonder whether something happened.

---

## Efficiency

Minimize clicks.

Reduce unnecessary navigation.

Support keyboard shortcuts in future versions.

---

## Accessibility

The interface should be usable by as many users as possible.

Accessibility is a requirement—not an afterthought.

---

# 4. Visual Personality

Business Suite should feel similar to modern enterprise applications such as:

- Microsoft 365 Admin Center
- Stripe Dashboard
- Linear
- Notion
- Atlassian
- GitHub
- Supabase Dashboard

Characteristics include:

- Minimalist
- Spacious
- Clean
- Professional
- High information density
- Strong typography
- Subtle colors
- Clear hierarchy

Avoid decorative design elements that do not improve usability.

---

# 5. Layout Philosophy

Every screen should follow a consistent layout.

```text
┌───────────────────────────────────────────────────────┐
│ Top Navigation                                        │
├───────────────┬───────────────────────────────────────┤
│               │                                       │
│ Sidebar       │  Page Header                          │
│               │---------------------------------------│
│               │                                       │
│               │  Page Content                         │
│               │                                       │
│               │                                       │
│               │                                       │
└───────────────┴───────────────────────────────────────┘
```

Users should immediately recognize where navigation, actions, and content are located.

---

# 6. Grid System

Business Suite uses a responsive grid system.

Content should align consistently across all pages.

Use a 12-column layout for desktop interfaces.

Spacing should follow a consistent rhythm.

Avoid arbitrary positioning of components.

---

# 7. Responsive Breakpoints

The application should support:

| Device        |           Width |
| ------------- | --------------: |
| Mobile        |         < 640px |
| Small Tablet  |   640px - 767px |
| Tablet        |  768px - 1023px |
| Laptop        | 1024px - 1279px |
| Desktop       | 1280px - 1535px |
| Large Desktop |        ≥ 1536px |

The interface should adapt gracefully across all supported screen sizes.

---

# 8. Spacing System

Business Suite uses an 8-point spacing system.

Approved spacing values:

```text
4px

8px

12px

16px

24px

32px

40px

48px

64px

80px
```

Developers should avoid arbitrary spacing values.

Use the approved spacing scale throughout the application.

---

# 9. Alignment Principles

Every screen should maintain strong visual alignment.

Rules:

- Align page titles.
- Align action buttons.
- Align table headers.
- Align form labels.
- Align cards.
- Maintain equal spacing between sections.

Visual consistency improves readability and reduces cognitive load.

---

# 10. Whitespace

Whitespace is an intentional design element.

Do not attempt to fill every empty area.

Whitespace should improve:

- Readability
- Navigation
- Focus
- Information hierarchy

Well-spaced interfaces feel more professional and easier to use.

---

# 11. Content Hierarchy

Every page should clearly communicate importance.

Hierarchy should generally follow:

```text
Page Title

↓

Page Description

↓

Primary Actions

↓

Summary Information

↓

Main Content

↓

Supporting Information
```

Users should understand the structure of a page within a few seconds.

---

# 12. Responsive Behaviour

Components should adapt—not simply shrink.

Examples:

Desktop

```text
Sidebar + Content
```

Tablet

```text
Collapsible Sidebar
```

Mobile

```text
Drawer Navigation
```

Tables should become horizontally scrollable only when necessary.

Forms should stack naturally on smaller screens.

The mobile experience should remain fully functional without removing core features.

---

# 13. Typography

Typography is the foundation of the user interface.

Business Suite prioritizes readability over decoration.

The interface should remain readable throughout long working sessions.

---

## Primary Font

Business Suite uses:

**Inter**

Reasons:

- Excellent readability
- Modern appearance
- Optimized for interfaces
- Widely adopted
- Excellent browser support

Avoid using multiple fonts throughout the application.

---

## Font Weights

Use the following font weights.

| Weight | Usage          |
| ------ | -------------- |
| 400    | Body Text      |
| 500    | Medium Labels  |
| 600    | Section Titles |
| 700    | Major Headings |

Avoid extremely thin or extremely bold fonts.

---

## Font Scale

Use a consistent typography scale.

| Size | Usage                 |
| ---- | --------------------- |
| 12px | Captions              |
| 14px | Small Labels          |
| 16px | Body Text             |
| 18px | Large Body            |
| 20px | Small Headings        |
| 24px | Section Titles        |
| 30px | Page Titles           |
| 36px | Major Titles          |
| 48px | Landing Page Headings |

Avoid arbitrary font sizes.

---

## Text Hierarchy

Use typography to communicate importance.

Hierarchy:

```text
Page Title

↓

Section Title

↓

Card Title

↓

Body Text

↓

Caption
```

Avoid relying on colors alone.

---

# 14. Design Tokens

Business Suite uses semantic design tokens instead of fixed colors.

Components should reference tokens rather than hardcoded color values.

---

## Primary Tokens

```text
Primary

Primary Hover

Primary Foreground
```

Used for:

- Primary Buttons
- Active Navigation
- Links
- Important Actions

---

## Secondary Tokens

```text
Secondary

Secondary Hover

Secondary Foreground
```

Used for:

- Secondary Buttons
- Alternative Actions

---

## Surface Tokens

```text
Background

Surface

Surface Elevated

Surface Hover
```

Used for:

- Pages
- Cards
- Dialogs
- Tables

---

## Text Tokens

```text
Text Primary

Text Secondary

Text Muted

Text Disabled
```

Every piece of text should use one of these tokens.

---

## Border Tokens

```text
Border

Border Strong

Divider
```

Avoid manually choosing border colors.

---

## Status Tokens

```text
Success

Warning

Danger

Info
```

These tokens should be used consistently throughout the application.

Examples:

Success

Approved

Completed

Saved

Warning

Pending

Expiring

Attention Required

Danger

Rejected

Deleted

Failed

Error

Info

New Information

Tips

Announcements

---

## Focus Tokens

```text
Focus Ring
```

Used for:

- Keyboard Navigation
- Accessibility
- Active Inputs

---

# 15. Theme System

Business Suite supports themes.

Version 1 includes:

- Light Theme
- Dark Theme

Future versions may include:

- Customer Branding
- White Label Themes
- High Contrast Themes

Components should never hardcode colors.

Everything should reference design tokens.

---

# 16. Color Philosophy

Business Suite uses restrained color.

Color should communicate meaning—not decoration.

Guidelines:

- Neutral interfaces
- Strong typography
- Minimal accent colors
- High readability

Avoid using bright colors simply for visual appeal.

---

## Color Usage

Primary

Primary Actions

Secondary

Supporting Actions

Success

Successful Operations

Warning

Requires Attention

Danger

Errors

Critical Actions

Info

Helpful Information

Neutral

Everything Else

The interface should remain calm and professional.

---

# 17. Iconography

Business Suite uses:

**Lucide React**

Only one icon library should be used across the platform.

---

## Icon Usage

Icons should:

- Support labels
- Improve recognition
- Improve navigation

Icons should never replace text entirely.

---

## Common Icons

Examples:

Dashboard

Building

Users

Settings

Reports

Inventory

Sales

Finance

Notifications

Profile

Search

Filter

Export

Print

Refresh

Upload

Download

Approval

Reject

Delete

Edit

---

## Icon Size

Recommended sizes:

16px

Small Actions

20px

Forms

24px

Navigation

32px

Dashboard Cards

Maintain consistent icon sizing.

---

# 18. Branding

Version 1 uses a neutral design language.

Brand customization will be supported in future releases.

Branding should eventually allow:

- Logo
- Company Name
- Primary Color
- Favicon
- Login Background
- Email Branding

The application architecture should support branding without requiring UI redesign.

---

# 19. Light & Dark Mode

Both themes should provide the same functionality.

Switching themes should:

- Preserve layout
- Preserve spacing
- Preserve typography

Only visual tokens should change.

Avoid creating separate interfaces for each theme.

---

# 20. Visual Consistency

Every module should appear as though it belongs to the same application.

Consistency applies to:

- Buttons
- Forms
- Tables
- Cards
- Navigation
- Dialogs
- Notifications
- Dashboards
- Reports

Visual consistency should take priority over individual module customization.

---

# 21. Buttons

Buttons are one of the primary interaction elements.

Every button should clearly communicate its purpose.

---

## Button Variants

Business Suite supports the following button variants.

Primary

Used for the main action.

Examples:

- Save
- Create
- Submit
- Approve
- Continue

---

Secondary

Used for supporting actions.

Examples:

- Cancel
- Back
- Close

---

Outline

Used for optional actions.

Examples:

- Preview
- View Details
- Download

---

Ghost

Used inside toolbars.

Examples:

- Refresh
- Filter
- Search
- Settings

---

Destructive

Used for dangerous operations.

Examples:

- Delete
- Suspend
- Remove
- Cancel Subscription

---

## Button Behaviour

Every asynchronous button must:

- Display a loading spinner.
- Disable itself while processing.
- Prevent duplicate submissions.
- Display feedback after completion.

Buttons should never leave users wondering if an action is running.

---

## Button Placement

Primary actions should appear consistently.

Examples:

Save

Cancel

or

Create

Cancel

Primary buttons should appear on the right where practical.

---

# 22. Forms

Business Suite relies heavily on forms.

Every form should follow the same behaviour.

---

## Form Structure

Recommended layout:

```text
Section Title

↓

Description (Optional)

↓

Form Fields

↓

Action Buttons
```

---

## Grouping

Fields should be grouped logically.

Example:

Company Information

↓

Contact Information

↓

Address

↓

Preferences

---

## Required Fields

Required fields should be clearly indicated.

Validation should occur immediately after interaction.

---

## Long Forms

Long forms should be divided into sections.

Very large forms may use tabs or step-based wizards.

---

# 23. Form Controls

Use consistent controls.

Examples:

Text Input

Textarea

Number Input

Email Input

Password Input

Date Picker

Time Picker

DateTime Picker

Dropdown

Autocomplete

Checkbox

Radio Button

Toggle Switch

File Upload

Rich Text Editor (Future)

---

## Input Behaviour

Every input should support:

- Labels
- Help Text
- Placeholder
- Validation Message

Avoid relying on placeholders as labels.

---

# 24. Modals

Modals are the preferred method for Create and Edit operations.

---

## Standard Modal Layout

```text
Title

Description

-----------------------

Content

-----------------------

Cancel

Save
```

---

## Modal Sizes

Small

Confirmation Dialogs

Medium

Simple Forms

Large

Complex Forms

Extra Large

Large Data Forms

Full Screen

Only when absolutely necessary.

---

## Modal Behaviour

Modals should:

- Trap keyboard focus.
- Close with Escape where appropriate.
- Prevent accidental data loss.
- Warn users about unsaved changes.

---

# 25. Tables

Tables are one of the most important components.

Every module should use the same table experience.

---

## Standard Toolbar

Every table should include:

Search

Filters

Refresh

Export

Print

Add New

Example:

```text
--------------------------------------------------

Search

Filters

Refresh

Export

Print

+ Add New

--------------------------------------------------

Table

--------------------------------------------------

Pagination
```

---

## Table Columns

Columns should support:

- Sorting
- Resizing (Future)
- Visibility Toggle
- Responsive Behaviour

---

## Table Actions

Actions should appear consistently.

Examples:

View

Edit

Delete

Approve

Print

More

---

## Bulk Actions

Where applicable:

- Delete
- Export
- Print
- Change Status

---

## Empty Tables

Display:

Illustration

Friendly Message

Primary Action

---

# 26. Cards

Cards group related information.

Examples:

Dashboard Statistics

Company Summary

Customer Summary

Inventory Summary

---

## Card Layout

```text
Title

Subtitle

Main Content

Footer (Optional)
```

---

## Card Behaviour

Cards should:

- Maintain equal padding.
- Maintain equal spacing.
- Avoid unnecessary borders.

---

# 27. Dashboard Widgets

Dashboard widgets should share the same layout.

Examples:

Statistics

Charts

Activity

Notifications

Tasks

Approvals

---

## Widget Structure

```text
Title

↓

Primary Information

↓

Supporting Information

↓

Actions (Optional)
```

Widgets should never overwhelm users.

---

# 28. Search Components

Search should always appear in the same location.

Features:

- Instant Search
- Debounced Search
- Clear Button
- Keyboard Focus

Search should work together with filters.

---

# 29. Filter Panels

Filters should use a consistent side panel or popover.

Common filters include:

Status

Date

User

Branch

Module

Category

Filters should be easy to clear.

---

# 30. Pagination

Pagination should appear consistently.

Display:

Current Page

Rows Per Page

Total Records

Previous

Next

Example:

Showing 1–20 of 326 records.

---

# 31. Status Badges

Statuses should use badges.

Examples:

Draft

Pending

Approved

Rejected

Completed

Cancelled

Active

Inactive

Suspended

Badges should use semantic design tokens rather than hardcoded colors.

---

# 32. Avatars

Users should display avatars where practical.

Priority:

Uploaded Photo

↓

Generated Initials

↓

Default Avatar

Avatars should maintain consistent sizing.

---

# 33. Tooltips

Tooltips should explain icons or advanced functionality.

Tooltips should be concise.

Avoid placing important information only inside tooltips.

---

# 34. Breadcrumbs

Every feature page should display breadcrumbs.

Example:

Dashboard

>

Administration

>

Users

Breadcrumbs should always reflect the current navigation path.

---

# 35. Page Headers

Every page should begin with a consistent header.

The header should contain:

Page Title

Page Description (Optional)

Primary Action

Secondary Actions (Optional)

The page header should establish context immediately.

---

# 36. Navigation Experience

Navigation should be fast, predictable, and consistent.

Users should never feel lost.

---

## Sidebar

The sidebar is the primary navigation component.

It should contain:

- Company Logo
- Workspace Switcher
- Module Navigation
- Favorites (Future)
- Collapse Button

The sidebar should remain consistent across all modules.

---

## Top Navigation

The top navigation should contain:

- Breadcrumbs
- Global Search (Future)
- Notifications
- Theme Switch
- User Profile
- Settings

---

## Workspace Switcher

The workspace switcher should always be visible.

It should display:

- Company Logo
- Company Name
- Current Role

Actions:

- Switch Workspace
- Set Default Workspace

---

## Navigation Behaviour

Navigation should:

- Preserve application state where appropriate.
- Avoid full page reloads.
- Highlight the active page.
- Remember the last expanded menu.

---

# 37. Dashboard Standards

Every module should have a dashboard.

Dashboards should provide:

- Summary
- Statistics
- Recent Activity
- Quick Actions

Avoid overcrowding dashboards.

---

## Dashboard Layout

Recommended layout:

```text
Page Header

↓

Statistics

↓

Charts

↓

Tables

↓

Recent Activity

↓

Tasks
```

---

## Statistics Cards

Each statistics card should include:

- Title
- Value
- Icon
- Trend (Optional)

Examples:

Total Customers

1,250

↑ 12%

---

## Charts

Charts should support:

- Hover details
- Legends
- Responsive resizing
- Export (Future)

Use charts only when they provide meaningful insight.

---

## Quick Actions

Dashboards should expose common actions.

Examples:

- Add Customer
- Create Invoice
- Invite User
- Add Branch

---

# 38. Empty States

Every feature should have a meaningful empty state.

Avoid:

"No records found."

Prefer:

"You haven't invited any users yet."

Include:

- Illustration (Optional)
- Description
- Primary Action

---

# 39. Loading Experience

Users should always know that work is in progress.

Use:

- Skeleton Loaders
- Loading Spinners
- Progress Bars

Avoid blank pages.

---

## Skeleton Loaders

Preferred for:

- Tables
- Cards
- Dashboards
- Forms

---

## Progress Indicators

Use for:

- File Uploads
- Imports
- Exports
- Background Jobs

---

# 40. Notifications

Every important action should notify the user.

Supported types:

- Success
- Warning
- Error
- Information

Notifications should be:

- Clear
- Concise
- Actionable

---

## Toast Notifications

Recommended for:

- Save Successful
- Record Deleted
- Invitation Sent
- Settings Updated

---

## Dialog Notifications

Recommended for:

- Confirmation
- Warnings
- Destructive Actions

---

# 41. Error Experience

Errors should help users recover.

Avoid technical language.

Instead of:

```text
500 Internal Server Error
```

Use:

```text
Something went wrong.

Please try again or contact your administrator.
```

---

## Retry Actions

Where possible, provide:

- Retry Button
- Refresh Button
- Contact Support

---

# 42. Accessibility

Business Suite should be usable by everyone.

Every feature should support:

- Keyboard Navigation
- Focus Indicators
- Accessible Labels
- Proper Heading Structure
- High Contrast
- Screen Reader Compatibility where practical

Accessibility should be considered during development, not after.

---

# 43. Responsive Design

The application should work seamlessly on:

- Desktop
- Laptop
- Tablet
- Mobile

The interface should adapt rather than simply shrink.

---

## Tables

On smaller screens:

- Enable horizontal scrolling when necessary.
- Preserve readability.
- Keep important actions accessible.

---

## Forms

Forms should stack naturally on smaller screens.

Avoid side-by-side inputs where space is limited.

---

# 44. Micro-Interactions

Subtle animations improve the user experience.

Recommended interactions:

- Button Hover
- Button Press
- Card Hover
- Dropdown Opening
- Modal Opening
- Toast Appearance
- Loading Spinner

Animations should be subtle and fast.

Avoid distracting animations.

---

# 45. Motion Standards

Recommended transition durations:

150ms

200ms

250ms

300ms

Avoid long animations.

Users value speed over visual effects.

---

# 46. Printing

Where applicable, pages should support printing.

Print layouts should:

- Remove unnecessary navigation.
- Preserve branding.
- Optimize spacing.
- Display page numbers where appropriate.

---

# 47. Export Experience

Supported formats:

- PDF
- Excel
- CSV

Exports should respect:

- Active Filters
- Search
- Sorting
- User Permissions

Provide progress feedback for large exports.

---

# 48. File Upload Experience

Uploads should display:

- Selected File
- Upload Progress
- Success State
- Failure State

Support:

- Drag & Drop
- Browse Files

Validate file type and size before upload.

---

# 49. Search Experience

Search should be available wherever it improves productivity.

Features:

- Instant Search
- Debounced Input
- Search Highlighting (Future)
- Search History (Future)

Search should remain responsive even with large datasets.

---

# 50. Overall Experience

Every module should feel like it belongs to the same product.

Users should never notice differences between modules.

Consistency builds confidence.

Predictability increases productivity.

A calm, modern interface helps users focus on their work rather than learning the software.

---

# 51. White Label Support

Business Suite is designed to support white-label deployments in future versions.

The architecture should allow each tenant to customize its branding without affecting other tenants.

Future branding options may include:

- Company Logo
- Favicon
- Login Background
- Primary Brand Color
- Email Branding
- SMS Branding
- Reports Branding
- PDF Branding

All branding should be configuration-driven.

No branding should require code changes.

---

# 52. Localization

Business Suite should support multiple languages.

The interface should avoid hardcoded text where possible.

Future support includes:

- English
- French
- Swahili
- Arabic
- Other languages

Dates, numbers, currencies, and time formats should respect the active language and regional settings.

---

# 53. Internationalization

The platform should support multiple countries.

Future modules should avoid assumptions such as:

- One currency
- One tax system
- One date format
- One phone format

These should always be configurable through reference data.

---

# 54. White Space Philosophy

White space is an intentional design element.

It improves:

- Readability
- Navigation
- User focus
- Information hierarchy

Do not fill empty space simply because it exists.

A clean interface is easier to understand.

---

# 55. Information Density

Business Suite is an enterprise application.

Information density should be balanced.

Avoid:

- Extremely crowded screens.
- Excessive empty space.

Users should see enough information to work efficiently without becoming overwhelmed.

---

# 56. Mobile Philosophy

The mobile experience should not be a reduced version of the desktop experience.

Instead:

- Reorganize layouts.
- Stack components naturally.
- Use drawers instead of sidebars.
- Simplify navigation.
- Prioritize the most important actions.

Core functionality should remain available on all supported devices.

---

# 57. Dashboard Philosophy

Dashboards should answer three questions immediately:

1. What is happening?
2. What requires my attention?
3. What should I do next?

Avoid dashboards that only display decorative charts.

Every dashboard widget should provide actionable information.

---

# 58. Reporting Philosophy

Reports should be:

- Easy to read
- Filterable
- Printable
- Exportable
- Consistent across modules

Every report should include:

- Report Title
- Filters Used
- Generated By
- Generated Date
- Page Numbers
- Company Branding (where applicable)

---

# 59. Future AI Integration

Business Suite is designed to support AI features.

Examples include:

- AI Assistants
- AI Search
- AI Report Summaries
- AI Data Analysis
- AI Recommendations
- AI Workflow Suggestions

Future AI functionality should integrate naturally into the existing user experience.

---

# 60. Shared Component Library

Business Suite should maintain a shared component library.

Every module should reuse these components rather than creating new implementations.

Examples include:

- AppButton
- AppInput
- AppSelect
- AppTextarea
- AppDatePicker
- AppFileUpload
- AppModal
- ConfirmationDialog
- Drawer
- DataTable
- SearchToolbar
- FilterPanel
- PageHeader
- SectionHeader
- DashboardCard
- StatisticCard
- StatusBadge
- EmptyState
- LoadingSkeleton
- ErrorState
- PermissionGate
- WorkspaceSwitcher

The shared component library promotes consistency, maintainability, and faster development.

---

# 61. AI Design Rules

AI-assisted development tools must follow this document when generating interfaces.

Generated screens should:

- Follow the approved layouts.
- Reuse shared components.
- Respect spacing rules.
- Respect typography.
- Use semantic design tokens.
- Use React Router for navigation.
- Follow accessibility guidelines.
- Maintain consistency with existing modules.

AI tools should extend the design language rather than invent new patterns.

---

# 62. Definition of Done (Design)

A feature is considered visually complete when it:

- Uses the approved layout.
- Uses the approved spacing.
- Uses the approved typography.
- Uses semantic design tokens.
- Uses shared components.
- Supports loading states.
- Supports empty states.
- Supports error states.
- Is responsive.
- Is accessible.
- Is consistent with the rest of the application.

---

# 63. Experience Checklist

Before a feature is approved, verify:

- The interface is intuitive.
- Navigation is predictable.
- Buttons behave consistently.
- Forms follow the standard.
- Tables follow the standard.
- Modals follow the standard.
- Feedback is immediate.
- Users can recover from errors.
- The feature is responsive.
- The feature is visually consistent.

---

# 64. Design Principles Summary

Every interface in Business Suite should be:

- Simple
- Modern
- Professional
- Consistent
- Predictable
- Responsive
- Accessible
- Efficient
- Scalable
- Calm

These principles take precedence over visual trends.

---

# 65. Conclusion

The Business Suite Design Language defines the visual and interaction standards for the platform.

Together with the Architecture, Coding Standards, and Platform Specifications, it forms the foundation for a consistent and scalable user experience.

All future modules, components, and interfaces must follow this design language to ensure that Business Suite evolves as a unified product rather than a collection of independent applications.

The objective is not only to build functional software, but to deliver a platform that users enjoy using every day because it is intuitive, consistent, and dependable.
