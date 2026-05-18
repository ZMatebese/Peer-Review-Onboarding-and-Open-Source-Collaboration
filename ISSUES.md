# GitHub Issues — RPL System
## Assignment 14: Issue Labels for Open-Source Collaboration

This file documents all issues to be created on GitHub with their labels.
Copy each issue title and body into GitHub → Issues → New Issue.

---

## 🟢 good-first-issue Labels (5 issues)

---

### Issue #42 — Add pagination to GET /api/applications

**Labels:** `good-first-issue`, `enhancement`, `api`

**Description:**
The `GET /api/applications` endpoint currently returns all records with no limit. As the system grows, this will become slow and unusable.

**Task:**
Add `page` and `page_size` query parameters to the applications list endpoint.

**Expected behaviour:**
```
GET /api/applications?page=1&page_size=10
```
Response should include a `pagination` object:
```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "page_size": 10,
    "total": 45,
    "total_pages": 5
  }
}
```

**Acceptance criteria:**
- [ ] `page` defaults to 1 if not provided
- [ ] `page_size` defaults to 20, max 100
- [ ] Returns 400 if `page` or `page_size` is negative
- [ ] At least 2 unit tests and 1 integration test added

**Files to change:** `api/app.py`, `tests/api/test_api.py`

**Difficulty:** Low (~50 lines of code)

---

### Issue #43 — Add Docker Compose for local development

**Labels:** `good-first-issue`, `devops`, `documentation`

**Description:**
Currently contributors must run the Flask API manually. A `docker-compose.yml` would let new contributors get the full stack running in one command.

**Task:**
Create a `docker-compose.yml` that starts:
- The Flask API (`api` service)
- A PostgreSQL database (`postgres` service)
- A Redis cache (`redis` service)

**Expected usage:**
```bash
docker-compose up
# API available at http://localhost:5000
```

**Acceptance criteria:**
- [ ] `docker-compose up` starts all services without manual config
- [ ] API service uses environment variables from a `.env.example` file
- [ ] `.env.example` file created (no real secrets)
- [ ] README updated with docker-compose instructions

**Files to change:** `docker-compose.yml` (new), `.env.example` (new), `README.md`

**Difficulty:** Low-Medium (~30 lines YAML + docs)

---

### Issue #44 — Add search/filter to GET /api/students

**Labels:** `good-first-issue`, `enhancement`, `api`

**Description:**
The students list endpoint has no filtering. Admins need to search for students by name or filter by status.

**Task:**
Add optional query parameters to `GET /api/students`:
- `?name=thabo` — case-insensitive partial match on `full_name`
- `?status=Active` — filter by student status

**Acceptance criteria:**
- [ ] `?name=` returns students whose name contains the search term
- [ ] `?status=` returns only students with that status (validates against valid values)
- [ ] No parameters returns all students (existing behaviour unchanged)
- [ ] Unit tests added to `StudentService`
- [ ] Integration tests added to `test_api.py`

**Files to change:** `api/app.py`, `services/student_service.py`, `tests/`

**Difficulty:** Low (~40 lines of code)

---

### Issue #45 — Add rate limiting to the API

**Labels:** `good-first-issue`, `security`, `api`

**Description:**
The API currently has no rate limiting. A bad actor could spam the `/api/applications` endpoint and degrade service for all students.

**Task:**
Add rate limiting using `flask-limiter`:
- 100 requests/minute per IP for all endpoints
- Return `429 Too Many Requests` with a `Retry-After` header when exceeded
- Add `flask-limiter` to `requirements.txt`

**Acceptance criteria:**
- [ ] Rate limit header `X-RateLimit-Remaining` included in every response
- [ ] `429` returned after limit exceeded
- [ ] Tests verify the 429 response
- [ ] `requirements.txt` updated

**Files to change:** `api/app.py`, `requirements.txt`, `tests/api/test_api.py`

**Difficulty:** Low (~20 lines + config)

---

### Issue #46 — Host Swagger UI on GitHub Pages

**Labels:** `good-first-issue`, `documentation`, `devops`

**Description:**
The OpenAPI spec is in `docs/openapi.yaml` but there's no hosted version. Contributors and stakeholders should be able to browse the API docs without cloning the repo.

**Task:**
Add a GitHub Actions step that:
1. Converts `docs/openapi.yaml` to a static Swagger UI HTML page
2. Deploys it to GitHub Pages on every merge to `main`

**Acceptance criteria:**
- [ ] `https://your-username.github.io/rpl-system/` hosts the Swagger UI
- [ ] Auto-updates on every merge to `main`
- [ ] README updated with the link

**Files to change:** `.github/workflows/ci.yml`, `docs/` (new `index.html`)

**Difficulty:** Low (~15 lines YAML)

---

## 🔵 feature-request Labels (3 issues)

---

### Issue #47 — Implement JWT authentication for the API

**Labels:** `feature-request`, `security`, `api`

**Description:**
The API currently has no authentication — any user can call any endpoint. We need JWT-based auth with role-based access control (Student, Assessor, Admin).

**Task:**
Implement JWT authentication:
- `POST /api/auth/login` — returns a signed JWT token
- Middleware validates `Authorization: Bearer <token>` on all protected endpoints
- Role-based access: Students can only access their own data
- Token expiry: 24 hours; refresh token support

**Acceptance criteria:**
- [ ] Login endpoint returns `access_token` and `refresh_token`
- [ ] Protected endpoints return `401` without a valid token
- [ ] Students cannot access other students' applications
- [ ] Admin can access everything
- [ ] Tests cover auth success, expired token, and forbidden access

**Technical notes:**
- Use `PyJWT` library
- Store user credentials in the existing `users` structure
- Add `flask-jwt-extended` or pure `PyJWT` — maintainer preference

**Difficulty:** High — discuss approach in comments before starting

---

### Issue #48 — PostgreSQL database backend

**Labels:** `feature-request`, `database`, `backend`

**Description:**
The current in-memory repositories reset every time the server restarts. We need real persistence via PostgreSQL.

**Task:**
Implement the `DatabaseStudentRepository`, `DatabaseRPLApplicationRepository`, and related classes (stubs already exist in `/repositories/database_stub/`).

**Requirements:**
- Use SQLAlchemy ORM
- Add Alembic for schema migrations
- All 8 entity repositories implemented
- `RepositoryFactory.get_*_repository("database", connection_string=...)` must work
- Docker Compose updated with a `postgres` service
- All existing 275 tests must still pass (using the in-memory backend)
- New integration tests using the database backend

**Acceptance criteria:**
- [ ] `python api/app.py --storage=database` starts with PostgreSQL
- [ ] Data persists across server restarts
- [ ] All 275 existing tests still pass
- [ ] New tests cover DB-specific behaviour (e.g. constraint violations)

**Difficulty:** High — coordinate with maintainer before starting

---

### Issue #49 — Application appeal workflow

**Labels:** `feature-request`, `business-logic`, `api`

**Description:**
Students whose applications are `Rejected` currently have no recourse. The RPL process requires an appeal pathway.

**Task:**
Implement an appeal workflow:
- Add `Appealed` status to `RPLApplication`
- `POST /api/applications/{id}/appeal` — student submits appeal with new justification
- Appeal must be reviewed by a **different** assessor from the original reviewer
- Maximum 1 appeal per application
- New business rule: `BR-011 — Only Rejected applications can be appealed`

**Acceptance criteria:**
- [ ] `POST /api/applications/{id}/appeal` creates appeal with new justification
- [ ] Returns `400` if application is not in `Rejected` status (BR-011)
- [ ] Returns `400` if application has already been appealed
- [ ] Appeal is assigned to a different assessor
- [ ] Service layer unit tests: 3+ tests for appeal logic
- [ ] API integration tests: 2+ tests
- [ ] `BR-011` documented in `PROTECTION.md` business rules table

**Difficulty:** Medium — good for contributors familiar with the service layer

---

## Issue Label Setup Instructions

To create these labels on GitHub:

1. Go to your repository → **Issues** → **Labels**
2. Click **New label** and create:

| Label name | Color | Description |
|---|---|---|
| `good-first-issue` | `#7057ff` | Good for newcomers |
| `feature-request` | `#0075ca` | New feature or request |
| `bug` | `#d73a4a` | Something isn't working |
| `documentation` | `#0075ca` | Improvements or additions to docs |
| `enhancement` | `#a2eeef` | New feature or request |
| `api` | `#e4e669` | REST API related |
| `security` | `#e11d48` | Security improvements |
| `database` | `#f9d71c` | Database/persistence related |
| `devops` | `#c2e0c6` | CI/CD and deployment |
| `business-logic` | `#fbca04` | Core RPL business rules |

3. Then create each issue above and apply the labels listed.
