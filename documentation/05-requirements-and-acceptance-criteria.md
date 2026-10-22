# 5. Initial Requirements and Acceptance Criteria

Priorities use **MoSCoW**: **M**ust (MVP, needed to pass), **S**hould (planned), **C**ould (if time), **W**on't (this release).
Acceptance criteria use Given / When / Then and are the source for the tests in [doc 07](07-verification-and-evaluation-plan.md). Status of all requirements: *Proposed*.

## 5.1 Actors

| Actor | Description |
|---|---|
| Visitor | Unauthenticated user; can browse the catalogue and see popular movies |
| User | Registered, logged-in user |
| Admin | Maintainer; runs ingestion/training, views metrics |
| System | Scheduled/offline jobs |
| TMDB API | External metadata provider |

## 5.2 Functional requirements

### Accounts (AUTH)

| ID | Requirement | Pri |
|---|---|---|
| FR-01 | A visitor can register with email and password. | M |
| FR-02 | A user can log in and log out. | M |
| FR-03 | A user can view/edit profile (display name, favourite genres). | S |
| FR-04 | A user can delete their account and all personal data. | M |
| FR-05 | A user can export their ratings/watchlist as CSV/JSON. | S |
| FR-06 | A user can reset a forgotten password by email. | C |

### Catalogue & search (CAT)

| ID | Requirement | Pri |
|---|---|---|
| FR-10 | The system lists movies with poster, title, year, genres (paginated). | M |
| FR-11 | A user/visitor can search by title (typo-tolerant). | M |
| FR-12 | A user/visitor can filter by genre and release-year range and sort by popularity/year. | S |
| FR-13 | A movie details page shows overview, cast, genres, year, runtime, trailer link and similar movies. | M |
| FR-14 | The system shows the TMDB attribution notice. | M |

### Feedback (FB)

| ID | Requirement | Pri |
|---|---|---|
| FR-20 | A user can rate a movie from 1 to 5 stars and change or delete the rating. | M |
| FR-21 | A user can add/remove movies to/from a watchlist. | M |
| FR-22 | A user can mark a recommendation as "not interested". | M |
| FR-23 | A user can view and manage their rated list. | S |
| FR-24 | The system records implicit events (impression, click) for analysis. | C |

### Onboarding & cold start (ONB)

| ID | Requirement | Pri |
|---|---|---|
| FR-30 | After first login the user selects ≥ 3 favourite genres. | M |
| FR-31 | The user is asked to rate ≥ 5 suggested movies (can skip). | M |
| FR-32 | A user with no ratings still receives sensible recommendations (popularity + genres). | M |

### Recommendations (REC)

| ID | Requirement | Pri |
|---|---|---|
| FR-40 | The system shows a "Recommended for you" list of N = 20 movies, ranked, excluding rated/hidden/watchlisted items. | M |
| FR-41 | Every recommendation shows a human-readable reason. | M |
| FR-42 | The details page shows "Similar movies" (item-based). | M |
| FR-43 | The home page shows "Because you liked X" rows for the user's top-rated films. | S |
| FR-44 | Recommendations update after new feedback (within one page refresh for content/popularity signals; after the next scheduled retrain for CF/MF). | M |
| FR-45 | The list is diversified (intra-list diversity re-ranking). | S |
| FR-46 | The hybrid switches weights by the number of user ratings (cold start → warm). | M |
| FR-47 | The user can adjust a "familiar ↔ adventurous" slider affecting novelty/diversity. | C |

### Administration & ML pipeline (ADM)

| ID | Requirement | Pri |
|---|---|---|
| FR-50 | Admin can run data ingestion (MovieLens + TMDB metadata) via CLI. | M |
| FR-51 | Admin can retrain models via CLI and the API loads the new artifacts without downtime. | M |
| FR-52 | Offline evaluation script reports metrics for all algorithms on a fixed split with a fixed random seed. | M |
| FR-53 | Admin dashboard shows catalogue/user/rating counts and last-train time. | C |

## 5.3 Non-functional requirements

| ID | Category | Requirement | Pri |
|---|---|---|---|
| NFR-01 | Performance | p95 latency of `GET /recommendations` ≤ 500 ms with 50 concurrent users (warm cache ≤ 150 ms). | M |
| NFR-02 | Performance | Initial page load (LCP) ≤ 2.5 s on a typical broadband connection. | S |
| NFR-03 | Security | Passwords hashed with a modern adaptive algorithm (argon2id or bcrypt); never logged. | M |
| NFR-04 | Security | All traffic over HTTPS in deployment; JWT/session tokens expire; input validated; OWASP Top-10 reviewed. | M |
| NFR-05 | Privacy | Collect only email, display name, ratings, watchlist, feedback; no third-party trackers; deletion removes all personal data within 24 h. | M |
| NFR-06 | Usability | New user completes onboarding in ≤ 3 minutes; mean SUS ≥ 68. | M |
| NFR-07 | Accessibility | WCAG 2.1 AA for main flows; Lighthouse accessibility ≥ 90. | S |
| NFR-08 | Compatibility | Latest 2 versions of Chrome, Firefox, Safari, Edge; responsive from 360 px width. | M |
| NFR-09 | Reliability | API handles TMDB outage gracefully (serves cached metadata). | S |
| NFR-10 | Maintainability | ≥ 70 % unit-test coverage for backend `recommendations` module; CI passes on every push. | S |
| NFR-11 | Reproducibility | All experiments reproducible from a single command with fixed seeds and documented dataset version. | M |
| NFR-12 | Portability | Entire system starts with `docker compose up`. | M |
| NFR-13 | Quality | Hybrid beats popularity baseline on NDCG@10 (95 % CI excludes 0). | M |

## 5.4 Acceptance criteria

### AUTH

* **FR-01** — *Given* a visitor on the sign-up page, *when* they submit a valid unused email and a password of ≥ 8 characters, *then* an account is created and they are logged in. *When* the email is already registered, *then* a clear error is shown and no duplicate is created. *When* the password is shorter than 8 characters, *then* the form is rejected with a message.
* **FR-02** — *Given* a registered user, *when* they log in with correct credentials, *then* they reach the home page; with wrong credentials, *then* a generic error is shown (no hint which field was wrong). *When* they click logout, *then* the token is invalidated client-side and protected pages redirect to login.
* **FR-04** — *Given* a logged-in user with ratings, *when* they confirm account deletion, *then* user, ratings, watchlist and feedback rows are removed from the database and subsequent login fails.
* **FR-05** — *Given* a user with N ratings, *when* they request export, *then* a file with exactly N ratings (movie id, title, rating, timestamp) and their watchlist downloads.

### CAT

* **FR-10** — *Given* the catalogue contains ≥ 5,000 movies, *when* a visitor opens the movies page, *then* 20 movies per page are shown with poster, title, year and genres and the next page loads on request.
* **FR-11** — *Given* the movie "Inception", *when* the user searches "incepton" (typo), *then* "Inception" appears in the top 5 results. *When* nothing matches, *then* an empty-state message appears.
* **FR-13** — *Given* a movie id, *when* the details page opens, *then* overview, genres, year, runtime and at least 6 similar movies are displayed within 1.5 s.
* **FR-14** — *Given* any page, *then* the footer contains the TMDB attribution text.

### FB

* **FR-20** — *Given* a logged-in user on a details page, *when* they click 4 stars, *then* the rating is stored and shown; *when* they click again to change to 2, *then* the stored value updates (no duplicates); *when* they remove it, *then* it is deleted.
* **FR-21** — *Given* a movie not in the watchlist, *when* the user clicks "Add to watchlist", *then* it appears in their watchlist and the button toggles; removing reverses this.
* **FR-22** — *Given* a recommendation card, *when* the user clicks "Not interested", *then* the item disappears immediately and never reappears in that user's recommendations.

### ONB

* **FR-30** — *Given* a first-time user, *when* they have not selected ≥ 3 genres, *then* the "Continue" button is disabled.
* **FR-31** — *Given* the rating step, *when* the user rates 5 films, *then* the progress indicator completes and the recommendations page is shown; *when* they skip, *then* recommendations are based on genres + popularity.
* **FR-32** — *Given* a user with zero ratings and genres {Sci-Fi, Thriller}, *then* ≥ 80 % of the 20 recommended movies contain at least one selected genre.

### REC

* **FR-40** — *Given* a user with ≥ 10 ratings, *when* they open the home page, *then* exactly 20 recommendations are returned, sorted by descending score, none of which the user has rated, hidden or watchlisted.
* **FR-41** — *Given* any recommendation response, *then* each item contains a non-empty `reason` field and the UI renders it.
* **FR-42** — *Given* a movie, *when* its details page opens, *then* similar movies are returned from the item-similarity model and exclude the movie itself.
* **FR-44** — *Given* a user rates a Horror movie 5★ when no CF retrain has run, *when* they refresh, *then* the content-based component reflects it (list changes) within 2 s.
* **FR-45** — *Given* a recommendation list, *then* its intra-list diversity (1 − mean pairwise genre similarity) is not lower than that of the same list without re-ranking by more than the accuracy-loss budget defined in doc 07 (≤ 2 % NDCG loss).
* **FR-46** — *Given* two users with 0 and 50 ratings, *then* the weight configuration returned in debug mode differs (cold-start weights vs. warm weights) as specified in the config file.

### ADM

* **FR-50** — *Given* an empty database and a valid TMDB key, *when* `python -m ml.cli ingest` runs, *then* movies, genres, links and ratings tables are filled and the command prints counts matching the source files.
* **FR-51** — *Given* a running API, *when* `train` finishes and `POST /admin/reload` is called, *then* subsequent requests use the new model version (reported by `/health`) with zero failed requests.
* **FR-52** — *Given* a fixed seed, *when* the evaluation script is run twice, *then* the metrics are identical to 4 decimal places.

### NFR (measurable)

* **NFR-01** — Locust run: 50 users, 5 minutes, p95 ≤ 500 ms and error rate < 1 %.
* **NFR-03** — Database inspection shows only argon2id/bcrypt hashes; log search finds no plaintext passwords.
* **NFR-05** — Deleting an account leaves no rows referencing the user id (SQL check).
* **NFR-06** — User study (≥ 15 participants): median onboarding time ≤ 3 min; mean SUS ≥ 68.
* **NFR-07** — Lighthouse accessibility ≥ 90 on home, details, and onboarding pages; axe reports no critical violations.
* **NFR-12** — Fresh clone + `docker compose up` brings up all services; health endpoint returns 200 in ≤ 2 minutes.
* **NFR-13** — Evaluation report: NDCG@10 (hybrid) − NDCG@10 (popularity) > 0 with 95 % bootstrap CI over users excluding 0.

## 5.5 Requirement dependencies and priorities for the MVP

* **Vertical slice 1** (weeks 4–7): FR-01, 02, 10, 11, 13, 20, 21, 30, 31, 32 (+ popularity baseline).
* **Vertical slice 2** (weeks 8–11): FR-40, 41, 42, 44, 46, 50–52.
* **Vertical slice 3** (weeks 12–14): FR-22, 45, NFR-01…13, user study.
* Cut order if time is short: Could → Should (in reverse list order) → never Must.

## 5.6 Open questions

1. Hosted demo, or local-only demonstration at the defence?
2. UI language: English only or Russian/Kazakh too?
3. Final dataset size (ml-1M vs. 25M) given the hardware available.
4. Does the university require a specific documentation template (e.g. GOST)?
