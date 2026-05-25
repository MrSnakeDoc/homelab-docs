# Redis Stack

The Redis stack provides the centralized in-memory data store of the homelab.

It offers fast-access data structures used by selected applications for caching, short-lived state, and background coordination.

Redis operates as a **shared ephemeral service** designed to improve performance and support asynchronous workflows.

It is not used as a primary data store.

All authoritative and durable data is stored in PostgreSQL.

---

## Purpose

Redis exists to provide fast-access ephemeral data storage for infrastructure services.

Its primary responsibilities include:

* in-memory caching
* transient application state
* background coordination data
* performance optimization
* non-critical learning and scoring data

Redis does not perform:

* durable persistence
* source-of-truth storage
* long-term data retention
* business-critical state management

If Redis data is lost, the infrastructure remains operational.

---

## Architectural Role

Redis provides the **ephemeral data layer** of the homelab.

It sits between applications and the persistent storage layer, accelerating operations that benefit from fast in-memory access.

```text
Applications
      ↓
Redis - ephemeral state
      ↓
PostgreSQL - durable state
```

Redis improves performance and coordination between services but never replaces the database.

---

## Stack Components

The Redis stack includes two complementary components. The Redis and RedisInsight management interface.

### Redis Server

Redis is the core service responsible for providing in-memory data structures.

Responsibilities include:

* caching frequently accessed data
* storing short-lived application state
* coordinating background tasks
* supporting asynchronous workflows

Redis operates as a shared internal service accessed by multiple applications.

---

### RedisInsight

RedisInsight provides a web-based interface for inspecting and managing Redis.

It is used for:

* monitoring Redis activity
* inspecting keys and data structures
* troubleshooting application behavior

RedisInsight is optional and not required for application runtime.

---

## Deployment Model

The Redis stack runs as containerized services within the infrastructure services layer.

Key characteristics include:

* single shared Redis instance
* containerized execution model
* persistent storage enabled but treated as best-effort
* administrative interface exposed through the reverse proxy

No clustering or replication is implemented.

Redis is intentionally kept simple and lightweight.

---

## Database Layout

Redis operates using a single logical database.

All applications share the same instance and are responsible for namespacing their keys.

This approach avoids unnecessary complexity while remaining sufficient for current workloads.

Redis logical databases are not used for isolation because they do not provide meaningful security separation.

---

## Dependent Services

Several infrastructure services rely on Redis for ephemeral data storage.

Typical use cases include:

* caching application data
* coordinating background jobs
* storing temporary workflow state
* supporting asynchronous operations

Each application uses Redis according to its own operational needs.

---

## Access Model

Redis is not exposed outside the internal service network.

Applications connect directly to the Redis server through the container network.

```text
Application container
  → Internal service network
    → Redis
```

Administrative inspection occurs through the management interface.

```text
Administrator
  → Reverse proxy
    → RedisInsight
      → Redis
```

The Redis server itself is never exposed directly to external networks.

---

## Failure Scenarios

| Failure                  | Impact                                          |
| ------------------------ | ----------------------------------------------- |
| Redis unavailable        | Caching and background coordination unavailable |
| Redis data loss          | Cached data rebuilt automatically               |
| RedisInsight unavailable | Administrative inspection unavailable           |
| Container restart        | Temporary degradation                           |

Redis failures degrade performance but do not break the infrastructure.

---

## Persistence Model

Redis persistence is enabled but treated as **best-effort storage**.

Redis data is considered:

* ephemeral
* reconstructible
* non-authoritative

If Redis data is lost, applications simply rebuild the required state.

All critical data must remain stored in PostgreSQL.

---

## Security Model

The Redis platform follows several security principles:

* Redis server not exposed to external networks
* container-network access only
* strong authentication credentials
* no reliance on Redis logical database separation
* administrative interface protected by HTTPS

Security relies primarily on strict network isolation.

---

## Design Principles

The architecture prioritizes simplicity and resilience.

Redis is intentionally deployed as a lightweight shared service.

```text
Applications
      ↓
Ephemeral state layer
      ↓
    Redis
```

Applications are designed to tolerate Redis instability and data loss.

This ensures Redis never becomes a critical single point of failure.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [https://github.com/MrSnakeDoc/homelab-blueprint/tree/main/redis-stack](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/redis/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.