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

## Setup Instructions

### Prerequisites

- Python 3.11+
- Git
- pip

### Local Setup

```bash
git clone https://github.com/YOUR_USERNAME/devops-capstone-gitops.git
cd devops-capstone-gitops
pip install -r requirements.txt
python app.py
pytest test_app.py -v
flake8 app.py test_app.py
```

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

---

## Reflection

### Q1: What is GitOps and why does Git serve as the single source of truth?

GitOps is a software development philosophy that uses Git as the authoritative record for
all changes to a system — code, configuration, and infrastructure alike. The term single
source of truth means that if something is not in Git, it does not officially exist. Before
GitOps, teams would often make hotfixes directly on production servers via SSH, meaning the
server state diverged from what anyone had documented. This caused disasters when servers
were replaced or when no one could reproduce the state.

In this project, every change — even to the CI pipeline itself — goes through a Pull Request.
The .github/workflows/ci.yml file is version-controlled alongside the application code.
This means the automation rules are also subject to peer review. If the CI file is changed
to remove a test stage, that change must go through a PR and be reviewed before taking effect.
This is the essence of GitOps: humans review intent via Git, and automation executes it.

The practical benefit for our team is auditability. Every decision has a commit, every commit
has an author, and every PR has a review thread. When something breaks, you run git log and
find the answer immediately instead of asking everyone what they changed last week.

### Q2: How does the CI pipeline prevent broken code from reaching main?

The CI pipeline acts as an automated gatekeeper. When a developer opens a Pull Request,
GitHub Actions immediately spins up a clean Ubuntu virtual machine, installs the project
dependencies, runs flake8 for style compliance, and then runs the full pytest test suite.
GitHub blocks merging until all of these checks show a green status.

The key insight is that the check runs in a clean environment — not the developer laptop
where things might work due to a locally installed library or a specific OS configuration.
This catches works-on-my-machine bugs. If the tests pass in the CI environment, they will
pass anywhere the same dependencies are installed.

During this project, I deliberately triggered a failing build by introducing a flake8
violation. The PR immediately showed a red check and the merge button was disabled. This
is the exact behavior that protects the main branch from receiving untested or
non-compliant code.

### Q3: What is the purpose of linting in a team environment?

Linting is automated style enforcement. Without it, five developers write code in five
different styles with different indentation, line lengths, and spacing conventions. While
this does not break the program, it makes code review much harder because reviewers spend
cognitive energy parsing style instead of logic.

Flake8 enforces PEP 8 — Python community style standard — automatically. This means style
is never a point of debate in code review. If flake8 passes, the style is acceptable. If
it does not, the CI build fails and the PR cannot be merged until the developer fixes it.

For a team onboarding a new developer, linting is especially valuable. A new contributor
writes code that looks unfamiliar to the team. If the linter passes, at least the formatting
is consistent with the rest of the codebase. This lowers the barrier to code review and
speeds up onboarding. In our CI pipeline, linting runs before tests because there is no
point running tests on code that does not meet style standards.

### Q4: How does the Trello-GitHub integration improve team visibility?

Without task tracking, work is invisible. A manager has no idea what a developer is working
on, and developers do not know if two people are solving the same problem. Trello solves this
by making work visible on a shared board that anyone on the team can check at any time.

The integration in this project is lightweight but effective: every commit message contains
a Trello card ID like [TRELLO-002]. This creates a bi-directional trace. If you look at a
commit in GitHub, you know exactly which business requirement it addresses. If you look at
a Trello card, you can search the Git log for all commits related to it.

The column movement from Backlog to In Progress to Review/QA to Done also provides a
real-time status update that replaces daily stand-up meetings. The board answers the
question of what everyone is working on visually, asynchronously, without interrupting
anyone.

### Q5: What did you learn about professional development workflows from this project?

The most significant lesson was understanding why processes that seem bureaucratic actually
exist. Requiring a Pull Request for every change, writing descriptive commit messages, and
running automated tests felt like overhead at first. But when I triggered the intentional
failing build, I immediately understood the value — the broken code never reached main.
In a real team, that could mean preventing a production outage.

I also learned that documentation is a first-class deliverable, not an afterthought. The
GETTING_STARTED.md file is designed so a new developer can be productive in under one hour.
In most teams, onboarding takes days because knowledge lives in people heads. Writing it
down is an act of respect for future teammates — including your future self.

Finally, this project reinforced that automation is a form of communication. The CI pipeline
communicates the team standards automatically, without anyone having to remember to check
or remind someone. That is the core principle of DevOps: automate the boring, enforce the
important, and let humans focus on the creative work.

---

## License

MIT
