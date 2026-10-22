# 4. Technology Comparison

> **How to read the scores.** Criteria weights and 1–5 scores below are the author's *judgement for this project* (solo developer, ML-heavy, 16 weeks), not objective truths. **Adjust the weights and the "familiarity" scores to match your real skills** — the weighted totals are recomputed by the formula `Σ weight × score` shown in each table. Re-check library maintenance status and versions at implementation time.

## 4.1 Backend framework

Weights: ML integration 30 %, development speed (solo) 25 %, performance/async 15 %, ecosystem & docs 15 %, learning curve 15 %.

| Criterion (weight) | FastAPI (Python) | Django + DRF (Python) | NestJS (Node/TS) | Spring Boot (Java) |
|---|---|---|---|---|
| ML integration (0.30) | 5 | 5 | 2 | 2 |
| Dev speed, solo (0.25) | 5 | 4 | 4 | 2 |
| Performance / async (0.15) | 4 | 3 | 4 | 5 |
| Ecosystem & docs (0.15) | 4 | 5 | 5 | 5 |
| Learning curve (0.15) | 4 | 3 | 3 | 2 |
| **Weighted total** | **4.55** | 4.15 | 3.40 | 2.90 |

**Decision: FastAPI.** Same language as the ML code (no inter-process bridge), automatic OpenAPI docs (useful for the thesis), async I/O, Pydantic validation. Django would be a fine alternative if a built-in admin panel were a priority.

## 4.2 Frontend framework

Weights: dev speed 25 %, ecosystem/libraries 25 %, learning curve 20 %, performance 20 %, SEO/SSR 10 % (low: personalised pages are behind login).

| Criterion (weight) | React + Vite | Next.js | Vue 3 | SvelteKit |
|---|---|---|---|---|
| Dev speed (0.25) | 5 | 4 | 4 | 4 |
| Ecosystem (0.25) | 5 | 5 | 4 | 3 |
| Learning curve (0.20) | 4 | 3 | 5 | 4 |
| Performance (0.20) | 4 | 4 | 4 | 5 |
| SEO / SSR (0.10) | 2 | 5 | 2 | 4 |
| **Weighted total** | **4.30** | 4.15 | 4.00 | 3.95 |

**Decision: React + TypeScript + Vite** (SPA). Largest ecosystem for UI components, testing (Playwright, Testing Library) and data fetching (TanStack Query). The scores are close — any of the four would be viable; the decision favours the widest community support.

## 4.3 Database

Weights: relational fit 30 %, JSON/flexibility 10 %, concurrency/production readiness 25 %, tooling 15 %, ops simplicity 20 %.

| Criterion (weight) | PostgreSQL | MongoDB | SQLite |
|---|---|---|---|
| Relational fit — users, ratings, joins (0.30) | 5 | 3 | 4 |
| JSON / flexible fields (0.10) | 4 | 5 | 3 |
| Concurrency / production (0.25) | 5 | 4 | 2 |
| Tooling & ORM support (0.15) | 5 | 4 | 3 |
| Ops simplicity (0.20) | 4 | 4 | 5 |
| **Weighted total** | **4.70** | 3.80 | 3.45 |

**Decision: PostgreSQL** (ratings and watchlist are naturally relational; JSONB for TMDB extras; `pg_trgm` for fuzzy title search). SQLite may be used for unit tests/local prototyping.

## 4.4 Caching

| Option | Pros | Cons | Decision |
|---|---|---|---|
| **Redis** | Fast, TTL, widely used, simple | Extra service | ✅ cache recommendation lists & popular rows |
| In-process cache | Zero ops | Not shared, lost on restart | Fallback in dev |
| No cache | Simplest | May miss latency target | Rejected |

## 4.5 Machine-learning libraries

| Library | Used for | Notes |
|---|---|---|
| **pandas / NumPy / SciPy** | Data prep, sparse matrices | Standard |
| **scikit-learn** | TF-IDF, cosine similarity, metrics helpers | Stable, simple |
| **implicit** | ALS / BPR for implicit feedback, fast on CPU | Good fit for ranking; verify current maintenance & install on your OS |
| **Surprise** | SVD/KNN for explicit rating prediction | Useful as a comparison/baseline; check maintenance status |
| **LightFM** | Hybrid MF with side features | Optional comparison; check maintenance status |
| **PyTorch** | NCF/SASRec experiments | Stretch goal only |
| **TF Recommenders** | Alternative DL stack | Not chosen (PyTorch preferred) |

**Decision:** scikit-learn + `implicit` as the core; Surprise/LightFM only as optional comparison models in notebooks; PyTorch for stretch experiments. Own thin wrappers keep the algorithms swappable.

## 4.6 Data sources

| Source | Provides | Licence/limits | Role |
|---|---|---|---|
| **MovieLens (ml-latest-small / 1M / 25M)** | Ratings, tags, links to IMDb/TMDB ids | Research/education use; no redistribution | Training & offline evaluation |
| **TMDB API** | Posters, overviews, cast, keywords | Free for non-commercial use with attribution; rate limits | Metadata enrichment |
| **OMDb API** | IMDb-style ratings, plot | Key required, limited free tier | Not used (TMDB sufficient) |
| Scraping IMDb/Kinopoisk | — | Against terms of service | **Rejected** |

Start with **ml-latest-small** (≈ 100k ratings) for fast iteration, then scale up to **ml-1M or larger** for the final evaluation. (Confirm current dataset sizes on the GroupLens site.)

## 4.7 Infrastructure and tooling

| Concern | Choice | Alternatives considered |
|---|---|---|
| Containers | **Docker + Docker Compose** | Podman, bare-metal (harder to reproduce) |
| CI | **GitHub Actions** (lint, tests, build) | GitLab CI |
| Migrations | **Alembic** (+ SQLAlchemy 2) | Django ORM migrations |
| API tests | **pytest + httpx** | Postman collections |
| E2E | **Playwright** | Cypress |
| Load | **Locust** (Python) | k6, JMeter |
| Accessibility | **Lighthouse + axe-core** | WAVE |
| Diagrams | **Mermaid** (text, versioned, renders on GitHub) | PlantUML, draw.io |
| Task tracking | **YouTrack** (required) | — |
| Hosting (demo) | Any small VPS / free tier | Local-only demo as fallback |

## 4.8 Architecture style decision (ADR-001)

* **Decision:** modular monolith (single FastAPI service with modules `auth`, `catalog`, `interactions`, `recommendations`, `admin`) + separate offline training job.
* **Why:** one developer, small traffic, simpler deployment and debugging; module boundaries keep a future split into services possible.
* **Rejected:** microservices — operational overhead not justified; serverless — cold starts conflict with in-memory model artifacts.
* **Consequence:** model artifacts are loaded at API start-up and refreshed by a reload endpoint/file watcher after each training run.

## 4.9 Summary of the stack

**React + TypeScript + Vite → FastAPI → PostgreSQL + Redis; offline training with pandas, scikit-learn, implicit; data from MovieLens + TMDB; Docker Compose; GitHub Actions; Mermaid diagrams; YouTrack.**
