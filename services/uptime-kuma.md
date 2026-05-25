# Uptime Kuma

Uptime Kuma is the primary availability monitoring system of the homelab.

It provides continuous visibility into:

* service reachability
* network availability
* DNS resolution
* TLS certificate validity
* overall infrastructure accessibility

Uptime Kuma does not perform analytics, root-cause analysis, or remediation.

Its sole responsibility is to detect failure and notify a human.

---

## Purpose

Uptime Kuma exists to answer one fundamental question:

> Can users actually reach the service?

It is responsible for:

* monitoring service availability
* detecting outages and degradations
* validating DNS resolution paths
* verifying TLS certificate expiration
* triggering alert notifications

It is not responsible for:

* metrics collection
* log aggregation
* performance profiling
* auto-remediation

It observes. It does not intervene.

---

## Architectural Position

Uptime Kuma sits outside the application flow.

It never proxies traffic and never participates in service delivery.

```
Uptime Kuma
   ↓
Active probes
   ↓
Infrastructure & Services
```

It is an external observer.

This separation ensures monitoring failures never impact production systems.

---

## Deployment Model

* Execution model: containerized
* Exposure: HTTPS via reverse proxy
* Authentication: native application accounts
* No direct port exposure

The service is reachable only through the controlled proxy layer.

---

## Architecture Overview

```
Client Browser
  → Reverse Proxy (HTTPS)
    → Uptime Kuma
      → Active probes
        → Target services
```

Monitoring requests follow the same DNS and HTTPS paths used by real users.

A successful check means the full delivery chain is functioning.

---

## Monitoring Scope

Uptime Kuma supervises all critical layers of the homelab.

### Monitor Types Used

* HTTP / HTTPS checks
* TCP port checks
* ICMP ping
* DNS resolution tests
* TLS certificate expiration monitoring

Monitoring is intentionally end-to-end.

Checks validate:

* public hostnames
* internal DNS resolution
* reverse proxy routing
* TLS configuration
* certificate validity

A green status means users can truly access the service.

---

## Coverage Model

Monitoring spans:

* DNS infrastructure
* core applications
* media services
* infrastructure endpoints
* reverse proxy entrypoints
* externally exposed services

This provides visibility from physical network edge to application layer.

---

## Notification System

Alerts are forwarded to a centralized notification channel.

Notification flow:

```
Uptime Kuma
  → Notification service
    → Mobile / Desktop alerts
```

Notifications include:

* downtime alerts
* recovery notifications
* latency anomalies

Uptime Kuma detects.
Notification systems distribute.

---

## Persistence Model

Uptime Kuma stores:

* monitor definitions
* incident history
* status page configuration
* notification settings

If this data is lost:

* monitoring history disappears
* configuration must be recreated
* production services remain unaffected

Monitoring visibility is lost, not infrastructure state.

---

## Failure Scenarios

| Failure                  | Impact               |
| ------------------------ | -------------------- |
| Uptime Kuma down         | Monitoring blind     |
| Service down             | Alert triggered      |
| DNS down                 | DNS alerts triggered |
| Reverse proxy down       | Services unreachable |
| Notification system down | No alert delivery    |
| Network outage           | Multiple alerts      |

Uptime Kuma failure never disrupts infrastructure.

It only removes visibility.

---

## Security Model

* HTTPS enforced
* no direct port exposure
* no service credentials stored
* no elevated privileges
* no ability to modify infrastructure

It is strictly read-only from a network perspective.

If compromised, it can observe, but not control.

---

## Design Principles

Uptime Kuma follows strict operational principles:

* observe, never intervene
* monitor real user paths
* fail loudly
* minimize false positives
* prioritize clarity over complexity

It favors reliability over sophistication.

---

## Related Resources

Deployment and configuration details are maintained in a separate repository:

* [Uptime Kuma Docker Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/uptimekuma/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
