# CSV Quality Check

A small full-stack app that profiles a CSV file: row and column counts, missing values per column, duplicate rows, inferred column types, and an overall quality score.

![CI](https://github.com/YOUR-USERNAME/dq-dashboard/actions/workflows/ci.yml/badge.svg)

## Stack

| Layer | Tools |
| --- | --- |
| Frontend | React 19, TypeScript, HTML5, CSS3, Vite |
| Backend | Python 3.12, FastAPI, REST API |
| Tooling | Node.js and npm (build, dev server, test runners) |
| Testing | pytest (backend unit and API tests), Vitest + React Testing Library (frontend unit tests), Playwright (end-to-end) |
| CI/CD | GitHub Actions: runs backend tests, type check, frontend tests, build, and e2e on every push and pull request |
| Containers | Docker, Docker Compose |
| Cloud | AWS deployment steps below |

## Run locally

Requires Python 3.12+ and Node.js 20+.

```bash
# terminal 1: API on http://localhost:8000
cd backend
pip install -r requirements-dev.txt
python -m uvicorn app.main:app --reload --port 8000

# terminal 2: web app on http://localhost:5173
cd frontend
npm install
npm run dev
```

Try it with `frontend/e2e/sample.csv`.

## Run the tests

```bash
cd backend && pytest                      # unit + API tests
cd frontend && npm run typecheck          # TypeScript
cd frontend && npm test                   # component tests
cd frontend && npx playwright install chromium && npm run test:e2e   # end-to-end
```

The Playwright config starts the FastAPI server and the Vite dev server for you.

## Run with Docker

```bash
docker compose up --build
# open http://localhost:8080
```

## API

| Method | Path | Description |
| --- | --- | --- |
| GET | `/api/health` | Health check |
| POST | `/api/validate` | Multipart upload (`file`, a `.csv` up to 1 MB). Returns the quality report as JSON. |

Interactive docs are served at `http://localhost:8000/docs`.

## Deploy on AWS (one option)

1. **Backend:** build the image in `backend/`, push it to Amazon ECR, and run it on AWS App Runner (port 8000).
2. **Frontend:** in `frontend/`, point the `/api` calls at the backend URL (for example with a `VITE_API_URL` variable or a CloudFront behavior that forwards `/api/*`), run `npm run build`, upload `dist/` to an S3 bucket, and serve it through CloudFront.
3. Update the CORS origin in `backend/app/main.py` to your CloudFront domain.
4. Add an AWS deploy job to `.github/workflows/` that runs after tests pass.

## Project structure

```
backend/    FastAPI app, data quality checks, pytest tests, Dockerfile
frontend/   React + TypeScript app, Vitest tests, Playwright e2e tests, Dockerfile
.github/    GitHub Actions workflow
```
