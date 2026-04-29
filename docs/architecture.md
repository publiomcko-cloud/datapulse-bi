# Architecture — DataPulse BI

## 1. Architectural Overview

DataPulse BI follows a modular full-stack architecture with a clear separation between data ingestion, transformation, backend API, frontend dashboard, and database.

The system is intentionally designed to be simple enough for a portfolio MVP, while still resembling a real-world data product.

## 2. Architecture Goals

The architecture must support:

- reproducible local execution
- clear separation of responsibilities
- easy testing
- simple deployment
- future scheduling of ingestion jobs
- future authentication
- future support for multiple datasets

## 3. High-Level Flow

```text
Source Dataset
    |
    v
Ingestion Service
    |
    v
Raw Tables
    |
    v
Transformation Service
    |
    v
Analytics Tables / Views
    |
    v
Backend API
    |
    v
Frontend Dashboard
```

## 4. Main Components

## 4.1 Frontend

The frontend is responsible for user interaction and data visualization.

Recommended stack:

- Next.js
- TypeScript
- Tailwind CSS
- Recharts or ECharts
- TanStack Query

Responsibilities:

- render dashboard layout
- request metrics from backend
- manage filters
- display charts and tables
- show loading, error, and empty states
- provide a professional portfolio presentation

The frontend must not calculate complex business metrics if those metrics should belong to the backend or database layer.

## 4.2 Backend

The backend exposes business metrics and operational information to the frontend.

Recommended stack:

- FastAPI
- Python
- Pydantic
- SQLAlchemy or SQLModel
- Alembic

Responsibilities:

- expose REST API endpoints
- validate query parameters
- read analytical tables
- return typed responses
- expose healthcheck endpoint
- expose ingestion run status
- handle API errors consistently

## 4.3 Data Pipeline

The data pipeline handles ingestion, validation, and transformation.

Recommended tools:

- Python
- Pandas
- optional Polars
- optional Pandera
- structured logging

Responsibilities:

- load source data
- validate required columns
- normalize values
- insert raw records
- transform raw records into analytical structures
- record ingestion and transformation logs

## 4.4 Database

PostgreSQL is the central data store.

Responsibilities:

- store raw imported data
- store transformed data
- store analytical tables
- store ingestion logs
- enforce constraints
- provide indexes for dashboard queries

## 4.5 Docker Environment

Docker Compose should run:

- PostgreSQL database
- optional backend container
- optional admin tool such as pgAdmin

For early development, the backend and frontend may run directly on the local machine while PostgreSQL runs in Docker.

## 5. Suggested Repository Structure

```text
datapulse-bi/
├── README.md
├── .gitignore
├── .env.example
├── docker-compose.yml
├── frontend/
│   ├── package.json
│   ├── src/
│   └── ...
├── backend/
│   ├── requirements.txt
│   ├── alembic.ini
│   ├── app/
│   │   ├── main.py
│   │   ├── api/
│   │   ├── core/
│   │   ├── db/
│   │   ├── models/
│   │   ├── schemas/
│   │   └── services/
│   ├── scripts/
│   │   ├── ingest_data.py
│   │   └── transform_data.py
│   └── tests/
└── docs/
```

## 6. Backend Internal Structure

```text
backend/app/
├── main.py
├── api/
│   ├── routes_health.py
│   ├── routes_metrics.py
│   └── routes_ingestion.py
├── core/
│   ├── config.py
│   └── logging.py
├── db/
│   ├── session.py
│   └── migrations/
├── models/
│   ├── raw_order.py
│   ├── fact_order.py
│   ├── dim_product.py
│   ├── dim_customer.py
│   └── ingestion_run.py
├── schemas/
│   ├── metrics.py
│   └── ingestion.py
└── services/
    ├── metrics_service.py
    └── ingestion_status_service.py
```

## 7. Database Layering Strategy

### 7.1 Raw layer

Stores source records with minimal transformation.

Example table:

- raw_orders

### 7.2 Staging layer

Stores cleaned and normalized data.

Example table:

- stg_orders

### 7.3 Analytics layer

Stores dashboard-ready data.

Example tables:

- fact_orders
- dim_products
- dim_customers
- dim_regions
- dim_channels

For the MVP, staging may be implemented as tables or transformation scripts. In later versions, dbt may be introduced.

## 8. API Design

### Health

```http
GET /health
```

Returns application and database status.

### Ingestion status

```http
GET /ingestion/runs
GET /ingestion/runs/latest
```

Returns ingestion execution history.

### Metrics

```http
GET /metrics/summary
GET /metrics/revenue-over-time
GET /metrics/top-products
GET /metrics/revenue-by-region
GET /metrics/revenue-by-channel
GET /orders
```

Common query parameters:

```text
start_date
end_date
category
region
channel
limit
offset
```

## 9. Frontend Screen Architecture

```text
Dashboard Layout
├── Header
├── Filter Bar
├── KPI Cards
├── Revenue Trend Chart
├── Revenue by Category Chart
├── Top Products Chart
├── Revenue by Region Chart
├── Orders Table
└── Ingestion Status Panel
```

## 10. Authentication Flow

Authentication is not required for the MVP.

A future version may add:

- login
- user organization
- role-based dashboard access
- private datasets
- API token protection

For now, the dashboard should use public demo data only.

## 11. Main Application Flow

1. Developer starts PostgreSQL.
2. Developer runs migrations.
3. Developer runs seed or ingestion script.
4. Pipeline stores raw data.
5. Transformation creates analytics data.
6. Backend reads analytics tables.
7. Frontend calls backend endpoints.
8. User views dashboard and filters data.

## 12. Technical Decisions and Justifications

### Next.js for frontend

Next.js is widely used, portfolio-friendly, and supports professional UI development with TypeScript.

### FastAPI for backend

FastAPI is simple, typed, well suited for data products, and integrates naturally with Python data tools.

### PostgreSQL for database

PostgreSQL is reliable, widely used, and strong for relational analytics at MVP scale.

### Docker Compose for local infrastructure

Docker Compose reduces environment differences and makes PostgreSQL easy to run locally.

### Python scripts for pipeline

Python scripts are enough for the MVP and easier to understand than adding orchestration too early.

## 13. Observability

Minimum observability:

- backend request logs
- ingestion logs
- transformation logs
- healthcheck endpoint
- ingestion run table

Recommended log fields:

- timestamp
- module
- status
- records_processed
- records_rejected
- duration_seconds
- error_message

## 14. Deployment Strategy

Recommended first deployment:

- frontend: Vercel
- backend: Render, Railway, Fly.io, or VPS
- database: managed PostgreSQL or Docker on VPS

Deployment must use environment variables and avoid hardcoded secrets.

## 15. Future Architecture Improvements

- scheduled ingestion jobs
- dbt transformation layer
- object storage for raw files
- authentication
- background workers
- data quality monitoring
- AI-generated business summaries
- multi-tenant architecture
