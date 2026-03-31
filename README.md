# GitOps Workflow & CI Automation

> A lightweight internal service demonstrating a production-grade GitOps workflow
> with automated CI enforcement via GitHub Actions and task tracking via Trello.

---

## Project Overview

This project implements a GitOps-style development workflow for a small engineering team.
The goal is to eliminate broken code reaching production by enforcing automated linting
and testing on every code change before it can be merged into the main branch.

The application is a minimal Python/Flask REST API with three endpoints:

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Returns service health status |
| `/sum` | POST | Returns the sum of two numbers |
| `/reverse-string` | POST | Returns the reverse of a given string |

---

## Architecture
```
Developer Machine
│
├── Creates Trello Card (Task Tracking)
├── Creates feature branch (Git Source Control)
├── Writes code and commits with Trello ID
└── Opens Pull Request
        │
        ▼
GitHub (Source Control + Automation Hub)
│
├── GitHub Actions triggers on PR
│   ├── Stage 1: Install Dependencies  (pip install)
│   ├── Stage 2: Lint Code             (flake8)
│   └── Stage 3: Run Tests             (pytest)
│
├── If ALL stages pass → PR can be merged to main
└── If ANY stage fails → PR is blocked
```

The main branch is always in a deployable state. No direct pushes are permitted.

---

## Workflow Description

This project follows GitHub Flow — a lightweight branching model:

1. Pick a card from the Trello Backlog column
2. Move it to In Progress on the Trello board
3. Create a feature branch from main: feature/TRELLO-###-short-description
4. Commit your work with proper Trello-linked messages
5. Push your branch to GitHub
6. Open a Pull Request against main
7. CI Pipeline runs automatically
8. Move card to Review/QA on Trello
9. If CI passes, merge and move card to Done

---

## Commit Convention

Every commit must reference a Trello card ID:
```
[TRELLO-###] Short description (imperative mood, under 72 chars)

Optional body explaining the why behind the change.
```

Examples:
```
[TRELLO-001] Initialize repository structure and starter files
[TRELLO-002] Add GitHub Actions CI pipeline with lint and test stages
[TRELLO-003] Add palindrome test for reverse-string endpoint
[TRELLO-004] Write README, GETTING_STARTED, and CONTRIBUTING docs
[TRELLO-005] Trigger intentional flake8 failure for CI evidence
```

---

### Prerequisites
- Python 3.11+
- Git
- pip
---

## CI/CD Pipeline

The pipeline is defined in .github/workflows/ci.yml and triggers on:
- Every push to main
- Every push to feature branches
- Every pull request targeting main

Pipeline Stages (strict order):
1. Install Dependencies — pip install -r requirements.txt
2. Lint Code           — flake8 app.py test_app.py
3. Run Tests           — pytest test_app.py -v

A failure at any stage marks the build as failed and blocks the PR from merging.


## License

MIT
