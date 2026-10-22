# 7. Verification and Evaluation Plan

*Verification* asks "did we build the product right?" (tests against requirements). *Evaluation* asks "is the recommender good, and do users find it useful?" (offline experiments + user study).

## 7.1 Test strategy overview

| Level | What | Tool | Trigger | Covers |
|---|---|---|---|---|
| Unit | Scorers, blending, MMR, metrics, validators | pytest | every push (CI) | FR-40…46, NFR-10 |
| Component/API | Endpoints with test DB, auth, error cases | pytest + httpx, SQLite/Postgres service | every push | FR-01…05, 10–14, 20–23 |
| Data/pipeline | Ingestion counts, schema constraints, deterministic training | pytest + SQL checks | on pipeline change | FR-50…52, NFR-11 |
| End-to-end | Onboarding → rate → recommendations → hide | Playwright (Chromium, Firefox) | nightly / before release | FR-30…32, FR-40…44, NFR-08 |
| Performance | Load test of `/recommendations`, `/movies` | Locust | before release | NFR-01 |
| Security | Checklist (OWASP Top 10 / ASVS L1 subset), dependency audit, auth tests | manual + `pip-audit`, `npm audit` | before release | NFR-03, 04, 05 |
| Accessibility | Lighthouse CI, axe-core, keyboard walkthrough | Lighthouse, axe | before release | NFR-07 |
| Usability | Task-based user study + SUS | questionnaire | week 13 | NFR-06 |
| Exploratory | Free testing on phone + desktop | manual | weekly | all |

**Entry/exit criteria.** A release candidate is accepted when all Must acceptance tests pass, no open critical/major defects exist, CI is green, and the evaluation report is generated from the tagged commit.

**Defect handling.** Bugs are logged in YouTrack with severity (Critical/Major/Normal/Minor), reproduction steps and the requirement ID.

## 7.2 Traceability matrix (requirement → verification)

| Requirement | Unit | API | E2E | Perf | Sec | A11y | Study | Offline eval |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| FR-01, FR-02 | ✓ | ✓ | ✓ | | ✓ | | | |
| FR-04, FR-05 | | ✓ | ✓ | | ✓ | | | |
| FR-10…FR-14 | | ✓ | ✓ | | | ✓ | | |
| FR-20…FR-22 | ✓ | ✓ | ✓ | | | | | |
| FR-30…FR-32 | ✓ | ✓ | ✓ | | | | ✓ | ✓ |
| FR-40…FR-42 | ✓ | ✓ | ✓ | ✓ | | | ✓ | ✓ |
| FR-44…FR-46 | ✓ | ✓ | ✓ | | | | | ✓ |
| FR-50…FR-52 | ✓ | | | | | | | ✓ |
| NFR-01 | | | | ✓ | | | | |
| NFR-03…05 | | ✓ | | | ✓ | | | |
| NFR-06 | | | | | | | ✓ | |
| NFR-07 | | | | | | ✓ | | |
| NFR-11, 13 | ✓ | | | | | | | ✓ |

## 7.3 Example test cases (to be implemented)

| ID | Req | Steps | Expected |
|---|---|---|---|
| TC-001 | FR-01 | POST register with a duplicate email | 409, no new row |
| TC-002 | FR-02 | Login with wrong password | 401 with generic message |
| TC-003 | FR-20 | PUT rating 4, then PUT rating 2 on same movie | single row, value 2 |
| TC-004 | FR-22 | POST hidden for movie X, then GET recommendations | X absent; also absent after cache flush |
| TC-005 | FR-32 | New user, genres {Sci-Fi, Thriller}, GET recommendations | ≥ 80 % items match a chosen genre |
| TC-006 | FR-40 | User with 10 ratings, GET recommendations | 20 items, sorted desc, none rated/hidden/watchlisted |
| TC-007 | FR-44 | Rate Horror 5★, refresh | list changes within 2 s |
| TC-008 | FR-52 | Run evaluation twice with seed 42 | identical metrics (4 d.p.) |
| TC-009 | FR-04 | Delete account | no rows with user id in any table |
| TC-010 | NFR-01 | Locust 50 users × 5 min | p95 ≤ 500 ms, errors < 1 % |

## 7.4 Offline evaluation of the recommender

### Research questions

* **RQ1.** Do personalized methods outperform non-personalized baselines (random, popularity) on top-N accuracy?
* **RQ2.** Which method family (content-based, item-kNN, ALS/BPR) works best, and does the hybrid improve on its best component?
* **RQ3.** How does performance differ for **cold** (< 5 ratings), **warming** (5–19) and **warm** (≥ 20) users?
* **RQ4.** What is the accuracy–diversity/novelty trade-off of the MMR re-ranking?

### Data

* MovieLens ml-latest-small for development; a larger MovieLens set (e.g. ml-1M or larger — decide by available hardware) for final results. Record the dataset name/version and download date.
* Filter: users with ≥ 20 ratings; items with ≥ 5 ratings (documented; report how many are removed).
* Treat ratings ≥ 4.0 as *relevant* (positive) for ranking metrics; use all ratings as implicit-feedback confidence for ALS/BPR; report the threshold sensitivity (3.5, 4.0, 4.5).

### Protocol

* **Split:** per-user leave-last-20 %-out by timestamp (primary); random per-user 80/10/10 split (sensitivity check). Validation split is used for hyper-parameter and hybrid-weight tuning; **the test split is touched once per final run.**
* **Candidate set:** rank **all** unseen items (full ranking) — avoids sampled-metric inconsistencies [Cremonesi2010].
* **Cold-start simulation:** for a random 30 % of test users, keep only the first *k* ∈ {0, 3, 5, 10, 20} training ratings and evaluate.
* **Seeds:** 5 seeds for stochastic models; report mean ± std.

### Methods compared

1. Random
2. Popularity (Bayesian weighted rating / rating count)
3. Content-based (TF-IDF cosine)
4. Item-kNN (cosine, K tuned)
5. ALS (implicit)
6. BPR (implicit)
7. **Hybrid** (weights from validation; with cold-start switching)
8. Hybrid + MMR diversification
9. *(Optional)* Surprise SVD as rating-prediction reference (RMSE), neural model if time allows

### Metrics

| Group | Metric | Definition (K = 10 unless noted) |
|---|---|---|
| Accuracy | Precision@K, Recall@K, HitRate@K | share of top-K that are relevant; share of relevant items retrieved; ≥ 1 hit |
| Ranking | **NDCG@K** (primary), MAP@K, MRR | position-aware gain [Jarvelin2002] |
| Beyond accuracy | Catalogue coverage | |∪ recommended items| / |catalogue| |
| | Novelty | mean −log₂(popularity share) of recommended items |
| | Intra-list diversity | 1 − mean pairwise genre-vector cosine similarity |
| | Gini / popularity bias | concentration of recommendations over items |
| Rating prediction (secondary) | RMSE, MAE | for Surprise/MF comparison only |

### Statistical analysis

* Per-user metric values → **paired bootstrap** (10,000 resamples of users) for mean differences and 95 % CIs between methods; also a paired Wilcoxon signed-rank test. Report effect sizes, not only p-values.
* Success criterion NFR-13: hybrid − popularity NDCG@10 > 0 with CI excluding 0.
* Multiple comparisons: report CIs and apply Holm correction to the headline p-values.

### Reporting artefacts

* `reports/offline_eval_<date>.md` + CSV generated by `python -m ml.cli evaluate` (table of methods × metrics, cold-start curves, diversity–accuracy trade-off plot, threshold sensitivity).
* Experiment config (seed, split, hyper-parameters, dataset version, git commit) saved alongside results.

### Threats to validity

| Threat | Mitigation |
|---|---|
| MovieLens raters are not representative | State limitation; include user study |
| Popularity bias inflates popularity baseline | Report novelty/coverage, popularity-debiased view |
| Hyper-parameter tuning leaks into test | Strict train/validation/test separation, test used once |
| Offline ≠ online value | Complement with user study (7.5) |
| Small data → noisy results | 5 seeds, bootstrap CIs |

## 7.5 User study (usability and perceived quality)

* **Participants:** ≥ 15 (target 20) adult film viewers outside the development circle where possible; informed consent, anonymous IDs, no personal data beyond what the app stores; right to withdraw. Check whether the university requires ethics approval for human-subject studies.
* **Design:** within-subject, 30–40 min session, counterbalanced order.
  1. Onboarding (genres + rate ≥ 5 movies + ~10 more) — timed.
  2. Blind comparison of three top-10 lists: **Popularity**, **Hybrid**, **Hybrid + explanations/diversity** (labelled A/B/C, random order). Participants mark each item *interested / not interested / already seen*, and rate list-level satisfaction.
  3. Task-based usability tasks: find a movie by search, add to watchlist, hide a recommendation, delete account (dry-run).
  4. Questionnaires: **SUS** [Brooke1996] and selected **ResQue** items [Pu2011] (perceived accuracy, novelty, diversity, transparency, satisfaction; 5-point Likert).
  5. Short semi-structured interview (what was surprising, was the explanation useful?).
* **Measures:** item-level precision (share marked *interested*), list satisfaction, onboarding time, task success/time/errors, SUS score, ResQue means, qualitative themes.
* **Analysis:** descriptive statistics + paired Wilcoxon tests across lists; SUS interpreted against the benchmark of 68; open responses coded thematically.
* **Success criteria:** mean SUS ≥ 68; Hybrid list satisfaction > Popularity (p < 0.05 or CI excludes 0); ≥ 80 % task success.

## 7.6 Performance and security checks

* **Load:** Locust scenarios (browse 50 %, details 25 %, recommendations 20 %, rate 5 %); ramp to 50 concurrent users, 5 minutes; record p50/p95/p99, throughput, error rate, CPU/RAM of containers. Run with cold and warm cache.
* **Security checklist:** password storage, JWT expiry/tampering, authorisation (user A cannot read/modify user B's data — IDOR tests), SQL injection and XSS probes, rate limiting on login, CORS, security headers, dependency audit, secrets scan.
* **Privacy checklist:** data inventory, deletion cascade verified by SQL, export correctness, consent text, no third-party trackers.

## 7.7 Schedule

| Week | Activity |
|---|---|
| 4–7 | Unit/API tests written with each feature (definition of done) |
| 8–11 | Offline evaluation harness; first results on ml-latest-small |
| 12 | Performance tuning, load tests |
| 13 | E2E, accessibility and security checks; user study |
| 14 | Final offline run on tagged commit; analysis of all results |
| 15 | Results chapter of the thesis |

## 7.8 Definition of Done (per YouTrack task)

Code merged via pull request with CI green; unit/API test added; acceptance criteria from doc 05 demonstrated; docs updated; no new lint errors; requirement ID referenced in commit/PR.
