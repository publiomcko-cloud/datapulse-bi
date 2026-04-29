# MVP Scope — DataPulse BI

## 1. Purpose

This document defines what belongs to the first version of DataPulse BI and what must be postponed.

The goal is to prevent uncontrolled scope growth and keep the first version realistic, complete, and presentable.

## 2. MVP Objective

The MVP must prove that the system can:

1. ingest a dataset
2. store raw data
3. transform data into analytical structures
4. expose metrics through an API
5. display those metrics in a dashboard
6. run locally with documented instructions
7. demonstrate portfolio-level engineering quality

## 3. Included Features

## 3.1 Dataset

The MVP includes one dataset.

Accepted options:

- synthetic sales dataset
- public CSV dataset
- simple public API dataset

The recommended option is a synthetic sales dataset because it allows strong control over the story and edge cases.

## 3.2 Data ingestion

Included:

- import from CSV or generated file
- raw table insertion
- ingestion run logging
- duplicate detection by row hash
- basic error handling

Not included:

- multiple external APIs
- authenticated data providers
- real-time ingestion
- streaming pipelines

## 3.3 Data validation

Included:

- required field validation
- date parsing
- numeric parsing
- positive quantity validation
- non-negative revenue validation
- duplicate detection
- rejection logging

Not included:

- advanced anomaly detection
- machine learning-based validation
- complex data contracts

## 3.4 Data transformation

Included:

- raw to staging transformation
- staging to analytics transformation
- category normalization
- region normalization
- channel normalization
- total revenue calculation
- dimension and fact loading

Not included:

- dbt in the first version
- slowly changing dimensions
- incremental warehouse logic
- distributed processing

## 3.5 Database

Included:

- PostgreSQL
- ingestion run table
- raw orders table
- staging orders table
- dimension tables
- fact orders table
- recommended indexes

Not included:

- multi-tenant database
- row-level security
- data warehouse service
- database replication

## 3.6 Backend API

Included:

- healthcheck endpoint
- ingestion status endpoint
- KPI summary endpoint
- revenue over time endpoint
- top products endpoint
- revenue by region endpoint
- revenue by channel endpoint
- orders endpoint with filters

Not included:

- authentication
- payment system
- admin panel
- user management
- write operations from the frontend

## 3.7 Frontend dashboard

Included:

- responsive dashboard page
- KPI cards
- revenue trend chart
- top products chart
- revenue by region chart
- revenue by channel chart
- recent orders table
- filter bar
- ingestion status panel
- loading, error, and empty states

Not included:

- multi-page admin system
- drag-and-drop dashboard builder
- custom report builder
- user-created charts
- mobile app

## 3.8 Testing

Included:

- unit tests for transformation rules
- unit tests for metric calculations
- API healthcheck test
- at least one API metric endpoint test

Not included:

- full end-to-end browser testing
- load testing
- complex test environment orchestration

## 3.9 Deployment

Included:

- documented deployment path
- environment variable strategy
- public frontend link if possible
- backend healthcheck validation

Not included:

- high-availability deployment
- Kubernetes
- autoscaling
- complex monitoring stack

## 4. Scope Boundaries

The MVP must stay focused on the complete data flow.

The following rule should guide decisions:

> If a feature does not help demonstrate ingestion, transformation, metrics, dashboard, or portfolio value, it should wait.

## 5. User Roles

The MVP has one user role:

### Business Viewer

A user who wants to view business indicators through a dashboard.

Permissions:

- view metrics
- use filters
- inspect recent orders
- view ingestion status

No login is required in the MVP because the data is public demo data.

## 6. Done Criteria

The MVP is done when:

- a developer can run the database locally
- a developer can run ingestion and transformation scripts
- analytical tables are populated
- backend endpoints return real metrics from PostgreSQL
- frontend displays those metrics
- filters affect displayed data
- at least four critical tests pass
- README explains the project clearly
- documentation is present
- screenshots or demo video are available

## 7. Expected Demo Flow

A strong demo should follow this sequence:

1. Open the repository README.
2. Explain the business problem.
3. Show the architecture diagram.
4. Start the local environment.
5. Run ingestion.
6. Run transformation.
7. Start backend.
8. Start frontend.
9. Open dashboard.
10. Filter by date, region, or channel.
11. Show KPI changes.
12. Show ingestion status.
13. Mention tests and deployment plan.

## 8. Items That Must Wait

The following items must not be included in the first version unless the MVP is already complete:

- authentication
- multi-tenant accounts
- Airflow
- Spark
- Kafka
- Kubernetes
- AI insight generation
- PDF report generation
- dashboard builder
- cloud warehouse
- paid data sources
- real customer data

## 9. Success Measurement

The MVP should be evaluated by:

- clarity of the data story
- completeness of the pipeline
- quality of SQL modeling
- dashboard usability
- ease of local setup
- strength of README
- ability to explain the project in interviews or client calls

## 10. Portfolio Acceptance Criteria

The project is portfolio-ready when it has:

- public repository
- clear README
- working local setup
- dashboard screenshots
- architecture diagram
- documented API endpoints
- test instructions
- known limitations
- roadmap
- optional deployed demo
