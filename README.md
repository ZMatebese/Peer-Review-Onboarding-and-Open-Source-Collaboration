# RPL System — Recognition of Prior Learning
## Assignments 9–13: Full-Stack Python System with CI/CD

[![CI/CD](https://github.com/your-username/rpl-system/actions/workflows/ci.yml/badge.svg)](https://github.com/your-username/rpl-system/actions/workflows/ci.yml)
[![Tests](https://img.shields.io/badge/tests-275%20passed-brightgreen)](https://github.com/your-username/rpl-system/actions)
[![Python](https://img.shields.io/badge/python-3.10%20|%203.11%20|%203.12-blue)](https://python.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)

---

## Project Structure

```
rpl_system/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml               ← Full CI/CD pipeline (lint + test + build + docker)
│   │   └── pr-check.yml         ← Fast PR status check (required before merge)
│   ├── pull_request_template.md
│   └── CODEOWNERS
│
├── src/                         ← Domain model (Assignment 9/10)
│   ├── student.py
│   ├── module_request.py
│   ├── rpl_application.py
│   └── assessor.py
│
├── creational_patterns/         ← 6 design patterns (Assignment 10)
├── repositories/                ← Repository layer (Assignment 11)
│   ├── interfaces.py
│   ├── inmemory/
│   ├── filesystem/
│   └── database_stub/
│
├── factories/                   ← Repository factory
├── di/                          ← Dependency injection container
├── services/                    ← Service layer (Assignment 12)
│   ├── student_service.py
│   ├── application_service.py
│   └── assessor_service.py
│
├── api/
│   └── app.py                   ← Flask REST API + Swagger UI
│
├── docs/
│   └── openapi.yaml             ← OpenAPI 2.0 specification
│
├── tests/
│   ├── test_creational_patterns.py   ← 89 tests (Assignment 10)
│   ├── test_repositories.py          ← 87 tests (Assignment 11)
│   ├── services/test_services.py     ← 58 tests (Assignment 12)
│   └── api/test_api.py               ← 41 tests (Assignment 12)
│
├── Dockerfile                   ← Container image for the API
├── setup.py                     ← Python package setup (for wheel artifact)
├── requirements.txt
├── PROTECTION.md                ← Branch protection rules documentation
└── CHANGELOG.md
```

---

## Quick Start

### Run the API locally

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Start the Flask API
python api/app.py

# API:        http://localhost:5000/api
# Swagger UI: http://localhost:5000/apidocs/
```

### Run with Docker

```bash
docker build -t rpl-system .
docker run -p 5000:5000 rpl-system
```

---

## Running Tests Locally

```bash
# Install test dependencies
pip install pytest pytest-cov flask flask-cors flasgger

# Run all 275 tests
pytest tests/ -v

# Run with coverage report
pytest tests/ --cov=src --cov=services --cov=repositories --cov=factories \
       --cov-report=term-missing --cov-fail-under=70

# Run a specific assignment's tests
pytest tests/test_creational_patterns.py -v   # Assignment 10 (89 tests)
pytest tests/test_repositories.py -v          # Assignment 11 (87 tests)
pytest tests/services/test_services.py -v     # Assignment 12 services (58 tests)
pytest tests/api/test_api.py -v               # Assignment 12 API (41 tests)
```

**Expected result:** `275 passed` across Python 3.10, 3.11, and 3.12.

---

## CI/CD Pipeline

### How it works

```
Developer pushes branch
        │
        ▼
┌───────────────────────────────────────────────┐
│  GitHub Actions: ci.yml                        │
│                                                │
│  Job 1: 🔍 Lint (flake8)                      │
│    └── Checks code style in src/, services/,   │
│        repositories/, api/                     │
│                                                │
│  Job 2: 🧪 Tests (pytest × 3 Python versions) │
│    └── Runs all 275 tests                      │
│    └── Generates coverage report (HTML + XML)  │
│    └── Uploads test artifacts                  │
│                                                │
│  [Only if push to main AND Jobs 1+2 pass]      │
│                                                │
│  Job 3: 📦 Build Release Artifact              │
│    └── Builds Python wheel (dist/)             │
│    └── Creates release zip archive             │
│    └── Uploads to GitHub Actions Artifacts     │
│                                                │
│  Job 4: 🐳 Build Docker Image                 │
│    └── Builds and pushes to ghcr.io            │
│                                                │
│  Job 5: 💬 PR Comment (on PRs only)           │
│    └── Posts pass/fail summary on the PR       │
└───────────────────────────────────────────────┘
        │
        ▼ (on PR to main)
 Merge blocked until:
   ✅ Lint passes
   ✅ All 275 tests pass
   ✅ At least 1 reviewer approves
```

### Workflow files

| File | Purpose | Triggers |
|------|---------|----------|
| `.github/workflows/ci.yml` | Full pipeline: lint, test (3 Python versions), build artifact, Docker | Every push + PRs to main |
| `.github/workflows/pr-check.yml` | Fast single-check required for PR merge | PRs to main only |

### Branch protection rules

See [`PROTECTION.md`](PROTECTION.md) for full documentation. Summary:

| Rule | Setting |
|------|---------|
| Require PR before merging | ✅ Enabled (1 approval required) |
| Require status checks | ✅ `PR Quick Check` must pass |
| Require up-to-date branch | ✅ Enabled |
| No admin bypass | ✅ Enabled |
| Direct push to main | ❌ Blocked |

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/health` | Health check |
| GET/POST | `/api/students` | List / register students |
| GET/PUT/DELETE | `/api/students/{id}` | Get / update / delete student |
| POST | `/api/students/{id}/suspend` | Suspend student |
| GET/POST | `/api/applications` | List / submit applications |
| GET | `/api/applications/stats` | Dashboard statistics |
| GET | `/api/applications/{id}` | Get application detail |
| POST | `/api/applications/{id}/withdraw` | Withdraw (BR-004) |
| PUT | `/api/applications/{id}/status` | Update status |
| POST | `/api/applications/{id}/evidence` | Upload evidence |
| POST | `/api/applications/{id}/evidence/{did}/verify` | Verify/reject evidence (BR-003) |
| GET/POST | `/api/assessors` | List / register assessors |
| POST | `/api/assessors/{id}/assign` | Assign to application (BR-008) |
| POST | `/api/assessors/{id}/reports` | Submit assessment report |
| PUT | `/api/assessors/{id}/availability` | Update availability |
| GET/POST | `/api/modules` | List / create modules |
| GET/PUT/DELETE | `/api/modules/{code}` | Get / update / deactivate module |
| GET | `/api/modules/{code}/eligibility` | Check NQF eligibility (BR-007) |

Full Swagger docs: **http://localhost:5000/apidocs/**

---

## Business Rules

| Rule | Description | Enforced In |
|------|-------------|-------------|
| BR-001 | Max 3 active applications per student | `RPLApplicationService.submit_application()` |
| BR-002 | At least 1 module request per application | `RPLApplicationService.submit_application()` |
| BR-003 | Verified evidence required before module approval | `RPLApplicationService.approve_module_request()` |
| BR-004 | Only Pending/UnderReview apps can be withdrawn | `RPLApplication.withdraw()` |
| BR-005 | Assessment panel requires quorum of 3 | `AssessmentPanel.convene_panel()` |
| BR-007 | Student NQF level must meet module requirement | `AcademicModuleService.check_eligibility()` |
| BR-008 | Assessors cannot review their own department | `AssessorService.assign_to_application()` |
| BR-010 | Credits confirmed only after Admin approval | `Admin.confirm_credit_grant()` |

---

## Test Summary

| Assignment | Tests | Status |
|------------|-------|--------|
| 10 — Creational Patterns | 89 | ✅ Passed |
| 11 — Repository Layer | 87 | ✅ Passed |
| 12 — Service Layer + API | 99 | ✅ Passed |
| **Total** | **275** | **✅ All passed** |


---

## Getting Started for Contributors

New to the project? Follow these steps:

```bash
# 1. Fork the repo on GitHub, then clone your fork
git clone https://github.com/YOUR-USERNAME/rpl-system.git
cd rpl-system

# 2. Create virtual environment and install dependencies
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

# 3. Run tests to confirm everything works
pytest tests/ -q                # Should print: 275 passed

# 4. Start the API and open Swagger UI
python api/app.py
# → http://localhost:5000/apidocs/
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full contributor guide.

---

## Features for Contribution

| Feature | Difficulty | Label | Issue |
|---------|-----------|-------|-------|
| Add pagination to list endpoints | 🟢 Low | `good-first-issue` | #42 |
| Docker Compose for local dev | 🟢 Low | `good-first-issue` | #43 |
| Search/filter on student list | 🟢 Low | `good-first-issue` | #44 |
| Rate limiting (flask-limiter) | 🟢 Low | `good-first-issue` | #45 |
| Host Swagger on GitHub Pages | 🟢 Low | `good-first-issue` | #46 |
| JWT Authentication | 🔵 High | `feature-request` | #47 |
| PostgreSQL database backend | 🔵 High | `feature-request` | #48 |
| Application appeal workflow | 🔵 Medium | `feature-request` | #49 |

See [ROADMAP.md](ROADMAP.md) for the full feature roadmap.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a PR.

[![Contributors](https://img.shields.io/github/contributors/your-username/rpl-system)](https://github.com/your-username/rpl-system/graphs/contributors)
[![Forks](https://img.shields.io/github/forks/your-username/rpl-system)](https://github.com/your-username/rpl-system/network/members)
[![Stars](https://img.shields.io/github/stars/your-username/rpl-system)](https://github.com/your-username/rpl-system/stargazers)
