# Local Setup and Execution Guide — DataPulse BI

## 1. Purpose

This document provides sequential setup instructions for running DataPulse BI locally.

The commands assume Linux or WSL2 Ubuntu.

## 2. Prerequisites

Install:

- Git
- Docker
- Docker Compose
- Python 3.11 or 3.12
- Node.js
- npm
- VS Code

Check versions:

```bash
git --version
docker --version
docker compose version
python3 --version
node -v
npm -v
```

## 3. Create Project Folder

Run in the Ubuntu terminal:

```bash
mkdir -p ~/projects
cd ~/projects
mkdir datapulse-bi
cd datapulse-bi
```

Expected validation:

```bash
pwd
```

Expected result:

```text
/home/<user>/projects/datapulse-bi
```

## 4. Initialize Git

```bash
git init
```

Create initial folders:

```bash
mkdir frontend backend docs data
touch README.md .gitignore .env.example docker-compose.yml
```

## 5. Suggested .gitignore

Add this to `.gitignore`:

```gitignore
# Environment
.env
.env.local
.env.*.local

# Python
__pycache__/
*.py[cod]
.venv/
venv/
.pytest_cache/
.coverage
htmlcov/

# Node
node_modules/
.next/
dist/
build/
npm-debug.log*

# Data
data/raw/*
data/processed/*
!data/raw/.gitkeep
!data/processed/.gitkeep

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
```

Create tracked data folders:

```bash
mkdir -p data/raw data/processed
touch data/raw/.gitkeep data/processed/.gitkeep
```

## 6. Create Docker Compose

Add this to `docker-compose.yml`:

```yaml
services:
  postgres:
    image: postgres:16
    container_name: datapulse_postgres
    restart: unless-stopped
    environment:
      POSTGRES_USER: datapulse
      POSTGRES_PASSWORD: datapulse
      POSTGRES_DB: datapulse
    ports:
      - "5432:5432"
    volumes:
      - datapulse_postgres_data:/var/lib/postgresql/data

volumes:
  datapulse_postgres_data:
```

Start database:

```bash
docker compose up -d
```

Validate:

```bash
docker ps
```

You should see the PostgreSQL container running.

## 7. Create Environment File

Copy:

```bash
cp .env.example .env
```

Add this to `.env.example`:

```env
POSTGRES_USER=datapulse
POSTGRES_PASSWORD=datapulse
POSTGRES_DB=datapulse
DATABASE_URL=postgresql+psycopg://datapulse:datapulse@localhost:5432/datapulse
ENVIRONMENT=local
CORS_ORIGINS=http://localhost:3000,http://127.0.0.1:3000
NEXT_PUBLIC_API_URL=http://localhost:8000
```

Add the backend values to `.env` for local development.

If you want to override the frontend API base URL explicitly, create `frontend/.env.local` with:

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
```

## 8. Create Backend Environment

Run:

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate
```

Upgrade pip:

```bash
python -m pip install --upgrade pip
```

Create `requirements.txt`:

```bash
cat > requirements.txt <<'EOF'
fastapi
uvicorn[standard]
sqlalchemy
psycopg[binary]
alembic
pydantic
pydantic-settings
python-dotenv
pandas
pytest
httpx
EOF
```

Install dependencies:

```bash
pip install -r requirements.txt
```

## 9. Use the Existing Backend Structure

The repository already includes the milestone 2 backend foundation:

- `backend/app/core/config.py`
- `backend/app/db/session.py`
- `backend/app/models/`
- `backend/alembic.ini`
- `backend/alembic/env.py`
- `backend/alembic/versions/20260429_0001_create_core_tables.py`
- `backend/scripts/check_db_connection.py`

## 10. Run Migrations and Verify the Database

From `backend/`:

```bash
alembic upgrade head
python scripts/check_db_connection.py
```

Expected validation:

- Alembic applies revision `20260429_0001`
- the connection check prints the database name, current user, and `healthcheck= 1`
- the local PostgreSQL instance contains:
  `ingestion_runs`, `raw_orders`, `stg_orders`, `dim_products`, `dim_customers`, `dim_regions`, `dim_channels`, `fact_orders`, and `data_quality_issues`

Optional table inspection:

```bash
cd ~/projects/datapulse-bi
docker exec datapulse_postgres psql -U datapulse -d datapulse -c "\dt"
```

## 11. Run the Backend App

From `backend/`:

```bash
source .venv/bin/activate
uvicorn app.main:app --reload
```

Expected validation:

- Uvicorn starts without import or database configuration errors
- FastAPI serves the application shell on `http://localhost:8000`
- `GET /health` returns `{"status":"ok","database":"ok","environment":"local"}`

## 12. Frontend Status

The frontend is implemented for milestone 6 and extended in milestone 9 with a manual order testing flow.

Current frontend stack:

- Next.js App Router
- TypeScript
- Tailwind CSS
- TanStack Query
- Recharts

Run frontend:

```bash
cd ~/projects/datapulse-bi/frontend
npm install
npm run dev
```

Expected validation:

- the dashboard loads on `http://localhost:3000`
- the manual order page loads on `http://localhost:3000/orders/new`
- the frontend uses `http://localhost:8000` by default or reads `NEXT_PUBLIC_API_URL` from `frontend/.env.local`
- the backend allows local browser requests through `CORS_ORIGINS=http://localhost:3000,http://127.0.0.1:3000`
- KPI cards, charts, recent orders, and the latest ingestion status panel render after the backend is available
- the top navigation links the dashboard and the manual order testing page

## 13. Ingestion and Transformation Status

The ingestion script is implemented for milestone 3.

Current dataset:

- `data/sample_orders.csv`
- 12 deterministic synthetic sales orders
- intended for repeatable local ingestion and duplicate detection checks

Run ingestion:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
python scripts/ingest_data.py
```

Generate a larger random dataset when you want to stress-test the dashboard with more records:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
python scripts/generate_random_orders.py --count 1000
python scripts/ingest_data.py --csv-path ../data/generated_orders_1000.csv --source-name random_orders_1000
python scripts/transform_data.py
```

Expected validation for the first run:

- `records_read= 12`
- `records_inserted= 12`
- `records_rejected= 0`
- one `ingestion_runs` row with status `success`

Expected validation for a second run against the same dataset:

- `records_read= 12`
- `records_inserted= 0`
- `records_rejected= 12`
- one additional `ingestion_runs` row with status `partial`
- `data_quality_issues` records created with `duplicate_record`

Transformation is implemented for milestone 4.

Run transformation:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
python scripts/transform_data.py
```

Expected validation for a clean dataset load:

- `records_read= 12`
- `records_inserted= 12`
- `records_rejected= 0`
- one `transform_orders` row in `ingestion_runs` with status `success`
- `stg_orders` contains normalized records
- `fact_orders` contains analytical order facts
- `dim_products`, `dim_customers`, `dim_regions`, and `dim_channels` are populated

## 14. Run Validation Commands

Backend tests:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
python -m compileall app scripts
pytest
```

Current expectation:

- `compileall` succeeds
- `pytest` passes the current milestone 2 through milestone 7 checks
- backend test suite currently reports `16 passed`

Backend smoke command:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
python scripts/run_smoke_checks.py
```

Current expectation:

- the script prints `health_status= ok`
- the script runs a fresh ingestion and transformation from `data/sample_orders.csv`
- the script validates `/ingestion/runs/latest`, `/metrics/summary`, and `/orders`
- the script ends with `smoke_status= ok`

Optional production-like validation:

```bash
cd ~/projects/datapulse-bi
docker compose -p datapulse-bi-prod -f docker-compose.production.yml up -d --build
docker exec datapulse_backend_prod alembic upgrade head
docker exec datapulse_backend_prod python scripts/seed_demo_data.py
curl http://localhost:8000/health
curl http://localhost:8000/metrics/summary
docker compose -p datapulse-bi-prod -f docker-compose.production.yml down
```

Current expectation:

- the stack builds both `backend/Dockerfile` and `frontend/Dockerfile`
- PostgreSQL runs on host port `5433` inside the production-like stack
- backend runs on `http://localhost:8000`
- frontend runs on `http://localhost:3000`
- healthcheck reports `environment":"production"`
- summary metrics return the seeded sample values

Frontend validation:

```bash
cd ~/projects/datapulse-bi/frontend
npm run lint
npm run build
```

Current expectation:

- `npm run lint` succeeds
- `npm run build` succeeds

## 15. Commit Project

From project root:

```bash
cd ~/projects/datapulse-bi
git status
git add .
git commit -m "Initial documentation and project structure"
```

## 16. Final Local Checklist

- [ ] Project folder created
- [ ] Git initialized
- [ ] Docker Compose configured
- [ ] PostgreSQL running
- [ ] Backend virtual environment created
- [ ] Backend dependencies installed
- [ ] Alembic migration applied
- [ ] Database connectivity validated
- [ ] Sample dataset ingested successfully
- [ ] Ingestion run recorded in PostgreSQL
- [ ] Transformation run recorded in PostgreSQL
- [ ] Staging and fact tables populated
- [ ] Backend app starts without import errors
- [ ] Environment files configured
- [ ] Documentation added
- [ ] Initial commit created

## 17. Daily Startup Commands

Terminal 1:

```bash
cd ~/projects/datapulse-bi
docker compose up -d
```

Terminal 2:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
python scripts/check_db_connection.py
uvicorn app.main:app --reload
```

Run `alembic upgrade head` after pulling schema changes or resetting the local database.

Run `python scripts/ingest_data.py` when you want to load the sample dataset into an empty or reset local database.
Run `python scripts/transform_data.py` after ingestion when you want to populate the analytical tables.

Useful API checks after the backend starts:

```bash
curl http://localhost:8000/health
curl http://localhost:8000/ingestion/runs/latest
curl http://localhost:8000/metrics/summary
curl "http://localhost:8000/orders?limit=5&offset=0"
```

## 18. Daily Shutdown

```bash
cd ~/projects/datapulse-bi
docker compose down
```

If you want to preserve database data, do not remove volumes.

To reset database completely:

```bash
docker compose down -v
```
