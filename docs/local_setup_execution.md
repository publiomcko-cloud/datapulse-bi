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
```

Add the same values to `.env` for local development.

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

## 9. Create Backend Structure

From `backend/`:

```bash
mkdir -p app/api app/core app/db app/models app/schemas app/services scripts tests
touch app/__init__.py
touch app/main.py
touch app/api/__init__.py
touch app/core/__init__.py
touch app/db/__init__.py
touch app/models/__init__.py
touch app/schemas/__init__.py
touch app/services/__init__.py
```

Create a temporary FastAPI app:

```bash
cat > app/main.py <<'EOF'
from fastapi import FastAPI

app = FastAPI(title="DataPulse BI API")

@app.get("/health")
def healthcheck():
    return {"status": "ok"}
EOF
```

Run backend:

```bash
uvicorn app.main:app --reload
```

Expected validation:

Open:

```text
http://localhost:8000/health
```

Expected response:

```json
{"status":"ok"}
```

## 10. Configure Alembic

From `backend/`:

```bash
alembic init alembic
```

Later, configure `alembic.ini` and `env.py` to read `DATABASE_URL`.

Initial migration commands after models exist:

```bash
alembic revision --autogenerate -m "create initial tables"
alembic upgrade head
```

## 11. Create Frontend

From project root:

```bash
cd ~/projects/datapulse-bi
npx create-next-app@latest frontend
```

Recommended answers:

```text
TypeScript: Yes
ESLint: Yes
Tailwind CSS: Yes
src directory: Yes
App Router: Yes
Turbopack: Yes
Import alias: Yes
```

Run frontend:

```bash
cd frontend
npm run dev
```

Expected validation:

Open:

```text
http://localhost:3000
```

## 12. Create Frontend Environment File

From `frontend/`:

```bash
cat > .env.local <<'EOF'
NEXT_PUBLIC_API_URL=http://localhost:8000
EOF
```

## 13. Run Data Pipeline

After scripts are implemented, run:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
python scripts/ingest_data.py
python scripts/transform_data.py
```

Expected validation:

- raw table contains records
- staging table contains cleaned records
- fact table contains analytical records
- ingestion run table contains execution logs

## 14. Run Tests

Backend tests:

```bash
cd ~/projects/datapulse-bi/backend
source .venv/bin/activate
pytest
```

Frontend validation:

```bash
cd ~/projects/datapulse-bi/frontend
npm run lint
```

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
- [ ] FastAPI healthcheck working
- [ ] Frontend created
- [ ] Frontend running
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
uvicorn app.main:app --reload
```

Terminal 3:

```bash
cd ~/projects/datapulse-bi/frontend
npm run dev
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
