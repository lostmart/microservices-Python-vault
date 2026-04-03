
This curriculum for EPITA students focuses on transitioning from monolithic to microservices architectures using Python. It features a heavy emphasis on practical application (75%) through an incrementally built ecosystem called **ShopMicro**. I will help you navigate these concepts and will ask guiding questions along the way to build your understanding.

## The Core Technology Stack

The curriculum relies on **FastAPI** ⚡ for service creation, chosen for its high concurrency and automatic OpenAPI documentation. To manage data effectively, it utilizes **polyglot persistence**, matching specific storage engines to the needs of each service:

- **PostgreSQL** 🐘: Used for structured ACID transactions in write-heavy services.
- **Redis** 🏃‍♂️: Employed for sub-millisecond lookups and distributed caching.    
- **MongoDB** 🍃: Utilized as a document store for flexible notification data.

## Communication and Messaging

A central challenge in this course is mastering how services talk to one another. The plan introduces a spectrum of protocols, most notably comparing two messaging philosophies:

- **RabbitMQ** 🐇: A traditional broker that pushes messages to consumers and deletes them once acknowledged.    
- **Apache Kafka** 🚂: An event streaming platform that stores messages in an append-only log, allowing consumers to "replay" historical data.

## Learning Paths

### 1. The Architectural Blueprint

In a microservices architecture, keeping different parts of a service independent is key to long-term maintenance. The curriculum emphasizes a clear "Separation of Concerns" by using **FastAPI** ⚡ for the transport layer and **SQLAlchemy** 🏗️ for the persistence layer.

Rather than having one tool handle everything, this modular approach creates a "firewall" between how data arrives at the service and how it is stored in the database.

### 2. Messaging & Reliability

Reliable messaging is the backbone of a distributed system, allowing services to communicate without being "locked" to each other's availability. I'll guide you through how these systems handle communication and failure, and we'll explore specific paths together.

In a traditional setup, if Service A asks Service B for data and Service B is down, the whole process fails. **Asynchronous Messaging** solves this by using a "middleman" (a broker). Service A sends a message and continues its work; the broker ensures the message eventually reaches its destination.

There are two primary ways to handle this communication:

- **Message Queues (like RabbitMQ)**: These function like a task list. A message is sent to a queue, processed by one consumer, and then deleted. This is ideal for discrete actions like "send a welcome email" or "process a payment." 📬
    
- **Event Streaming (like Kafka)**: This works like a continuous, immutable log. Messages (events) are stored and can be read by many different services at once. Because the log is permanent, services can "replay" past events to reconstruct state or perform analytics. 🪵

To make these systems truly reliable, we use **Resilience Patterns**. The most common is the **Circuit Breaker** ⚡. Just like an electrical fuse, if a service starts failing, the circuit "trips" to stop requests from hitting the failing service. This prevents a single failure from cascading and crashing other parts of the system.