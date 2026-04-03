# MicroservicesWithPython — Instructor Vault

This is an **Obsidian vault and instructor workspace** for the EPITA *Practical Microservices with Python* course. It is **not** the student repo — it is the source of truth from which course materials, starter scaffolds, and reference solutions are maintained.

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
├── docker-compose.infra.yml          ← master copy of infra Compose file
├── .env.example                      ← environment variable template
├── locustfile.py                     ← load testing scenarios (Locust)
│
├── infra/                            ← configuration files for all infra services
│   ├── postgres/init.sql
│   ├── keycloak/realm-export.json
│   ├── prometheus/prometheus.yml
│   ├── grafana/provisioning/
│   ├── loki/loki-config.yaml
│   └── promtail/promtail-config.yaml
│
├── modules/                          ← one folder per course module
│   ├── module-01/exercise.md
│   ├── module-02/ (exercise.md + docker-compose.override.yml)
│   ├── ...
│   └── module-10/
│
├── helm/                             ← Helm chart for Kubernetes (Module 8)
│   └── user-service/
│
├── .github/workflows/                ← GitHub Actions CI/CD (Module 10)
│   ├── user-service.yml
│   └── product-service.yml
│
└── studentsRepo/                     ← embeddable student-facing repo (see below)
    ├── README.md                     ← student-facing welcome + instructions
    ├── docker-compose.infra.yml      ← copy of infra Compose file
    ├── .env.example
    ├── locustfile.py
    ├── .gitignore                    ← excludes solution/ and .env
    ├── infra/                        ← copy of all infra configs
    ├── modules/                      ← copy of all exercise.md + override files
    ├── services/                     ← where students build their code
    │   └── .gitkeep                  ← note telling students this is their workspace
    └── solution/                     ← reference implementations (gitignored)
        ├── user-service/
        ├── product-service/
        ├── order-service/
        ├── inventory-service/
        ├── notification-service-python/
        └── notification-service-node/
```

---

## The student repo (`studentsRepo/`)

`studentsRepo/` is a self-contained directory designed to be extracted and shared with students as their starting point. It can be:

- Pushed to a separate GitHub repo (e.g., `epita-shopmicro-starter`)
- Distributed as a zip archive
- Added as a git submodule

Students work inside `studentsRepo/services/`, guided by `studentsRepo/modules/module-XX/exercise.md`.

**The `solution/` folder** inside `studentsRepo/` contains reference implementations for all services (both the Python versions and the Node.js `notification-service`). It is listed in `.gitignore` so it is never accidentally pushed when sharing the student repo. As the instructor, you can populate it here and use it for grading, live demos, or unblocking students.

---

## Key files for course management

| File | Purpose |
|---|---|
| `PLAN.md` | Phase-by-phase implementation checklist — track what has been created and what is still missing |
| `CLAUDE.md` | Rules for Claude Code in this repo — enforces step-by-step file creation, teaching standards, etc. |
| `Rationale.md` | Teaching notes with analogies and "why" explanations to use when introducing each concept |

---

## Course structure at a glance

10 modules, each building on the previous. Students end with a running, observable, secured microservices system.

| Module | Service(s) added | Core concept |
|---|---|---|
| 01 | — (design on paper) | Bounded contexts, Conway's Law, CAP theorem |
| 02 | `user-service`, `product-service` | FastAPI + DDD layers, SQLAlchemy async |
| 03 | `order-service` | REST via httpx, retries, gRPC intro |
| 04 | `inventory-service`, `notification-service` | Kafka vs RabbitMQ, dead-letter queues |
| 05 | Refactor order-service | CQRS, Event Sourcing, polyglot persistence |
| 06 | All services updated | Keycloak, OAuth 2.0, JWT middleware |
| 07 | All services updated | OpenAPI versioning, SDK generation |
| 08 | All services | Helm, minikube, Traefik ingress |
| 09 | All services | OpenTelemetry, Prometheus, Jaeger, Loki |
| 10 | All services | Circuit breaker, cache warming, GitHub Actions |

The capstone is **ShopMicro**: a working e-commerce backend spanning 5 services, 3 databases, 2 message brokers, and a full observability stack.

---

## Working in this vault

This vault uses **Claude Code** as an implementation assistant. See `CLAUDE.md` for the rules in force. In short:

- Changes are made **one file at a time** with explicit approval between steps
- Every non-trivial file includes a teaching rationale comment or companion note
- `PLAN.md` is the source of truth for what has been done and what is next
- Never generate code in bulk — the course has specific pedagogical sequencing

---

## Infrastructure

All infrastructure runs locally via Docker Compose. Students start it once and leave it running:

```bash
docker compose -f docker-compose.infra.yml up -d
```

Services included: PostgreSQL, Redis, MongoDB, RabbitMQ, Kafka + Zookeeper, Keycloak, Traefik, Prometheus, Grafana, Jaeger, Loki, Promtail.

Each module's services are layered on top via `docker-compose.override.yml`.
