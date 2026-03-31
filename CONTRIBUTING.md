# Contributing Guide

This document is the definitive reference for all development standards on this project.

---

## Development Workflow

1. Pick a card from Trello Backlog
2. Move it to In Progress
3. Create a feature branch from main
4. Write code and commit with Trello ID
5. Push and open a Pull Request
6. CI runs automatically
7. Move card to Review/QA
8. Merge after CI passes
9. Move card to Done

---

## Branching Rules

All branches must follow this exact format:

```
feature/TRELLO-###-kebab-case-description
```

Valid examples:

```
feature/TRELLO-001-setup-repository
feature/TRELLO-002-configure-ci-pipeline
feature/TRELLO-003-add-palindrome-test
```

The main branch is protected. No direct pushes are ever permitted.

---

## Commit Format

```
[TRELLO-###] Short description in imperative mood

Optional body explaining WHY, not what.
```

Rules:

- Every commit starts with [TRELLO-###]
- Use imperative mood: "Add feature" not "Added feature"
- First line must be under 72 characters
- Minimum 8 commits across the project

Good examples:

```
[TRELLO-002] Add GitHub Actions CI pipeline with lint and test stages
[TRELLO-003] Add palindrome test to verify symmetric string reversal
[TRELLO-005] Introduce E501 violation to demonstrate failed lint build
```

Bad examples:

```
fix
updated files
working now
```

---

## Pull Request Process

PR title format:

```
[TRELLO-###] Brief description of change
```

PR checklist before opening:

- Branch follows naming convention
- All commits follow the commit format
- flake8 app.py test_app.py passes with zero output
- pytest test_app.py -v passes with all 8 tests green
- PR description explains what changed and why

Minimum 3 PRs are required for this project.
All CI checks must pass before a PR can be merged.

---

## CI/CD Explanation

The pipeline lives in .github/workflows/ci.yml and runs on every push and PR.

Stages in strict order:

1. Install Dependencies — pip install -r requirements.txt
2. Lint Code           — flake8 app.py test_app.py
3. Run Tests           — pytest test_app.py -v

Linting runs before testing. A failed lint skips the test stage entirely.
Any failure blocks the PR from being merged.

To see build details: go to the Actions tab in GitHub or click Details
on the check in the PR page.

---

## Coding Standards

| Rule | Detail |
|------|--------|
| Indentation | 4 spaces, never tabs |
| Max line length | 79 characters |
| Blank lines | 2 blank lines between top-level functions |
| Imports | One per line, at top of file |
| Trailing whitespace | Never allowed |

---

## Failure Handling

When CI fails:

1. Click Details on the failing check in the PR
2. Identify which stage failed
3. Fix the issue locally
4. Commit the fix with a proper message
5. Push — CI re-triggers automatically

Common fix commits:

```
[TRELLO-###] Fix E501 line length violation
[TRELLO-###] Remove unused import causing F401 error
[TRELLO-###] Add missing blank lines for E302 compliance
```
