# Reporting Engine Specification

Version: 1.0

Status: Draft

Module: Reporting Engine

---

# 1. Purpose

The Reporting Engine is a shared platform service responsible for generating reports, dashboards, datasets, exports, and business analytics across the Business Suite.

It provides centralized reporting capabilities for all platform services and business modules while ensuring consistency, security, and scalability.

---

# 2. Core Principle

Business modules do not generate reports directly.

Business modules expose data through approved datasets.

The Reporting Engine is responsible for:

- Dataset execution
- Filtering
- Aggregation
- Sorting
- Grouping
- Visualization
- Export
- Report generation

---

# 3. Objectives

The Reporting Engine aims to:

- Centralize reporting.
- Eliminate duplicate report logic.
- Support reusable datasets.
- Support interactive reports.
- Support dashboards.
- Support PDF exports.
- Support Excel exports.
- Support CSV exports.
- Support tenant isolation.
- Support future scheduled reports.

---

# 4. Scope

The Reporting Engine includes:

- Report Definitions
- Datasets
- Filters
- Parameters
- Visualizations
- Dashboards
- Report Execution
- Export
- Report History
- Report Permissions

The Reporting Engine acts as the analytics layer for Business Suite.

---

# 5. Out of Scope

The Reporting Engine does not:

- Store business transactions.
- Replace business modules.
- Perform workflow approvals.
- Modify business data.

Business modules remain responsible for maintaining business data.

The Reporting Engine is responsible for presenting and exporting information.

---

# 6. Core Concepts

The Reporting Engine is built around the following concepts:

- Reports
- Datasets
- Parameters
- Filters
- Visualizations
- Dashboards
- Exports
- Report History
- Report Permissions

---

# 7. Report Types

The Reporting Engine supports three major report types.

## Operational Reports

Used for day-to-day business operations.

Examples:

- Customer List
- Invoice Register
- Stock Balance
- Purchase Order List
- Employee List

---

## Analytical Reports

Used for summaries, trends, comparisons, and KPIs.

Examples:

- Monthly Sales Trend
- Revenue by Branch
- Expenses by Category
- Stock Movement Analysis
- Leave Utilization Analysis

---

## Dashboards

Used for live visual summaries.

Examples:

- Sales Dashboard
- Finance Dashboard
- Inventory Dashboard
- HR Dashboard
- Procurement Dashboard

---

# 8. Dataset

A Dataset defines the source and structure of report data.

A dataset may include:

- Source Module
- Fields
- Joins
- Filters
- Aggregations
- Permissions
- Tenant Scope

Business modules expose approved datasets.

The Reporting Engine consumes datasets.

---

# 9. Report Definition

A Report Definition describes how a report should appear and behave.

It includes:

- Report Name
- Report Type
- Dataset
- Parameters
- Filters
- Columns
- Sorting
- Grouping
- Export Options
- Permissions

---

# 10. Parameters

Parameters allow users to control report output.

Examples:

- Date From
- Date To
- Branch
- Department
- Customer
- Supplier
- Status
- Category

Parameters should be validated before report execution.

---

# 11. Filters

Filters refine report data.

Examples:

- Status = Active
- Branch = Kampala
- Date Between 1 July and 31 July
- Amount Greater Than 1,000,000

Filters may be:

- Required
- Optional
- User-defined
- System-defined

---

# 12. Visualizations

Reports may include visualizations.

Supported visualizations include:

- Table
- Summary Cards
- Bar Chart
- Line Chart
- Pie Chart
- Area Chart

Visualizations should be used only where they improve understanding.

---

# 13. Exports

Reports should support export formats.

Version 1 supports:

- PDF
- Excel
- CSV

Exports should respect:

- Filters
- Sorting
- User Permissions
- Tenant Isolation

---

# 14. Report History

Every executed report should be recorded.

History should include:

- User
- Tenant
- Report
- Parameters
- Execution Time
- Export Format
- Date & Time

Report history supports audit, troubleshooting, and usage analytics.

---

# 15. Report Permissions

Reports must be permission-controlled.

Users may have permissions to:

- View Report
- Run Report
- Export Report
- Configure Report
- View Dashboard

Permissions are evaluated through Platform Core.

---

# 16. Dashboard Reporting

Dashboards are composed of reusable report widgets.

Examples:

- Summary Cards
- Charts
- Tables
- Activity Lists
- KPI Widgets

Dashboard widgets should be built from approved datasets.

A dashboard should never query business module tables directly.

---

# 17. Standard Reports

Each module should define its standard reports.

Examples:

## CRM

- Customer List
- Lead Pipeline
- Customer Categories
- Customer Activity

---

## Sales

- Quotation Register
- Sales Order Register
- Invoice Register
- Receipt Register
- Sales by Customer
- Sales by Branch

---

## Inventory

- Item List
- Stock Balance
- Stock Movement
- Low Stock
- Stock Valuation

---

## Procurement

- Purchase Request Register
- Purchase Order Register
- Supplier List
- Goods Received Notes

---

## Finance

- Receipt Register
- Payment Register
- Journal Register
- Trial Balance
- Profit and Loss
- Balance Sheet

---

## HR

- Employee List
- Leave Register
- Payroll Summary
- Recruitment Report

---

# 18. Integration

Business modules integrate with the Reporting Engine by exposing approved datasets.

Integration flow:

```text
Business Module

↓

Dataset

↓

Reporting Engine

↓

Report / Dashboard / Export

```
