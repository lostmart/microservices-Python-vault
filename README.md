# GameHub — Instructor Vault

This is an **Obsidian vault and instructor workspace** for the EPITA *Practical Microservices with Python* course. It is **not** the student repo — it is the source of truth from which course materials, starter scaffolds, and reference solutions are maintained.

---

## Domain — GameHub

**GameHub** is a social platform for gamers. Users share what they are currently playing, follow friends' activity, discover new games based on community trends, and receive personalised recommendations driven by their play history. A privacy-first design is built into the platform from day one: users must explicitly opt in before any activity is recorded, in compliance with GDPR.

This domain was chosen because it maps cleanly onto microservices boundaries that students can reason about intuitively (a user is not the same thing as a game, an activity event is not the same thing as a notification), while staying far enough from the overused e-commerce example to keep discussion fresh.

---

## What lives here

```
MicroservicesWithPython/              ← this vault (instructor-only)
│
├── README.md                         ← you are here
├── PLAN.md                           ← implementation tracker (phases + file-level checklist)
├── CLAUDE.md                         ← working rules for Claude Code in this repo
├── Rationale.md                      ← teaching notes: analogies, "why" before "how"
│
├── docker-compose.infra.yml          ← master infra Compose file (introduced incrementally)
├── .env.example                      ← environment variable template
├── locustfile.py                     ← load testing scenarios (Locust)
│
├── infra/                            ← configuration files for all infra services
│   ├── postgres/init.sql
│   ├── prometheus/prometheus.yml
│   ├── grafana/provisioning/
│   ├── loki/loki-config.yaml
│   └── promtail/promtail-config.yaml
│
├── modules/                          ← one folder per course module
│   ├── module-01/exercise.md
│   ├── module-02/ (exercise.md)
│   ├── ...
│   └── module-10/
│
├── helm/                             ← Helm chart for Kubernetes (Module 9)
│   └── user-service/
│
├── .github/workflows/                ← GitHub Actions CI/CD (Module 10)
│   ├── user-service.yml
│   └── game-service.yml
│
└── studentsRepo/                     ← embeddable student-facing repo (see below)
    ├── README.md                     ← student-facing welcome + instructions
    ├── docker-compose.infra.yml      ← copy of infra Compose file
    ├── .env.example
    ├── locustfile.py
    ├── .gitignore                    ← excludes solution/ and .env
    ├── docs/                         ← domain description + tech stack specs
    │   ├── domain.md
    │   └── specs.md
    ├── infra/                        ← copy of all infra configs
    ├── modules/                      ← copy of all exercise.md + override files
    ├── services/                     ← where students build their code
    │   └── .gitkeep
    └── solution/                     ← reference implementations (gitignored)
        ├── user-service/
        ├── game-service/
        ├── activity-service/
        ├── notification-service/     ← Node.js, runs locally (no Docker)
        └── logging-service/
```

---

## The student repo (`studentsRepo/`)

`studentsRepo/` is a self-contained directory designed to be extracted and shared with students as their starting point. It can be:

- Pushed to a separate GitHub repo (e.g., `epita-gamehub-starter`)
- Distributed as a zip archive
- Added as a git submodule

Students work inside `studentsRepo/services/`, guided by `studentsRepo/modules/module-XX/exercise.md`.

**The `solution/` folder** inside `studentsRepo/` contains reference implementations for all services. It is listed in `.gitignore` so it is never accidentally pushed when sharing the student repo. As the instructor, you can populate it here and use it for grading, live demos, or unblocking students.

---

## Key files for course management

| File | Purpose |
|---|---|
| `PLAN.md` | Phase-by-phase implementation checklist — track what has been created and what is still missing |
| `CLAUDE.md` | Rules for Claude Code in this repo — enforces step-by-step file creation, teaching standards, etc. |
| `Rationale.md` | Teaching notes with analogies and "why" explanations to use when introducing each concept |

---

## Technology stack

### Python services

Different services use different frameworks to demonstrate that microservices architectures are technology-agnostic. All services communicate via standard HTTP and message protocols.

| Service | Framework |
|---|---|
| user-service, game-service, activity-service, auth-service | **FastAPI** |
| logging-service | **Flask** |

#### FastAPI services (user-service, game-service, activity-service, auth-service)

| Tool | Role |
|---|---|
| FastAPI | Service framework (REST + async) |
| SQLAlchemy 2.x async | ORM — works with both SQLite (local dev) and PostgreSQL (Docker) |
| aiosqlite | Async SQLite driver for local development phases |
| asyncpg | Async PostgreSQL driver for Docker phases |
| Pydantic v2 | Validation and DTO schemas |
| uvicorn | ASGI server |
| httpx | Async HTTP client for inter-service REST calls |
| python-jose | JWT decode and validation middleware |
| aiokafka | Kafka producer/consumer (activity-service) |
| aio-pika | RabbitMQ client (notification-service bridge) |
| tenacity | Retry + exponential backoff |

#### logging-service (Flask)

| Tool | Role |
|---|---|
| Flask | Service framework (REST, WSGI) |
| SQLAlchemy | ORM — same models work with SQLite and PostgreSQL |
| Flask-Migrate | Database migrations (Alembic under the hood) |
| kafka-python | Kafka consumer (`activity.logged` events) |

### notification-service (Node.js — local only, no Docker)

| Tool | Role |
|---|---|
| Node.js + Express | Lightweight REST + consumer process |
| better-sqlite3 | Embedded SQLite, zero infra required |
| amqplib | RabbitMQ consumer |

### Databases

| Store | Used by | When introduced |
|---|---|---|
| SQLite | All Python services | Modules 1–7 (local dev) |
| PostgreSQL | All Python services | Module 8 (Docker phase) |
| Redis | game-service (cache) | Module 5 via Docker infra only |

### Infrastructure services (Docker)

| Tool | Role | Introduced |
|---|---|---|
| RabbitMQ | Background task queue, notification delivery | Module 4 |
| Kafka + Zookeeper | Real-time event streaming, audit log feed | Module 4 |
| Redis | Distributed cache + CQRS read model | Module 5 |
| Traefik | Reverse proxy + API gateway | Module 8 |
| Prometheus + Grafana | Metrics scraping and dashboards | Module 9 |
| Jaeger | Distributed trace UI | Module 9 |
| Loki + Promtail | Centralised log aggregation | Module 9 |

### Orchestration and CI/CD

| Tool | Role |
|---|---|
| Docker Compose | Service containerisation (from Module 8) |
| minikube / k3d | Local Kubernetes cluster |
| Helm | Kubernetes templating |
| GitHub Actions | Build → test → push image → deploy pipeline |

---

## Course structure at a glance

10 modules, each building on the previous. The first half runs entirely locally with no Docker required. Docker is introduced incrementally for infrastructure only, then services are containerised in Module 8.

| Module | Service(s) touched | Docker needed | Core concept |
|---|---|---|---|
| 01 | — (design on paper) | None | Bounded contexts, Conway's Law, CAP theorem |
| 02 | `user-service`, `game-service` | None | FastAPI + DDD, SQLAlchemy async, SQLite |
| 03 | `activity-service` | None | REST via httpx, retries, gRPC intro |
| 04 | `notification-service`, `activity-service` | Kafka + RabbitMQ infra only | Kafka vs RabbitMQ, dead-letter queues |
| 05 | `logging-service`, `game-service` | + Redis infra | CQRS, Event Sourcing, GDPR consent, polyglot persistence |
| 06 | All services | No new containers | OAuth 2.0, JWT auth-service, role-based access control |
| 07 | All services | No new containers | OpenAPI versioning, contract testing, SDK generation |
| 08 | All services | **All services containerised** | Dockerfiles, PostgreSQL swap, Helm, minikube |
| 09 | All services | + Observability stack | OpenTelemetry, Prometheus, Jaeger, Loki |
| 10 | All services | Full stack | Circuit breaker, cache warming, GitHub Actions CI/CD |

The capstone is **GameHub**: a running, observable, secured gamer social platform spanning 5 services, 2 databases, 2 message brokers, GDPR-compliant audit logging, and a full observability stack.

---

## Hardware-friendly design

A deliberate goal of this course is to avoid overwhelming student laptops. The approach:

1. **Modules 1–3**: zero Docker, zero daemons — just `pip install` and `uvicorn`
2. **Modules 4–7**: Docker is used only for infra (brokers, cache, auth) — never more than 4–5 containers at once, services still run as local processes
3. **Module 8+**: full Docker stack, but students already understand every service before containerising it

The `notification-service` (Node.js) is intentionally kept outside Docker for the entire course to demonstrate that microservices heterogeneity does not require every service to be containerised.

---

## GDPR and the logging-service

The `logging-service` is a first-class teaching vehicle for privacy-by-design in distributed systems. Key concepts covered:

- **Art. 7 — Consent**: users explicitly opt in before any activity is recorded
- **Art. 13 — Transparency**: the service exposes what data is collected and why
- **Art. 17 — Right to erasure**: `DELETE /logs/{user_id}` purges the audit trail on request
- **Art. 25 — Data minimisation**: IP addresses are hashed before storage; raw IPs are never persisted

Feature gating is enforced by the logging-service: `activity-service` and `game-service` query `/consent/{user_id}` before publishing events or serving personalised recommendations. Users who opt out lose the activity feed and recommendations but retain full access to the game catalogue and social profile.

Audit events are written as JSONL to a mounted log volume (not a database table), keeping the service lightweight and making log rotation and export straightforward.

---

## Working in this vault

This vault uses **Claude Code** as an implementation assistant. See `CLAUDE.md` for the rules in force. In short:

- Changes are made **one file at a time** with explicit approval between steps
- Every non-trivial file includes a teaching rationale comment or companion note
- `PLAN.md` is the source of truth for what has been done and what is next
- Never generate code in bulk — the course has specific pedagogical sequencing

---

## Infrastructure

Docker infrastructure is introduced incrementally. Students do not run the full stack until Module 8.

```bash
# Module 4 — brokers only
docker compose -f docker-compose.infra.yml up kafka zookeeper rabbitmq -d

# Module 5 — add Redis
docker compose -f docker-compose.infra.yml up redis -d

# Module 8 — full infra + containerised services
docker compose -f docker-compose.infra.yml up -d
```
