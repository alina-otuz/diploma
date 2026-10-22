# 8. Project Management and YouTrack

## 8.1 YouTrack project

**Board / project link:** <https://REPLACE-ME.youtrack.cloud/agiles/…> ← _paste your link here and in the README._

YouTrack Cloud offers a free tier for small teams. Make sure the link is viewable by your supervisor/reviewers (grant them access or enable public/guest access as your university allows).

### Setup steps (10 minutes)

1. Create a YouTrack project **CINE** (Scrum template).
2. Import the backlog: *Administration → Import → CSV* (or *Projects → CINE → Import issues from CSV*) with [`youtrack/backlog.csv`](youtrack/backlog.csv). Map columns: Summary → Summary, Description → Description, Assignee → Assignee, Priority → Priority, Type → Type, Subsystem → Subsystem, Sprint → Sprint, State → State. If a field is missing in your project, add it in *Custom fields* or ignore it. If CSV import is not available on your plan, create the issues manually from [`youtrack/backlog_table.md`](youtrack/backlog_table.md).
3. Before generating the CSV, set `ASSIGNEE` in [`youtrack/generate_backlog.py`](youtrack/generate_backlog.py) to your YouTrack login and run `python generate_backlog.py`; otherwise bulk-assign all issues to yourself after import.
4. Create an **agile board** (Scrum) with Sprint 1…8; set sprint dates to match your calendar.
5. Mark Sprint 1 tasks that are already done in this repo as **Done**.
6. Paste the board link into the README table.

## 8.2 Working method

* One-person Scrum-lite: 8 sprints (weeks 1-3, then 2 weeks each, last sprint 3 weeks).
* Priorities: **Critical** = Must and on the critical path; **Major** = Must/Should; **Normal** = Should; **Minor** = Could/stretch.
* Weekly review with the supervisor; re-prioritise using the MoSCoW cut order in doc 05.
* Branching: `main` (stable) + short-lived `feature/<issue-id>-slug`; pull requests with CI; commit messages reference the YouTrack issue (e.g. `CINE-12: add ratings endpoint`).
* Definition of Done: see doc 07 §7.8.

## 8.3 Sprint plan

| Sprint | Weeks | Goal |
|---|---|---|
| 1 | 1–3 | Planning documents, architecture, prototype, repo + CI |
| 2 | 4–5 | Data ingestion, schema, authentication |
| 3 | 6–7 | Catalogue, search, ratings, watchlist, UI shell, onboarding |
| 4 | 8–9 | Evaluation harness, popularity/content/item-kNN, first recommendations API |
| 5 | 10–11 | ALS/BPR, hybrid, explanations, model store, "not interested" |
| 6 | 12 | Integrated recommendation UI, caching, near-real-time updates |
| 7 | 13 | Testing, performance, security, accessibility, user study |
| 8 | 14–16 | Final experiments, analysis, thesis, defence |

## 8.4 Backlog (51 tasks, ~442 h excluding stretch)

The table is generated from the same data as `backlog.csv`. Regenerate it with `python docs/youtrack/generate_backlog.py` after editing.

| # | Sprint | Epic | Task | Priority | Type | Est. (h) | Requirement |
|---|---|---|---|---|---|---|---|
| 1 | Sprint 1 | Project | Write proposal, scope and risk register | Critical | Task | 8 | docs/01 |
| 2 | Sprint 1 | Project | Literature review and references | Critical | Task | 16 | docs/02 |
| 3 | Sprint 1 | Project | Existing system and technology comparison | Major | Task | 8 | docs/03, 04 |
| 4 | Sprint 1 | Project | Requirements and acceptance criteria | Critical | Task | 10 | docs/05 |
| 5 | Sprint 1 | Project | Architecture diagrams (Mermaid sources) | Major | Task | 8 | docs/06 |
| 6 | Sprint 1 | Project | Clickable prototype | Major | Task | 8 | prototype/ |
| 7 | Sprint 1 | Project | Verification and evaluation plan | Major | Task | 6 | docs/07 |
| 8 | Sprint 1 | DevOps | Repo setup, Docker Compose skeleton, CI (lint + tests) | Critical | Task | 8 | NFR-12, NFR-10 |
| 9 | Sprint 2 | Data | DB schema + Alembic migrations (ER diagram) | Critical | Task | 8 | doc 06 diagram 3 |
| 10 | Sprint 2 | Data | Ingest MovieLens ratings/movies/links | Critical | Feature | 8 | FR-50 |
| 11 | Sprint 2 | Data | Enrich movies with TMDB metadata (posters, overview, keywords) with caching | Major | Feature | 10 | FR-50, FR-14 |
| 12 | Sprint 2 | Backend | Registration and login (argon2id, JWT) | Critical | Feature | 10 | FR-01, FR-02, NFR-03 |
| 13 | Sprint 2 | Backend | Account deletion and data export | Major | Feature | 6 | FR-04, FR-05, NFR-05 |
| 14 | Sprint 3 | Backend | Movie list, filters and details endpoints | Critical | Feature | 10 | FR-10, FR-12, FR-13 |
| 15 | Sprint 3 | Backend | Typo-tolerant title search (pg_trgm) | Major | Feature | 6 | FR-11 |
| 16 | Sprint 3 | Backend | Ratings and watchlist endpoints | Critical | Feature | 8 | FR-20, FR-21 |
| 17 | Sprint 3 | Frontend | App shell, routing, auth screens | Critical | Feature | 12 | FR-01, FR-02 |
| 18 | Sprint 3 | Frontend | Catalogue, search, details page, rating widget, watchlist | Critical | Feature | 16 | FR-10..FR-21, FR-14 |
| 19 | Sprint 3 | Frontend | Onboarding flow (genres + first ratings) | Critical | Feature | 10 | FR-30, FR-31 |
| 20 | Sprint 4 | ML | Evaluation harness: splits, metrics, bootstrap CI | Critical | Feature | 14 | FR-52, NFR-11 |
| 21 | Sprint 4 | ML | Baselines: random and popularity (Bayesian) | Critical | Feature | 4 | RQ1 |
| 22 | Sprint 4 | ML | Content-based recommender (TF-IDF cosine) | Critical | Feature | 10 | FR-32 |
| 23 | Sprint 4 | ML | Item-kNN collaborative filtering | Critical | Feature | 10 | FR-42 |
| 24 | Sprint 4 | Backend | Recommendations endpoint v1 (popularity + content, genres for cold start) | Critical | Feature | 8 | FR-32, FR-40 |
| 25 | Sprint 5 | ML | ALS and BPR matrix factorization with tuning | Major | Feature | 14 | RQ2 |
| 26 | Sprint 5 | ML | Weighted hybrid with cold-start switching; tune weights on validation | Critical | Feature | 12 | FR-46 |
| 27 | Sprint 5 | ML | Explanation generator (reason text) | Critical | Feature | 6 | FR-41 |
| 28 | Sprint 5 | ML | MMR diversity re-ranking | Minor | Feature | 6 | FR-45, RQ4 |
| 29 | Sprint 5 | Backend | Model store, versioning, hot reload, /health | Major | Feature | 8 | FR-51 |
| 30 | Sprint 5 | Backend | Not interested endpoint and cache invalidation | Major | Feature | 4 | FR-22 |
| 31 | Sprint 6 | Frontend | Recommendation rows with reasons and Not interested button | Critical | Feature | 12 | FR-40, FR-41, FR-22 |
| 32 | Sprint 6 | Frontend | Similar movies and Because you liked X rows | Major | Feature | 6 | FR-42, FR-43 |
| 33 | Sprint 6 | Backend | Redis caching for recommendation lists | Major | Task | 6 | NFR-01 |
| 34 | Sprint 6 | Backend | Instant update of content signal after new rating | Major | Feature | 6 | FR-44 |
| 35 | Sprint 7 | QA | Unit and API tests to 70% coverage on recommendations module | Major | Task | 12 | NFR-10 |
| 36 | Sprint 7 | QA | Playwright E2E: onboarding to recommendations | Major | Task | 10 | FR-30..FR-44 |
| 37 | Sprint 7 | QA | Locust load test and tuning | Major | Task | 8 | NFR-01 |
| 38 | Sprint 7 | QA | Security and privacy checklist, dependency audit | Major | Task | 8 | NFR-03..05 |
| 39 | Sprint 7 | QA | Accessibility audit (Lighthouse, axe, keyboard) | Normal | Task | 6 | NFR-07 |
| 40 | Sprint 7 | Evaluation | Prepare study protocol, consent form, questionnaires (SUS, ResQue) | Critical | Task | 6 | doc 07 section 7.5 |
| 41 | Sprint 7 | Evaluation | Recruit and run the user study (>= 15 participants) | Critical | Task | 16 | NFR-06 |
| 42 | Sprint 8 | Evaluation | Final offline experiment run on tagged commit; report | Critical | Task | 10 | NFR-13, RQ1-RQ4 |
| 43 | Sprint 8 | Evaluation | Analyse user study results | Critical | Task | 8 | NFR-06 |
| 44 | Sprint 8 | Thesis | Write thesis chapters (design, implementation, evaluation) | Critical | Task | 40 |  |
| 45 | Sprint 8 | Thesis | Defence slides and demo script | Major | Task | 10 |  |
| 46 | Sprint 8 | Thesis | Final polish: README, cleanup, release tag | Normal | Task | 6 |  |
| 47 | Backlog | Stretch | Neural/sequential model experiment (NCF or SASRec-style) | Minor | Feature | 20 | doc 01 stretch |
| 48 | Backlog | Stretch | Russian/Kazakh UI localisation | Minor | Feature | 12 | doc 01 stretch |
| 49 | Backlog | Stretch | Password reset by email | Minor | Feature | 6 | FR-06 |
| 50 | Backlog | Stretch | Admin dashboard | Minor | Feature | 8 | FR-53 |
| 51 | Backlog | Stretch | Familiar-adventurous slider | Minor | Feature | 6 | FR-47 |

