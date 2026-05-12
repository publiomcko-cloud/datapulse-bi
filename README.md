# DataPulse BI

A full-stack data product portfolio project that demonstrates lightweight data engineering, SQL modeling, and business intelligence through a complete public-data pipeline, dashboard, and API.

## 1. Project Summary

**DataPulse BI** is a portfolio-grade data product designed to show the ability to ingest external data, transform it into a clean analytical model, expose business-ready metrics, and present insights through an interactive dashboard.

The project simulates a real client scenario: a small company or analyst team needs to monitor key indicators from raw public or synthetic datasets without manually cleaning spreadsheets every week.

The system focuses on:

- automated data ingestion
- data cleaning and transformation
- relational modeling with PostgreSQL
- analytical queries
- backend API for metrics
- dashboard for business users
- reproducible local setup
- clear documentation and deployment path

## 2. Problem Being Solved

Many small businesses, analysts, and teams still depend on manual spreadsheet workflows. This creates several problems:

- data is copied manually from different sources
- calculations are repeated by hand
- errors are difficult to detect
- historical comparisons are inconsistent
- dashboards are not connected to a reliable data model
- decision-making depends on outdated or fragmented information

This project solves that problem by turning raw data into a structured, repeatable, and visual data product.

## 3. Target Audience

This project is useful for:

- recruiters evaluating full-stack and data engineering skills
- freelance clients that need dashboards and automated reports
- small businesses that depend on spreadsheet-based workflows
- technical reviewers evaluating SQL, backend, and deployment practices
- portfolio visitors looking for evidence of real project execution

## 4. Main Features

### MVP Features

- Import data from CSV files or a public API
- Validate and normalize raw records
- Store raw and transformed data in PostgreSQL
- Run transformation jobs through Python
- Expose metrics through a backend API
- Display charts, KPIs, filters, and tables in a frontend dashboard
- Switch the revenue trend between day, week, month, and year buckets
- Navigate between the dashboard and a manual test-order page from a shared top menu
- Submit a single test order through the UI to exercise the live ingestion and transformation path
- Provide logs for ingestion and transformation runs
- Include seed data for local testing
- Include basic automated tests
- Provide a reproducible local setup with Docker

### Future Features

- Scheduled ingestion jobs
- Authentication for private dashboards
- Export reports as CSV or PDF
- Multiple datasets
- Row-level access by organization
- AI-generated insight summaries
- Data quality alerts
- Cloud data warehouse integration

## 5. Suggested Use Case for the MVP

The recommended MVP dataset is a **public business or economic dataset**, such as:

- Brazilian public economic indicators
- e-commerce sales sample data
- public mobility or tourism data
- synthetic small-business sales data
- public energy consumption data
- public financial market daily data

For portfolio clarity, the first version may use synthetic sales data because it allows clean storytelling:

> A fictional business wants to monitor revenue, orders, average ticket, product performance, customer segments, and regional trends.

## 6. Technology Stack

### Frontend

- Next.js
- TypeScript
- Tailwind CSS
- Recharts
- TanStack Query

### Backend

- FastAPI
- Python
- Pydantic
- SQLAlchemy or SQLModel
- Alembic

### Data Pipeline

- Python
- Pandas
- Polars optional for performance
- Pandera or Great Expectations optional for validation
- Cron or scheduler in later versions

### Database

- PostgreSQL
- Separate schemas or tables for raw, staging, and analytics layers

### Infrastructure

- Docker
- Docker Compose
- GitHub Actions
- Vercel for frontend
- Render, Railway, Fly.io, or a VPS for backend and database

## 7. Summarized Architecture

```text
External Data Source
        |
        v
Ingestion Script / API Connector
        |
        v
Raw Data Tables
        |
        v
Transformation Layer
        |
        v
Analytics Tables / Views
        |
        v
FastAPI Backend
        |
        v
Next.js Dashboard
```

## 8. Basic Run Instructions

```bash
git clone <repository-url>
cd datapulse-bi
cp .env.example .env
docker compose up -d
```

Run backend:

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
alembic upgrade head
python scripts/check_db_connection.py
uvicorn app.main:app --reload
```

Run ingestion:

```bash
cd backend
source .venv/bin/activate
python scripts/ingest_data.py
python scripts/transform_data.py
```

Generate a larger random dataset for local testing:

```bash
cd backend
source .venv/bin/activate
python scripts/generate_random_orders.py --count 1000
python scripts/ingest_data.py --csv-path ../data/generated_orders_1000.csv --source-name random_orders_1000
python scripts/transform_data.py
```

Run smoke checks:

```bash
cd backend
source .venv/bin/activate
python scripts/run_smoke_checks.py
```

Inspect local database state:

```bash
docker compose ps
docker exec datapulse_postgres psql -U datapulse -d datapulse -c "\dt"
docker exec datapulse_postgres psql -U datapulse -d datapulse -c "SELECT job_name, source_name, status, records_read, records_inserted, records_rejected FROM ingestion_runs ORDER BY created_at, started_at;"
docker exec datapulse_postgres psql -U datapulse -d datapulse -c "SELECT COUNT(*) AS stg_orders_count FROM stg_orders; SELECT COUNT(*) AS fact_orders_count FROM fact_orders;"
```

Inspect the API:

```bash
curl http://localhost:8000/health
curl http://localhost:8000/ingestion/runs/latest
curl http://localhost:8000/metrics/summary
curl "http://localhost:8000/orders?limit=5&offset=0"
curl -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"order_date":"2026-04-06","customer_id":"cust-manual-001","product_id":"prod-manual-001","product_name":"Portable Projector","category":"electronics","region":"south","channel":"online","quantity":1,"unit_price":459.0}'
```

Run frontend:

```bash
cd frontend
cp .env.local.example .env.local
npm install
npm run dev

cd backend
uvicorn app.main:app --reload
```

Expected local URLs:

- dashboard: `http://localhost:3000`
- add test order page: `http://localhost:3000/orders/new`
- API: `http://localhost:8000`
- API docs: `http://localhost:8000/docs`

Production-like Docker validation:

```bash
docker compose -p datapulse-bi-prod -f docker-compose.production.yml up -d --build
docker exec datapulse_backend_prod alembic upgrade head
docker exec datapulse_backend_prod python scripts/seed_demo_data.py
curl http://localhost:8000/health
curl http://localhost:8000/metrics/summary
docker compose -p datapulse-bi-prod -f docker-compose.production.yml down
```

## 9. Roadmap

### Phase 1: Documentation and repository setup

- Create project structure
- Add documentation
- Define database model
- Add local environment files

### Phase 2: Data ingestion and database

- Create PostgreSQL schema
- Add seed dataset
- Build ingestion script
- Add transformation logic

### Phase 3: Backend API

- Create FastAPI application
- Add metric endpoints
- Add filters and pagination
- Add tests

### Phase 4: Frontend dashboard

- Create dashboard layout
- Add shared filters for date, category, region, and channel
- Add KPI cards, charts, and recent orders table
- Add ingestion status and data quality summary panel
- Add shared top navigation and a manual test-order page for reviewers

### Phase 5: Deployment and portfolio polish

- Deploy frontend
- Deploy backend
- Add demo video
- Add screenshots
- Improve README and case study

## 10. Project Status

Milestone 2 through milestone 8 deployment foundation implemented:

- local PostgreSQL environment configured with Docker Compose
- backend scaffold created with FastAPI, SQLAlchemy, and settings management
- Alembic configured with an initial migration
- core raw, staging, analytics, operational, and data quality tables created
- synthetic seed dataset added at `data/sample_orders.csv`
- ingestion pipeline implemented at `backend/scripts/ingest_data.py`
- transformation pipeline implemented at `backend/scripts/transform_data.py`
- deterministic random dataset generator implemented at `backend/scripts/generate_random_orders.py`
- ingestion runs are logged in PostgreSQL with read, insert, and reject counts
- duplicate rows are rejected by `row_hash` and recorded as `data_quality_issues`
- valid raw rows are normalized into `stg_orders`, dimensions, and `fact_orders`
- transformation rejections are recorded in `data_quality_issues`
- Next.js frontend scaffolded in `frontend/` with Tailwind CSS
- dashboard implemented with TanStack Query and Recharts
- dashboard connected to `/metrics/*`, `/orders`, `POST /orders`, and `/ingestion/runs/latest`
- filterable KPI cards, charts, recent orders table, and pipeline status panel are available
- revenue trend includes a day, week, month, and year grouping selector with automatic weekly fallback for new long date ranges
- shared top navigation is available between the dashboard and `/orders/new`
- manual order page can submit a single record and display the resulting pipeline status
- local backend now exposes CORS configuration for `http://localhost:3000`
- FastAPI endpoints implemented for health, ingestion status, metrics, and orders
- the API is validated by automated tests and manual endpoint checks against the local dataset
- database connectivity and ingestion flow validated against the local PostgreSQL container
- dedicated metric service tests validate summary, breakdown, filter, and ordering behavior
- smoke coverage exists in `backend/tests/test_smoke.py`
- local smoke command exists at `backend/scripts/run_smoke_checks.py`
- current validation baseline is `16` passing backend tests plus successful frontend lint and production build
- production-ready backend image exists at `backend/Dockerfile`
- production-ready frontend image exists at `frontend/Dockerfile`
- Render blueprint exists at `render.yaml`
- production-like local stack exists at `docker-compose.production.yml`
- demo-data seed command exists at `backend/scripts/seed_demo_data.py`
- production-like deployment flow was validated locally on April 29, 2026

## 11. Deployment Status

- frontend live URL: pending deployment
- backend live URL: pending deployment
- live API docs URL: pending deployment
- production configuration status: ready and locally validated
- hosting validation status: pending Vercel and Render account setup

## 12. Portfolio Value

This project is designed to prove skills that are valuable for both employment and freelance work:

- SQL modeling
- data pipelines
- API design
- dashboard development
- Docker-based local setup
- deployment
- technical documentation
- ability to transform data into business value
