# Evidence of Relevance and User Research (section 2 of the proposal)


## 2.1 Methodology

To ground the system requirements in real user needs rather than speculative assumptions, **4 asynchronous text-based user interviews** were conducted via Telegram in October 2026. The participants represented target user profiles ranging from casual series watchers to cinema enthusiasts.

The interviews evaluated current movie discovery habits, pain points with existing recommendation algorithms (e.g., Netflix, HDRezka, Kinopoisk), decision-making criteria, and desired platform features.

## 2.2 Summary of User Discussions

### User 1 (Active Viewer)
- **Habits:** Watches movies/series on HDRezka, VK, and niche fan sites.
- **Pain Points:** Choice process takes ~20 minutes. Standard algorithms fail to capture emotional "vibe" or mood. Dry genre tags (e.g., "Action") combine mismatched tones (e.g., suggesting emotional *Leon* when wanting fast-paced *John Wick*).
- **Key Needs:** Mood/vibe filtering, embedded trailers, and searching by emotional phrases from reviews (e.g., "ending blew my mind").

### User 2 (Selective Viewer)
- **Habits:** Watches movies every few months; relies on curated external lists (e.g., Rolling Stone Top 100).
- **Pain Points:** Suffers from "filter bubbles" and echo chambers where algorithms relentlessly push narrow genres after a single view. Frustrated by commercial mainstream bias over artistic quality.
- **Key Needs:** Diversity in recommendations, a "Randomizer with custom parameters", and nuanced movie descriptions emphasizing authorial vision and story pacing over generic genre labels.

### User 3 (Regular Series Viewer)
- **Habits:** Frequently watches series on HDRezka/KinoGo.
- **Pain Points:** Annoyed by intrusive ad banners and outdated web UI. Frustrated by repeated popular recommendations and lack of a central watchlist.
- **Key Needs:** Clean ad-free interface, "Watch Later" list, cast/director filtering, and clear indication of Russian audio dubbing availability.

### User 4 (Cinema & Netflix Viewer)
- **Habits:** Visits cinema twice monthly; uses Netflix for series; follows online movie critics.
- **Pain Points:** Single-provider catalog lock-in (Netflix only knows its own titles). Repeated recommendation cards that the user has already seen or rejected.
- **Key Needs:** Ability to hide already watched/seen titles, filtering by specific micro-topics (e.g., mythology, superheroes), and filtering by favorite actors/franchises.

## 2.3 Verified Findings vs. Initial Assumptions

| Category | Initial Assumption (Pre-Interview Hypothesis) | Verified Finding (Interview Evidence) | Resulting Requirement |
|---|---|---|---|
| **Discovery Criteria** | Users filter primarily by release year, director, and standard genre lists. | 3 out of 4 users select films based on **mood / emotional vibe** and specific micro-topics rather than release year. | **FR-60, FR-61:** Implement Mood/Vibe tag filtering and micro-topic tags. |
| **Recommendation Diversity** | Algorithm should strictly surface the highest similarity score items. | Users feel trapped in **"filter bubbles"** and demand controlled randomness/novelty. | **FR-45, FR-62:** Implement diversity re-ranking and a customizable "Randomizer" tool. |
| **Media & Trust** | Textual metadata and platform star ratings are sufficient for decision making. | Users rely on **embedded trailers**, external critic scores (IMDb/Rotten Tomatoes), and cast familiarity. | **FR-63, FR-64:** Integrate embedded YouTube trailers and external rating aggregation. |
| **List Management** | Users only need basic search and "Recommended for You" grids. | Users are frustrated by seeing already watched movies repeated and demand an explicit **Watchlist** and **"Hide Watched"** toggle. | **FR-21, FR-22, FR-67:** Provide "Watch Later" bookmarking and item exclusion controls ("Hide Watched / Not Interested"). |

## 2.4 Functional Requirements Traceability Matrix

> **ID alignment.** The interview-derived requirements were originally numbered FR-01…FR-08. Those numbers collided with the account/catalogue requirements in [doc 05](05-requirements-and-acceptance-criteria.md) (where FR-01 = registration), so they were renumbered into one scheme. The *Original ID* column keeps the link to the first version of this document.

| Requirement ID | Original ID | Description | Source / Evidence | Priority (MoSCoW) |
|---|---|---|---|---|
| **FR-60** | FR-01 | Mood/Vibe tag filter | User 1 & User 3 interviews | Must |
| **FR-61** | FR-01 | Micro-topic tags (e.g. mythology, superheroes) | User 1 & User 4 interviews | Must |
| **FR-45, FR-62** | FR-02 | Diversity re-ranking & Randomizer with user-defined boundaries | User 2 interview | Must |
| **FR-63** | FR-03 | Embedded trailer player modal | User 1 & User 3 interviews | Must |
| **FR-21** | FR-04 | Watchlist ("Watch Later") management | User 3 interview | Must |
| **FR-22, FR-67** | FR-05 | "Not interested" and "Hide watched" feedback controls | User 4 interview | Must |
| **FR-65** | FR-06 | Cast & Director filtering / profile page | User 3 & User 4 interviews | Should |
| **FR-64** | FR-07 | External ratings (IMDb / Rotten Tomatoes / Metacritic) | User 2 & User 3 interviews | Should |
| ~~FR-66~~ | FR-08 | Audio track / Russian dubbing availability indicator | User 3 interview | **Descoped by the author** (no reliable data source) |
| ~~FR-68~~ | (persona 1 need) | Search by phrases from reviews ("ending blew my mind") | User 1 interview | **Descoped by the author** |

Not derived from a single interview but implied by several: a clean, ad-free interface (Users 3), single watchlist (User 3) and cross-catalogue discovery (User 4) are covered by NFR-06/NFR-07 and by the platform's catalogue-agnostic design.

## 2.5 Data-source feasibility of the interview-derived features

The interviews produced requirements that the chosen datasets do not provide out of the box. The plan for each:

| Feature | Data needed | Source / approach | Risk | Decision |
|---|---|---|---|---|
| Mood/Vibe filter (FR-60) | Per-movie mood labels | Define ~8 moods (e.g. cozy, adrenaline, dark & gritty, mind-bending, romantic, funny & light, emotional, scary). Derive scores from the **MovieLens Tag Genome** (relevance of tags like "dark", "feel-good", "atmospheric" per movie) by a curated tag→mood mapping, supplemented by TMDB keywords/genres. The genome is not in every MovieLens download size; confirm availability in the version you use. **Validate**: two annotators label ~50 movies, compute agreement and the precision of the automatic labels. | Medium: labels may be noisy; genome covers fewer new films | Must, with a validation experiment (becomes a thesis contribution) |
| Micro-topic tags (FR-61) | Topics such as mythology, superheroes | TMDB keywords (ingested per movie) + genome tags; expose a curated top-N list | Low | Must |
| Randomizer (FR-62) | Candidate pool + boundaries | Own logic over catalogue; see doc 05 acceptance criteria | Low | Must |
| Trailers (FR-63) | YouTube video key | TMDB videos endpoint. Load the player **only after click**, from the privacy-enhanced YouTube domain, so no third-party requests happen on page load (NFR-05) | Low | Must |
| External ratings (FR-64) | IMDb / RT / Metacritic scores | These are not in TMDB or MovieLens. A third-party API such as OMDb returns them; check its current terms, quotas and attribution requirements, cache aggressively, show only for movies already requested. Never scrape. | Medium: licence/quota | Should; degrade gracefully if unavailable |
| Cast & director (FR-65) | People per movie | TMDB credits endpoint | Low | Should |
| ~~Dubbing indicator (FR-66)~~ — descoped | Which audio tracks exist | **No free, reliable source** exists for dubbing per title (TMDB lists original/spoken languages and translations of text, not dubs of releases). Options: curated CSV for a small set of titles, or user-reported flags. | High | Could; build UI + data field, fill only if a source is found |
| ~~Review phrase search (FR-68)~~ — descoped | Review text | TMDB reviews are sparse; would need text search/NLP | High | Won't now; stretch |

## 2.6 Limitations of the research and how to strengthen it

* **Sample:** 4 text interviews, all from a similar age group (20–22) and student context, so findings are *indicative*, not representative. State this in the thesis.
* **Method:** asynchronous text interviews allow no probing; keep the interview guide and anonymised transcripts as an appendix, with participants' consent.
* **Legal/ethical note:** interviewees mentioned unofficial streaming sites as their current tools. Describe them neutrally as "current practice" and do not use them as benchmarks or link to them; CineMatch itself only recommends and links to metadata and trailers.
