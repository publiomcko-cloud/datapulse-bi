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
- Recharts or ECharts
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
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

Run frontend:

```bash
cd frontend
npm install
npm run dev
```

Run ingestion:

```bash
cd backend
python scripts/ingest_data.py
python scripts/transform_data.py
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
- Add KPI cards
- Add charts
- Add tables and filters

### Phase 5: Deployment and portfolio polish

- Deploy frontend
- Deploy backend
- Add demo video
- Add screenshots
- Improve README and case study

## 10. Project Status

Initial documentation package ready for development.

## 11. Portfolio Value

This project is designed to prove skills that are valuable for both employment and freelance work:

- SQL modeling
- data pipelines
- API design
- dashboard development
- Docker-based local setup
- deployment
- technical documentation
- ability to transform data into business value
