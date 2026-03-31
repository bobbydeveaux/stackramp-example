# launchpad-example

A working example app deployed entirely by [Launchpad](https://github.com/bobbydeveaux/launchpad) — an open-source, zero-config deployment platform delivered as a GitHub Action.

## What this repo demonstrates

- A **React + Vite** frontend deployed to Firebase Hosting
- A **Python FastAPI** backend deployed to Cloud Run
- The frontend fetches live data from the backend at runtime
- The entire deployment is driven by **8 lines of `launchpad.yaml`** and **9 lines of `deploy.yml`**
- No GCP console. No Terraform. No secrets.

## Live site

https://launchpad-example-prod.web.app

## The config

**`launchpad.yaml`** (the only app-level config needed):

```yaml
name: launchpad-example

frontend:
  framework: react
  dir: frontend
  node_version: "20"

backend:
  language: python
  dir: backend
  port: 8080

database: false
```

**`.github/workflows/deploy.yml`** (the only CI/CD config needed):

```yaml
name: Deploy

on:
  push:
    branches: [main]
  pull_request:

jobs:
  deploy:
    permissions:
      id-token: write
      contents: read
      pull-requests: write
    uses: bobbydeveaux/launchpad/.github/workflows/platform.yml@main
    secrets: inherit
```

## What Launchpad does on push

1. Parses `launchpad.yaml`
2. Detects what changed (frontend, backend, or both)
3. Provisions GCP infrastructure idempotently via Terraform (Firebase Hosting site + Cloud Run service)
4. Builds and deploys only what changed
5. On PRs: creates a preview deployment and posts the URL as a comment

## Repo structure

```
launchpad-example/
├── launchpad.yaml              ← platform config
├── .github/
│   └── workflows/
│       └── deploy.yml          ← one-line CI/CD
├── frontend/                   ← React + Vite
│   ├── src/
│   │   ├── App.jsx
│   │   └── App.css
│   ├── index.html
│   └── package.json
└── backend/                    ← Python FastAPI
    ├── main.py
    └── requirements.txt
```

## Running locally

**Frontend:**
```bash
cd frontend
npm install
npm run dev
```

**Backend:**
```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8080
```

## Platform

Launchpad is at [github.com/bobbydeveaux/launchpad](https://github.com/bobbydeveaux/launchpad).
