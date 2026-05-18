# CHANGELOG — RPL System

## [2.0.0] — Assignment 11: Repository Layer

### Added — Repository Interfaces (`/repositories/interfaces.py`)
- Generic `Repository[T, ID]` abstract base class with 6 CRUD methods
- 8 entity-specific interfaces: `StudentRepository`, `RPLApplicationRepository`,
  `ModuleRequestRepository`, `EvidenceDocumentRepository`, `AssessorRepository`,
  `AcademicModuleRepository`, `AssessmentReportRepository`, `NotificationRepository`
- Each interface adds domain-specific query methods (e.g. `find_by_student_id`)

### Added — In-Memory Implementations (`/repositories/inmemory/`)
- `InMemoryStudentRepository` — HashMap + `find_by_email`, `find_by_status`
- `InMemoryRPLApplicationRepository` — HashMap + active application filter (BR-001)
- `InMemoryModuleRequestRepository` — HashMap with application-ID index
- `InMemoryEvidenceDocumentRepository` — HashMap with module-request-ID index
- `InMemoryAssessorRepository` — HashMap + availability filter
- `InMemoryAcademicModuleRepository` — HashMap + NQF level, faculty, active filters
- `InMemoryAssessmentReportRepository` — HashMap + assessor/application lookup
- `InMemoryNotificationRepository` — HashMap + unread filter

### Added — Filesystem Implementations (`/repositories/filesystem/`)
- `FileSystemStudentRepository` — JSON file persistence with full serialisation/deserialisation
- `FileSystemRPLApplicationRepository` — JSON file persistence including nested module requests

### Added — Database Stubs (`/repositories/database_stub/`)
- `DatabaseStudentRepository` — documented stub with SQL schema comments
- `DatabaseRPLApplicationRepository` — documented stub with SQL schema comments

### Added — Repository Factory (`/factories/repository_factory.py`)
- `RepositoryFactory` static factory for all 8 entity repositories
- Supports `"memory"`, `"filesystem"`, `"database"` storage types
- Raises `ValueError` for unknown storage types
- Raises `NotImplementedError` for backends not yet fully implemented

### Added — DI Container (`/di/container.py`)
- `RPLServiceContainer` — creates all repos via factory, injects into services
- `ApplicationService` — register student, submit/withdraw application, query by status
- `AssessorService` — register assessor, assign to application, write report

### Added — Tests (`/tests/test_repositories.py`)
- 87 new unit tests across 12 test classes
- Covers CRUD, domain queries, filesystem persistence, factory, and DI container
- **87/87 passed**

### GitHub Issues — Assignment 11
| Issue | Title | Status |
|---|---|---|
| #14 | Design generic Repository interface | ✅ Done |
| #15 | Create 8 entity-specific repository interfaces | ✅ Done |
| #16 | Implement InMemory repositories for all 8 entities | ✅ Done |
| #17 | Implement FileSystem repositories (Student, Application) | ✅ Done |
| #18 | Create database repository stubs with SQL schema docs | ✅ Done |
| #19 | Implement RepositoryFactory for backend selection | ✅ Done |
| #20 | Implement DI container with ApplicationService and AssessorService | ✅ Done |
| #21 | Write 87 unit tests for all repository layer components | ✅ Done |

---

## [1.0.0] — Assignment 10: Class Implementation & Creational Patterns

### Added
- Core domain classes: Student, StudentProfile, ModuleRequest, EvidenceDocument,
  RPLApplication, Notification, Assessor, Admin, AcademicModule,
  AssessmentReport, AssessmentPanel
- All 6 creational patterns: Simple Factory, Factory Method, Abstract Factory,
  Builder, Prototype, Singleton
- 89 unit tests — 89/89 passed

---

## [3.0.0] — Assignment 12: Service Layer & REST API

### Added — Service Layer (`/services`)
- `student_service.py` — StudentService: register, update profile, suspend/reactivate, delete; email validation, duplicate check
- `application_service.py` — RPLApplicationService: submit (BR-001/BR-002), withdraw (BR-004), upload/verify evidence (BR-003), approve module requests, stats
- `assessor_service.py` — AssessorService: register, assign to application (BR-008), submit reports, availability management
- `assessor_service.py` — AcademicModuleService: create/update/deactivate modules, NQF eligibility check (BR-007)

### Added — REST API (`/api/app.py`)
- 30+ RESTful endpoints across 4 resource groups (Students, Applications, Assessors, Modules)
- Swagger/OpenAPI UI at http://localhost:5000/apidocs/ via Flasgger
- Consistent JSON response envelope: `{"success": bool, "data": ...}` / `{"success": false, "error": "..."}`
- Seed data loaded on startup (2 students, 2 assessors, 5 modules)
- Health check endpoint: `GET /api/health`

### Added — OpenAPI Documentation (`/docs/openapi.yaml`)
- Full OpenAPI 2.0 specification for all 30+ endpoints
- Request/response schemas with examples
- Business rule annotations (BR-001 through BR-008)
- Error response schemas for all 400/404 cases

### Added — Tests
- `tests/services/test_services.py` — 58 unit tests for all 4 service classes
- `tests/api/test_api.py` — 41 integration tests using Flask test client

### GitHub Issues — Assignment 12
| Issue | Title | Status |
|---|---|---|
| #22 | Implement StudentService with validation | ✅ Done |
| #23 | Implement RPLApplicationService (BR-001 to BR-004) | ✅ Done |
| #24 | Implement AssessorService (BR-008) | ✅ Done |
| #25 | Implement AcademicModuleService (BR-007) | ✅ Done |
| #26 | Build Flask REST API with 30+ endpoints | ✅ Done |
| #27 | Add Swagger/OpenAPI documentation via Flasgger | ✅ Done |
| #28 | Write 58 service unit tests | ✅ Done |
| #29 | Write 41 API integration tests | ✅ Done |
| #30 | Generate openapi.yaml specification | ✅ Done |

### Test Summary (All Assignments)
| Assignment | Tests | Result |
|---|---|---|
| Assignment 10 — Creational Patterns | 89 | ✅ Passed |
| Assignment 11 — Repository Layer | 87 | ✅ Passed |
| Assignment 12 — Services & API | 99 | ✅ Passed |
| **Total** | **275** | **✅ 275 passed** |

---

## [4.0.0] — Assignment 13: CI/CD Pipeline

### Added — GitHub Actions Workflows (`.github/workflows/`)
- `ci.yml` — Full 5-job CI/CD pipeline:
  - Job 1: flake8 lint check across all source directories
  - Job 2: pytest matrix (Python 3.10, 3.11, 3.12) — all 275 tests + coverage report
  - Job 3: Python wheel + release zip artifact (main branch only, after tests pass)
  - Job 4: Docker image build & push to ghcr.io (main branch only)
  - Job 5: Automatic PR comment with pass/fail summary table
- `pr-check.yml` — Lightweight fast check required as GitHub status check on every PR

### Added — Supporting Files
- `Dockerfile` — Multi-stage build for the Flask API; health check included
- `setup.py` — Python package config for `python -m build` wheel generation
- `requirements.txt` — Pinned dependencies for reproducible builds
- `.github/pull_request_template.md` — PR checklist including BR-rule impact section
- `.github/CODEOWNERS` — Auto-assign reviewers per directory
- `PROTECTION.md` — Branch protection rules documentation with developer workflow guide

### Updated — README.md
- CI badge, test count badge, Python version badge
- "Running Tests Locally" section with per-assignment commands
- "CI/CD Pipeline" section with ASCII flow diagram
- Full API endpoint table
- Business rules table with enforcement locations

### GitHub Issues — Assignment 13
| Issue | Title | Status |
|---|---|---|
| #31 | Create ci.yml with lint + test + artifact jobs | ✅ Done |
| #32 | Create pr-check.yml as required status check | ✅ Done |
| #33 | Add Dockerfile for containerised deployment | ✅ Done |
| #34 | Add setup.py for Python wheel packaging | ✅ Done |
| #35 | Write PROTECTION.md branch rules documentation | ✅ Done |
| #36 | Add PR template with business rules checklist | ✅ Done |
| #37 | Update README with CI/CD docs and badges | ✅ Done |

---

## [5.0.0] — Assignment 14: Open-Source Collaboration

### Added — Repository Preparation
- `CONTRIBUTING.md` — Full contributor guide: prerequisites, local setup, coding standards, branching strategy, PR checklist, first-timer tips
- `ROADMAP.md` — 15 planned features across 6 phases; 8 tagged as open for contribution
- `LICENSE` — MIT License
- `ISSUES.md` — 8 labeled GitHub issues ready to create (5× `good-first-issue`, 3× `feature-request`)
- `VOTING_RESULTS.md` — Template for peer engagement tracking (stars/forks/feedback)
- `REFLECTION.md` — 900-word reflection on open-source collaboration experience

### Updated — README.md
- Added "Getting Started for Contributors" section with copy-paste setup commands
- Added "Features for Contribution" table linking to 8 labeled issues
- Added MIT license badge, contributor badge, forks badge, stars badge
- Added links to CONTRIBUTING.md, ROADMAP.md, LICENSE

### GitHub Issues to Create (Assignment 14)
| Issue | Title | Label |
|---|---|---|
| #42 | Add pagination to GET /api/applications | `good-first-issue` |
| #43 | Add Docker Compose for local development | `good-first-issue` |
| #44 | Add search/filter to GET /api/students | `good-first-issue` |
| #45 | Add rate limiting to the API | `good-first-issue` |
| #46 | Host Swagger UI on GitHub Pages | `good-first-issue` |
| #47 | Implement JWT authentication | `feature-request` |
| #48 | PostgreSQL database backend | `feature-request` |
| #49 | Application appeal workflow (BR-011) | `feature-request` |
