# RPL System — Project Roadmap

This roadmap outlines planned features, improvements, and long-term goals for the RPL System. Items are grouped by priority and complexity. Community contributions are welcome on any item marked 🟢 **open for contribution**.

> **Last updated:** May 2026 | **Current version:** 1.0.0

---

## Legend

| Symbol | Meaning |
|--------|---------|
| ✅ | Completed |
| 🔄 | In progress |
| 📋 | Planned — not yet started |
| 🟢 | Open for community contribution |
| 🔒 | Requires maintainer — complex/sensitive |

---

## ✅ Completed (Assignments 9–13)

| Feature | Assignment |
|---------|-----------|
| Domain model — 10 entities, 10 business rules | 9 |
| All 6 creational design patterns | 10 |
| Repository layer — in-memory, filesystem, DB stubs | 11 |
| Service layer — StudentService, RPLApplicationService, AssessorService, AcademicModuleService | 12 |
| Flask REST API — 30+ endpoints with Swagger UI | 12 |
| OpenAPI YAML documentation | 12 |
| CI/CD pipeline — lint, test (×3 Python versions), artifact, Docker | 13 |
| Branch protection, CODEOWNERS, PR template | 13 |

---

## 🔄 Phase 2 — Persistence & Data Layer (Near-term)

### 2.1 PostgreSQL Database Backend 🟢
**Complexity:** Medium | **Label:** `feature-request`

Replace the in-memory repository with a real PostgreSQL backend using SQLAlchemy ORM.

- Implement `DatabaseStudentRepository`, `DatabaseRPLApplicationRepository`, etc. (stubs already exist in `/repositories/database_stub/`)
- Add Alembic for database migrations
- Update `RepositoryFactory` to support `"database"` storage type fully
- Add `DATABASE_URL` environment variable support

```python
# Target usage
container = RPLServiceContainer(
    storage_type="database",
    connection_string="postgresql://user:pass@localhost/rpl_db"
)
```

---

### 2.2 Redis Caching Layer 🟢
**Complexity:** Medium | **Label:** `feature-request`

Cache frequently-read data (module lists, student profiles) in Redis to reduce database load.

- Cache `AcademicModule.find_all()` results with a 10-minute TTL
- Cache `Student.find_by_id()` with invalidation on update
- Use `redis-py` library
- Add `REDIS_URL` environment variable

---

### 2.3 File Upload Storage — AWS S3 / MinIO 🟢
**Complexity:** Medium | **Label:** `feature-request`

Currently evidence documents are tracked by name only — no actual file bytes are stored.

- Integrate `boto3` for S3-compatible storage
- Upload evidence files and store the S3 URL in `EvidenceDocument.file_url`
- Support local MinIO for development (Docker Compose config)
- Add file size and MIME type validation (max 10 MB, PDF/DOCX/JPG only)

---

## 📋 Phase 3 — API & UX Improvements

### 3.1 Pagination on List Endpoints 🟢
**Complexity:** Low | **Label:** `good-first-issue`

All list endpoints currently return every record. Add standard pagination:

```json
GET /api/applications?page=1&page_size=20

{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "page_size": 20,
    "total": 142,
    "total_pages": 8
  }
}
```

---

### 3.2 Search and Filtering 🟢
**Complexity:** Low-Medium | **Label:** `good-first-issue`

Add query parameter search to key endpoints:

- `GET /api/students?name=thabo` — partial name search
- `GET /api/applications?from_date=2025-01-01&to_date=2025-06-30`
- `GET /api/modules?nqf_level=6&faculty=Engineering`

---

### 3.3 JWT Authentication 🔒
**Complexity:** High | **Label:** `feature-request`

The current API has no authentication. Add JWT-based auth:

- `POST /api/auth/login` returns a signed JWT
- All endpoints require `Authorization: Bearer <token>` header
- Role-based access control (Admin, Assessor, Student can only access their own data)
- Token expiry + refresh token flow
- Use `PyJWT` library

---

### 3.4 Email Notifications via SendGrid 🟢
**Complexity:** Medium | **Label:** `feature-request`

Currently `Notification` objects are stored in memory only. Send real emails:

- Trigger email on application status change
- Use SendGrid API (`sendgrid` Python library)
- Add `SENDGRID_API_KEY` environment variable
- Queue emails using Celery + Redis to avoid blocking API responses

---

### 3.5 Rate Limiting 🟢
**Complexity:** Low | **Label:** `good-first-issue`

Add API rate limiting to prevent abuse:

- 100 requests/minute per IP for unauthenticated endpoints
- 500 requests/minute for authenticated users
- Use `flask-limiter` library
- Return `429 Too Many Requests` with `Retry-After` header

---

## 📋 Phase 4 — Assessment Workflow Enhancements

### 4.1 Assessment Panel Voting 🔒
**Complexity:** High

Implement multi-assessor panel voting for borderline applications:

- `POST /api/panels` — create an assessment panel
- `POST /api/panels/{id}/members` — add assessors (BR-005: min 3)
- `POST /api/panels/{id}/vote` — cast a vote (Approve/Reject)
- Auto-decide on majority vote; require quorum

---

### 4.2 Application Appeal Workflow 🟢
**Complexity:** Medium | **Label:** `feature-request`

Students should be able to appeal a `Rejected` decision:

- Add `Appealed` status to `RPLApplication`
- `POST /api/applications/{id}/appeal` — submit appeal with new justification
- Appeal reviewed by a different assessor (not the original)
- Max 1 appeal per application

---

### 4.3 Credit Transfer Report PDF Generation 🟢
**Complexity:** Medium | **Label:** `feature-request`

Auto-generate a PDF credit transfer certificate for `Approved` applications:

- Use `reportlab` or `weasyprint` library
- Include student name, modules approved, credits, NQF levels, date, assessor signature
- `GET /api/applications/{id}/certificate` — returns PDF

---

## 📋 Phase 5 — DevOps & Deployment

### 5.1 Kubernetes Deployment Config 🟢
**Complexity:** Medium | **Label:** `feature-request`

Add Kubernetes manifests for production deployment:

- `k8s/deployment.yaml` — API deployment with 2 replicas
- `k8s/service.yaml` — ClusterIP + LoadBalancer
- `k8s/configmap.yaml` — environment variables
- `k8s/ingress.yaml` — NGINX ingress with TLS

---

### 5.2 Docker Compose for Local Development 🟢
**Complexity:** Low | **Label:** `good-first-issue`

Add a `docker-compose.yml` that spins up the full stack locally:

```yaml
services:
  api:       # Flask app
  postgres:  # Database
  redis:     # Cache
  minio:     # S3-compatible file storage
```

---

### 5.3 Monitoring with Prometheus + Grafana 📋
**Complexity:** High

Add observability:

- Expose `/metrics` endpoint via `prometheus-flask-exporter`
- Track: request count, response time, error rate, active applications
- Grafana dashboard pre-configured in `monitoring/dashboard.json`

---

### 5.4 GitHub Pages API Documentation 🟢
**Complexity:** Low | **Label:** `good-first-issue`

Host the Swagger/OpenAPI documentation as a static GitHub Pages site:

- Convert `docs/openapi.yaml` to a hosted Swagger UI page
- Auto-deploy via GitHub Actions on every merge to `main`
- URL: `https://your-username.github.io/rpl-system/`

---

## 📋 Phase 6 — Testing & Quality

### 6.1 Property-Based Testing with Hypothesis 🟢
**Complexity:** Medium | **Label:** `good-first-issue`

Add property-based tests for business rule validation:

```python
from hypothesis import given, strategies as st

@given(st.integers(min_value=0, max_value=20))
def test_br007_nqf_eligibility_property(nqf_level):
    # Should never raise an exception for valid NQF values
    ...
```

---

### 6.2 Load Testing with Locust 🟢
**Complexity:** Medium | **Label:** `feature-request`

Add a Locust load test script to validate the API can handle 1000 concurrent users (NFR from Assignment 4):

- `locustfile.py` simulating Student, Assessor, Admin workflows
- Target: p95 response time < 500ms at 1000 concurrent users
- Add load test results to CI artifacts

---

## Community Wishlist

These are features requested by users and classmates. Vote with 👍 on the linked issue:

| Feature | Votes | Issue |
|---------|-------|-------|
| Mobile-responsive React frontend | 👍👍👍👍👍 | #38 |
| CSV export of all applications | 👍👍👍 | #39 |
| Slack/Teams notifications integration | 👍👍 | #40 |
| Dark mode for Swagger UI | 👍 | #41 |

---

## How to Propose a New Feature

1. Check if a similar issue already exists
2. Open a new issue with the `feature-request` label
3. Describe: **what** the feature does, **why** it's valuable, and **how** it fits the RPL domain
4. Maintainers will add it to the roadmap if accepted

---

*This roadmap is a living document. Priorities may shift based on community feedback and academic requirements.*
