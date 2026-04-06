# GameHub — Lesson Plan

15 lessons × 2 hours = 30 hours total

**Structure:** 11 taught lessons · 1 independent work session · 1 flex lesson · 2 buffer lessons

---

## Overview table

| Lesson | Type | Module | Topic | Docker? |
|---|---|---|---|---|
| 1 | Taught | M1 | Service decomposition + course setup | None |
| 2 | Taught | M2 | FastAPI services — user-service + game-service | None |
| 3 | Taught | M3 | Synchronous communication — activity-service | None |
| 4 | Taught | M4 | Async messaging — Kafka + RabbitMQ | Kafka, RabbitMQ |
| 5 | Taught | M5 | CQRS, Redis, GDPR consent flow | + Redis |
| 6 | Taught | M6 | Security — FastAPI JWT auth service, role-based access | None new |
| 7 | Taught | M7 | API design — versioning, OpenAPI, contract testing | No new |
| 8 | **Independent** | — | Catch-up + consolidation (M1–7) | Student choice |
| 9 | Taught | M8 | Containerisation — Dockerfiles, PostgreSQL migration | All services |
| 10 | Taught | M9 | Observability — Prometheus, Grafana, Jaeger, Loki | + Obs stack |
| 11 | Taught | M10-A | Resilience — circuit breaker, Redis cache warming | Full stack |
| 12 | Taught | M10-B | Final demo + system walkthrough | Full stack |
| 13 | Flex | — | CI/CD — GitHub Actions, Locust load testing | Full stack |
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
- Hands-on: build `activity-service` with two outbound calls:
  - **Validate** user exists via user-service (blocks the request — 404 if missing)
  - **Enrich** response with game data from game-service (best-effort — returns `"game": null` if unreachable)
- Exercise M3: add retry logic with tenacity on validation; graceful degradation on enrichment; gRPC bonus track
- Key insight: not all outbound calls deserve the same treatment — retry a gate, degrade a decoration
- Discussion: what happens if user-service is slow? what is the timeout budget? why not retry the enrichment call?

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

> **Framework note:** logging-service uses Flask internally; auth-service (introduced in Lesson 6) uses FastAPI. Both are equally valid choices — the API contract is what matters, not the framework.

- Theory: CQRS pattern, event sourcing intro, eventual consistency trade-offs
- Add Redis: `docker compose -f docker-compose.infra.yml up -d redis`
- Hands-on: Redis read projection on game-service; benchmark with `hey`
- Exercise M5: implement full GDPR consent lifecycle in logging-service (opt-in, opt-out, erasure)
- Discussion: what features disappear when a user withdraws consent?

---

### Lesson 6 — Security (Module 6)
**Goals:** Protect all services with JWT using a native FastAPI auth service — no new infrastructure.

- Theory: OAuth2 password flow, JWT anatomy (header/payload/signature), M2M client credentials, role-based access control
- Hands-on: build `auth-service` — a FastAPI service with `POST /token` (issue JWT) and a reusable `get_current_user` dependency
- Libraries: `python-jose` for JWT signing, `passlib` for password hashing; hardcoded user store (dict or SQLite)
- Decode the token at jwt.io — identify `sub`, `roles`, `exp`
- Exercise M6: protect `DELETE /games/{id}` with an `admin` role check; wire M2M token in activity-service (hardcoded client credentials)
- Discussion: `gamer` vs `admin` role; token expiry; why public key rotation is out of scope here

---

### Lesson 7 — API Design & Documentation (Module 7)
**Goals:** Make services production-grade API citizens — versioned, documented, and contract-tested.

- Theory: URL versioning vs header versioning; when to bump a major version; contract testing concepts
- Hands-on: add `/v2/games` with a breaking shape change (camelCase, flattened); keep `/v1/games` working; both documented in OpenAPI
- Exercise M7: write contract tests directly against the live service (no generated client needed)
- Brief mention only: `Accept: application/vnd.gamehub.v2+json` — media-type versioning exists, no hands-on

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

- Theory: multi-stage Dockerfiles, image size optimisation
- Hands-on: review Dockerfile stages; update `DATABASE_URL` to PostgreSQL; run Alembic against PostgreSQL
- Exercise M8: `docker compose -f ... -f module-08/docker-compose.override.yml up --build` — all Python services containerised
- Key insight: notification-service stays local — it intentionally never gets a Dockerfile
- **Demo (instructor only):** Kubernetes concepts — Pod, Deployment, Service, Ingress (whiteboard); Helm chart structure walkthrough; minikube scaling to 3 replicas. Students watch, no hands-on.

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

### Lesson 12 — Final Demo + System Walkthrough (Module 10-B)
**Goals:** Walk the entire GameHub system end-to-end as a working distributed system.

- No new content — this lesson is the finish line
- Final demo checklist:
  - All services healthy and running
  - Login via `auth-service` (`POST /token`) → log activity → notification received → logging-service records with consent check
  - Grafana RED metrics visible for at least user-service and activity-service
  - Jaeger trace spanning across 3 services — identify the slowest span
  - Circuit breaker demo live: stop logging-service, trigger 5 calls, watch circuit open, restart, watch recovery
  - GDPR consent flow: opt-out a user, verify activity is no longer logged

---

### Lesson 13 — CI/CD (Flex)
**Goals:** Automate the delivery pipeline and validate the system under load. Delivered if the course is on pace; used as a buffer if not.

**If delivered as a full lesson:**
- Theory: GitHub Actions pipeline anatomy (lint → test → build → deploy)
- Hands-on: review `.github/workflows/user-service.yml`; trigger a run by pushing a commit
- Exercise M13: run Locust with `GameHubUser`; find the bottleneck service in Grafana during the load test

**If used as buffer:**
- Student catch-up, Q&A, or deep-dives on topics that ran over
- No material is lost — CI/CD concepts are introduced but not assessed

---

### Lessons 14–15 — Buffer / Overflow / Q&A
Reserved for:
- Student catch-up
- Deep-dives on topics that ran over
- Student project demos
- Final Q&A

---

## Pacing notes

- **Before lesson 4**: ensure Docker Desktop is installed and running on student machines
- **Before lesson 8**: students should have at least M2 and M3 working end-to-end
- **Before lesson 9**: ensure minikube is installed (`minikube start` tested) — students may explore it independently; the instructor uses it for the Kubernetes demo
- **Lesson 12 is protected**: no new setup tasks in this lesson — CI/CD content lives in L13 so the final demo has the space it deserves
- **Lesson 13 is a flex lesson**: deliver the CI/CD content if the course is on pace; absorb overflow if not — no material is lost either way
