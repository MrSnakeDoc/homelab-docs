# Homepage

Homepage is the central navigation dashboard of the homelab.

It provides a unified entry point to internal services, infrastructure interfaces, and monitoring tools through a single web interface.

Homepage does not host services itself. It acts purely as a **visual aggregation and discovery layer**.

---

## Purpose

Homepage serves as the human-facing index of the infrastructure.

Its primary responsibilities include:

* centralizing access to internal services
* providing a consistent overview of the homelab
* exposing service health and status widgets
* acting as a convenient navigation hub for administrators

Homepage is intentionally limited to **read-only visibility**.

It is not used for:

* authentication or identity management
* access control enforcement
* service configuration
* automation or orchestration
* monitoring evaluation

Homepage provides visibility, not authority.

---

## Architectural Role

Homepage sits outside the operational service path.

It does not proxy traffic and never participates in service delivery.

Instead, it provides a consolidated interface pointing toward services already exposed through the infrastructure's reverse proxy layer.

```
Administrator
   → Homepage dashboard
      → Reverse proxy
         → Target service
```

Homepage improves discoverability and usability without becoming a dependency for service operation.

---

## Deployment Model

Homepage runs as a containerized application within the application services layer.

Key characteristics include:

* containerized execution model
* HTTPS exposure through the reverse proxy layer
* no direct port exposure
* file-based configuration

The service is fully stateless and can be rebuilt at any time without affecting the rest of the infrastructure.

High availability is not required.

Temporary unavailability only removes the navigation dashboard and does not impact infrastructure operation.

---

## Access Model

Access to Homepage follows the same controlled access path used by other internal services.

### Local Access

```
Client Browser
  → Internal DNS
    → Reverse Proxy
      → Homepage
```

### Remote Access

When remote access is enabled, requests are routed through the infrastructure's secure external access layer before reaching the reverse proxy.

Homepage never terminates TLS and never exposes service ports directly.

---

## Data & Visibility Model

Homepage aggregates information about infrastructure services through configuration files and service integrations.

Typical dashboard elements include:

* links to internal services
* service availability indicators
* infrastructure widgets
* monitoring status displays

The service does not control or modify infrastructure components.

It only displays information obtained from other systems.

---

## Failure Scenarios

| Failure                   | Impact                   |
| ------------------------- | ------------------------ |
| Homepage unavailable      | Dashboard inaccessible   |
| Reverse proxy unavailable | Access path interrupted  |
| DNS unavailable           | Service links unresolved |

A failure of Homepage does **not affect service availability**.

Administrators can still access services directly through their individual URLs.

---

## Security Model

The dashboard follows a minimal-privilege design.

Security measures include:

* HTTPS enforced by the reverse proxy
* no direct service ports exposed
* read-only visibility into infrastructure status
* access limited to trusted networks or authenticated remote connections

Homepage does not store service credentials or administrative secrets.

If compromised, the service can expose limited information about the environment but cannot modify infrastructure components.

---

## Design Principles

The dashboard follows several operational principles:

* usability without operational dependency
* read-only visibility
* minimal system privileges
* simple configuration and fast recovery

The service acts as a convenience layer rather than a management system.

```
Administrator
   → Homepage
      → Reverse proxy
         → Service
```

This design improves infrastructure usability while keeping the operational surface minimal.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [Homepage Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/homepage/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
