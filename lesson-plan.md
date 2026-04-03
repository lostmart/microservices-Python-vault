# GameHub — Lesson Plan

15 lessons × 2 hours = 30 hours total

**Structure:** 11 taught lessons · 1 independent work session · 3 buffer/flex lessons

---

## Overview table

| Lesson | Type | Module | Topic | Docker? |
|---|---|---|---|---|
| 1 | Taught | M1 | Service decomposition + course setup | None |
| 2 | Taught | M2 | FastAPI services — user-service + game-service | None |
| 3 | Taught | M3 | Synchronous communication — activity-service | None |
| 4 | Taught | M4 | Async messaging — Kafka + RabbitMQ | Kafka, RabbitMQ |
| 5 | Taught | M5 | CQRS, Redis, GDPR consent flow | + Redis |
| 6 | Taught | M6 | Security — Keycloak, JWT, role-based access | + Keycloak |
| 7 | Taught | M7 | API design — versioning, OpenAPI, SDK generation | No new |
| 8 | **Independent** | — | Catch-up + consolidation (M1–7) | Student choice |
| 9 | Taught | M8 | Containerisation — Dockerfiles, PostgreSQL, Helm | All services |
| 10 | Taught | M9 | Observability — Prometheus, Grafana, Jaeger, Loki | + Obs stack |
| 11 | Taught | M10-A | Resilience — circuit breaker, Redis cache warming | Full stack |
| 12 | Taught | M10-B | CI/CD — GitHub Actions, Locust load testing, final demo | Full stack |
| 13 | Buffer | — | Overflow / Q&A / student demos | — |
| 14 | Buffer | — | Overflow / Q&A / student demos | — |
| 15 | Buffer | — | Overflow / Q&A / student demos | — |

---

## Lesson details

### Lesson 1 — Service Decomposition (Module 1)
**Goals:** Understand bounded contexts; design the GameHub service map before writing any code.

- Intro to Micro Services vs Monolitic projects
- Course intro: what GameHub is, how the 10 modules build up progressively
- Prerequisite check: Python 3.12, Node.js 20, terminal, Git
- Theory: bounded contexts, Conway's Law, CAP theorem
- Exercise M1: identify bounded contexts, define service contracts, draw the service map
- Discussion: why notification-service is Node.js, why GDPR needs its own service

---

### Lesson 2 — FastAPI Services (Module 2)
**Goals:** Build two real services locally with no infrastructure overhead.

- Theory: DDD layering (domain / application / infrastructure), FastAPI routing, Pydantic v2
- Hands-on: scaffold `user-service` and `game-service` using SQLite + uvicorn
- Exercise M2: run migrations with Alembic, write pytest tests, implement search endpoint
- Key insight: same SQLAlchemy code will work with PostgreSQL in Module 8 — zero rewrite

---

### Lesson 3 — Synchronous Communication (Module 3)
**Goals:** Connect services over HTTP; understand failure modes in synchronous calls.

- Theory: REST vs gRPC, when sync is appropriate, cascading failures
- Hands-on: build `activity-service`; add user validation via httpx
- Exercise M3: add retry logic with tenacity; gRPC bonus track
- Discussion: what happens if user-service is slow? what is the timeout budget?

---

### Lesson 4 — Async Messaging (Module 4)
**Goals:** First Docker lesson — infrastructure only. Introduce event-driven communication.

- Theory: message queues vs event streams, RabbitMQ vs Kafka, dead-letter queues
- Setup: `docker compose -f docker-compose.infra.yml up -d rabbitmq zookeeper kafka`
- Hands-on: publish activity → notification via RabbitMQ; publish activity → logging via Kafka
- Exercise M4: verify message flow with RabbitMQ UI and Kafka CLI tools
- Key insight: services still run locally with uvicorn — Docker is only for the broker

---

### Lesson 5 — CQRS, Redis & GDPR (Module 5)
**Goals:** Separate read and write paths; implement the GDPR consent flow.

- Theory: CQRS pattern, event sourcing intro, eventual consistency trade-offs
- Add Redis: `docker compose -f docker-compose.infra.yml up -d redis`
- Hands-on: Redis read projection on game-service; benchmark with `hey`
- Exercise M5: implement full GDPR consent lifecycle in logging-service (opt-in, opt-out, erasure)
- Discussion: what features disappear when a user withdraws consent?

---

### Lesson 6 — Security (Module 6)
**Goals:** Protect all services with JWT; understand Keycloak realm + role concepts.

- Theory: OAuth2 flows, JWT anatomy, M2M client credentials, role-based access control
- Add Keycloak: `docker compose -f docker-compose.infra.yml up -d keycloak`
- Hands-on: get a token via curl, decode at jwt.io, add JWT middleware to user-service
- Exercise M6: protect DELETE with `admin` role; use M2M token in activity-service
- Discussion: `gamer` vs `admin` role; token expiry; public key rotation

---

### Lesson 7 — API Design & Documentation (Module 7)
**Goals:** Make services production-grade API citizens — versioned, documented, testable.

- Theory: URL versioning vs header versioning; when to bump major version; contract testing
- Hands-on: add `/v2/games` with breaking shape change; keep `/v1/games` working
- Exercise M7: generate a Python client SDK with openapi-generator; write contract tests
- Discussion: `Accept: application/vnd.gamehub.v2+json` — why media-type versioning is hard

---

### Lesson 8 — Independent Work Session
**Goals:** Students consolidate Modules 1–7 at their own pace. No new material.

Suggested focus areas:
- Finish any incomplete exercise from M1–7
- Add missing tests
- Wire up the full consent flow end-to-end (activity → logging-service consent check)
- Experiment with Kafka consumer group lag
- Read the JWT payload and understand each claim

Instructor is available for questions. No lecture.

---

### Lesson 9 — Containerisation (Module 8)
**Goals:** Move from local uvicorn + SQLite to Docker + PostgreSQL. Understand multi-stage builds.

- Theory: multi-stage Dockerfiles, image size optimisation, Kubernetes concepts (Pod, Deployment, Service, Ingress)
- Hands-on: review Dockerfile stages; update `DATABASE_URL` to PostgreSQL; run Alembic against PostgreSQL
- Exercise M8: `docker compose -f ... -f module-08/docker-compose.override.yml up --build`
- Helm: install user-service on minikube, scale to 3 replicas, rolling update
- Key insight: notification-service stays local — it never gets a Dockerfile

---

### Lesson 10 — Observability (Module 9)
**Goals:** Instrument the running system; trace a request across 3 services.

- Theory: the three pillars (logs, metrics, traces); RED method; OpenTelemetry architecture
- Hands-on: start full observability stack; generate traffic with `hey`
- Exercise M9: build a Grafana dashboard with Rate/Errors/Duration for user-service
- Tracing: find a `POST /activities` trace in Jaeger; identify the slowest span
- Loki: correlate a log line with a trace by timestamp

---

### Lesson 11 — Resilience & Performance (Module 10-A)
**Goals:** Make the system survive downstream failures; understand caching trade-offs.

- Theory: circuit breaker states (closed / open / half-open), bulkhead pattern, cache-aside vs write-through
- Hands-on: implement circuit breaker on activity-service → logging-service call
- Demo: stop logging-service, trigger 5 calls, watch circuit open, restart and watch recovery
- Exercise M10-A: add cache warming on game-service startup; benchmark hit vs miss

---

### Lesson 12 — CI/CD, Load Testing & Final Demo (Module 10-B)
**Goals:** Automate the delivery pipeline; validate the system under load.

- Theory: GitHub Actions pipeline anatomy (lint → test → build → deploy); Testcontainers for integration tests
- Hands-on: review `.github/workflows/user-service.yml`; trigger a run by pushing a commit
- Exercise M10-B: run Locust with `GameHubUser`; find the bottleneck service in Grafana
- Final demo checklist:
  - All services healthy
  - Log in via Keycloak → log activity → notification received → logging-service records with consent
  - Grafana RED metrics visible
  - Jaeger trace spans across 3 services
  - Circuit breaker demo live

---

### Lessons 13–15 — Buffer / Overflow / Q&A
Reserved for:
- Student catch-up
- Deep-dives on topics that ran over
- Student project demos
- Final Q&A

---

## Pacing notes

- **Before lesson 4**: ensure Docker Desktop is installed and running on student machines
- **Before lesson 8**: students should have at least M2 and M3 working end-to-end
- **Before lesson 9**: ensure minikube is installed (`minikube start` tested)
- **Module 10 is split across 2 lessons** because CI/CD setup (GitHub secrets, kubeconfig) takes time that can't be rushed
