# Getting Started — New Contributor Onboarding

> Target: You should be ready to make your first commit in under 1 hour.
> Follow every step in order. Do not skip steps.

---

## Prerequisites

| Tool | Check Command | Minimum Version |
|------|--------------|-----------------|
| Python | `python --version` | 3.11+ |
| Git | `git --version` | 2.x+ |
| pip | `pip --version` | 22+ |

---

## Step 1 — Clone the Repository
```bash
git clone https://github.com/YOUR_USERNAME/devops-capstone-gitops.git
cd devops-capstone-gitops
```

---

## Step 2 — Install Dependencies
```bash
pip install -r requirements.txt
```

This installs flask, pytest, and flake8.

---

## Step 3 — Verify Everything Works
```bash
flake8 app.py test_app.py
```
Expected: No output (silence means success).
```bash
pytest test_app.py -v
```
Expected: 8 tests passing.

---

## Step 4 — Configure Git Identity
```bash
git config --global user.name "Your Full Name"
git config --global user.email "your.email@example.com"
```

---

## Step 5 — Branch Workflow

Never commit directly to main. Always create a feature branch:
```bash
git checkout main
git pull origin main
git checkout -b feature/TRELLO-###-short-description
```

---

## Step 6 — Commit Your Work
```bash
git add .
git commit -m "[TRELLO-###] Your descriptive message here"
git push origin feature/TRELLO-###-description
```

---

## Step 7 — Open a Pull Request

1. Go to the repository on GitHub
2. Click "Compare & pull request"
3. Title format: [TRELLO-###] Your description
4. Click "Create pull request"
5. Wait for CI to pass before merging

---

## Troubleshooting

### flake8 errors

| Error | Meaning | Fix |
|-------|---------|-----|
| E501 | Line too long | Break the line at 79 chars |
| W291 | Trailing whitespace | Remove spaces at line end |
| E302 | Expected 2 blank lines | Add blank lines before functions |
| F401 | Unused import | Remove the import |

### Tests failing

Run locally first: `pytest test_app.py -v`
Read the error output — it shows exactly which assertion failed.

### Branch behind main
```bash
git checkout main
git pull origin main
git checkout your-feature-branch
git merge main
```

---

## Quick Reference
```bash
# Start new task
git checkout main && git pull origin main
git checkout -b feature/TRELLO-###-description

# Commit and push
git add .
git commit -m "[TRELLO-###] Description"
git push origin feature/TRELLO-###-description

# Check quality locally
flake8 app.py test_app.py
pytest test_app.py -v
```
