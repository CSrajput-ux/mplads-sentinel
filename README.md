# MPLADS Sentinel (e-drishti)

**AI-assisted risk and anomaly monitoring for MPLADS (Members of Parliament Local Area Development Scheme) projects and expenditures.**

MPLADS Sentinel ingests project, expenditure, vendor and MP data, scores every project with a **hybrid risk engine** (machine learning + rule-based red flags), raises alerts for suspicious patterns, and presents everything through a web dashboard backed by a FastAPI service and a PostgreSQL database.

> This repository is a fork of [ajaychoudhary512/mplads-sentinel](https://github.com/ajaychoudhary512/mplads-sentinel).

<!-- TODO: add live demo link here once the deployment is back online, e.g. https://mplads-sentinel-alpha.vercel.app -->

---

## Table of Contents

1. [Features](#features)
2. [Architecture](#architecture)
3. [Tech Stack](#tech-stack)
4. [Repository Structure](#repository-structure)
5. [Risk Scoring Engine](#risk-scoring-engine)
6. [Database Schema](#database-schema)
7. [Getting Started (Local)](#getting-started-local)
8. [Configuration](#configuration)
9. [Running the App](#running-the-app)
10. [API and Health Checks](#api-and-health-checks)
11. [Testing](#testing)
12. [Deployment](#deployment)
13. [Contributing](#contributing)
14. [License](#license)
15. [Acknowledgements](#acknowledgements)

---

## Features

- **Hybrid risk scoring**: every project gets a 0-100 score combining an ML model (60%) with rule-based red flags (40%).
- **Four risk levels**: Low, Medium, High and Critical, with configurable thresholds.
- **Rule-based red flags** for common irregularities:
  - unusual expenditure
  - extreme cost overrun and ordinary cost overrun
  - delayed projects
  - suspicious vendors
  - duplicate payments
  - geographic inconsistency
  - transaction outliers
- **Alerts** generated from flagged projects and transactions.
- **Dataset versioning and AI analysis run tracking** so that results can be traced back to the data and model run that produced them.
- **Audit logging** of key actions.
- **Baseline dataset of 28,706 projects** with risk intelligence records, seeded with a single command.
- **Web dashboard** (Vite frontend) that talks to the FastAPI backend.
- **Multiple deployment paths**: Render, Railway, Vercel/Netlify (frontend), Docker, or a plain VPS.
- **Health and readiness endpoint** that reports database status and latency.

---

## Architecture

```
[ Frontend: Vercel / Netlify / static server ]
                  |
                  v
[ Backend API: FastAPI on Render / Railway / VPS / systemd ]
                  |
                  v   (TLS / SSL connection pool)
[ Database: Neon Serverless PostgreSQL ]
```

The data flow inside the backend is:

```
raw data (Excel / CSV)  ->  data_pipeline  ->  PostgreSQL
                                                  |
                          models (ML)  +  rule engine
                                                  |
                              hybrid risk score + alerts
                                                  |
                                       FastAPI endpoints
                                                  |
                                          Frontend dashboard
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| API | FastAPI, Uvicorn, Gunicorn |
| Data validation | Pydantic 2 |
| Database access | SQLAlchemy 2, psycopg (PostgreSQL) |
| Database host | Neon Serverless PostgreSQL (recommended) |
| Data processing | pandas, NumPy, SciPy, openpyxl (Excel input) |
| Machine learning | scikit-learn, joblib (model persistence) |
| HTTP client | httpx |
| Config | python-dotenv |
| Tests | pytest |
| Frontend | Vite-based web app (built with `npm run build`, output in `dist`) |
| Containers | Dockerfile and docker-compose |
| Hosting configs | `render.yaml`, `Procfile`, `.mise.toml` |

Python version and tool versions can be pinned through `.mise.toml`.

---

## Repository Structure

```
mplads-sentinel/
├── backend/                  # FastAPI application (entry point: backend.main:app)
│   └── db/                   # Database layer, includes migrate_to_neon.py
├── frontend/                 # Vite web dashboard
├── data/                     # Datasets used for seeding and analysis
├── data_pipeline/            # Data ingestion and preparation scripts
├── models/                   # Trained ML models and related code
├── tests/                    # pytest test suite
│
├── risk_config.json          # Risk thresholds, ML/rule weights, rule weights
├── requirements.txt          # Python dependencies
├── package.json              # Workspace wrapper (forwards to frontend/)
├── start.py                  # Production entry point (single worker, low-memory)
├── start-production.sh       # One-click launcher for Linux / macOS
├── start-production.bat      # One-click launcher for Windows
├── Dockerfile                # Container image
├── docker-compose.yml        # Local / container run on port 8000
├── render.yaml               # Render blueprint
├── Procfile                  # Process definition for PaaS hosts
├── DEPLOYMENT.md             # Detailed deployment guide (no Docker + Neon)
├── scratch_test_pandas.py    # Scratch script for pandas experiments
└── LICENSE
```

> The one-line descriptions for `backend/`, `frontend/`, `data/`, `data_pipeline/`, `models/` and `tests/` are based on the folder names and the deployment guide. Update them with more detail as the code evolves.

---

## Risk Scoring Engine

All scoring parameters live in [`risk_config.json`](./risk_config.json), so behaviour can be tuned without changing code.

### Final score

```
risk_score = 0.60 * ml_score + 0.40 * rule_score
```

| Parameter | Value |
|---|---|
| `ml_weight` | 0.60 |
| `rule_weight` | 0.40 |

### Risk levels

| Level | Score range |
|---|---|
| Low | up to 24.99 |
| Medium | 25 to 49.99 |
| High | 50 to 74.99 |
| Critical | 75 and above |

### Rule weights

| Red flag | Weight |
|---|---|
| `flag_unusual_expenditure` | 18 |
| `flag_extreme_cost_overrun` | 18 |
| `flag_duplicate_payment` | 18 |
| `flag_delayed_project` | 14 |
| `flag_suspicious_vendor` | 14 |
| `flag_geographic_inconsistency` | 8 |
| `flag_cost_overrun` | 8 |
| `flag_transaction_outlier` | 8 |

Each triggered flag adds its weight to the rule-based component. Higher weights mean the pattern is treated as a stronger indicator of possible irregularity.

> A high score is a **signal for review, not proof of wrongdoing**. Findings should always be verified by a human against the source records.

---

## Database Schema

The migration script creates eight tables and their indexes:

| Table | Purpose |
|---|---|
| `projects` | MPLADS projects (the baseline dataset has 28,706 records) |
| `expenditures` | Payments and spending linked to projects |
| `vendors` | Contractors and suppliers |
| `mps` | Members of Parliament |
| `alerts` | Alerts raised by the risk engine |
| `dataset_versions` | Versions of the ingested datasets |
| `ai_analysis_runs` | Records of ML and analysis runs |
| `audit_logs` | Audit trail of actions |

---

## Getting Started (Local)

### Prerequisites

- Python 3.10 or newer (check `.mise.toml` for the pinned version)
- Node.js 18 or newer with npm (pnpm also works, a `pnpm-lock.yaml` is included)
- A PostgreSQL database, for example a free [Neon](https://neon.tech) project
- Git

### 1. Clone the repository

```bash
git clone https://github.com/CSrajput-ux/mplads-sentinel.git
cd mplads-sentinel
```

### 2. Backend setup

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
pip install python-calamine      # faster Excel reading, used in the cloud build too
```

### 3. Configure environment variables

Create a `.env` file in the project root:

```env
DATABASE_URL=postgresql://<user>:<password>@<host>/<db>?sslmode=require
ENVIRONMENT=development
CORS_ORIGINS=http://localhost:5173,http://localhost:8443
```

### 4. Initialise and seed the database

```bash
python backend/db/migrate_to_neon.py
```

This will:

- open a secure SSL connection to the database
- create all 8 tables and indexes
- seed the baseline 28,706 projects and risk intelligence records
- verify table counts and query latency

### 5. Frontend setup

```bash
npm --prefix frontend install
```

---

## Configuration

| Variable | Where | Description |
|---|---|---|
| `DATABASE_URL` | backend | PostgreSQL connection string (use `sslmode=require` for Neon) |
| `ENVIRONMENT` | backend | `development` or `production` |
| `CORS_ORIGINS` | backend | Comma-separated list of allowed frontend origins |
| `PORT` | backend | Port for `start.py` (default `8000`) |
| `HOST` | backend | Bind address for `start.py` (default `0.0.0.0`) |
| `VITE_API_BASE_URL` | frontend | Base URL of the deployed backend API |

Never commit real credentials. Keep `.env` in `.gitignore`.

---

## Running the App

### Development

```bash
# Terminal 1: backend
python -m uvicorn backend.main:app --reload --port 8000

# Terminal 2: frontend (from the repo root)
npm run dev
```

### Production (no Docker)

```bash
# Multi-worker ASGI server
python -m uvicorn backend.main:app --host 0.0.0.0 --port 8000 --workers 4

# Build and serve the frontend
npm run build
npx serve -s dist -l 8443
```

Or use the one-click launchers:

```bash
./start-production.sh        # Linux / macOS
start-production.bat         # Windows
```

### Low-memory hosting (512 MB RAM)

`start.py` starts the backend with a **single worker** and memory-friendly settings, which is what small instances such as Render's free tier need:

```bash
python start.py
```

### Docker

```bash
export DATABASE_URL="postgresql://..."
docker compose up --build
```

The app is then available on `http://localhost:8000`.

---

## API and Health Checks

FastAPI generates interactive documentation automatically. With the backend running, open:

- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

Readiness check (backend and database):

```bash
curl http://localhost:8000/api/health/ready
```

Expected response:

```json
{
  "status": "READY",
  "database": {
    "status": "HEALTHY",
    "dialect": "postgresql",
    "latency_ms": 12.4
  },
  "version": "1.2.0"
}
```

---

## Testing

```bash
pytest
```

Tests live in the `tests/` directory.

---

## Deployment

The full walkthrough is in [DEPLOYMENT.md](./DEPLOYMENT.md). In short:

### Database (Neon)

1. Create a project at [neon.tech](https://neon.tech).
2. Copy the connection string (pooled or standard) from **Connection Details**.
3. Set it as `DATABASE_URL`, then run `python backend/db/migrate_to_neon.py`.

### Backend (Render / Railway / AWS App Runner)

| Setting | Value |
|---|---|
| Root directory | `.` |
| Build command | `pip install -r requirements.txt && pip install python-calamine` |
| Start command | `uvicorn backend.main:app --host 0.0.0.0 --port $PORT --workers 4` |
| `DATABASE_URL` | your Neon connection string |
| `ENVIRONMENT` | `production` |
| `CORS_ORIGINS` | `https://your-frontend-domain.vercel.app,http://localhost:8443` |

On instances with 512 MB RAM, use `python start.py` as the start command instead, so that only one worker runs.

### Frontend (Vercel / Netlify)

| Setting | Value |
|---|---|
| Build command | `npm run build` |
| Output directory | `dist` |
| Install command | `npm install` |
| `VITE_API_BASE_URL` | URL of your deployed backend |

---

## Contributing

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/my-change`.
3. Make your changes and add tests where it makes sense.
4. Run `pytest` and make sure the app still builds with `npm run build`.
5. Commit and open a pull request describing what changed and why.

---

## License

Distributed under the terms of the [LICENSE](./LICENSE) file included in this repository.

---

## Acknowledgements

- Original project by [ajaychoudhary512](https://github.com/ajaychoudhary512/mplads-sentinel).
- Fork maintained by [CSrajput-ux](https://github.com/CSrajput-ux).
- Built with FastAPI, scikit-learn, SQLAlchemy, Vite and Neon PostgreSQL.
