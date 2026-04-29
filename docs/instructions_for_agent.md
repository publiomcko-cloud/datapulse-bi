# Instructions for Development Agent — DataPulse BI

## 1. Purpose

This document guides an AI development agent or human developer responsible for implementing DataPulse BI.

The project must be developed as a professional portfolio project, not as a quick tutorial.

## 2. Project Objective

Build a complete full-stack data product that demonstrates:

- data ingestion
- data validation
- data transformation
- PostgreSQL modeling
- FastAPI backend
- Next.js dashboard
- business metrics
- local reproducibility
- testing
- deployment readiness
- strong documentation

## 3. Project Priorities

The priorities are:

1. working end-to-end flow
2. clear data model
3. professional dashboard
4. reproducible local setup
5. tests for critical logic
6. strong README
7. deployment readiness

Avoid adding advanced tools before the core flow works.

## 4. Document Hierarchy

In case of conflict, follow this order:

1. `docs/mvp_scope.md`
2. `docs/architecture.md`
3. `docs/database_modeling.md`
4. `docs/mvp_backlog.md`
5. `docs/technical_specification.md`
6. `docs/screen_flows.md`
7. `docs/local_setup_execution.md`
8. `README.md`

The MVP scope controls what should and should not be built first.

## 5. Required Stack

### Frontend

- Next.js
- TypeScript
- Tailwind CSS
- Recharts or ECharts
- TanStack Query recommended

### Backend

- FastAPI
- Python
- Pydantic
- SQLAlchemy or SQLModel
- Alembic

### Database

- PostgreSQL

### Data pipeline

- Python
- Pandas
- optional Pandera for validation

### Local infrastructure

- Docker
- Docker Compose

### Testing

- Pytest for backend and pipeline tests
- frontend linting at minimum

## 6. Implementation Order

Follow this order:

1. create project structure
2. configure Docker Compose with PostgreSQL
3. create backend app with healthcheck
4. configure database connection
5. configure migrations
6. create database models
7. add seed dataset
8. implement ingestion script
9. implement transformation script
10. create metric query services
11. expose API endpoints
12. create frontend dashboard
13. connect frontend to backend
14. add tests
15. polish README
16. prepare deployment

## 7. Scope Rules

The MVP must include:

- one dataset
- one dashboard
- one backend API
- one PostgreSQL database
- one ingestion flow
- one transformation flow
- basic tests
- documentation

The MVP must not include:

- authentication
- payment system
- multi-tenant architecture
- Airflow
- Kafka
- Spark
- Kubernetes
- complex AI features
- real private customer data

## 8. Quality Rules

The agent must:

- use clear file organization
- write readable code
- avoid hardcoded secrets
- use environment variables
- document important decisions
- create small commits when possible
- avoid unnecessary dependencies
- keep the dashboard simple and professional
- implement error handling
- add basic tests before calling the project complete

## 9. Backend Rules

The backend must:

- expose `/health`
- use typed response schemas
- validate query parameters
- separate routes from services
- separate database models from schemas
- handle empty results gracefully
- return consistent error responses
- avoid business logic directly inside route functions

Recommended endpoint structure:

```text
GET /health
GET /ingestion/runs/latest
GET /metrics/summary
GET /metrics/revenue-over-time
GET /metrics/top-products
GET /metrics/revenue-by-region
GET /metrics/revenue-by-channel
GET /orders
```

## 10. Data Pipeline Rules

The pipeline must:

- preserve raw data
- validate important fields
- log ingestion runs
- reject invalid records clearly
- produce analytics-ready tables
- be executable from command line
- avoid modifying raw records manually

Scripts:

```text
backend/scripts/ingest_data.py
backend/scripts/transform_data.py
```

## 11. Database Rules

The database must:

- include ingestion logs
- include raw records
- include clean records
- include dimension tables
- include fact table
- include useful indexes
- avoid sensitive real data

Use migrations instead of manually creating tables.

## 12. Frontend Rules

The frontend must:

- load metrics from backend
- provide filters
- show KPI cards
- show charts
- show recent orders
- show ingestion status
- handle loading states
- handle error states
- be responsive
- use clear labels

The dashboard must communicate business value, not only technical data.

## 13. Testing Rules

Minimum tests:

- transformation rule test
- revenue calculation test
- healthcheck API test
- one metric endpoint test

The project should not be considered complete if no tests exist.

## 14. Documentation Rules

The agent must update documentation when:

- architecture changes
- commands change
- environment variables change
- database model changes
- deployment steps change

Documentation should remain practical and direct.

## 15. Minimum Done Criteria

The first complete version is done when:

- local environment runs
- data ingestion works
- transformation works
- database stores analytical data
- backend returns metrics
- frontend displays metrics
- filters work
- tests pass
- README explains setup and value
- project can be shown as a portfolio case study

## 16. First Deliverable Expected from Agent

The agent should first deliver:

- project folder structure
- Docker Compose
- backend healthcheck
- PostgreSQL connection
- initial database migration
- seed dataset plan

Do not start by building UI before the data flow is defined.

## 17. Agent Behavior

The agent should work incrementally.

For each implementation cycle:

1. explain what will be changed
2. edit or create files
3. run relevant command
4. fix errors
5. summarize result
6. suggest next step

Do not skip validation.
