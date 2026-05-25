# Gotify

Gotify is the centralized notification service of the homelab.

It provides real-time push notifications to administrative devices, allowing infrastructure services and automation tools to deliver operational alerts without relying on external messaging providers.

The service acts as the final delivery step of the observability pipeline.

---

## Purpose

Gotify exists to deliver infrastructure events to a human operator.

Its primary responsibilities include:

* Infrastructure services and automation components generate events which are forwarded to the notification service
* delivering real-time notifications to administrative devices
* providing a simple and reliable notification channel

It is intentionally limited to **notification delivery only**.

Gotify does not perform:

* monitoring
* alert evaluation
* log aggregation
* message brokering

Those responsibilities are handled by dedicated systems elsewhere in the infrastructure.

---

## Architectural Role

Gotify sits at the end of the alerting chain.

Infrastructure components generate events which are forwarded to the notification service using a simple HTTP-based API.

```
Infrastructure Services
   → Alert / Event
      → Gotify API
         → Push notification
            → Administrator devices
```

The service never participates in application or infrastructure traffic delivery.

It exists solely to ensure that operational events reach a human operator.

---

## Deployment Model

The notification service runs as a containerized application within the public application network segment.

Key characteristics:

* containerized execution model
* HTTPS exposure through the reverse proxy layer
* no direct port exposure
* token-based API access for publishing notifications

Administrative access to the web interface follows the same restricted exposure model used by other publicly accessible services.

---

## Notification Flow

Notifications are generated programmatically by infrastructure services and automation tools.

```
Service / Automation
  → Notification API
    → Gotify Server
      → Push delivery
        → Mobile / Desktop clients
```

Applications authenticate using dedicated tokens which define the permissions and scope of the notifications they are allowed to send.

This design allows services to publish alerts without requiring user-level credentials.

---

## Authentication Model

Gotify uses its native authentication system.

Security relies on:

* authenticated user accounts
* application-specific API tokens
* scoped publishing permissions

User registration is disabled to prevent unauthorized access.

Only explicitly created accounts and application tokens are allowed to interact with the system.

---

## Failure Scenarios

| Failure                          | Impact                                            |
| -------------------------------- | ------------------------------------------------- |
| Notification service unavailable | Alerts cannot be delivered                        |
| Network disruption               | Notifications delayed or lost                     |
| Client devices offline           | Notifications delivered once connectivity returns |

A failure of the notification service does **not impact infrastructure operation**.

It only removes the ability to deliver alerts to administrators.

---

## Security Model

The notification system follows several security principles:

* HTTPS is enforced for all access
* no direct service ports are exposed
* API access requires authentication tokens
* user registration is disabled
* administrative access is restricted to authenticated and explicitly authorized endpoints

The service is treated as a restricted public-facing utility component with minimal privileges.

If compromised, it can send notifications but cannot control or modify infrastructure components.

---

## Design Principles

The notification system follows several operational principles:

* simplicity over complexity
* reliable event delivery
* minimal external dependencies
* clear separation between monitoring and alert delivery

The service represents the final stage of the observability pipeline:

```
Monitoring / Automation
    → Alert generation
        → Notification service
            → Human operator
```

This separation ensures monitoring systems remain independent from notification delivery mechanisms.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

- [Gotify Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/gotify/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
