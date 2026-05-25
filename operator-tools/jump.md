# Jump

Jump is a lightweight navigation service used by the homelab operator to quickly access self-hosted services.

It transforms the browser address bar into a fuzzy launcher, allowing services to be accessed using short keywords instead of full URLs, bookmarks, or dashboards.

Jump resolves user intent and performs HTTP redirects to the appropriate service.

It does not proxy traffic or participate in application delivery.

---

## Purpose

Jump exists to provide fast, keyboard-driven navigation across self-hosted services.

Its primary responsibilities include:

* translating fuzzy user queries into service URLs
* redirecting the browser to the appropriate destination
* centralizing navigation logic for infrastructure services
* reusing existing configuration sources

Jump does not perform:

* request proxying
* TLS termination
* authentication
* traffic forwarding
* service availability checks

Once a redirect is issued, Jump is no longer involved in the request path.

---

## Architectural Role

Jump acts as the **navigation layer** of the homelab.

It sits between the user and the infrastructure services, resolving short queries into service destinations.

```text
Browser
  → Jump
    → Reverse proxy
      → Target service
```

Jump only resolves the destination and issues an HTTP redirect.

All application traffic bypasses Jump entirely.

---

## Deployment Model

Jump runs as a containerized application within the internal services layer.

Key characteristics include:

* stateless service architecture
* configuration through environment variables
* service metadata loaded from external configuration files
* optional Redis integration for caching and usage learning

Redis is used as an optimization layer and is not required for operation.

If Redis is unavailable, the service continues functioning without caching or usage learning.

---

## Configuration Sources

Jump avoids maintaining its own service registry.

Instead, it reuses configuration already maintained elsewhere in the infrastructure.

### Service Definitions

Service metadata is loaded from an existing dashboard configuration file.

This file acts as the single source of truth for service names and URLs.

Changes made to the dashboard configuration automatically apply to Jump.

### External Bookmarks

Jump can also resolve external URLs using a dedicated bookmark configuration file.

These entries are isolated from internal service searches and are only used when explicitly requested.

---

## Query Routing Model

Jump isolates search logic into distinct routing realms.

| Prefix   | Realm      | Description                 |
| -------- | ---------- | --------------------------- |
| *(none)* | Services   | Self-hosted services        |
| `.`      | Subdomains | Explicit subdomain matching |
| `/`      | Internal   | Jump internal endpoints     |
| `@`      | Bookmarks  | External URLs               |

Search realms are strictly isolated.

Queries targeting one realm never fall back to another.

This design prevents ambiguous routing and ensures predictable behavior.

---

## Access Model

Jump is a browser-facing service but does not expose internal infrastructure directly.

All traffic passes through the infrastructure reverse proxy layers.

### Local Access

```text
Client
  → Internal DNS
    → Reverse proxy
      → Jump
```

Once a query is resolved, the browser is redirected to the target service.

---

## Failure Scenarios

| Failure                    | Impact                           |
| -------------------------- | -------------------------------- |
| Jump unavailable           | Navigation shortcuts unavailable |
| Redis unavailable          | No caching or usage learning     |
| Reverse proxy unavailable  | Jump inaccessible                |
| Target service unavailable | Redirect fails                   |

Jump failures do **not impact infrastructure services**.

Only the navigation convenience layer is affected.

---

## Security Model

The navigation service follows several security principles:

* redirect targets restricted to allowed domains
* TLS validation performed before issuing redirects
* administrative endpoints restricted to trusted networks
* containerized execution with minimal privileges
* no direct involvement in application traffic

These controls prevent open redirect vulnerabilities and ensure only trusted services can be targeted.

---

## Design Principles

Jump is designed around a small set of operational principles:

* no duplication of configuration
* explicit redirect behavior
* strict isolation of search domains
* stateless core architecture
* optional performance optimizations

The service intentionally avoids proxying or participating in application traffic.

Jump acts purely as a **navigation resolver**, translating human queries into service locations.

---

## Related Resources

The source code and implementation details of Jump are available in the project repository:

- [Jump Blueprint](https://github.com/MrSnakeDoc/jump-blueprint)

Jump is intentionally designed as an opinionated personal tool.

The repository contains the reference implementation, which can be adapted or forked to fit other self-hosted environments.

This document focuses on the architectural role of the service within the homelab rather than its specific implementation.