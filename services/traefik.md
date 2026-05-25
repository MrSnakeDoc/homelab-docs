# Traefik

Traefik is the central traffic routing and exposure system of the homelab.

It is not deployed as a single reverse proxy.
Instead, it operates as a **distributed, multi-layer proxy architecture**, where each instance serves a strictly limited purpose within a defined trust boundary.

This design enforces separation between:

* public internet entry points
* edge transport handling
* internal application routing
* infrastructure-critical services

The architecture favors isolation and containment over simplicity.

---

## Purpose

Traefik is responsible for:

* routing inbound and internal traffic
* enforcing HTTPS
* managing TLS certificates
* providing service discovery
* applying middleware and access control
* exposing selected services safely

It acts as the controlled gateway between users and applications.

---

## Architectural Philosophy

Rather than centralizing all responsibilities into a single reverse proxy, Traefik is divided into multiple instances.

Each instance:

* operates in a different trust zone
* has minimal permissions
* performs a single responsibility
* does not share certificates or internal state with others

This prevents:

* certificate leakage
* HTTP inspection in semi-trusted zones
* unintended Docker provider exposure
* privilege escalation across layers

The system is designed so that compromise in one layer does not grant access to another.

---

## Traffic Model

### External Traffic

External traffic follows a layered pipeline:

```
Client
  → Public DNS
    → Secure tunnel
      → Edge transport Traefik proxy (TCP only)
        → Internal application Traefik proxy
          → Service
```

Key properties:

* TLS is not terminated at the edge transport layer
* Edge instances cannot inspect HTTP traffic
* Application certificates exist only within trusted internal zones
* No service is directly reachable from the internet

The edge layer only forwards encrypted traffic based on SNI routing.

---

### Internal Traffic

Internal traffic uses a separate, fully trusted path:

```
Client (LAN / VPN)
  → Internal DNS
    → Local Traefik instance
      → Service
```

This allows:

* direct and fast local access
* independence from external providers
* simplified routing for trusted networks

External and internal exposure models are intentionally separated.

---

## Trust Zones

The infrastructure is divided into explicit trust boundaries.

At a high level:

* Public and tunnel layers cannot decrypt application traffic
* Edge transport layers cannot access TLS private keys
* Internal application proxies handle TLS termination
* Services receive traffic only after policy enforcement

TLS private keys are stored only in trusted application zones and are never replicated outward.

---

## Proxy Roles

### Edge Transport Layer

This layer:

* accepts inbound encrypted traffic
* performs TCP routing based on SNI
* does not terminate TLS for applications
* has no HTTP awareness

Its purpose is strictly transport-level forwarding.

Even in case of compromise, application traffic remains encrypted.

---

### Application Routing Layer

This layer is the core routing engine.

Responsibilities include:

* TLS termination
* ACME certificate management (DNS-based validation)
* dynamic service discovery
* HTTP routing rules
* middleware enforcement
* authentication integration

All application-level security logic is enforced here.

Only explicitly labeled services attached to the correct internal network can be exposed.

No container publishes ports directly.

---

### Infrastructure-Specific Proxies

Certain infrastructure-critical services run behind dedicated Traefik instances.

Design goals:

* prevent DNS or infrastructure failure from depending on application routing
* isolate management interfaces
* avoid cascading failures

These instances are intentionally scoped and independent from application workloads.

---

## Certificate Strategy

All Traefik instances use DNS-based certificate validation.

### Reasons

* no inbound port exposure required
* compatible with tunnel-based internet exposure
* usable across isolated network segments
* does not rely on HTTP challenge reachability

### Certificate Ownership Model

Certificates are not shared between instances.

Each Traefik instance manages only the certificates required for its own responsibility:

* Edge instances manage certificates only for their own dashboards
* Application routing instances manage service certificates
* Infrastructure-specific instances manage their own administrative interfaces

Application certificates never leave trusted zones.

---

## Exposure Rules

The following principles are globally enforced:

* no service exposes ports directly
* no virtual machine is directly internet-reachable
* no application bypasses Traefik
* no certificate private key exists in semi-trusted zones
* no routing occurs without explicit configuration

All exposure paths are intentional and auditable.

---

## Network Access Policies

In addition to routing and TLS enforcement, Traefik is used to implement **network-level access policies** for application services.

Access to services is restricted using IP-based middleware rules applied at the proxy layer.

These policies ensure that applications are only reachable from explicitly authorized network segments.

Typical allowed networks include:

* trusted administrative devices
* VPN client networks
* internal infrastructure services
* container networks used for inter-service communication

Certain services may also permit access from specific devices or controlled edge systems when required.

Requests originating from unauthorized networks are rejected before reaching the application.

---

## Policy Enforcement Model

Access restrictions are implemented using Traefik middleware attached directly to service routers.

These middleware rules act as a **network access filter** applied before request forwarding.

```text
Client
  → Traefik
    → Access policy middleware
      → Service
```

If the client IP address is not part of an authorized range, the request is denied and never reaches the application.

---

## Service Access Classes

Services are grouped into different exposure categories depending on their required accessibility.

Typical categories include:

### Internal Services

Accessible only from trusted networks and VPN clients.

Used for:

* administrative interfaces
* infrastructure services
* internal tooling

---

### Restricted Services

Accessible from trusted networks and selected infrastructure components.

Used when controlled cross-network access is required.

---

### Device-Specific Services

Certain services may permit access from specific devices when necessary.

This is typically used for devices that cannot use VPN connectivity but require controlled access to a limited set of services.

### Public Services

Accessible from the internet through the edge transport layer. Strictly limited to services that require public exposure. All public services are protected by TLS and have explicit routing rules.

These services are the exception rather than the norm. They are carefully audited to ensure they do not expose sensitive functionality.

Public services remain accessible through the internal routing layer for trusted users connected via VPN or local networks. External access always passes through the edge transport layer.

---

## Security Benefits

This model provides several advantages:

* applications never receive traffic from unauthorized networks
* infrastructure services remain inaccessible to untrusted segments
* access rules remain centralized and auditable
* policy enforcement occurs before application processing

The proxy therefore acts as both a routing system and a **network access control layer**.

---

## Failure Model

The system is layered so failures remain contained:

* If the edge layer fails, external access is unavailable
* If the application routing layer fails, applications are unreachable
* If DNS fails, name resolution is unavailable
* If certificate renewal fails, existing certificates remain valid

Failures do not automatically cascade across layers.

---

## Security Guarantees

By design, the following scenarios are prevented:

* decrypting application traffic at the edge
* exposing container orchestration interfaces publicly
* leaking TLS private keys from semi-trusted zones
* unintentionally exposing services
* routing traffic without explicit declaration

Security boundaries are structural, not optional.

---

## Summary

Traefik in this homelab is not simply a reverse proxy.

It is a **layered traffic control system** implementing:

* strict trust separation
* encrypted end-to-end delivery
* minimal exposure
* explicit routing declarations
* controlled certificate ownership

The architecture trades simplicity for clarity and containment.

Each layer knows only what it must know, and nothing more.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [Traefik Docker Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/traefik/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
