# Deployment Strategy — DataPulse BI

## 1. Purpose

This document describes how DataPulse BI can be published in a remote environment.

The first deployment should be simple, low-cost, and portfolio-friendly.

## 2. Deployment Goals

The deployment must provide:

- public frontend URL
- public or protected backend URL
- hosted PostgreSQL database
- environment variables
- healthcheck validation
- documented setup
- stable demo for recruiters and clients

## 3. Recommended MVP Deployment Architecture

```text
User Browser
    |
    v
Vercel Frontend
    |
    v
Backend API on Render / Railway / Fly.io / VPS
    |
    v
Managed PostgreSQL
```

## 4. Frontend Deployment

Recommended service:

- Vercel

Why:

- simple Next.js deployment
- free or low-cost tier
- automatic deployments from GitHub
- environment variable support

Required environment variable:

```env
NEXT_PUBLIC_API_URL=https://your-backend-url
```

Deployment steps:

1. Push repository to GitHub.
2. Import frontend project into Vercel.
3. Set root directory to `frontend`.
4. Add environment variables.
5. Deploy.
6. Validate dashboard loads.

## 5. Backend Deployment

Recommended services:

- Render
- Railway
- Fly.io
- VPS with Docker

Required environment variables:

```env
DATABASE_URL=postgresql+psycopg://user:password@host:5432/database
ENVIRONMENT=production
```

Recommended production command:

```bash
uvicorn app.main:app --host 0.0.0.0 --port $PORT
```

If the platform requires a start command, use its expected port variable.

## 6. Database Deployment

Recommended options:

- managed PostgreSQL from Render
- managed PostgreSQL from Railway
- Supabase PostgreSQL
- Neon PostgreSQL
- VPS-hosted PostgreSQL for advanced control

The database must not use local development credentials in production.

## 7. Publication Order

Recommended order:

1. Create hosted PostgreSQL database.
2. Copy production database URL.
3. Deploy backend with `DATABASE_URL`.
4. Run migrations.
5. Load seed or demo data.
6. Validate backend healthcheck.
7. Deploy frontend with backend URL.
8. Validate dashboard.
9. Add links to README.

## 8. Migrations in Production

After backend deployment, run:

```bash
alembic upgrade head
```

The exact method depends on the hosting platform.

Options:

- platform shell
- release command
- local command pointing to production database
- CI/CD workflow

## 9. Demo Data Loading

For a portfolio demo, the project should load safe demo data.

Command example:

```bash
python scripts/ingest_data.py
python scripts/transform_data.py
```

Do not use real private customer data in the public demo.

## 10. Healthcheck

Backend must expose:

```http
GET /health
```

Expected response:

```json
{
  "status": "ok"
}
```

Future version may include:

```json
{
  "status": "ok",
  "database": "ok"
}
```

## 11. Logs

Minimum logs to check after deployment:

- backend startup logs
- database connection logs
- API request errors
- ingestion script logs
- transformation script logs

## 12. Post-Deployment Validation

After deployment, validate:

- frontend opens
- backend healthcheck works
- API accepts filter parameters
- dashboard loads KPI cards
- charts load data
- orders table loads data
- ingestion status appears
- no secrets are visible
- README contains live links

## 13. Environment Variables Checklist

### Backend

```env
DATABASE_URL=
ENVIRONMENT=production
ALLOWED_ORIGINS=
```

### Frontend

```env
NEXT_PUBLIC_API_URL=
```

## 14. CORS

The backend must allow requests from the deployed frontend URL.

Example allowed origin:

```text
https://your-project.vercel.app
```

Do not allow all origins in production unless it is intentional for a public demo.

## 15. Deployment Risks

### Risk 1 — Database URL mismatch

The backend may work locally but fail remotely if `DATABASE_URL` is wrong.

### Risk 2 — CORS errors

The frontend may fail to call backend if CORS is not configured.

### Risk 3 — Missing migrations

The backend may deploy successfully while tables do not exist.

### Risk 4 — No demo data

The dashboard may look empty if seed data is not loaded.

### Risk 5 — Free-tier sleep

Some hosting platforms sleep after inactivity. Mention this in README if relevant.

## 16. Future Deployment Improvements

Future versions may add:

- Dockerized backend deployment
- GitHub Actions CI/CD
- scheduled ingestion jobs
- monitoring
- error tracking
- uptime checks
- preview deployments
- production database backups

## 17. Deployment Acceptance Criteria

The deployment is acceptable when:

- frontend public URL works
- backend healthcheck works
- database is connected
- migrations are applied
- demo data exists
- dashboard displays real metrics
- README contains the live links
- known limitations are documented
