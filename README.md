# CineRec — Personalized Movie Recommendation Web Platform

> Diploma project: Development of a personalized movie recommendation web platform  
> Author: Alina Otuzbayeva · Supervisor: Chingiz Kharmysov · University: Astana IT University / Software Engineering · Year: 2026

CineRec is a movie recommendation system designed to help users decide what to watch next. The platform combines user preferences, ratings, watchlist data, and a rejection signal to generate ranked and explained suggestions. The underlying approach is a hybrid recommendation system that combines content-based filtering, collaborative filtering, and matrix factorization.

This repository contains the diploma project documentation, architecture materials, diagrams, and a working interactive prototype of the user experience.

---

## Project status

| Stage | Status |
|---|---|
| Problem definition and project scope | ✅ Done — [documentation/01-proposal-and-scope.md](documentation/01-proposal-and-scope.md) |
| Literature review | ✅ Done — [documentation/02-literature-review.md](documentation/02-literature-review.md) |
| Existing systems comparison | ✅ Done — [documentation/03-existing-systems-comparison.md](documentation/03-existing-systems-comparison.md) |
| Technology comparison | ✅ Done — [documentation/04-technology-comparison.md](documentation/04-technology-comparison.md) |
| Requirements and acceptance criteria | ✅ Done — [documentation/05-requirements-and-acceptance-criteria.md](documentation/05-requirements-and-acceptance-criteria.md) |
| Architecture and diagrams | ✅ Done — [documentation/06-architecture.md](documentation/06-architecture.md) |
| Verification and evaluation plan | ✅ Done — [documentation/07-verification-and-evaluation-plan.md](documentation/07-verification-and-evaluation-plan.md) |
| Project management and backlog | ✅ Done — [documentation/08-project-management.md](documentation/08-project-management.md) |
| Interactive prototype | ✅ Done — [source code/index.html](source%20code/index.html) |
| Full MVP implementation | ⏳ Planned |

---

## Core idea

1. Hybrid recommendation approach: popularity baseline, content-based filtering, item-based collaborative filtering, and matrix factorization.
2. Explainability: every recommendation includes a clear reason such as “Because you liked Inception”.
3. User-centered interaction: genres, ratings, watchlist, search, and “not interested” feedback are all part of the experience.
4. Evaluation focus: precision, recall, NDCG, diversity, novelty, and user study metrics.

---

## Repository structure

```text
cinerec/
├── README.md
├── documentation/
│   ├── 01-proposal-and-scope.md
│   ├── 01a-user-research.md
│   ├── 02-literature-review.md
│   ├── 03-existing-systems-comparison.md
│   ├── 04-technology-comparison.md
│   ├── 05-requirements-and-acceptance-criteria.md
│   ├── 06-architecture.md
│   ├── 07-verification-and-evaluation-plan.md
│   ├── 08-project-management.md
├── diagrams/
│   ├── src/
│   └── rendered/
├── source code/
│   ├── README.md
│   ├── index.html
│   └── recommended_page.PNG
└── .gitignore
```

---

## Prototype

The repository includes a clickable prototype that demonstrates the user flow and core recommendation logic. It can be opened directly in a browser without installation.

Quick start:

```bash
# open the prototype in a browser
# Windows
start "" "source code\index.html"

# macOS
open "source code/index.html"

# Linux
xdg-open "source code/index.html"
```

For more details, see [source code/README.md](source%20code/README.md).

---

## Architecture and documentation

The main design work and project documentation are located in the [documentation/](documentation/) folder. The architectural diagrams and Mermaid sources are in [diagrams/src/](diagrams/src/) and [diagrams/rendered/](diagrams/rendered/).

Important documents:

- [documentation/01-proposal-and-scope.md](documentation/01-proposal-and-scope.md)
- [documentation/05-requirements-and-acceptance-criteria.md](documentation/05-requirements-and-acceptance-criteria.md)
- [documentation/06-architecture.md](documentation/06-architecture.md)
- [documentation/07-verification-and-evaluation-plan.md](documentation/07-verification-and-evaluation-plan.md)

---

## Planned technology stack

| Layer | Choice |
|---|---|
| Frontend | React + TypeScript + Vite |
| Backend | Python + FastAPI |
| Database | PostgreSQL |
| Cache | Redis |
| ML | pandas, scikit-learn, implicit |
| Data sources | MovieLens, TMDB |
| Deployment | Docker Compose |
| Testing | pytest, Playwright |

---

## Data and attribution

- MovieLens is used for research and educational purposes; raw datasets should not be redistributed in this repository.
- TMDB data is used for metadata and poster enrichment and must be used according to TMDB terms and attribution requirements.

---

## License and project context

This project is a diploma thesis work and is intended for academic and learning purposes. The repository is organized to support the full project lifecycle: research, design, requirements, architecture, evaluation, and prototype demonstration.

