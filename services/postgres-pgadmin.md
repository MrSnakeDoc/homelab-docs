# PostgreSQL Stack

The PostgreSQL stack provides the primary relational database infrastructure of the homelab.

It delivers persistent structured data storage for multiple application services and includes a dedicated administration interface through pgAdmin4.

The database platform is treated as a **core infrastructure dependency**, similar in importance to reverse proxying, identity services, and internal DNS.

---

## Purpose

The PostgreSQL stack exists to provide reliable relational data storage for application services.

Its primary responsibilities include:

* persistent structured data storage
* transactional consistency
* relational data modeling
* logical isolation between applications
* centralized database administration

PostgreSQL does not perform:

* caching
* message queuing
* job scheduling
* metrics storage
* event streaming

These responsibilities are handled by separate infrastructure systems.

---

## Architectural Role

The PostgreSQL stack provides the **structured data layer** of the homelab.

Many application services rely on relational storage to persist structured data such as user accounts, configuration, metadata, and application state.

PostgreSQL acts as the central persistence engine responsible for ensuring transactional consistency and reliable long-term data storage.

Within the infrastructure architecture, it sits at the foundation of the application platform.

```text
Applications
      ↓
Relational storage layer
      ↓
PostgreSQL
```

This separation allows application services to remain stateless while delegating durable storage to the database platform.

---

## Stack Components

The PostgreSQL stack consists of two complementary components: the PostgreSQL server and the pgAdmin44 management interface.

### PostgreSQL Server

The core database engine responsible for storing and managing relational data.

Responsibilities include:

* handling application queries
* maintaining transactional integrity
* storing application data
* enforcing database permissions

This component represents the **data plane** of the database system.

---

### pgAdmin4

pgAdmin4 provides a web-based administrative interface for PostgreSQL.

It is used for:

* database provisioning
* role and permission management
* schema inspection
* troubleshooting and maintenance

pgAdmin4 is **not required for application operation** and functions only as a management interface.

---

## Deployment Model

The database platform runs as containerized services within the infrastructure services layer.

Key characteristics include:

* containerized execution model
* persistent storage volumes
* internal network access for applications
* HTTPS-protected administrative interface

The database engine itself is not exposed outside the internal service network.

---

## Database Strategy

A single PostgreSQL instance serves multiple applications.

Isolation is enforced logically rather than through separate database servers.

### Per-Application Isolation

Each application receives:

* a dedicated database
* a dedicated database user
* unique authentication credentials
* restricted permissions

No application shares schemas or credentials with another service.

This approach balances operational simplicity with strong logical separation.

---

## Dependent Services

Several infrastructure services rely on the PostgreSQL platform for persistent data storage.

Examples include:

* identity services
* application platforms
* collaboration tools
* personal knowledge systems

Each application connects using its own credentials and can only access its assigned database.

---

## Access Model

The PostgreSQL server itself is never exposed externally.

Applications connect to the database through the internal service network.

```text
Application container
  → Internal service network
    → PostgreSQL
```

Administrative access occurs through the management interface.

```text
Administrator
  → Reverse proxy
    → pgAdmin4
      → PostgreSQL
```

This model separates operational management from application traffic.

---

## Failure Scenarios

| Failure                 | Impact                                    |
| ----------------------- | ----------------------------------------- |
| PostgreSQL unavailable  | Dependent applications become unavailable |
| pgAdmin4 unavailable     | Administrative access unavailable         |
| Storage failure         | Data loss possible                        |
| Disk capacity exhausted | Write operations fail                     |

Because multiple services rely on PostgreSQL, it is considered a **central infrastructure dependency**.

---

## Security Model

The database platform follows several security principles:

* database engine not publicly exposed
* application access restricted to internal networks
* per-application credentials and permissions
* administrative access protected by HTTPS
* no shared database roles between applications

This design minimizes the attack surface and limits the blast radius of compromised applications.

---

## Design Principles

The architecture prioritizes **centralization with logical isolation**.

Running a single PostgreSQL instance simplifies operational management while maintaining strong application separation through database-level access controls.

```text
Multiple applications
      ↓
Logical database isolation
      ↓
Shared PostgreSQL instance
```

This approach reduces operational overhead while preserving security boundaries between services.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [PostgreSQL Stack Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/postgres/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.