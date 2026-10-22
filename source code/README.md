# CineRec clickable prototype

**Run:** open [`index.html`](index.html) in any modern browser (no build, no server, no network). State is stored in `localStorage`; use *My taste → Delete all my data* to reset.

**What it demonstrates** (maps to doc 05 requirements)

| Flow | Requirement |
|---|---|
| Onboarding: ≥ 3 genres, then rate films | FR-30, FR-31, FR-32 |
| "Recommended for you" with a reason on every card | FR-40, FR-41 |
| Weights switch with the number of ratings (cold / warming / warm) | FR-46 |
| MMR diversity re-ranking | FR-45 |
| "Not interested" removes a title immediately | FR-22 |
| Ratings (click the same star to unrate), watchlist | FR-20, FR-21 |
| Search / browse, details dialog with similar movies | FR-11, FR-13, FR-42 |
| "Because you liked X" row | FR-43 |
| Transparent taste profile, data export (JSON), delete data | FR-04, FR-05 |

**What it is not:** 28 hand-entered films, an illustrative popularity prior and a tiny content-based model in JavaScript — a UX and logic prototype. The real system (doc 06) uses MovieLens/TMDB data, TF-IDF, item-kNN, ALS/BPR and a FastAPI backend.

![Recommended Page after onboarding](recommended_page.png)

