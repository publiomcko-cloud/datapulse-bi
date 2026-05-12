# Screen Flows and Navigation — DataPulse BI

## 1. Purpose

This document guides frontend construction and user experience for the DataPulse BI dashboard.

The MVP should have a clean, professional, and recruiter-friendly interface that clearly communicates business value.

## 2. User Roles

## 2.1 Business Viewer

The only MVP role.

The Business Viewer can:

- view KPIs
- filter dashboard data
- inspect charts
- inspect recent orders
- view ingestion status
- understand data quality status
- submit a manual test order through the portfolio UI

No authentication is required in the MVP.

## 3. Planned Screens

## 3.0 Global Navigation

### Purpose

Provide a simple top menu so reviewers can move between the dashboard and the manual test flow without changing URLs manually.

### Main items

- Dashboard
- Add Test Order

### UX rule

The active page should be clearly highlighted and the navigation should stay visible across both primary portfolio screens.

## 3.1 Dashboard Screen

### Purpose

Main screen for business metrics and insights.

### Main sections

- header
- filter bar
- KPI cards
- revenue trend chart
- revenue trend grouping selector
- top products chart
- revenue by region chart
- revenue by channel chart
- recent orders table
- ingestion status panel
- data quality summary

### Available actions

- select date range
- filter by region
- filter by channel
- filter by category
- switch the revenue trend between day, week, month, and year grouping
- reset filters
- inspect latest ingestion run
- view recent orders
- open the Add Test Order page

### Data loaded from API

- `/metrics/summary`
- `/metrics/revenue-over-time`
- `/metrics/top-products`
- `/metrics/revenue-by-region`
- `/metrics/revenue-by-channel`
- `/orders`
- `/ingestion/runs/latest` with latest status, rejected counts, and recent issue summary

## 3.2 Manual Order Entry Screen

### Purpose

Let a reviewer create one new order through the UI and push it through the same ingestion and transformation flow used by the dataset pipeline.

### Main sections

- top navigation
- page introduction and usage guidance
- manual order form
- submission status card
- created order summary
- quality summary

### Available actions

- enter a new order payload
- leave `source_record_id` blank for backend generation
- submit the test order
- reset the form
- return to the dashboard
- inspect ingestion or quality outcomes

### Data loaded from API

- `POST /orders`

## 3.3 Project Case Study Section

This may be part of the README or a simple page in the frontend.

### Purpose

Explain the project as a portfolio case study.

### Main sections

- problem
- solution
- architecture
- data flow
- stack
- screenshots
- limitations
- roadmap

### Available actions

- open GitHub repository
- open API documentation
- open live dashboard

## 3.4 API Documentation Screen

Optional for MVP if FastAPI Swagger is exposed.

### Purpose

Allow technical reviewers to inspect API endpoints.

### Available actions

- view endpoints
- test sample requests
- inspect schemas

## 4. Dashboard Layout

Recommended layout:

```text
+------------------------------------------------------+
| Header: DataPulse BI                                 |
| Nav: Dashboard | Add Test Order                      |
| Subtitle: Lightweight data engineering dashboard     |
+------------------------------------------------------+
| Filters: date range | region | channel | category    |
+------------------------------------------------------+
| KPI 1      | KPI 2       | KPI 3       | KPI 4        |
+------------------------------------------------------+
| Revenue Over Time Chart                              |
+------------------------------------------------------+
| Top Products Chart       | Revenue by Region Chart   |
+------------------------------------------------------+
| Revenue by Channel Chart | Ingestion + Quality Panel |
+------------------------------------------------------+
| Recent Orders Table                                  |
+------------------------------------------------------+
```

## 5. Dashboard Components

## 5.1 Header

### Content

- project name
- top navigation links
- short description
- optional GitHub link
- optional API docs link

### UX rule

The header must quickly explain what the dashboard does.
It should also make moving to the manual order entry page obvious.

## 5.2 Filter Bar

### Fields

- start date
- end date
- region
- channel
- category

### Actions

- apply filters automatically or through button
- reset filters

### UX rule

Filters must be visible and easy to understand.

## 5.3 KPI Cards

### Minimum KPIs

- total revenue
- total orders
- average order value
- top category or top product

### States

- loading skeleton
- value available
- no data
- error

## 5.4 Revenue Over Time Chart

### Purpose

Show business performance over time.

### Chart type

- line chart or bar chart

### Data fields

- date
- revenue
- order count optional

## 5.5 Top Products Chart

### Purpose

Show best-performing products.

### Chart type

- horizontal bar chart

### Data fields

- product name
- revenue
- quantity sold

## 5.6 Revenue by Region Chart

### Purpose

Compare regions.

### Chart type

- bar chart

### Data fields

- region
- revenue

## 5.7 Revenue by Channel Chart

### Purpose

Compare sales channels.

### Chart type

- pie chart, donut chart, or bar chart

### Data fields

- channel
- revenue

## 5.8 Recent Orders Table

### Purpose

Show detailed records behind the metrics.

### Columns

- order date
- product
- category
- region
- channel
- quantity
- total amount

### Actions

- pagination
- sorting optional
- filtering through global filters

## 5.9 Ingestion Status Panel

### Purpose

Show that the system has operational awareness.

### Fields

- latest run status
- job name
- source name
- started at
- finished at
- records read
- records inserted
- records rejected
- error message if any

## 5.10 Data Quality Summary

### Purpose

Show rejected or problematic records.

### Fields

- total rejected records
- main issue types
- latest issue timestamp

## 6. Main User Flow

```text
Open dashboard
    |
    v
View KPI summary
    |
    v
Inspect trend chart
    |
    v
Apply filter
    |
    v
Compare chart changes
    |
    v
Inspect recent orders
    |
    v
Check ingestion status
```

## 7. Interface States

Each API-connected component should support:

### Loading

Show skeleton or loading indicator.

### Empty

Show useful message:

```text
No data found for the selected filters.
```

### Error

Show clear message:

```text
Unable to load metrics. Please check the backend API.
```

### Success

Show data with clean formatting.

## 8. Reusable Components

Recommended components:

- `DashboardHeader`
- `FilterBar`
- `KpiCard`
- `ChartCard`
- `RevenueTrendChart`
- `TopProductsChart`
- `RegionChart`
- `ChannelChart`
- `OrdersTable`
- `IngestionStatusPanel`
- `DataQualityPanel`
- `ErrorState`
- `EmptyState`
- `LoadingSkeleton`

## 9. Basic UX Rules

- Use simple language
- Avoid visual clutter
- Show units clearly
- Format currency consistently
- Format dates consistently
- Keep filters visible
- Make charts readable on desktop and mobile
- Never show raw technical errors to users
- Use tooltips for unclear fields
- Prefer business meaning over technical jargon

## 10. Navigation

The MVP can be single-page.

Optional navigation:

```text
Dashboard
API Docs
GitHub
Case Study
```

If multiple pages are used:

- `/` redirects to dashboard
- `/dashboard` shows metrics
- `/case-study` explains the project
- `/api-docs` links to backend Swagger

## 11. Mobile Behavior

The dashboard should be responsive.

Recommended mobile structure:

1. header
2. filters
3. KPI cards stacked
4. revenue trend chart
5. ranking charts stacked
6. ingestion status
7. recent orders table with horizontal scroll

## 12. Frontend Acceptance Criteria

The frontend is acceptable when:

- the dashboard loads without errors
- KPI values come from the backend
- filters update API requests
- charts render correctly
- table displays recent orders
- loading and error states exist
- layout works on desktop and mobile
- the UI looks professional enough for portfolio presentation
