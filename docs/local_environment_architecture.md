# Local Development Environment Architecture — DataPulse BI

## 1. Purpose

This document explains how the local development environment should be structured and operated.

It is not only a command list. It describes the role of each tool and how the developer should work with the project.

## 2. Environment Strategy

The project should be developed in a local environment with three main parts:

```text
Local machine
├── Frontend running with Node.js
├── Backend running with Python
└── PostgreSQL running with Docker
```

This setup is simple, flexible, and realistic for portfolio development.

## 3. Recommended Operating System

The recommended environment is Linux or WSL2 on Windows.

For a Windows machine, use:

- Windows as the host system
- WSL2 Ubuntu for development commands
- Docker Desktop integrated with WSL2
- VS Code connected to WSL

## 4. Applications Used

## 4.1 VS Code

Used for editing code, managing files, running terminal commands, and committing changes.

Recommended extensions:

- Python
- Pylance
- ESLint
- Prettier
- Docker
- GitLens
- PostgreSQL or SQLTools
- Markdown Preview

## 4.2 Git

Used for version control.

Recommended workflow:

- small commits
- clear commit messages
- feature branches optional
- main branch always stable

## 4.3 Docker

Used to run PostgreSQL locally without installing it directly on the operating system.

## 4.4 Node.js

Used to run the frontend development server.

Recommended version:

- Node.js LTS or newer stable version

## 4.5 Python

Used for backend and data pipeline.

Recommended version:

- Python 3.11 or 3.12

## 4.6 PostgreSQL

Used as the main relational database.

Runs inside Docker for local development.

## 5. Where to Store the Project

Recommended location in WSL:

```bash
~/projects/datapulse-bi
```

Avoid developing directly inside Windows paths such as:

```text
/mnt/c/Users/...
```

Using the Linux filesystem improves performance and reduces path issues.

## 6. Where to Run Each Part

## 6.1 Database

Run from the project root:

```bash
docker compose up -d
```

## 6.2 Backend

Run from:

```bash
~/projects/datapulse-bi/backend
```

Command:

```bash
source .venv/bin/activate
alembic upgrade head
python scripts/check_db_connection.py
uvicorn app.main:app --reload
```

Useful manual checks once the API is running:

```bash
curl http://localhost:8000/health
curl http://localhost:8000/metrics/summary
```

## 6.3 Frontend

Run from:

```bash
~/projects/datapulse-bi/frontend
```

Command:

```bash
npm install
npm run dev
```

Current status:

- milestone 6 frontend is implemented and milestone 9 adds the manual order testing page
- the dashboard uses TanStack Query for API state
- charts are rendered with Recharts
- the frontend reads `NEXT_PUBLIC_API_URL`
- the backend must allow `CORS_ORIGINS=http://localhost:3000,http://127.0.0.1:3000`

## 6.4 Ingestion and Transformation

Run from:

```bash
~/projects/datapulse-bi/backend
```

Commands:

```bash
python scripts/ingest_data.py
python scripts/transform_data.py
```

Current status:

- `python scripts/ingest_data.py` is implemented in milestone 3
- `python scripts/transform_data.py` is implemented in milestone 4

## 7. Environment Variables

The project should use `.env` files.

Root or backend `.env` example:

```env
DATABASE_URL=postgresql+psycopg://datapulse:datapulse@localhost:5432/datapulse
POSTGRES_USER=datapulse
POSTGRES_PASSWORD=datapulse
POSTGRES_DB=datapulse
ENVIRONMENT=local
CORS_ORIGINS=http://localhost:3000,http://127.0.0.1:3000
```

Frontend `.env.local` example:

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
```

The repository should include `.env.example`, not real `.env` files.

## 8. Folder Organization

Recommended structure:

```text
datapulse-bi/
├── README.md
├── .env.example
├── .gitignore
├── docker-compose.yml
├── frontend/
├── backend/
│   ├── alembic/
│   ├── app/
│   ├── scripts/
│   └── tests/
├── data/
└── docs/
```

## 9. Operational Development Flow

Daily development flow:

1. Open terminal in WSL.
2. Go to the project folder.
3. Start Docker services.
4. Activate Python virtual environment.
5. Apply migrations when the schema changes.
6. Run the database connectivity check when needed.
7. Start backend.
8. Run ingestion when sample raw data needs to be loaded or duplicate detection needs to be checked.
9. Run transformation when the analytical tables need to be rebuilt from newly ingested raw data.
10. Test changes.
11. Commit progress.

Example:

```bash
cd ~/projects/datapulse-bi
docker compose up -d

cd backend
source .venv/bin/activate
alembic upgrade head
python scripts/check_db_connection.py
uvicorn app.main:app --reload
```

Open a second terminal for the frontend once the backend is running:

```bash
cd ~/projects/datapulse-bi/frontend
npm install
npm run dev
```

Run `python scripts/ingest_data.py` separately when the local database is empty or when you intentionally want to verify duplicate detection behavior.
Run `python scripts/transform_data.py` after ingestion when you want to populate `stg_orders`, dimensions, and `fact_orders`.
Run `python scripts/run_smoke_checks.py` when you want one command to confirm that health, ingestion, transformation, and key API responses still work together.
Run `docker compose -p datapulse-bi-prod -f docker-compose.production.yml up -d --build` when you want a production-like stack with the backend and frontend running from their Docker images.

## 10. Recommended Ports

| Service | Port |
|---|---:|
| Frontend | 3000 |
| Backend | 8000 |
| PostgreSQL | 5432 |
| PostgreSQL production-like stack | 5433 |
| pgAdmin optional | 5050 |

## 11. Good Practices

- Keep backend and frontend in separate folders
- Do not commit `.env`
- Use `.env.example`
- Keep Docker Compose simple
- Run tests before important commits
- Update documentation when architecture changes
- Use clear commit messages
- Keep sample data small enough for Git
- Avoid real private data in the repository

## 12. Common Mistakes

### Mistake 1 — Running commands in the wrong folder

Always check:

```bash
pwd
```

### Mistake 2 — Forgetting to activate Python virtual environment

Check:

```bash
which python
```

### Mistake 3 — Database not running

Check:

```bash
docker ps
```

### Mistake 4 — Frontend calling wrong backend URL

Check `NEXT_PUBLIC_API_URL`.

### Mistake 5 — Committing secrets

Check files before commit:

```bash
git status
```

## 13. Development Philosophy

The local environment should stay simple.

Do not add production-level complexity before the MVP works. The first goal is a clean loop:

```text
edit → run → test → commit → improve
```

## 14. Minimum Local Environment Acceptance

The local environment is ready when:

- PostgreSQL runs with Docker
- backend connects to PostgreSQL
- frontend opens in the browser
- ingestion script can insert sample data
- transformation script can generate analytics data
- API returns metrics
- dashboard displays metrics
- the top navigation links `/` and `/orders/new`
- dashboard can reach the backend from the browser without CORS errors
- the production-like Docker stack can serve the dashboard and backend healthcheck
