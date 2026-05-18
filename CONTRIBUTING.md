# Contributing to the RPL System

Thank you for your interest in contributing to the **Recognition of Prior Learning (RPL) System**! This guide will get you from zero to your first merged PR as quickly as possible.

---

## Table of Contents
1. [Code of Conduct](#code-of-conduct)
2. [Prerequisites](#prerequisites)
3. [Local Setup](#local-setup)
4. [Project Structure](#project-structure)
5. [Coding Standards](#coding-standards)
6. [How to Pick an Issue](#how-to-pick-an-issue)
7. [Branching Strategy](#branching-strategy)
8. [Submitting a Pull Request](#submitting-a-pull-request)
9. [Running Tests](#running-tests)
10. [First-Timer Tips](#first-timer-tips)

---

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/). By participating you agree to uphold a welcoming, respectful, and harassment-free environment.

---

## Prerequisites

Before you begin, make sure you have the following installed:

| Tool | Version | Install |
|------|---------|---------|
| Python | 3.10+ | [python.org](https://python.org/downloads) |
| Git | any recent | [git-scm.com](https://git-scm.com) |
| pip | bundled with Python | — |

Optional (for Docker work):
- Docker Desktop — [docker.com](https://www.docker.com/products/docker-desktop/)

---

## Local Setup

### 1. Fork and clone

```bash
# 1. Click "Fork" on the GitHub repo page, then:
git clone https://github.com/YOUR-USERNAME/rpl-system.git
cd rpl-system

# 2. Add the upstream remote so you can pull updates
git remote add upstream https://github.com/ORIGINAL-OWNER/rpl-system.git
```

### 2. Create and activate a virtual environment

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS / Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Verify everything works

```bash
# Run the full test suite — should print "275 passed"
pytest tests/ -q

# Start the API
python api/app.py
# Open http://localhost:5000/apidocs/ in your browser
```

---

## Project Structure

```
rpl_system/
├── src/              Domain model classes (Student, RPLApplication, etc.)
├── services/         Business logic layer (StudentService, etc.)
├── repositories/     Data access layer (in-memory, filesystem, DB stub)
├── factories/        Repository factory for backend switching
├── di/               Dependency injection container
├── api/              Flask REST API (app.py)
├── docs/             OpenAPI YAML specification
├── tests/            All test files (275 tests total)
└── .github/          CI/CD workflows, PR template, CODEOWNERS
```

---

## Coding Standards

### Style — flake8
All code must pass flake8 linting:

```bash
pip install flake8
flake8 src/ services/ repositories/ factories/ di/ api/ \
  --max-line-length=120 \
  --extend-ignore=E501,W503,E302,E303
```

### Key conventions
- **Private attributes:** use name-mangling (`self.__field_name`) for all class fields
- **Properties:** expose private fields via `@property` and `@field.setter`
- **Docstrings:** every public class and method must have a docstring
- **Business rules:** document which BR is enforced in each method's docstring
- **No magic strings:** use named constants or enums for status values
- **Type hints:** add type hints to all service method signatures

### Testing
- **Minimum coverage:** 70% (enforced by CI)
- Every new feature **must** come with at least 3 tests: happy path, edge case, and error case
- New API endpoints must have at least 1 integration test in `tests/api/test_api.py`
- New service methods must have unit tests in `tests/services/test_services.py`

```bash
# Run tests with coverage
pytest tests/ --cov=src --cov=services --cov=repositories \
       --cov-report=term-missing --cov-fail-under=70
```

---

## How to Pick an Issue

1. Browse [Issues](https://github.com/ORIGINAL-OWNER/rpl-system/issues)
2. Filter by label:
   - 🟢 **`good-first-issue`** — perfect for newcomers, small and well-scoped
   - 🔵 **`feature-request`** — larger features, great for intermediate contributors
   - 🔴 **`bug`** — something is broken
   - 📚 **`documentation`** — docs improvements, no code required
3. Comment on the issue: *"I'd like to work on this"* — a maintainer will assign it to you
4. Don't start work without being assigned — prevents duplicate effort

---

## Branching Strategy

We use **GitHub Flow**:

```
main           ← protected, always deployable
  └── feature/short-description      ← your work branch
  └── fix/issue-number-short-desc
  └── docs/what-you-updated
  └── chore/dependency-update
```

```bash
# Always branch off the latest main
git checkout main
git pull upstream main
git checkout -b feature/your-feature-name
```

**Branch naming examples:**
- `feature/add-pagination-to-applications`
- `fix/42-br003-not-enforced-on-partial-approval`
- `docs/update-api-swagger-descriptions`

---

## Submitting a Pull Request

1. **Make sure tests pass locally** before pushing:
   ```bash
   pytest tests/ -q
   flake8 src/ services/ repositories/ factories/ di/ api/ --max-line-length=120 --extend-ignore=E501,W503,E302,E303
   ```

2. **Push your branch:**
   ```bash
   git push origin feature/your-feature-name
   ```

3. **Open a PR** on GitHub targeting `main`
   - The PR template will guide you through the checklist
   - Fill in the description: *what* changed, *why*, and *which issue* it closes (`Closes #42`)

4. **CI runs automatically** — both the lint and test jobs must pass before merging

5. **A maintainer will review** — address any requested changes by pushing new commits to the same branch

6. **After approval** — a maintainer merges the PR; the CD pipeline automatically builds the release artifact

### PR Checklist (from the template)
- [ ] All existing tests still pass
- [ ] New tests added for new logic
- [ ] flake8 passes
- [ ] CHANGELOG.md updated
- [ ] Linked to an issue with `Closes #number`

---

## Running Tests

```bash
# All tests
pytest tests/ -v

# Specific assignment tests
pytest tests/test_creational_patterns.py   # Assignment 10 — 89 tests
pytest tests/test_repositories.py         # Assignment 11 — 87 tests
pytest tests/services/test_services.py    # Assignment 12 — 58 tests
pytest tests/api/test_api.py              # Assignment 12 API — 41 tests

# With coverage
pytest tests/ --cov=src --cov=services --cov-report=term-missing
```

---

## First-Timer Tips

- **Not sure where to start?** Look for issues labelled `good-first-issue` and `documentation` — these require no deep knowledge of the business rules
- **Stuck?** Open a discussion in the issue thread — we're happy to help
- **Small PRs are better** — a focused 50-line PR gets reviewed in minutes; a 500-line PR takes days
- **Write the test first** — it forces you to understand what the code is supposed to do
- **Ask questions early** — post a comment on the issue before writing code if you're unsure about scope

---

## Recognition

All contributors are listed in the project's [CHANGELOG.md](CHANGELOG.md) and GitHub's contributor graph. Significant contributions may be highlighted in release notes.

Thank you for helping improve the RPL System! 🎓
