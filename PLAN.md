<!-- This file replaced the original curriculum plan. See plan.md for the curriculum. -->
# ShopMicro — Implementation Plan

Work through this one step at a time. Propose → approve → create. No bulk writes.

---

## Status Key
- ✅ Done
- 🔲 Not started
- ⚠️ Partial / needs review

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
| 1.9 | `infra/keycloak/realm-export.json` | ✅ |
| 1.10 | `locustfile.py` | 🔲 (missing) |
| 1.11 | `README.md` | ✅ |

---

## Phase 2 — user-service (`services/user-service/`)

| # | File | Status |
|---|---|---|
| 2.1 | `requirements.txt` | 🔲 |
| 2.2 | `Dockerfile` | 🔲 |
| 2.3 | `alembic.ini` | 🔲 |
| 2.4 | `alembic/env.py` | 🔲 |
| 2.5 | `app/config.py` | 🔲 |
| 2.6 | `app/domain/models.py` | 🔲 |
| 2.7 | `app/application/schemas.py` | 🔲 |
| 2.8 | `app/application/service.py` | 🔲 |
| 2.9 | `app/infrastructure/database.py` | 🔲 |
| 2.10 | `app/infrastructure/repository.py` | 🔲 |
| 2.11 | `app/observability.py` | 🔲 |
| 2.12 | `app/security.py` | 🔲 |
| 2.13 | `app/main.py` | 🔲 |
| 2.14 | `tests/test_users.py` | 🔲 |

---

## Phase 3 — product-service (`services/product-service/`)

| # | File | Status |
|---|---|---|
| 3.1 | `requirements.txt` | 🔲 |
| 3.2 | `Dockerfile` | 🔲 |
| 3.3 | `alembic.ini` | 🔲 |
| 3.4 | `alembic/env.py` | 🔲 |
| 3.5 | `app/config.py` | 🔲 |
| 3.6 | `app/domain/models.py` | 🔲 |
| 3.7 | `app/application/schemas.py` | 🔲 |
| 3.8 | `app/application/service.py` | 🔲 |
| 3.9 | `app/infrastructure/database.py` | 🔲 |
| 3.10 | `app/infrastructure/repository.py` | 🔲 |
| 3.11 | `app/infrastructure/cache.py` | 🔲 |
| 3.12 | `app/observability.py` | 🔲 |
| 3.13 | `app/main.py` | 🔲 |
| 3.14 | `tests/test_products.py` | 🔲 |

---

## Phase 4 — order-service (`services/order-service/`)

| # | File | Status |
|---|---|---|
| 4.1 | `requirements.txt` | 🔲 |
| 4.2 | `Dockerfile` | 🔲 |
| 4.3 | `alembic.ini` | 🔲 |
| 4.4 | `alembic/env.py` | 🔲 |
| 4.5 | `app/config.py` | 🔲 |
| 4.6 | `app/domain/models.py` | 🔲 |
| 4.7 | `app/application/schemas.py` | 🔲 |
| 4.8 | `app/infrastructure/database.py` | 🔲 |
| 4.9 | `app/infrastructure/repository.py` | 🔲 |
| 4.10 | `app/infrastructure/kafka_producer.py` | 🔲 |
| 4.11 | `app/infrastructure/read_model.py` | 🔲 |
| 4.12 | `app/infrastructure/circuit_breaker.py` | 🔲 |
| 4.13 | `app/observability.py` | 🔲 |
| 4.14 | `app/main.py` | 🔲 |
| 4.15 | `tests/test_orders.py` | 🔲 |

---

## Phase 5 — inventory-service (`services/inventory-service/`)

| # | File | Status |
|---|---|---|
| 5.1 | `requirements.txt` | 🔲 |
| 5.2 | `Dockerfile` | 🔲 |
| 5.3 | `alembic.ini` | 🔲 |
| 5.4 | `alembic/env.py` | 🔲 |
| 5.5 | `app/config.py` | 🔲 |
| 5.6 | `app/domain/models.py` | 🔲 |
| 5.7 | `app/application/schemas.py` | 🔲 |
| 5.8 | `app/infrastructure/database.py` | 🔲 |
| 5.9 | `app/infrastructure/kafka_consumer.py` | 🔲 |
| 5.10 | `app/observability.py` | 🔲 |
| 5.11 | `app/main.py` | 🔲 |
| 5.12 | `tests/test_inventory.py` | 🔲 |

---

## Phase 6 — notification-service (`services/notification-service/`)

| # | File | Status |
|---|---|---|
| 6.1 | `requirements.txt` | 🔲 |
| 6.2 | `Dockerfile` | 🔲 |
| 6.3 | `app/config.py` | 🔲 |
| 6.4 | `app/infrastructure/mongo.py` | 🔲 |
| 6.5 | `app/infrastructure/rabbitmq_consumer.py` | 🔲 |
| 6.6 | `app/observability.py` | 🔲 |
| 6.7 | `app/main.py` | 🔲 |
| 6.8 | `tests/test_notifications.py` | 🔲 |

---

## Phase 7 — Module files

| # | File | Status |
|---|---|---|
| 7.1 | `modules/module-01/exercise.md` | ✅ |
| 7.2 | `modules/module-02/exercise.md` | ✅ |
| 7.3 | `modules/module-02/docker-compose.override.yml` | ✅ |
| 7.4 | `modules/module-03/exercise.md` | ✅ |
| 7.5 | `modules/module-03/docker-compose.override.yml` | ✅ |
| 7.6 | `modules/module-04/exercise.md` | ✅ |
| 7.7 | `modules/module-04/docker-compose.override.yml` | ✅ |
| 7.8 | `modules/module-05/exercise.md` | ✅ |
| 7.9 | `modules/module-06/exercise.md` | ✅ |
| 7.10 | `modules/module-07/exercise.md` | ✅ |
| 7.11 | `modules/module-08/exercise.md` | ✅ |
| 7.12 | `modules/module-09/exercise.md` | ✅ |
| 7.13 | `modules/module-09/docker-compose.override.yml` | ✅ |
| 7.14 | `modules/module-10/exercise.md` | ✅ |
| 7.15 | `modules/module-10/docker-compose.override.yml` | 🔲 |

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
| 8.9 | `.github/workflows/product-service.yml` | ✅ |

---

## Progress Summary

| Phase | Description | Done |
|---|---|---|
| 1 | Infrastructure | 9/11 |
| 2 | user-service | 0/14 |
| 3 | product-service | 0/14 |
| 4 | order-service | 0/15 |
| 5 | inventory-service | 0/12 |
| 6 | notification-service | 0/8 |
| 7 | Module files | 14/15 |
| 8 | Helm + CI/CD | 9/9 |

**Next: Step 1.10 — `locustfile.py`**

---

Full Technology Stack

Core Framework

┌────────────────────────┬───────────────────────────────────────────────┐
│ Tool │ Role │
├────────────────────────┼───────────────────────────────────────────────┤
│ FastAPI │ Service framework (REST + async) │
├────────────────────────┼───────────────────────────────────────────────┤
│ SQLAlchemy 2.x (async) │ ORM │
├────────────────────────┼───────────────────────────────────────────────┤
│ Alembic │ DB migrations │
├────────────────────────┼───────────────────────────────────────────────┤
│ Pydantic v2 │ Validation / DTO schemas (built into FastAPI) │
├────────────────────────┼───────────────────────────────────────────────┤
│ uvicorn │ ASGI server │
└────────────────────────┴───────────────────────────────────────────────┘

Databases (Polyglot Persistence)

┌────────────┬─────────────────────────────────────────────────┐
│ Tool │ Role │
├────────────┼─────────────────────────────────────────────────┤
│ PostgreSQL │ Primary relational DB (all write-side services) │
├────────────┼─────────────────────────────────────────────────┤
│ Redis │ Distributed caching + read-side CQRS │
├────────────┼─────────────────────────────────────────────────┤
│ MongoDB │ Document store (notification-service demo) │
└────────────┴─────────────────────────────────────────────────┘

Inter-Service Communication

┌─────────────────────────┬────────────────────────────────────────────────────────┐
│ Tool │ Role │
├─────────────────────────┼────────────────────────────────────────────────────────┤
│ httpx (async) │ HTTP client for REST calls │
├─────────────────────────┼────────────────────────────────────────────────────────┤
│ grpcio + grpcio-tools │ gRPC exercise (one service pair) │
├─────────────────────────┼────────────────────────────────────────────────────────┤
│ RabbitMQ + aio-pika │ Task queues, pub-sub, dead-letter queues │
├─────────────────────────┼────────────────────────────────────────────────────────┤
│ Apache Kafka + aiokafka │ Event streaming, CQRS read projections, Event Sourcing │
└─────────────────────────┴────────────────────────────────────────────────────────┘

API Gateway

┌─────────┬──────────────────────────────────────────────────────────────────────────────┐
│ Tool │ Role │
├─────────┼──────────────────────────────────────────────────────────────────────────────┤
│ Traefik │ Reverse proxy + API gateway (Docker-native, integrates with Compose and K8s) │
└─────────┴──────────────────────────────────────────────────────────────────────────────┘

Security

┌─────────────┬────────────────────────────────────────────────────┐
│ Tool │ Role │
├─────────────┼────────────────────────────────────────────────────┤
│ Keycloak │ OAuth 2.0 + OpenID Connect provider (Docker image) │
├─────────────┼────────────────────────────────────────────────────┤
│ python-jose │ JWT decode/validate in FastAPI middleware │
├─────────────┼────────────────────────────────────────────────────┤
│ mkcert │ Local TLS certificates │
└─────────────┴────────────────────────────────────────────────────┘

Observability

┌───────────────────────────────────┬───────────────────────────────────────────────────┐
│ Tool │ Role │
├───────────────────────────────────┼───────────────────────────────────────────────────┤
│ OpenTelemetry Python SDK │ Unified instrumentation (traces + metrics + logs) │
├───────────────────────────────────┼───────────────────────────────────────────────────┤
│ prometheus-fastapi-instrumentator │ Auto metrics endpoint per service │
├───────────────────────────────────┼───────────────────────────────────────────────────┤
│ Prometheus │ Metrics scraper │
├───────────────────────────────────┼───────────────────────────────────────────────────┤
│ Grafana │ Dashboards │
├───────────────────────────────────┼───────────────────────────────────────────────────┤
│ Jaeger │ Distributed trace UI │
├───────────────────────────────────┼───────────────────────────────────────────────────┤
│ Loki + Promtail │ Centralized log aggregation │
└───────────────────────────────────┴───────────────────────────────────────────────────┘

Resilience

┌──────────────────────────────────────────┬────────────────────────────────────────┐
│ Tool │ Role │
├──────────────────────────────────────────┼────────────────────────────────────────┤
│ tenacity │ Retry + exponential backoff │
├──────────────────────────────────────────┼────────────────────────────────────────┤
│ httpx timeouts + circuit-breaker pattern │ Manual CB implementation (educational) │
└──────────────────────────────────────────┴────────────────────────────────────────┘

Orchestration

┌────────────────┬────────────────────────────────────────────────────┐
│ Tool │ Role │
├────────────────┼────────────────────────────────────────────────────┤
│ Docker Compose │ Local dev environment (students already know this) │
├────────────────┼────────────────────────────────────────────────────┤
│ minikube / k3d │ Local Kubernetes cluster │
├────────────────┼────────────────────────────────────────────────────┤
│ Helm │ K8s templating │
└────────────────┴────────────────────────────────────────────────────┘

CI/CD & Testing

┌─────────────────────────┬─────────────────────────────────────┐
│ Tool │ Role │
├─────────────────────────┼─────────────────────────────────────┤
│ GitHub Actions │ Build → test → push image → deploy │
├─────────────────────────┼─────────────────────────────────────┤
│ pytest + pytest-asyncio │ Unit + integration tests │
├─────────────────────────┼─────────────────────────────────────┤
│ httpx (AsyncClient) │ FastAPI test client │
├─────────────────────────┼─────────────────────────────────────┤
│ Testcontainers-python │ Real DB/broker in integration tests │
├─────────────────────────┼─────────────────────────────────────┤
│ Locust │ Load / performance testing │
└─────────────────────────┴─────────────────────────────────────┘

---

10-Module Course Structure

Module 1 — Microservices Fundamentals (theory-heavy)

Topics: Monolith vs microservices trade-offs, Conway's Law, Domain-Driven Design, bounded contexts, CAP theorem basics
Exercise: Decompose a given monolithic e-commerce spec into bounded contexts + define service contracts on paper
Capstone step: Students define the "ShopMicro" service map and data ownership

---

Module 2 — Service Design with FastAPI + DDD

Topics: FastAPI project layout (domain / application / infrastructure layers), SQLAlchemy 2 async models, Alembic migrations, Pydantic v2 schemas as DTOs, repository pattern
Exercise: Scaffold user-service and product-service with full CRUD + OpenAPI docs auto-generated
Capstone step: user-service and product-service merged into ShopMicro repo

---

Module 3 — Synchronous Communication (REST + gRPC intro)

Topics: Service-to-service REST with httpx, OpenAPI contract-first design, gRPC basics (protobuf, codegen, unary call)
Exercise: Connect order-service → user-service via REST; rewrite one internal call to gRPC to compare
Capstone step: order-service created; REST communication between services wired up

---

Module 4 — Asynchronous Messaging (RabbitMQ + Kafka)

Topics: Message queues vs event streams, RabbitMQ exchanges/queues/DLQ with aio-pika, Kafka topics/partitions/consumer groups with aiokafka
Exercise 1 (RabbitMQ): Order placement → notification queue → notification-service email mock
Exercise 2 (Kafka): Order placed event → inventory-service consumer updates stock
Capstone step: inventory-service and notification-service added

---

Module 5 — Data Management (Polyglot, CQRS, Event Sourcing)

Topics: Database-per-service pattern, polyglot persistence, CQRS with Redis read model, Event Sourcing with Kafka as event log
Exercise: Implement order-service with PostgreSQL (commands) + Redis read projections + event replay from Kafka
Capstone step: order-service refactored to CQRS; full event log in Kafka

---

Module 6 — Security (OAuth 2.0, JWT, TLS)

Topics: OAuth 2.0 flows (auth code, client credentials), OpenID Connect, Keycloak setup, JWT validation middleware in FastAPI, service-to-service auth, TLS with mkcert
Exercise: Protect all endpoints behind Keycloak; implement login + token refresh; add M2M client credentials for internal calls
Capstone step: All ShopMicro services secured

---

Module 7 — API Design & Documentation

Topics: OpenAPI/Swagger deep dive, versioning strategies (/v1/, headers), API changelog discipline, generating typed clients from spec
Exercise: Generate a Python client SDK from product-service OpenAPI spec; write contract tests against it
Capstone step: All services have versioned, documented APIs

---

Module 8 — Docker + Kubernetes Deployment

Topics: Multi-stage Dockerfiles, Docker Compose for dev (students extend existing knowledge), Kubernetes objects (Deployment, Service, ConfigMap, Secret, Ingress), Traefik as K8s Ingress controller, Helm chart authoring
Exercise: Write Helm chart for user-service; deploy full ShopMicro to minikube
Capstone step: ShopMicro running on minikube with Traefik ingress

---

Module 9 — Observability (Metrics, Tracing, Logging)

Topics: OpenTelemetry instrumentation, Prometheus scraping, Grafana dashboards, Jaeger trace waterfall, structured logging with Loki
Exercise: Instrument all services; use Jaeger to trace a slow order request across 4 services and identify the bottleneck
Capstone step: Full observability stack deployed alongside ShopMicro

---

Module 10 — Resilience, Performance & CI/CD

Topics: Circuit breaker pattern (implement with tenacity + state machine), bulkhead, Redis caching strategies + invalidation, Traefik rate limiting middleware, GitHub Actions pipeline (build → test → push → deploy), Strangler Fig  
 migration pattern
Exercise 1: Add circuit breaker to order→inventory call; simulate inventory-service failure
Exercise 2: Write full GitHub Actions pipeline for one service
Capstone step: Complete ShopMicro with CI/CD pipeline; final presentation

---

Capstone Project — "ShopMicro"

┌──────────────────────┬───────────────────────────────────┬───────────────────┬─────────────────────┐
│ Service │ Database │ Inbound │ Outbound │
├──────────────────────┼───────────────────────────────────┼───────────────────┼─────────────────────┤
│ api-gateway │ — │ HTTP │ Traefik → services │
├──────────────────────┼───────────────────────────────────┼───────────────────┼─────────────────────┤
│ user-service │ PostgreSQL │ REST │ — │
├──────────────────────┼───────────────────────────────────┼───────────────────┼─────────────────────┤
│ product-service │ PostgreSQL + Redis │ REST │ — │
├──────────────────────┼───────────────────────────────────┼───────────────────┼─────────────────────┤
│ order-service │ PostgreSQL (write) + Redis (read) │ REST │ Kafka events │
├──────────────────────┼───────────────────────────────────┼───────────────────┼─────────────────────┤
│ inventory-service │ PostgreSQL │ Kafka consumer │ — │
├──────────────────────┼───────────────────────────────────┼───────────────────┼─────────────────────┤
│ notification-service │ MongoDB │ RabbitMQ consumer │ — │
├──────────────────────┼───────────────────────────────────┼───────────────────┼─────────────────────┤
│ Keycloak │ (internal) │ OAuth flows │ JWT to all services │
└──────────────────────┴───────────────────────────────────┴───────────────────┴─────────────────────┘

Students build one service per module, wiring everything together incrementally.

---

Infrastructure Docker Compose (provided as starter)

Students receive a docker-compose.infra.yml with:

- PostgreSQL (x3 databases)
- Redis
- MongoDB
- RabbitMQ (with management UI)
- Kafka + Zookeeper (or KRaft)
- Keycloak
- Prometheus + Grafana + Jaeger + Loki
- Traefik

Each module's service gets its own docker-compose.override.yml.

---

Additional Tools Worth Mentioning (bonus/optional)

- Celery — if students want a more opinionated task queue alternative to raw RabbitMQ
- Istio / Linkerd — mention as advanced service mesh topic, not required
- ArgoCD — GitOps alternative to GitHub Actions deploy step (bonus module)
- OpenAPI Generator — client SDK generation exercise in Module 7

---

Verification / Success Criteria

By end of course, each student should be able to:

1.  Run docker compose up and have all ShopMicro services healthy
2.  Log in via Keycloak, place an order via REST, see inventory updated asynchronously, notification sent
3.  Open Grafana and see per-service RED metrics (Rate, Errors, Duration)
4.  Open Jaeger and see a full distributed trace for one order request
5.  Trigger a service failure and show circuit breaker activating
6.  Push a code change → GitHub Actions builds, tests, and deploys automatically
    ╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌
