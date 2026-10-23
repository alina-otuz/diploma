# 3. Existing System Comparison

> **Method and caveat.** This is a qualitative, feature-level comparison of publicly visible behaviour, based on general knowledge of the products. Products change often: **before submission, re-check each system yourself (create a test account, take dated screenshots) and fix any cell that is out of date.** Put screenshots in `docs/evidence/` and reference them here.

Legend: ● strong / yes · ◐ partial / limited · ○ weak / no · ? verify

## 3.1 Systems reviewed

| System | Type | Main purpose |
|---|---|---|
| **Netflix** | Commercial streaming service | Watch content from its own catalogue; industry reference for personalization [GomezUribe2015] |
| **IMDb** | Film database + watchlist | Information, ratings, reviews, charts |
| **Letterboxd** | Social film diary | Log/rate films, lists, reviews, follow friends |
| **MovieLens** | Academic recommender (GroupLens) | Research platform; personalised recs from explicit ratings [Harper2015] |
| **JustWatch** | Streaming search engine | Find where to stream a title; watchlist |
| **Kinopoisk** | Regional film database + streaming | Popular in CIS countries; Russian-language database, ratings, recommendations, streaming |
| **TMDB** | Open community movie database/API | Metadata and API (not a recommender product) — reviewed as a data source |

## 3.2 Feature comparison

| Criterion | Netflix | IMDb | Letterboxd | MovieLens | JustWatch | Kinopoisk | **CineMatch (target)** |
|---|---|---|---|---|---|---|---|
| Personalised ranking from user's own ratings | ● | ◐ | ◐ | ● | ◐ | ◐ | ● |
| Works across *any* movie (not limited to one catalogue) | ○ | ● | ● | ● | ● | ● | ● |
| Explanations of why an item is shown | ◐ ("Because you watched…") | ○ | ○ | ◐ | ○ | ? | ● (every item) |
| Cold-start onboarding | ● | ○ | ○ | ● | ◐ | ? | ● |
| User can say "not interested"/scrutinise | ● | ○ | ○ | ◐ | ○ | ? | ● |
| Transparent algorithm / open methodology | ○ | ○ | ○ | ● (papers) | ○ | ○ | ● (documented, reproducible) |
| Social features | ○ | ◐ | ● | ○ | ◐ | ◐ | ○ (out of scope) |
| Streaming-availability info | ● (own only) | ◐ | ◐ | ○ | ● | ◐ | ○ (out of scope) |
| Free to use | ○ (subscription) | ● (ads) | ● / paid tier | ● | ● | ◐ | ● |
| Data export / deletion control | ? | ? | ◐ | ? | ? | ? | ● (requirement) |
| Beyond-accuracy control (diversity/novelty) | ◐ | ○ | ○ | ◐ | ○ | ○ | ◐ (diversity re-ranking) |
| Multi-algorithm evaluation published | ◐ | ○ | ○ | ● | ○ | ○ | ● (thesis) |

*(Cells marked ? could not be asserted from general knowledge — verify.)*

### 3.2b Discovery features requested in the user interviews

Based on [doc 1a](01a-user-research.md). Same legend and the same caveat: verify in the live products.

| Feature | Netflix | IMDb | Letterboxd | MovieLens | JustWatch | Kinopoisk | **CineMatch (target)** |
|---|---|---|---|---|---|---|---|
| Mood / vibe filtering | ◐ (curated mood-style rows/categories) | ○ | ◐ (themes/tags, community lists) | ◐ (tag genome / tag search) | ○ | ? | ● (FR-60) |
| Micro-topic tags | ◐ | ◐ (keywords) | ● (themes, nanogenres) | ◐ (tags) | ○ | ? | ● (FR-61) |
| Randomizer with user boundaries | ○ ("play something" only) | ○ | ◐ (random from list) | ○ | ○ | ? | ● (FR-62) |
| Embedded trailers | ● | ● | ◐ | ○ | ◐ | ● | ● (FR-63) |
| External critic ratings shown | ○ | ◐ (own + Metascore) | ◐ | ○ | ◐ | ◐ | ◐ (FR-64, via API) |
| Hide watched / not interested | ◐ | ○ | ◐ (watched toggle) | ● | ◐ | ? | ● (FR-67, FR-22) |
| Cast/director filtering | ◐ | ● | ● | ○ | ◐ | ● | ◐ (FR-65) |

**Reading:** no single reviewed product combines personalised ranking, mood filtering, a bounded randomizer and a visible "why" in one clean interface. Individual features exist (Letterboxd themes, Netflix mood rows, IMDb trailers), so CineMatch's contribution is the **integration plus transparent evaluation**, not any single feature.

### 3.2c "Current practice" reported by interviewees

Interviewees named unofficial streaming aggregator sites (e.g. HDRezka, KinoGo) as where they watch. They are mentioned only as the user-reported baseline: users cited ad banners, outdated UI, no central watchlist and weak recommendations. They are **not** a design benchmark or a legal model, and should not be linked in the thesis.

## 3.3 Observations

1. **Closed personalization (Netflix)** is the quality benchmark but only recommends from its own catalogue and does not expose reasoning beyond brief labels.
2. **Databases and social sites (IMDb, Letterboxd, Kinopoisk)** hold rich metadata and communities, but personalization is secondary to browsing, charts and curation.
3. **MovieLens** is closest in function but is a research tool with a dated interface and minimal explanation of individual items.
4. **JustWatch** solves a different problem (where to watch), complementary rather than competing.
5. **Gap:** a clean, free, explainable, *catalogue-agnostic* recommender with onboarding for cold start, **mood-based discovery**, a **bounded randomizer to escape filter bubbles**, and visible user control — this is CineMatch's niche.

## 3.4 What we adopt and what we avoid

| Adopt | From |
|---|---|
| Row-based home page ("Because you liked X") | Netflix |
| Rating-first onboarding | MovieLens, Netflix |
| Clean detail page with metadata and similar titles | IMDb, TMDB |
| Lightweight watchlist | IMDb, JustWatch, Letterboxd |
| Avoid: heavy social layer, ad-driven layout, opaque rankings | — |

## 3.5 Differentiators for the thesis

* Every recommendation is **explained** and can be dismissed.
* The platform treats **cold start as a first-class flow**, with separate evaluation for new vs. active users.
* **Transparent, reproducible evaluation** of several algorithm families under one protocol.
* **Privacy-minimal** design with export and deletion.
* **Mood and micro-topic discovery grounded in user research**, with a validation experiment for the mood labels.
* **Bounded randomizer** that gives users control over novelty (anti-filter-bubble).
