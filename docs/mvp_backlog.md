# MVP Backlog — DataPulse BI

## 1. Purpose

This backlog turns the project concept into an implementation sequence.

The goal is to guide development in a practical order, reducing rework and keeping the MVP focused.

## 2. MVP Strategy

The MVP should prove the complete data product flow:

```text
source data → ingestion → database → transformation → API → dashboard → documentation
```

The first version does not need advanced orchestration, authentication, or big-data infrastructure. It must be clear, functional, and demonstrable.

## 3. Phase 0 — Repository and Documentation Setup

### Objective

Prepare the repository so development can start with clarity.

### Deliverables

- repository created
- README added
- docs folder added
- .gitignore added
- .env.example added
- initial docker-compose.yml added
- project structure created

### Tasks

- [ ] Create root folder `datapulse-bi`
- [ ] Create `frontend/`, `backend/`, and `docs/`
- [ ] Add README
- [ ] Add all documentation files
- [ ] Add .gitignore
- [ ] Add .env.example
- [ ] Add docker-compose.yml with PostgreSQL
- [ ] Create initial Git commit

### Priority

High

## 4. Phase 1 — Database and Local Infrastructure

### Objective

Create a working local PostgreSQL environment and database schema.

### Deliverables

- PostgreSQL running locally
- database connection working
- migrations configured
- core tables created

### Tasks

- [ ] Configure Docker Compose for PostgreSQL
- [ ] Define database environment variables
- [ ] Install backend dependencies
- [ ] Configure SQLAlchemy or SQLModel
- [ ] Configure Alembic
- [ ] Create migration for `ingestion_runs`
- [ ] Create migration for `raw_orders`
- [ ] Create migration for `stg_orders`
- [ ] Create migration for dimensions
- [ ] Create migration for `fact_orders`
- [ ] Create indexes
- [ ] Validate connection through a simple script

### Priority

High

## 5. Phase 2 — Seed Dataset and Ingestion

### Objective

Create a reproducible dataset and load it into the raw layer.

### Deliverables

- sample dataset
- ingestion script
- ingestion logs
- raw table populated

### Tasks

- [ ] Choose MVP dataset
- [ ] Create `data/sample_orders.csv`
- [ ] Create synthetic dataset generator if needed
- [ ] Build ingestion script
- [ ] Add duplicate detection using row hash
- [ ] Insert ingestion run record
- [ ] Count records read, inserted, and rejected
- [ ] Store raw values as text
- [ ] Add basic logging
- [ ] Add test for ingestion function

### Priority

High

## 6. Phase 3 — Validation and Transformation

### Objective

Convert raw records into clean analytical structures.

### Deliverables

- staging table populated
- dimension tables populated
- fact table populated
- invalid records handled

### Tasks

- [ ] Implement date parser
- [ ] Implement number parser
- [ ] Normalize categories
- [ ] Normalize regions
- [ ] Normalize channels
- [ ] Calculate total amount
- [ ] Reject invalid records
- [ ] Store data quality issues
- [ ] Upsert dimension records
- [ ] Insert fact records
- [ ] Add transformation logs
- [ ] Add tests for transformation rules

### Priority

High

## 7. Phase 4 — Backend API

### Objective

Expose data to the frontend through clean endpoints.

### Deliverables

- FastAPI app
- healthcheck
- metrics endpoints
- ingestion status endpoints
- API tests

### Tasks

- [ ] Create FastAPI project structure
- [ ] Add settings module
- [ ] Add database session management
- [ ] Add `/health`
- [ ] Add `/ingestion/runs/latest`
- [ ] Add `/metrics/summary`
- [ ] Add `/metrics/revenue-over-time`
- [ ] Add `/metrics/top-products`
- [ ] Add `/metrics/revenue-by-region`
- [ ] Add `/metrics/revenue-by-channel`
- [ ] Add `/orders`
- [ ] Add query filters
- [ ] Add response schemas
- [ ] Add error handling
- [ ] Add tests for critical endpoints

### Priority

High

## 8. Phase 5 — Frontend Dashboard

### Objective

Create a professional dashboard that communicates business value.

### Deliverables

- dashboard layout
- KPI cards
- charts
- filters
- recent orders table
- ingestion status panel

### Tasks

- [ ] Create Next.js app
- [ ] Configure TypeScript
- [ ] Configure Tailwind CSS
- [ ] Create dashboard layout
- [ ] Create API client
- [ ] Add filter bar
- [ ] Add KPI cards
- [ ] Add revenue trend chart
- [ ] Add top products chart
- [ ] Add revenue by region chart
- [ ] Add revenue by channel chart
- [ ] Add recent orders table
- [ ] Add ingestion status panel
- [ ] Add loading states
- [ ] Add error states
- [ ] Add empty states
- [ ] Add responsive behavior

### Priority

High

## 9. Phase 6 — Testing and Quality

### Objective

Make the project credible and safer to modify.

### Deliverables

- backend tests
- transformation tests
- frontend smoke validation
- documented test commands

### Tasks

- [ ] Add pytest
- [ ] Add test database strategy
- [ ] Test validation rules
- [ ] Test transformation rules
- [ ] Test metric calculations
- [ ] Test healthcheck endpoint
- [ ] Test one filtered metric endpoint
- [ ] Add frontend lint command
- [ ] Add project quality checklist
- [ ] Document how to run tests

### Priority

Medium

## 10. Phase 7 — Deployment

### Objective

Publish the project with public links for portfolio use.

### Deliverables

- deployed frontend
- deployed backend
- deployed database
- public demo link
- deployment documentation

### Tasks

- [ ] Choose deployment services
- [ ] Configure production environment variables
- [ ] Deploy database
- [ ] Run migrations remotely
- [ ] Load seed data remotely
- [ ] Deploy backend
- [ ] Deploy frontend
- [ ] Configure frontend API URL
- [ ] Validate healthcheck
- [ ] Validate dashboard
- [ ] Add demo link to README
- [ ] Add screenshots
- [ ] Record short demo video

### Priority

Medium

## 11. Phase 8 — Portfolio Polish

### Objective

Make the project convincing for recruiters and clients.

### Deliverables

- strong README
- screenshots
- architecture diagram
- demo video
- case study section

### Tasks

- [ ] Add problem statement to README
- [ ] Add business value explanation
- [ ] Add architecture diagram
- [ ] Add screenshots
- [ ] Add demo video link
- [ ] Add known limitations
- [ ] Add roadmap
- [ ] Add LinkedIn post draft
- [ ] Pin repository on GitHub profile

### Priority

Medium

## 12. Recommended Implementation Sequence

1. Create repository and docs
2. Add Docker Compose with PostgreSQL
3. Configure backend project
4. Create database migrations
5. Add seed dataset
6. Build ingestion script
7. Build transformation script
8. Build metric queries
9. Expose backend endpoints
10. Create frontend dashboard
11. Add tests
12. Deploy
13. Polish README and portfolio presentation

## 13. Execution Risks

### Risk 1 — Overengineering

Avoid adding Airflow, Spark, Kubernetes, or complex infrastructure before the MVP works.

### Risk 2 — Weak dataset story

A dashboard with random data is less convincing. Use a dataset that allows clear business questions.

### Risk 3 — No deployment

A portfolio project without a public link is much weaker.

### Risk 4 — Missing tests

Even a small number of tests greatly increases credibility.

### Risk 5 — Poor README

A strong README is part of the product. It must explain the problem, the solution, and how to run the project.

## 14. MVP Done Checklist

- [ ] PostgreSQL runs locally
- [ ] Data can be ingested
- [ ] Data can be transformed
- [ ] Dashboard metrics are calculated from database data
- [ ] Backend API works
- [ ] Frontend dashboard works
- [ ] Tests cover critical logic
- [ ] README is professional
- [ ] Project has screenshots
- [ ] Project has deployment plan or public demo
