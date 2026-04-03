
This curriculum for EPITA students focuses on transitioning from monolithic to microservices architectures using Python. It features a heavy emphasis on practical application (75%) through an incrementally built ecosystem called **GameHub** — a gamer social platform where users manage profiles, track games, log activity, and receive notifications. I will help you navigate these concepts and will ask guiding questions along the way to build your understanding.

## The Local-First Approach

A key pedagogical choice in this course is the **local-first** progression. In Modules 1–3, students run services directly on their machines using **uvicorn** and **SQLite** — no Docker, no containers, no infrastructure complexity. This lets you focus purely on service design, HTTP APIs, and inter-service communication before adding operational concerns.

Docker is introduced incrementally starting at Module 4, but only for **infrastructure** (Kafka, RabbitMQ). Services themselves are not containerized until Module 8, when the transition from SQLite to PostgreSQL also happens. This mirrors how real teams often start: prototype locally, then containerize for deployment.

## The Core Technology Stack

The curriculum relies on **FastAPI** ⚡ for service creation, chosen for its high concurrency and automatic OpenAPI documentation. To manage data effectively, it utilizes **polyglot persistence**, matching specific storage engines to the needs of each service:

- **SQLite** 📁: Used for local development in Modules 1–7. Zero setup, no server process — each service gets its own `.db` file.
- **PostgreSQL** 🐘: Used for structured ACID transactions in production (from Module 8 onward).
- **Redis** 🏃‍♂️: Employed for sub-millisecond lookups and distributed caching (from Module 5).

The notification-service is built with **Node.js** and always uses SQLite, giving students exposure to a polyglot service architecture without adding database complexity.

## Communication and Messaging

A central challenge in this course is mastering how services talk to one another. The plan introduces a spectrum of protocols, most notably comparing two messaging philosophies:

- **RabbitMQ** 🐇: A traditional broker that pushes messages to consumers and deletes them once acknowledged. Used for activity → notification events.
- **Apache Kafka** 🚂: An event streaming platform that stores messages in an append-only log, allowing consumers to "replay" historical data. Used for activity → logging events.

## GDPR and the Logging Service

The **logging-service** introduces a real-world constraint that pure technical courses often ignore: **data privacy**. It implements a GDPR consent flow — user activity is only logged when the user has given explicit consent, and they can withdraw that consent at any time.

This is not just a compliance checkbox. It forces students to think about:
- Event filtering at the consumer level (Kafka consumer skips events for non-consented users)
- Data retention and deletion policies
- The tension between "log everything for debugging" and "respect user privacy"

## Learning Paths

### 1. The Architectural Blueprint

In a microservices architecture, keeping different parts of a service independent is key to long-term maintenance. The curriculum emphasizes a clear "Separation of Concerns" by using **FastAPI** ⚡ for the transport layer and **SQLAlchemy** 🏗️ for the persistence layer.

Rather than having one tool handle everything, this modular approach creates a "firewall" between how data arrives at the service and how it is stored in the database.

### 2. Messaging & Reliability

Reliable messaging is the backbone of a distributed system, allowing services to communicate without being "locked" to each other's availability. I'll guide you through how these systems handle communication and failure, and we'll explore specific paths together.

In a traditional setup, if Service A asks Service B for data and Service B is down, the whole process fails. **Asynchronous Messaging** solves this by using a "middleman" (a broker). Service A sends a message and continues its work; the broker ensures the message eventually reaches its destination.

There are two primary ways to handle this communication:

- **Message Queues (like RabbitMQ)**: These function like a task list. A message is sent to a queue, processed by one consumer, and then deleted. This is ideal for discrete actions like "notify a user about a friend request" or "send a game recommendation." 📬

- **Event Streaming (like Kafka)**: This works like a continuous, immutable log. Messages (events) are stored and can be read by many different services at once. Because the log is permanent, services can "replay" past events to reconstruct state or perform analytics. 🪵

To make these systems truly reliable, we use **Resilience Patterns**. The most common is the **Circuit Breaker** ⚡. Just like an electrical fuse, if a service starts failing, the circuit "trips" to stop requests from hitting the failing service. This prevents a single failure from cascading and crashing other parts of the system.
