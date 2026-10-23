# 1. Project Proposal and Scope

## 1.1 Background and Problem Statement

Streaming platforms and film databases hold tens of thousands of titles. Viewers face *choice overload*: they spend more time searching than watching. Large services (e.g., Netflix) invest heavily in personalization, but the experience is locked to one catalogue and one subscription. Open, film-database-style sites mostly offer lists, charts, and generic "similar movies". There is room for a **platform-independent, transparent recommender** that learns a viewer's taste, adapts to emotional context, and explains its suggestions.

**Problem Statement.** A person with a few dozen ratings, or none at all, has no easy way to get an accurate, explainable, personalized list of movies to watch from a broad catalogue, cannot easily filter content by mood or specific micro-themes, and cannot tell why a title is suggested.

## 1.2 Project Goal

Design, build, and evaluate a **web platform that produces personalized movie recommendations** with explanations, handles new users (cold start), incorporates mood/vibe filtering, and demonstrates measurable improvement over non-personalized baselines.

## 1.3 Objectives (SMART)

| # | Objective | Measure of Success |
|---|---|---|
| O1 | Review the literature and existing systems and justify design choices | Docs 02–04 complete, ≥ 25 sources, comparison of ≥ 6 systems |
| O2 | Specify requirements with testable acceptance criteria based on user research | ≥ 90 % of Must-have requirements have an acceptance test (traceability matrix in doc 07) |
| O3 | Implement a web application: accounts, catalogue, search, ratings, watchlist, onboarding | All *Must* functional requirements pass acceptance tests |
| O4 | Implement and compare ≥ 4 recommendation approaches (popularity, content-based, item-kNN, matrix factorization) plus a hybrid | Offline evaluation report with NDCG@10, Recall@10, coverage, novelty |
| O5 | Show the hybrid beats the popularity baseline | NDCG@10 improvement with 95 % bootstrap CI excluding 0 |
| O6 | Provide explanations and feedback controls | ≥ 80 % of shown recommendations carry a reason; "not interested / hide watched" removes item within one refresh |
| O7 | Evaluate usability and perceived quality with real users | ≥ 15 participants; mean SUS ≥ 68 |
| O8 | Meet basic non-functional targets | p95 recommendation API latency ≤ 500 ms at 50 concurrent users; Lighthouse accessibility ≥ 90 |

## 1.4 Scope

### In Scope (MVP)

- User registration, login, logout, profile, account deletion, and data export.
- Movie catalogue (imported from MovieLens + TMDB metadata): browse, search, filter by genre/year, details page.
- Explicit feedback: 1–5 star ratings (half stars optional), watchlist ("Watch Later"), "not interested" / "hide watched".
- Cold-start onboarding: choose favourite genres and rate ≥ 5 movies.
- Personalized home page ("Recommended for You"), "Because You Liked X" rows, "Similar Movies" on the details page.
- Mood / Vibe Tagging & Filter Engine (Mood-based discovery validated by user research).
- Diversification & Randomizer tool with user-defined boundaries to break filter bubbles.
- Cast/Director profile integration (filter and person page).
- Recommendation engine: popularity baseline, content-based, item-kNN CF, ALS/BPR matrix factorization, weighted hybrid, cold-start fallbacks, diversity re-ranking.
- Explanations for every recommendation.
- Offline training/evaluation pipeline; reproducible experiment scripts.
- Containerized deployment (Docker Compose).
- Automated tests and a user study.

### Out of Scope

- Streaming or hosting video; payments.
- Social network features (friends, comments, public reviews).
- Native mobile applications (the web UI is responsive instead).
- Real-time online learning; session-based neural models in production (stretch goal only).
- Streaming-availability by country/provider.
- Multi-language catalogue translation (UI in English; Russian/Kazakh localisation is a stretch goal).

### Stretch Goals (Only if time remains)

- Neural / sequential model (e.g., NCF or SASRec-style) as an additional offline experiment.
- "Group watch" recommendations for 2+ users.
- Implicit signals (clicks, dwell time) and an A/B test harness.
- Russian/Kazakh UI localisation.

## 1.5 Target Users and Personas (Informed by User Interviews)

| Persona | Needs & Pain Points | Derived Features |
|---|---|---|
| **Persona 1: Kamilla, 20 (User 1 - Active Viewer)** — watches films on HDRezka/niche sites | Frustrated by broad/dry genres; spends 20+ mins choosing; wants movies matching exact emotional mood ("vibe") and trailers. | Mood/Vibe filter widget, embedded YouTube trailers. |
| **Persona 2: Alikhan, 22 (User 2 - Selective Viewer)** — watches movies every couple of months | Annoyed by "filter bubbles" and commercial trend pushing; trusts external critic ratings (Rolling Stone/IMDb); seeks authorial vision. | "Filter bubble" break / diversity re-ranking, Randomizer with boundaries, external rating integration. |
| **Persona 3: Dalida, 21 (User 3 - Regular Series Fan)** — watches on HDRezka/KinoGo | Dislikes clunky UI with banner ads; needs a single watchlist; heavily influenced by cast. | Clean ad-free UI, "Watch Later" watchlist, Cast/Director spotlight. |
| **Persona 4: Tekka, 22 (User 4 - Moviegoer / Netflix User)** — attends cinema monthly | Frustrated by single-provider lock-in and seeing already watched/rejected items repeated in recommendations. | "Hide watched" filter, micro-topic tags (mythology, superheroes), cross-catalogue discovery. |

## 1.6 Assumptions, Constraints, Dependencies

- **Solo Project**: One developer; effort budget ≈ 33 h/week over 16 weeks (≈ 530 h), matching the 526 h core backlog in [doc 08](08-project-management.md). Dubbing indicator and review-phrase search were deliberately descoped.
- Data is limited to public datasets (MovieLens + TMDB API) — user study provides initial live validation.
- Free tiers/limits of the TMDB API; local or low-cost hosting only.
- University (Astana IT University) thesis guidelines and deadlines.

## 1.7 Deliverables

1. Source code repository with README, documentation, and diagrams.
2. Working web platform (Docker Compose) and a hosted demo.
3. Offline evaluation report and user-study results.
4. Thesis text and defence presentation.

## 1.8 Risks and Mitigation

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | Scope too large for one person | High | High | Strict MoSCoW; MVP vertical slice first; stretch goals optional |
| R2 | ML results are not better than popularity | Medium | High | Report honestly; tune on validation split; use hybrid with popularity prior |
| R3 | Cold start gives poor first impressions | High | Medium | Onboarding flow; content-based + popularity fallback |
| R4 | TMDB rate limits / downtime / terms | Medium | Medium | Cache metadata in DB at ingestion; pre-download poster URLs only |
| R5 | Dataset licence prevents redistribution | Certain | Low | Do not commit raw data; provide download script |
| R6 | Too few user-study participants | Medium | Medium | Recruit early (classmates, groups); minimum 10 as fallback |
| R7 | Performance of similarity computation | Low | Medium | Precompute top-N neighbours offline; cache in Redis |
| R8 | Privacy / data-protection concerns | Low | High | Anonymized user research data; deletion + export, no third-party trackers |
| R9 | Single point of failure (illness, hardware) | Low | High | Git remote, YouTrack backlog, weekly backups |

## 1.9 Success Criteria for the Diploma

The project is successful if (a) the Must-have requirements pass their acceptance tests, (b) the evaluation report shows a statistically supported improvement of the hybrid over the popularity baseline or an honest analysis of why not, and (c) the user study yields SUS ≥ 68.
