# GameHub — High-Level Overview

## What it is

A 10-module course for EPITA students building a real microservices system from scratch.
The domain is a gamer social platform. Every architectural decision is a teaching moment.
Students write every line — no scaffolding, no starter code.

---

## Architecture

```
                    ┌─────────────────────┐
    HTTP ──────────▶│  gateway (FastAPI)  │  port 8000
                    │  JWT · versioning   │  built in M3, grows to M10
                    │  circuit breakers   │
                    └──────────┬──────────┘
                               │
          ┌────────────────────┼──────────────────────┐
          ▼                    ▼                       ▼
   ┌────────────┐     ┌────────────┐     ┌────────────────┐
   │user-service│     │game-service│     │activity-service│
   │  :8001     │     │  :8002     │     │  :8003         │
   └────────────┘     │ + Redis    │     └───────┬────────┘
                      └────────────┘        RabbitMQ │ Kafka
                                        ┌───────────┴──────────┐
                                        ▼                       ▼
                          ┌──────────────────┐   ┌──────────────────┐
                          │notification      │   │logging-service   │
                          │:8004 Node.js     │   │:8006             │
                          │RabbitMQ consumer │   │Kafka consumer    │
                          │always local      │   │GDPR consent      │
                          └──────────────────┘   └──────────────────┘

   auth-service :8005 — JWT issuance (shared SECRET_KEY with gateway)
```

---

## Port Map

| Port | Service | Runtime |
|---|---|---|
| 8000 | gateway | FastAPI / uvicorn |
| 8001 | user-service | FastAPI / uvicorn |
| 8002 | game-service | FastAPI / uvicorn |
| 8003 | activity-service | FastAPI / uvicorn |
| 8004 | notification-service | Node.js (never containerized) |
| 8005 | auth-service | FastAPI / uvicorn |
| 8006 | logging-service | FastAPI / uvicorn |

---

## Tech Stack

| Concern | Choice | Why |
|---|---|---|
| Service framework | FastAPI + uvicorn | Async, OpenAPI auto-docs, familiar to students |
| Local DB (M1–7) | SQLite via `aiosqlite` | Zero infra, same SQLAlchemy API as PostgreSQL |
| Production DB (M8+) | PostgreSQL via `asyncpg` | Swap `DATABASE_URL` only — no code change |
| ORM | SQLAlchemy async | Repository pattern, works with both DBs |
| Schema validation | Pydantic v2 | Native FastAPI integration |
| Migrations | Alembic | Students run `alembic init` themselves in M2 |
| Caching | Redis | Read model / CQRS in game-service (M5) |
| Async messaging (tasks) | RabbitMQ | activity → notification (fire and forget) |
| Async messaging (events) | Kafka | activity → logging (append-only, replayable) |
| Auth | python-jose + passlib | JWT issuance and validation built by students in M6 |
| Polyglot service | Node.js | notification-service — local, SQLite, never Docker |
| Observability | Prometheus + Grafana + Jaeger + Loki | Introduced in M9 as exercise |
| Containerisation | Docker Compose | Infra from M4, services from M8 |
| Orchestration demo | Helm + minikube | Instructor demo in M8 |
| CI/CD | GitHub Actions | M10 flex session |
| Load testing | Locust | Flex session |

---

## Module Progression

| Module | New this module | Key concept |
|---|---|---|
| 01 | — (paper design) | Bounded contexts, service map, Conway's Law |
| 02 | user-service, game-service | FastAPI layout, SQLAlchemy, Pydantic, CRUD |
| 03 | activity-service, **gateway** | Inter-service HTTP calls, dumb router pattern |
| 04 | notification-service + Kafka + RabbitMQ | Async messaging, event vs task queue |
| 05 | logging-service + Redis | GDPR consent, CQRS, caching, event sourcing |
| 06 | auth-service + JWT in gateway | OAuth2 flow, RBAC, M2M credentials |
| 07 | versioning routing in gateway | URL versioning, contract tests, breaking changes |
| 08 | Docker + PostgreSQL + gateway Dockerfile | Containerisation, DB migration, Helm demo |
| 09 | Observability stack | Distributed tracing, RED metrics, log correlation |
| 10 | Circuit breakers (service + gateway) | Resilience patterns, CI/CD pipeline |

---

## Gateway Evolution (the main pedagogical thread)

The gateway is the spine of the course. It starts simple and grows with students' knowledge:

| Module | What students add |
|---|---|
| 03 | Dumb router — single catch-all route, path-based forwarding |
| 04 | Register notification-service in ROUTES |
| 05 | Register logging-service (consent + logs resources) |
| 06 | JWT validation before forwarding; register auth-service |
| 07 | Version enforcement per resource (`SUPPORTED_VERSIONS`) |
| 08 | Dockerfile — containerised alongside all other services |
| 10 | Per-service circuit breakers |

**Key insight students take away:** downstream services never change — only the gateway grows.

---

## Key Design Decisions

**Local-first progression** — services run with `uvicorn` + SQLite for M1–7. Docker only for infrastructure (Kafka, RabbitMQ) starting M4. Services containerised in M8. Students understand what they're containerising before they containerise it.

**Custom FastAPI gateway over Traefik/Nginx** — every line is readable Python. No config file format to learn. The gateway is a teaching artifact, not a black box.

**Native auth over Keycloak** — students see exactly what a JWT contains and how RBAC works. No admin UI, no realm config, no hidden magic.

**Two messaging brokers** — RabbitMQ for tasks (deleted after ACK), Kafka for events (permanent, replayable). Students justify the choice for each use case.

**GDPR consent flow** — logging-service only records activity for users who consented. Forces students to think about data minimisation, right to erasure, and the tension between observability and privacy.

**Polyglot service** — notification-service is Node.js + SQLite, always local, never containerised. One deliberate outlier to demonstrate that microservices have no language constraint.

---

## Folder Structure

```
MicroservicesWithPython/
├── gateway/                  ← FastAPI API gateway (built M3, grows to M10)
├── services/
│   ├── user-service/
│   ├── game-service/
│   ├── activity-service/
│   ├── notification-service/ ← Node.js
│   ├── logging-service/
│   └── auth-service/
├── infra/                    ← Prometheus, Grafana, Loki, Promtail, PostgreSQL config
├── helm/                     ← Helm chart for user-service (instructor demo)
├── modules/
│   ├── module-01/ … module-10/
│   │   ├── exercise.md
│   │   └── docker-compose.override.yml  (M4 onward)
├── studentsRepo/             ← Student-facing repo (README + modules)
├── docker-compose.infra.yml  ← Kafka, RabbitMQ, Redis, PostgreSQL, observability
├── PLAN.md                   ← Implementation plan and status tracker
├── Rationale.md              ← Why each architectural decision was made
└── high-overview.md          ← This file
```
