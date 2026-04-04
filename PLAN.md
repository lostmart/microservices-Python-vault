<!-- This file replaced the original curriculum plan. See plan.md for the curriculum. -->
# GameHub — Implementation Plan

Work through this one step at a time. Propose → approve → create. No bulk writes.

---

## Status Key
- ✅ Done
- 🔲 Not started
- ⚠️ Partial / needs review

---

## Design decisions

| Decision | Choice | Reason |
|---|---|---|
| Local DB (Modules 1–7) | SQLite via `aiosqlite` | Zero infra, same SQLAlchemy API as PostgreSQL |
| Docker DB (Module 8+) | PostgreSQL via `asyncpg` | Students swap `DATABASE_URL` only — no code change |
| Messaging | Kafka (events) + RabbitMQ (tasks) | Introduced via Docker infra only; services stay local until Module 8 |
| notification-service runtime | Node.js, local, no Docker | Demonstrates polyglot microservices without burning laptop resources |
| Observability instrumentation | Added in Module 9 as exercise | Not pre-scaffolded — students instrument live code |
| Alembic migrations | Introduced as Module 2 exercise step | Not pre-scaffolded — students run `alembic init` themselves |
| Auth | Dedicated `auth-service` in FastAPI | Students build token issuance + validation natively; no Keycloak, no new Docker container in M6 |
| Auth middleware | Added in Module 6 as exercise | `get_current_user` dependency injected into protected endpoints; role check via JWT claims |
| API Gateway | FastAPI app at `gateway/` (top-level, not inside `services/`) | Clearly distinct from domain services; students see it as infrastructure, not a domain; evolves across modules |
| Port map | 8000=gateway, 8001=user, 8002=game, 8003=activity, 8004=notification, 8005=auth, 8006=logging | Authoritative — applies locally and in Docker; no Traefik, gateway is the single ingress throughout |

---

## Phase 0 — gateway (`gateway/`)

Top-level folder, clearly separate from `services/`. FastAPI app. No database, no domain logic.
Evolves incrementally across modules as an exercise — students add to `gateway/app/main.py` each time.

| # | File | Module introduced | Capability added | Status |
|---|---|---|---|---|
| 0.1 | `requirements.txt` | 3 | `fastapi`, `uvicorn`, `httpx` | 🔲 |
| 0.2 | `app/config.py` | 3 | Service URLs from env vars | 🔲 |
| 0.3 | `app/main.py` | 3 | Dumb router — path-based forwarding to all services; `GET /health` returns `{"status":"ok","service":"gateway"}` | 🔲 |
| 0.4 | `app/main.py` | 6 | JWT validation before forwarding | 🔲 |
| 0.5 | `app/main.py` | 7 | `/v1` vs `/v2` versioning routing | 🔲 |
| 0.6 | `app/main.py` | 10 | Circuit breaker on downstream calls; `GET /health` upgraded to aggregate per-service status | 🔲 |
| 0.7 | `Dockerfile` | 8 | Containerised with other services | 🔲 |
| 0.8 | `tests/test_gateway.py` | 3 | Basic routing smoke tests | 🔲 |

**Note:** Module 1 service map must show the gateway as a labeled box with the note "built in Module 3".

---

## Phase 1 — Infrastructure & Root Files

| # | File | Status |
|---|---|---|
| 1.1 | `.env.example` | ✅ |
| 1.2 | `docker-compose.infra.yml` | ✅ |
| 1.3 | `infra/postgres/init.sql` | ✅ |
| 1.4 | `infra/prometheus/prometheus.yml` | ✅ |
| 1.5 | `infra/grafana/provisioning/datasources/datasources.yml` | ✅ |
| 1.6 | `infra/grafana/provisioning/dashboards/dashboards.yml` | ✅ |
| 1.7 | `infra/loki/loki-config.yaml` | ✅ |
| 1.8 | `infra/promtail/promtail-config.yaml` | ✅ |
| 1.9 | ~~`infra/keycloak/realm-export.json`~~ | ❌ removed — replaced by native auth-service |
| 1.10 | `locustfile.py` | 🔲 |
| 1.11 | `README.md` | ✅ |

---

## Phase 2 — user-service (`services/user-service/`)

Runs locally with `uvicorn`. SQLite in Modules 1–7, PostgreSQL from Module 8.

Concepts: FastAPI project layout, SQLAlchemy async models, Pydantic v2 schemas, repository pattern, basic CRUD.

| # | File | Status |
|---|---|---|
| 2.1 | `requirements.txt` | 🔲 |
| 2.2 | `Dockerfile` | 🔲 |
| 2.3 | `app/config.py` | 🔲 |
| 2.4 | `app/models.py` | 🔲 |
| 2.5 | `app/schemas.py` | 🔲 |
| 2.6 | `app/service.py` | 🔲 |
| 2.7 | `app/database.py` | 🔲 |
| 2.8 | `app/main.py` | 🔲 |
| 2.9 | `tests/test_users.py` | 🔲 |

---

## Phase 3 — game-service (`services/game-service/`)

Runs locally with `uvicorn`. Adds Redis cache (Docker infra) from Module 5.

Concepts: caching strategies, cache invalidation, Redis read model, CQRS intro.

| # | File | Status |
|---|---|---|
| 3.1 | `requirements.txt` | 🔲 |
| 3.2 | `Dockerfile` | 🔲 |
| 3.3 | `app/config.py` | 🔲 |
| 3.4 | `app/models.py` | 🔲 |
| 3.5 | `app/schemas.py` | 🔲 |
| 3.6 | `app/service.py` | 🔲 |
| 3.7 | `app/database.py` | 🔲 |
| 3.8 | `app/cache.py` | 🔲 |
| 3.9 | `app/main.py` | 🔲 |
| 3.10 | `tests/test_games.py` | 🔲 |

---

## Phase 4 — activity-service (`services/activity-service/`)

Runs locally with `uvicorn`. Publishes Kafka events (Docker infra) from Module 4.
Queries `logging-service` for consent before publishing any event — consent check active from Module 5 when logging-service is introduced.

Concepts: event-driven design, Kafka producer, consent-gated event publishing, httpx inter-service call.

| # | File | Status |
|---|---|---|
| 4.1 | `requirements.txt` | 🔲 |
| 4.2 | `Dockerfile` | 🔲 |
| 4.3 | `app/config.py` | 🔲 |
| 4.4 | `app/models.py` | 🔲 |
| 4.5 | `app/schemas.py` | 🔲 |
| 4.6 | `app/service.py` | 🔲 |
| 4.7 | `app/database.py` | 🔲 |
| 4.8 | `app/events.py` | 🔲 |
| 4.9 | `app/main.py` | 🔲 |
| 4.10 | `tests/test_activity.py` | 🔲 |

---

## Phase 5 — notification-service (`services/notification-service/`)

**Node.js. Runs locally. No Docker — ever.**
Consumes RabbitMQ (Docker infra from Module 4). Stores notification records in SQLite.

Concepts: polyglot microservices, RabbitMQ consumer, dead-letter queues, lightweight local services.

| # | File | Status |
|---|---|---|
| 5.1 | `package.json` | 🔲 |
| 5.2 | `app.js` | 🔲 |
| 5.3 | `db.js` | 🔲 |
| 5.4 | `consumer.js` | 🔲 |
| 5.5 | `routes.js` | 🔲 |
| 5.6 | `tests/test_notifications.js` | 🔲 |

---

## Phase 6 — logging-service (`services/logging-service/`)

Runs locally with `uvicorn` on **port 8006**. Consumes Kafka events published by activity-service from Module 4 onward.
GDPR-compliant: stores consent status in SQLite/PostgreSQL; writes audit events to a JSONL log file.

Concepts: GDPR Art. 7/13/17/25, consent management, audit logging, feature gating, right to erasure, data minimisation (IP hashing).

**Feature gating rule:** `activity-service` and `game-service` call `GET /consent/{user_id}` before publishing events or serving personalised content. No consent = no activity feed, no recommendations.

| # | File | Notes | Status |
|---|---|---|---|
| 6.1 | `requirements.txt` | | 🔲 |
| 6.2 | `Dockerfile` | | 🔲 |
| 6.3 | `app/config.py` | Includes `AUDIT_LOG_PATH` env var | 🔲 |
| 6.4 | `app/models.py` | `ConsentRecord` model | 🔲 |
| 6.5 | `app/schemas.py` | Consent request/response + log entry shape | 🔲 |
| 6.6 | `app/service.py` | Consent CRUD + erasure logic | 🔲 |
| 6.7 | `app/database.py` | | 🔲 |
| 6.8 | `app/consumer.py` | Kafka consumer → consent check → JSONL write | 🔲 |
| 6.9 | `app/main.py` | `POST /v1/consent/{user_id}`, `GET /v1/consent/{user_id}`, `DELETE /v1/consent/{user_id}`, `DELETE /v1/logs/{user_id}` | 🔲 |
| 6.10 | `tests/test_logging.py` | | 🔲 |

**Audit log format** (JSONL, one entry per line, append-only):
```json
{"timestamp": "2025-03-01T10:00:00Z", "user_id": "abc123", "action": "game.viewed", "service": "game-service", "ip_hash": "e3b0c44..."}
```

---

## Phase 6-B — auth-service (`services/auth-service/`)

Runs locally with `uvicorn` from Module 6. Containerised in Module 8 alongside all other Python services.

Concepts: OAuth2 password flow, JWT issuance, `get_current_user` FastAPI dependency, role-based access control via claims, M2M client credentials (hardcoded).

| # | File | Notes | Status |
|---|---|---|---|
| 6B.1 | `requirements.txt` | `python-jose`, `passlib[bcrypt]`, `fastapi`, `uvicorn` | 🔲 |
| 6B.2 | `app/config.py` | `SECRET_KEY`, `ALGORITHM`, `ACCESS_TOKEN_EXPIRE_MINUTES` | 🔲 |
| 6B.3 | `app/users.py` | Hardcoded user store (dict or SQLite); `gamer` and `admin` roles | 🔲 |
| 6B.4 | `app/security.py` | `create_access_token`, `get_current_user` dependency | 🔲 |
| 6B.5 | `app/main.py` | `POST /v1/auth/token` (password flow) + `GET /v1/auth/me` (test endpoint) | 🔲 |
| 6B.6 | `Dockerfile` | Containerised in Module 8 alongside other services; port 8005 | 🔲 |

---

## Phase 7 — Module exercise files

All exercise files are rewritten for GameHub. Docker override files are introduced per module as infra expands.

| # | File | Docker introduced | Status |
|---|---|---|---|
| 7.1 | `modules/module-01/exercise.md` | None | ✅ |
| 7.2 | `modules/module-02/exercise.md` | None | ✅ |
| 7.3 | `modules/module-03/exercise.md` | None | ✅ |
| 7.4 | `modules/module-04/exercise.md` | Kafka + RabbitMQ | ✅ |
| 7.5 | `modules/module-04/docker-compose.override.yml` | | ✅ |
| 7.6 | `modules/module-05/exercise.md` | Redis | ✅ |
| 7.7 | `modules/module-05/docker-compose.override.yml` | | ✅ |
| 7.8 | `modules/module-06/exercise.md` | No new Docker — auth-service (native FastAPI JWT) | ✅ |
| 7.9 | `modules/module-06/docker-compose.override.yml` | Comment only — no containers added | ✅ |
| 7.10 | `modules/module-07/exercise.md` | URL versioning + contract tests (no SDK gen) | ✅ |
| 7.11 | `modules/module-08/exercise.md` | All services containerised | ✅ |
| 7.12 | `modules/module-08/docker-compose.override.yml` | | ✅ |
| 7.13 | `modules/module-09/exercise.md` | Observability stack | ✅ |
| 7.14 | `modules/module-09/docker-compose.override.yml` | | ✅ |
| 7.15 | `modules/module-10/exercise.md` | Full stack | ✅ |
| 7.16 | `modules/module-10/docker-compose.override.yml` | | ✅ |

---

## Phase 8 — Helm & CI/CD

| # | File | Status |
|---|---|---|
| 8.1 | `helm/user-service/Chart.yaml` | ✅ |
| 8.2 | `helm/user-service/values.yaml` | ✅ |
| 8.3 | `helm/user-service/templates/_helpers.tpl` | ✅ |
| 8.4 | `helm/user-service/templates/deployment.yaml` | ✅ |
| 8.5 | `helm/user-service/templates/service.yaml` | ✅ |
| 8.6 | `helm/user-service/templates/configmap.yaml` | ✅ |
| 8.7 | `helm/user-service/templates/ingress.yaml` | ✅ |
| 8.8 | `.github/workflows/user-service.yml` | ✅ |
| 8.9 | `.github/workflows/game-service.yml` | ✅ |

---

## Progress Summary

| Phase | Description | Done |
|---|---|---|
| 0 | gateway (FastAPI, top-level) | 0/8 |
| 1 | Infrastructure | 9/11 (keycloak removed, locustfile pending) |
| 2 | user-service | 0/9 |
| 3 | game-service | 0/10 |
| 4 | activity-service | 0/10 |
| 5 | notification-service (Node.js) | 0/6 |
| 6 | logging-service (GDPR) | 0/10 |
| 6-B | auth-service (native JWT) | 0/6 |
| 7 | Module exercise files | 16/16 ✅ (all docker-compose overrides present) |
| 8 | Helm + CI/CD | 9/9 |

**Next: Step 1.10 — `locustfile.py`**

---

## Full module cadence reference

| Module | Services in scope | Docker containers active | Key deliverable |
|---|---|---|---|
| 01 | None — paper design | None | GameHub service map + bounded contexts; **gateway shown as a labeled box** |
| 02 | user-service, game-service | None | CRUD APIs, SQLite, Pydantic schemas |
| 03 | activity-service + **gateway (dumb router)** | None | REST inter-service call (activity → user); gateway forwards all paths to correct service |
| 04 | notification-service, activity-service | Kafka, RabbitMQ | Events published; notifications consumed |
| 05 | logging-service, game-service | + Redis | GDPR consent flow; Redis cache on game-service |
| 06 | All + auth-service + **gateway (JWT validation)** | No new containers | All endpoints protected via native JWT; gateway validates token before forwarding |
| 07 | All + **gateway (versioning routing)** | No new containers | Versioned OpenAPI (`/v1`, `/v2` coexist); gateway routes by version prefix |
| 08 | All + **gateway (Dockerised)** | All services + full infra | Dockerfiles; SQLite → PostgreSQL; Helm/minikube as instructor demo |
| 09 | All | + Prometheus, Grafana, Jaeger, Loki | Distributed trace across 3 services in Jaeger |
| 10 | All + **gateway (circuit breaker)** | Full stack | Circuit breaker on gateway downstream calls; GitHub Actions pipeline live |
