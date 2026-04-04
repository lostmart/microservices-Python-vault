
# Course Rationale — GameHub Microservices Curriculum

This curriculum for EPITA students focuses on transitioning from monolithic to microservices architectures using Python. It features a heavy emphasis on practical application (75%) through an incrementally built ecosystem called **GameHub** — a gamer social platform where users manage profiles, track games, log activity, and receive notifications.

---

## The Local-First Approach

A key pedagogical choice is the **local-first** progression. In Modules 1–3, students run services directly on their machines using **uvicorn** and **SQLite** — no Docker, no containers, no infrastructure complexity. This lets students focus purely on service design, HTTP APIs, and inter-service communication before adding operational concerns.

Docker is introduced incrementally starting at Module 4, but only for **infrastructure** (Kafka, RabbitMQ). Services themselves are not containerized until Module 8, when the transition from SQLite to PostgreSQL also happens. This mirrors how real teams often start: prototype locally, then containerize for deployment.

---

## The Core Technology Stack

The curriculum relies on **FastAPI** for service creation, chosen for its high concurrency and automatic OpenAPI documentation. To manage data effectively, it uses **polyglot persistence** — matching specific storage engines to the needs of each service:

- **SQLite**: Used for local development in Modules 1–7. Zero setup, no server process — each service gets its own `.db` file.
- **PostgreSQL**: Used for structured ACID transactions in production (from Module 8 onward).
- **Redis**: Employed for sub-millisecond lookups and distributed caching (from Module 5).

The notification-service is built with **Node.js** and always uses SQLite, giving students exposure to a polyglot service architecture without adding database complexity.

---

## The API Gateway

From Module 3 onward, all client requests enter through a **custom FastAPI gateway** located at the top-level `gateway/` folder. It is deliberately kept separate from the `services/` directory to make clear that it is infrastructure, not a domain service.

The gateway evolves across modules as a teaching artifact:
- **Module 3** — dumb router: forwards requests to the right service based on the URL path
- **Module 6** — adds JWT validation: rejects unauthenticated requests before they reach a service
- **Module 7** — adds version routing: enforces supported API versions per resource
- **Module 10** — adds circuit breakers: protects clients from cascading failures

This progression was chosen over a ready-made tool (Traefik, Nginx) because students can read every line of it. There is no magic, no config file format to learn — just Python they already know. The key lesson at each step: downstream services never change, only the gateway grows.

---

## Security — Native FastAPI Auth Service

Rather than using Keycloak (an external IdP requiring Docker and realm configuration), Module 6 introduces a dedicated **auth-service** built with FastAPI. Students implement JWT token issuance and validation themselves using `python-jose` and `passlib` — seeing the full mechanics of the OAuth2 password flow, role claims, and M2M client credentials without fighting a third-party admin UI.

This keeps Module 6 free of new Docker infrastructure and puts the focus where the learning is: understanding what a JWT contains and why role-based access control works the way it does.

---

## Communication and Messaging

A central challenge in this course is mastering how services talk to one another. The curriculum introduces a spectrum of protocols, comparing two messaging philosophies:

- **RabbitMQ**: A traditional broker that pushes messages to consumers and deletes them once acknowledged. Used for activity → notification events.
- **Apache Kafka**: An event streaming platform that stores messages in an append-only log, allowing consumers to replay historical data. Used for activity → logging events.

The choice between them is not arbitrary — students are asked to justify why each protocol fits its use case. RabbitMQ is a task queue (fire and forget); Kafka is an audit log (permanent, replayable).

---

## GDPR and the Logging Service

The **logging-service** introduces a real-world constraint that pure technical courses often ignore: **data privacy**. It implements a GDPR consent flow — user activity is only logged when the user has given explicit consent, and they can withdraw that consent at any time.

This is not just a compliance checkbox. It forces students to think about:
- Event filtering at the consumer level (Kafka consumer skips events for non-consented users)
- Data retention and deletion policies
- The tension between "log everything for debugging" and "respect user privacy"

---

## Resilience Patterns

The **Circuit Breaker** pattern is introduced in Module 10 at two levels:

1. **Inside a service** (activity-service → logging-service): protects a service from its own failing dependency
2. **At the gateway level**: protects clients from cascading failures when a downstream service is degraded

Teaching it at both levels makes the distinction concrete — a circuit breaker at the gateway is about client experience; a circuit breaker inside a service is about internal reliability.
