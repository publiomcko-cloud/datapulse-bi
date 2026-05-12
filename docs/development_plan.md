# DataPulse BI Development Plan

## 1. Project Summary

DataPulse BI is a portfolio-grade data product designed to demonstrate a complete data flow:

- ingest raw data from a CSV or synthetic dataset
- validate and store raw records in PostgreSQL
- transform raw records into clean analytical tables
- expose business metrics through a FastAPI backend
- present insights in a Next.js dashboard
- support reproducible local development with Docker

This development plan is based on the existing project documentation and aligns with the MVP scope, backlog, architecture, database modeling, testing, and deployment strategy.

Current repository status:

- the documentation baseline already exists
- milestone 2 through milestone 8 deployment foundation are implemented
- milestone 9 portfolio shell now includes shared top navigation and a manual order testing page
- local PostgreSQL, backend configuration, ORM models, and Alembic migration are in place
- the sample dataset, ingestion pipeline, transformation pipeline, API routes, and frontend dashboard are in place
- a deterministic random dataset generator is available for larger local validation loads
- database connectivity, ingestion behavior, transformation behavior, API behavior, smoke checks, frontend linting, frontend production build, and production-like container deployment have been validated against the local Docker environment

---

## 2. Development Process

The development process is organized in phases to keep the MVP focused and deliverable.

1. Repository and documentation setup
2. Local infrastructure and database modeling
3. Data ingestion and seed dataset
4. Data validation and transformation
5. Backend API implementation
6. Frontend dashboard implementation
7. Testing and quality validation
8. Deployment
9. Portfolio publication and polish

Each phase includes clear milestones, deliverables, and task lists.

---

## 3. Milestones

### Milestone 1: Repository and Documentation Setup

Objective: establish a clear project foundation and documentation baseline.

Deliverables:

- repository structure created
- README and docs in place
- `.gitignore`, `.env.example`, and `docker-compose.yml` configured
- basic instructions for local development

Tasks:

- [x] Create root repository layout
- [x] Add frontend, backend, data, and docs folders
- [x] Write README with project summary and setup steps
- [x] Create documentation files for scope, backlog, architecture, database modeling, testing, deployment, and screen flows
- [x] Provide `.env.example` and Docker Compose configuration

### Milestone 2: Local Infrastructure and Database Modeling

Objective: provide a working local PostgreSQL environment and a database schema that supports raw, staging, analytics, and operational data.

Deliverables:

- Docker Compose runs PostgreSQL locally
- database connection is verified
- migrations and schema definitions exist
- raw, staging, dimension, fact, ingestion log, and data quality tables are defined

Tasks:

- [x] Configure `docker-compose.yml` for PostgreSQL
- [x] Define database environment variables
- [x] Build database models for ingestion runs, raw orders, staging orders, fact orders, dimensions, and `data_quality_issues`
- [x] Create Alembic migrations or equivalent schema setup
- [x] Validate database connectivity with a simple script

### Milestone 3: Seed Dataset and Ingestion

Objective: ingest sample data into the raw layer and capture ingestion metadata.

Deliverables:

- sample dataset available in `data/`
- ingestion script loads raw records
- ingestion run logging records execution details
- duplicate detection is in place

Tasks:

- [x] Choose or create the MVP dataset (synthetic sales recommended)
- [x] Add `data/sample_orders.csv` or generator script
- [x] Implement ingestion script to load raw records
- [x] Store raw values as text and preserve source fields
- [x] Calculate row hashes and prevent duplicates
- [x] Record ingestion run status, counts, and errors
- [x] Add basic unit tests for ingestion behavior

### Milestone 4: Validation and Transformation

Objective: clean raw data and populate analytical tables.

Deliverables:

- staging table populated with normalized data
- dimensions loaded and normalized
- fact table created with revenue and order metrics
- invalid records are rejected and logged for downstream quality reporting

Tasks:

- [x] Implement required field validation and parsing logic
- [x] Normalize categories, regions, and channels
- [x] Parse dates, quantities, and monetary values
- [x] Calculate total order amounts when needed
- [x] Upsert dimension records for products, customers, regions, and channels
- [x] Insert analytic facts into `fact_orders`
- [x] Create `data_quality_issues` records for rejected or suspicious rows
- [x] Add transformation logging and error reporting
- [x] Add tests for transformation rules and invalid input handling

### Milestone 5: Backend API

Objective: expose analytic metrics and operational status to the frontend.

Deliverables:

- FastAPI backend running
- healthcheck endpoint
- ingestion status endpoints with latest-run data quality summary
- metric endpoints supporting filters
- orders endpoints for recent records and manual order submission
- API tests for key endpoints

Tasks:

- [x] Create FastAPI project scaffold
- [x] Add settings, database session management, and models
- [x] Implement `/health`
- [x] Implement `/ingestion/runs` and `/ingestion/runs/latest`
- [x] Make `/ingestion/runs/latest` return rejected counts and recent issue summary for the dashboard
- [x] Implement `/metrics/summary`
- [x] Implement `/metrics/revenue-over-time`
- [x] Implement `/metrics/top-products`
- [x] Implement `/metrics/revenue-by-region`
- [x] Implement `/metrics/revenue-by-channel`
- [x] Implement `GET /orders` with query filters
- [x] Implement `POST /orders` for manual order submission
- [x] Add response schemas and error handling
- [x] Add tests for healthcheck and metric endpoints

### Milestone 6: Frontend Dashboard

Objective: present the business story through a polished dashboard.

Deliverables:

- Next.js app with dashboard layout
- KPI cards, charts, filters, tables
- ingestion status and data quality summary
- responsive presentation
- revenue trend grouping controls for dense time-series ranges

Tasks:

- [x] Initialize Next.js frontend with TypeScript and Tailwind CSS
- [x] Build dashboard page and layout
- [x] Add filter bar for date range, region, channel, category
- [x] Add KPI cards for revenue, orders, average order value, top product
- [x] Add revenue trend chart
- [x] Add day, week, month, and year grouping controls to the revenue trend
- [x] Add top products chart
- [x] Add region and channel charts
- [x] Add recent orders table
- [x] Add ingestion status panel with latest-run quality summary
- [x] Add loading, error, and empty states
- [x] Connect frontend to backend APIs using TanStack Query or similar

### Milestone 7: Testing and Validation

Objective: ensure the core data and API flows are reliable.

Deliverables:

- backend unit and integration tests
- smoke tests for startup and healthcheck
- documented test commands

Tasks:

- [x] Add unit tests for validation and transformation logic
- [x] Add integration tests for metric endpoints
- [x] Add API healthcheck test
- [x] Add smoke tests for pipeline flows
- [x] Confirm tests run successfully with `pytest`
- [x] Document test commands in README

### Milestone 8: Deployment

Objective: make the project deployable and ready for portfolio review.

Deliverables:

- deployment documentation exists
- backend production configuration is defined
- frontend production URL is prepared
- live demo links can be added to README

Tasks:

- [x] Document deployment steps for frontend and backend
- [x] Add production environment variable guidance
- [x] Define CORS policy for deployed backend
- [x] Add migration and seed loading instructions for production
- [ ] Validate backend healthcheck and dashboard on hosted environment
- [x] Add deployment status and links to README

### Milestone 9: Portfolio Publication and Polish

Objective: make the project convincing and easy to review for recruiters, clients, and technical evaluators.

Deliverables:

- polished README and case study section
- screenshots and architecture visuals
- demo video or walkthrough link
- known limitations and roadmap published
- top navigation between portfolio screens
- manual order entry page for reviewer testing

Tasks:

- [x] Add a top navigation menu between the dashboard and the manual order entry page
- [x] Add a manual order entry page wired to `POST /orders` so reviewers can inject test records
- [ ] Add business problem and portfolio narrative to README
- [ ] Add architecture diagram and screenshots
- [ ] Add demo video or walkthrough link
- [ ] Add known limitations and next-step roadmap
- [ ] Make README link clearly to live dashboard and API docs when available

---

## 4. To-Do Milestones

Use this section as a checklist to track implementation progress.

### Phase 0 — Repository and Docs

- [x] Project structure created
- [x] README written
- [x] docs folder and documentation files completed
- [x] `.gitignore` added
- [x] `.env.example` added
- [x] `docker-compose.yml` added

### Phase 1 — Database and Local Infrastructure

- [x] Docker Compose PostgreSQL configured
- [x] backend dependencies installed
- [x] DB session and config implemented
- [x] migrations or schema scripts created
- [x] raw/staging/analytics tables defined
- [x] data quality issue table defined

### Phase 2 — Ingestion and Seed Data

- [x] MVP dataset chosen or generated
- [x] ingestion script built
- [x] ingestion logging implemented
- [x] duplicate detection implemented
- [x] raw table populated

### Phase 3 — Transformation and Modeling

- [x] validation logic implemented
- [x] staging layer filled
- [x] dimensions loaded
- [x] fact table loaded
- [x] data quality issues handled

### Phase 4 — Backend API

- [x] FastAPI scaffold created
- [x] metrics endpoints added
- [x] ingestion status endpoints added
- [x] response schemas created
- [x] endpoint tests written

### Phase 5 — Frontend Dashboard

- [x] dashboard page created
- [x] KPI cards added
- [x] charts and tables added
- [x] filter controls added
- [x] API integration added

### Phase 6 — Testing

- [x] validation tests added
- [x] transformation tests added
- [x] API tests added
- [x] smoke tests added
- [x] test commands documented

### Phase 7 — Deployment

- [x] deployment strategy documented
- [x] production env vars defined
- [x] migration instructions added
- [x] demo data loading documented
- [x] README includes deployment links and status

### Phase 8 — Portfolio Polish

- [x] top navigation added
- [x] manual test order page added
- [ ] README case study completed
- [ ] screenshots added
- [ ] demo video or walkthrough link added
- [ ] limitations and roadmap published

---

## 5. Completed Tasks (Placeholder)

This section is reserved for future completion tracking. Add dates or summaries when tasks are finished.

### Completed Phase 0

- [x] Repository structure finalized
- [x] README and docs created
- [x] Docker Compose and environment examples added

### Completed Phase 1

- [x] Local PostgreSQL environment verified
- [x] Database modeling and migrations created
- [x] Core tables defined

### Completed Phase 2

- [x] Sample dataset finalized
- [x] Ingestion pipeline implemented
- [x] Raw data loaded and validated

### Completed Phase 3

- [x] Transformation pipeline completed
- [x] Staging and analytics tables populated
- [x] Data normalization rules implemented

### Completed Phase 4

- [x] Backend API endpoints available
- [x] Healthcheck and metrics endpoints implemented
- [x] API tests passing

### Completed Phase 5

- [x] Frontend dashboard built
- [x] API integration complete
- [x] Dashboard displays KPI and chart data

### Completed Phase 6

- [x] Tests added and passing
- [x] Smoke tests executed
- [x] Test documentation updated

### Completed Phase 7

- [x] Deployment documentation finalized
- [x] Production environment variables configured
- [ ] Demo and live links added to README

### Completed Phase 8

- [x] top navigation between dashboard pages implemented
- [x] manual order testing page connected to the live API
- [ ] README case study completed
- [ ] Screenshots published
- [ ] Demo video or walkthrough link published
- [ ] Portfolio narrative finalized

---

## 6. Notes and Recommendations

- Keep the MVP tightly focused on the core data flow: ingestion → transformation → API → dashboard.
- Prefer synthetic sales data for a strong portfolio narrative.
- Avoid adding authentication, multi-tenant support, or advanced orchestration in the first version.
- Keep the latest ingestion response useful for the dashboard by including data quality counts and recent issue summaries.
- Use `alembic upgrade head` plus `python scripts/check_db_connection.py` as the baseline environment verification for local development.
- Re-running `python scripts/ingest_data.py` should demonstrate duplicate protection by increasing rejected counts without duplicating `raw_orders`.
- Run `python scripts/transform_data.py` after a successful ingestion run to populate `stg_orders`, dimensions, and `fact_orders`.
- Use `/health`, `/ingestion/runs/latest`, `/metrics/summary`, and `/orders` as the quickest manual validation path for the backend API.
- Use the dashboard top navigation to move between `/` and `/orders/new` when demonstrating the project to reviewers.
- Use `docker compose -p datapulse-bi-prod -f docker-compose.production.yml up -d --build` plus `python scripts/seed_demo_data.py` inside the backend container for production-like validation before a real hosting push.
- Document every step clearly so recruiters and reviewers can understand both architecture and implementation.
- Use the existing docs as the source of truth for scope, backlog, architecture, and testing.
