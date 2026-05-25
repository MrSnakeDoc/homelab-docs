# Outline

Outline is the primary knowledge management platform of the homelab.

It acts as the centralized documentation system and long-term knowledge repository for infrastructure design, operational procedures, architectural decisions, and personal technical research.

Outline is considered a critical service.

Loss of its content would result in irreversible knowledge loss.

---

## Purpose

Outline serves as the single source of truth for human-readable knowledge.

Its responsibilities include:

* structured long-form documentation
* architectural decision records
* operational procedures
* versioned document history
* internal publishing of documentation

Outline is not used for:

* monitoring
* automation
* alerting
* configuration management

It stores human knowledge, not live system state.

---

## Criticality Level

Outline is classified as a **Tier-0 Human Dependency Service**.

This means:

* the infrastructure can technically continue running without it
* but humans cannot safely operate or evolve the infrastructure without it

Because of this, Outline is treated with higher data-integrity importance than most other applications.

---

## Architecture Overview

The service follows a layered application model:

```
Client Browser
→ Reverse Proxy (HTTPS)
→ Outline Application
→ PostgreSQL (persistent data)
→ Redis (ephemeral services)
```

File uploads and attachments are stored on persistent local storage.

The database remains the authoritative source of truth.

---

## Core Dependencies

Outline relies on three external systems.

### PostgreSQL: Primary Data Store

PostgreSQL is the authoritative data layer.

It stores:

* documents
* collections
* users
* revisions
* permissions
* audit logs
* metadata

If PostgreSQL data is lost, the knowledge base is permanently lost.

---

### Redis: Background Services & Coordination

Redis is used for:

* background workers
* realtime collaboration
* caching
* job queues

Redis contains no authoritative data.

If Redis becomes unavailable:

* Outline may not start correctly
* background jobs may stall
* no permanent knowledge loss occurs

---

### Persistent Storage: File Assets

Persistent storage holds:

* uploaded files
* images
* attachments
* exports

If this storage is lost:

* documents remain intact in the database
* attachments may be missing

The knowledge core remains preserved.

---

## Authentication Model

Outline uses external identity federation exclusively.

Characteristics:

* no local user accounts
* no passwords stored in Outline
* centralized authentication via identity provider
* access control managed externally

If the identity provider is unavailable:

* login becomes impossible
* data remains safe

Outline fully delegates authentication to the identity platform.

---

## Network Exposure

Outline is accessible only through the reverse proxy layer.

Security characteristics:

* HTTPS is mandatory
* no direct port exposure
* no plain HTTP access
* external access is mediated through controlled edge services

The application is never directly reachable from untrusted networks.

---

## Data Classification

Outline data is separated into three logical layers:

| Layer              | Content                         | Criticality |
| ------------------ | ------------------------------- | ----------- |
| Database           | Documents, history, permissions | Critical    |
| Persistent storage | Attachments, images             | High        |
| Redis              | Cache, background jobs          | Low         |

The database layer is the only irreplaceable component.

---

## Backup Philosophy

Outline follows a **knowledge-first backup strategy**.

The primary asset is human-readable content, not infrastructure state.

Traditional full-stack backups (containers, Redis, runtime configuration) are considered secondary.

---

## Primary Backup Mechanism

Knowledge preservation relies on native export functionality.

Export format:

* full workspace export
* Markdown files
* packaged as a ZIP archive

The export contains:

* all documents
* collections hierarchy
* document history
* internal links
* Markdown-compatible metadata

Markdown is intentionally chosen for:

* human readability
* vendor independence
* long-term durability
* portability

If Outline disappears entirely, the knowledge remains usable.

---

## Recovery Model

In case of total system loss:

1. Outline is redeployed from scratch
2. Identity integration is restored
3. A new database is initialized
4. Markdown export is re-imported

Infrastructure may change.

Knowledge persists.

---

## Failure Scenarios

| Failure                 | Impact                |
| ----------------------- | --------------------- |
| Database unavailable    | Service unavailable   |
| Database data loss      | Total knowledge loss  |
| Redis unavailable       | Temporary instability |
| Persistent storage loss | Missing attachments   |
| Identity provider down  | Login impossible      |
| Reverse proxy down      | Service unreachable   |

Only database data loss is catastrophic.

---

## Design Principles

Outline is deployed following strict architectural principles:

* identity externalized
* persistence prioritized
* stateless containers
* explicit storage
* minimal exposure surface
* no hidden configuration

All critical state is visible and deliberate.

---

## Operational Notes

* Database migrations must never be skipped during upgrades
* Database backups must be periodically verified
* Persistent storage permissions must remain consistent
* Redis outages are acceptable
* Database outages are not

---

## Summary

Outline provides:

* durable institutional memory
* versioned documentation
* centralized knowledge
* long-term architectural traceability

It is not merely an application.

It is the memory of the homelab.

If Outline disappears, the servers may continue running, but understanding why they exist would not.

---

## Related Resources

Deployment and configuration details are maintained in a separate repository:

* [Outline Docker Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/outline/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
