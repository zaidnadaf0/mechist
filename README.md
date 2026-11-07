# MECHIST ⚙️

A full-stack platform that connects vehicle owners with certified mechanics — booking, diagnostics, matching and admin oversight, with an optional machine-learning ranking service.

[![Node](https://img.shields.io/badge/node-%E2%89%A520-339933?logo=nodedotjs&logoColor=white)](https://nodejs.org)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Firebase](https://img.shields.io/badge/Firebase-Auth%20%C2%B7%20Firestore%20%C2%B7%20Functions-FFCA28?logo=firebase&logoColor=black)](https://firebase.google.com)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-pipeline-F7931E?logo=scikitlearn&logoColor=white)](ml/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

## Overview

A vehicle owner submits a request with symptoms and a location; the platform ranks candidate mechanics, routes the request, and tracks it through its lifecycle. Mechanics progress the job, and admins review mechanics, monitor dispatch and read analytics.

## Architecture

| Layer | Technology |
|---|---|
| UI | React 18, TypeScript, Vite 5, Tailwind CSS 3, framer-motion, GSAP, three.js |
| Maps | Leaflet + react-leaflet (OpenStreetMap), optional Geoapify provider |
| Auth | Firebase Authentication with role-based access (owner / mechanic / admin) |
| Data | Cloud Firestore with security rules and indexes |
| Server logic | Firebase Cloud Functions (TypeScript) |
| Files | Firebase Storage |
| ML (optional at runtime) | Python scikit-learn ranker + FastAPI service in `ml/` |
| Testing | Vitest, rules gate, phase verification scripts, Firebase Emulator Suite |

Matching logic lives in `functions/src/matching.ts`. When the ML API is not configured or is unreachable, the frontend falls back to the deterministic baseline ranker in `src/lib/mlRank.ts` — the app never shows fabricated scores.

## Features

- **Vehicle owner**: request intake with symptoms and location, mechanic list and detail views, request history, notifications, profile
- **Mechanic**: dashboard, request handling, status progression
- **Admin**: dashboard, dispatch view, mechanic review, users, requests queue, reports, analytics, logs
- **Diagnostics**: ML ranking pipeline with a documented baseline, metrics and packaging metadata
- **Security**: Firestore and Storage rules, exercised through the emulator suite

## Getting started

### Prerequisites

- Node.js ≥ 20 and npm
- Firebase CLI (`npm i -g firebase-tools`)
- Python 3.11+ only if you want to run the ML API locally

### Install

```bash
git clone https://github.com/zaidnadaf0/mechist.git
cd mechist
npm install
npm --prefix functions install
```

### Environment

```bash
cp .env.example .env.local
```

```
VITE_FIREBASE_API_KEY=
VITE_FIREBASE_AUTH_DOMAIN=
VITE_FIREBASE_PROJECT_ID=
VITE_FIREBASE_STORAGE_BUCKET=
VITE_FIREBASE_MESSAGING_SENDER_ID=
VITE_FIREBASE_APP_ID=
VITE_FIREBASE_MEASUREMENT_ID=

VITE_MAPS_PROVIDER=leaflet          # or "geoapify"
VITE_GEOAPIFY_API_KEY=              # only needed for geoapify; falls back to Leaflet/OSM
# VITE_ML_API_URL=http://localhost:8000
```

`.env.local` is git-ignored; `.env.example` documents every variable the code reads.

### Run

```bash
npm run dev                 # Vite dev server
npm run emulators           # Firestore, Auth, Functions, Pub/Sub, Storage + UI
python -m uvicorn ml.api.main:app --reload --port 8000   # optional ML service
```

The first admin account is created with the steps in [`docs/ADMIN_BOOTSTRAP.md`](docs/ADMIN_BOOTSTRAP.md).

## Project structure

```
mechist/
├── src/
│   ├── app/            # providers and app bootstrap
│   ├── components/     # ui, shell, map, motion, hero, illustrations, effects
│   ├── lib/            # Firebase access, matching fallback, geo, notifications
│   └── pages/          # admin · auth · customer · landing · mechanic
├── functions/src/      # admin, matching, projection, shared types
├── ml/
│   ├── api/            # FastAPI service (POST /rank-mechanics)
│   ├── data/ features/ # dataset generation and feature engineering
│   ├── model/          # packaged model, feature schema, metrics
│   ├── scripts/ tests/ # training, evaluation, benchmarks, tests
│   └── reports/
├── docs/               # architecture, ML pipeline, limitations, admin bootstrap
├── firestore.rules
└── firebase.json
```

## The ML ranker

[`docs/ml-pipeline.md`](docs/ml-pipeline.md) documents the pipeline end to end: a synthetic dataset (seed 42), 34 engineered features grouped by request, a leakage audit, a weighted baseline, and a comparison of four models. The packaged winner is a `LogisticRegression` ranker (`ml/model/model.joblib`) reporting test Hit@1 0.907 / NDCG@3 0.959 against a 0.84 / 0.924 baseline.

Those numbers come from a **synthetic** dataset and are reported as such — see the limitations below.

## Testing and verification

| Gate | Command |
|---|---|
| Unit tests (Vitest) | `npm test` |
| Rules static gate | `npm run lint:rules` |
| Phase verification | `npm run verify:phaseB` |
| All frontend gates | `npm run verify:all` |
| Typecheck | `npm run typecheck` |
| Functions build | `npm --prefix functions run build` |
| Emulator-backed work | `npm run emulators` |

## Limitations

Stated deliberately rather than hidden (full list in [`docs/limitations.md`](docs/limitations.md)):

- The ML model is trained on a **synthetic** dataset and is not real-world validated; every metric is a proxy metric
- ETA uses a haversine + average-speed proxy, not road routing
- No temporal, traffic, pricing or fatigue modelling
- The symptom classifier is trained on templated text — a demonstrator, not a production NLP model
- Matching relies on Firestore listeners with a client-side declined-mechanic filter
- The ML API runs locally unless deployed; the frontend degrades gracefully to the deterministic ranker

## Documentation

`docs/` holds the architecture notes, ML pipeline, deployment guide, admin bootstrap, scope lock and the faculty demo pack.

## License

[Apache License 2.0](LICENSE)

## Author

**Mohammed Zaid Nadaf** — [GitHub](https://github.com/zaidnadaf0) · [LinkedIn](https://www.linkedin.com/in/mohammed-zaid-nadaf-1333b0368/)
