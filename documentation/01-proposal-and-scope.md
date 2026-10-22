# 1. Project Proposal and Scope

## 1.1 Background and problem statement

Streaming platforms and film databases hold tens of thousands of titles. Viewers face *choice overload*: they spend more time searching than watching. Large services (e.g. Netflix) invest heavily in personalization, but the experience is locked to one catalogue and one subscription. Open, film-database-style sites mostly offer lists, charts and generic "similar movies". There is room for a **platform-independent, transparent recommender** that learns a viewer's taste and explains its suggestions.

**Problem statement.** A person with a few dozen ratings, or none at all, has no easy way to get an accurate, explainable, personalized list of movies to watch from a broad catalogue, and cannot tell why a title is suggested.

## 1.2 Project goal

Design, build and evaluate a **web platform that produces personalized movie recommendations** with explanations, handles new users (cold start), and demonstrates measurable improvement over non-personalized baselines.

## 1.3 Objectives (SMART)

| # | Objective | Measure of success |
|---|---|---|
| O1 | Review the literature and existing systems and justify design choices | Docs 02–04 complete, ≥ 25 sources, comparison of ≥ 6 systems |
| O2 | Specify requirements with testable acceptance criteria | ≥ 90 % of Must-have requirements have an acceptance test (traceability matrix in doc 07) |
| O3 | Implement a web application: accounts, catalogue, search, ratings, watchlist, onboarding | All *Must* functional requirements pass acceptance tests |
| O4 | Implement and compare ≥ 4 recommendation approaches (popularity, content-based, item-kNN, matrix factorization) plus a hybrid | Offline evaluation report with NDCG@10, Recall@10, coverage, novelty |
| O5 | Show the hybrid beats the popularity baseline | NDCG@10 improvement with 95 % bootstrap CI excluding 0 |
| O6 | Provide explanations and feedback controls | ≥ 80 % of shown recommendations carry a reason; "not interested" removes item within one refresh |
| O7 | Evaluate usability and perceived quality with real users | ≥ 15 participants; mean SUS ≥ 68 |
| O8 | Meet basic non-functional targets | p95 recommendation API latency ≤ 500 ms at 50 concurrent users; Lighthouse accessibility ≥ 90 |

## 1.4 Scope

### In scope (MVP)

* User registration, login, logout, profile, account deletion and data export.
* Movie catalogue (imported from MovieLens + TMDB metadata): browse, search, filter by genre/year, details page.
* Explicit feedback: 1–5 star ratings (half stars optional), watchlist, "not interested".
* Cold-start onboarding: choose favourite genres and rate ≥ 5 movies.
* Personalized home page ("Recommended for you"), "Because you liked X" rows, "Similar movies" on the details page.
* Recommendation engine: popularity baseline, content-based, item-kNN CF, ALS/BPR matrix factorization, weighted hybrid, cold-start fallbacks, diversity re-ranking.
* Explanations for every recommendation.
* Offline training/evaluation pipeline; reproducible experiment scripts.
* Containerized deployment (Docker Compose).
* Automated tests and a user study.

### Out of scope

* Streaming or hosting video; payments.
* Social network features (friends, comments, public reviews).
* Native mobile applications (the web UI is responsive instead).
* Real-time online learning; session-based neural models in production (stretch goal only).
* Streaming-availability by country/provider.
* Multi-language catalogue translation (UI in English; Russian/Kazakh localisation is a stretch goal).

### Stretch goals (only if time remains)

* Neural / sequential model (e.g. NCF or SASRec-style) as an additional offline experiment.
* "Group watch" recommendations for 2+ users.
* Implicit signals (clicks, dwell time) and an A/B test harness.
* Russian/Kazakh UI localisation.

## 1.5 Target users and personas

| Persona | Needs |
|---|---|
| **Aigerim, 22, student** — watches 2–3 films a week, no consistent platform | Quick, relevant suggestions after rating a few films; wants to know *why*; limited time. |
| **Daniyar, 35, film enthusiast** — has rated hundreds of films elsewhere | Novel, non-obvious picks beyond blockbusters; control over what is excluded. |
| **Marat, 50, casual viewer** — rarely rates | Zero-effort start: pick genres and see a decent list immediately. |
| **Admin / maintainer** (the author) | Re-import data, retrain the model, see health and quality metrics. |

## 1.6 Assumptions, constraints, dependencies

* **Solo project**: one developer; effort budget ≈ 28 h/week over 16 weeks (≈ 440 h; the backlog in doc 08 totals 442 h excluding stretch items). **Adjust to your real availability**; the MoSCoW priorities in doc 05 define what is cut first if the budget is smaller.
* Data is limited to public datasets (MovieLens) — no real user base exists at the start; the user study provides the first live interactions.
* Free tiers/limits of the TMDB API; local or low-cost hosting only.
* University deadlines and format of the final thesis _[fill in]_.

## 1.7 Deliverables

1. Source code repository with README, documentation and diagrams (this repository).
2. Working web platform (Docker Compose) and a hosted demo _[optional]_.
3. Offline evaluation report and user-study results.
4. Thesis text and defence presentation.

## 1.8 Risks and mitigation

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | Scope too large for one person | High | High | Strict MoSCoW; MVP vertical slice first; stretch goals optional |
| R2 | ML results are not better than popularity | Medium | High | Report honestly (negative results are valid); tune on validation split; use hybrid with popularity prior |
| R3 | Cold start gives poor first impressions | High | Medium | Onboarding flow; content-based + popularity fallback |
| R4 | TMDB rate limits / downtime / terms | Medium | Medium | Cache metadata in DB at ingestion; pre-download posters URLs only; keep attribution |
| R5 | Dataset licence prevents redistribution | Certain | Low | Do not commit raw data; provide download script |
| R6 | Too few user-study participants | Medium | Medium | Recruit early (classmates, groups); minimum 10 as fallback; use within-subject design |
| R7 | Performance of similarity computation | Low | Medium | Precompute top-N neighbours offline; cache in Redis |
| R8 | Privacy / data-protection concerns | Low | High | Data minimisation, hashing, deletion + export, no third-party trackers, consent text |
| R9 | Single point of failure (illness, hardware) | Low | High | Git remote, YouTrack backlog, weekly backups |

## 1.9 Success criteria for the diploma

The project is successful if (a) the Must-have requirements pass their acceptance tests, (b) the evaluation report shows a statistically supported improvement of the hybrid over the popularity baseline *or* an honest analysis of why not, and (c) the user study yields SUS ≥ 68.
