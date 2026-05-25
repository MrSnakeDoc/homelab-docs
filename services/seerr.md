# Seerr

Seerr is the media request management platform of the homelab.

It provides a user-facing interface allowing users to request movies and TV shows that will later be processed by the automated media pipeline.

The service acts as the interaction layer between media consumers and the media automation infrastructure.

---

## Purpose

Seerr exists to manage user media requests and bridge the gap between users and the automated media stack.

Its primary responsibilities include:

* receiving media requests from users
* managing approval workflows
* synchronizing requests with media servers
* forwarding approved requests to media automation services

Seerr does not perform:

* media downloading
* media indexing
* transcoding
* storage management

Those responsibilities are handled by specialized services in the media automation pipeline.

---

## Architectural Role

Seerr sits at the entry point of the media request pipeline.

Users interact with Seerr to request content, while backend automation services handle the acquisition and organization of media files.

```text
Users
  → Seerr
    → Media automation pipeline
      → Media storage
        → Media server
          → Client playback
```

This separation allows users to interact with the media platform without being exposed to the complexity of the automation stack.

---

## Deployment Model

Seerr runs as a containerized application within the application services layer.

Key characteristics include:

* containerized execution model
* persistent configuration and request database
* internal cache for metadata and images
* integration with media servers for library synchronization

High availability is not implemented.

Temporary unavailability only affects request management and does not impact media playback.

---

## Media Request Flow

Media requests originate from users interacting with the Seerr interface.

Approved requests are forwarded to the media automation stack which handles content acquisition and library management.

```text
User
  → Seerr interface
    → Request approval
      → Media automation services
        → Download and processing
          → Media storage
            → Media server library
```

Seerr itself never downloads or stores media files.

---

## Access Model

Seerr is a user-facing service but is never directly exposed from the host system.

All access is routed through the infrastructure reverse proxy layers.

### Local Access

```text
Client
  → Internal DNS
    → Reverse proxy
      → Seerr
```

### Remote Access

Remote access to Seerr requires a VPN connection to the internal network.

Once connected to the VPN, requests follow the same access path as local clients.

```text
Client
  → VPN
    → Internal DNS
      → Reverse proxy
        → Seerr
```

This model ensures that Seerr remains an **internal-only service** and is never exposed directly to the internet.

All remote users must first authenticate through the VPN before accessing the service.

---

## Authentication Model

Seerr relies on media server authentication for user identity.

Supported mechanisms include:

* Plex authentication
* Jellyfin authentication

User permissions and roles are derived from the upstream media platform.

This approach ensures a single identity source for media access and avoids maintaining a separate user database.

---

## Failure Scenarios

| Failure                         | Impact                                 |
| ------------------------------- | -------------------------------------- |
| Seerr unavailable               | Users cannot submit or manage requests |
| Media server unavailable        | Library synchronization unavailable    |
| Automation pipeline unavailable | Requests remain queued                 |
| Reverse proxy unavailable       | Service inaccessible                   |

Existing media playback remains unaffected.

---

## Security Model

The service follows several security principles:

* no direct internet exposure
* HTTPS enforced through reverse proxy layers
* authentication delegated to trusted media platforms
* containerized execution with minimal privileges
* no direct access to storage or downloader services

If compromised, Seerr could expose request history but cannot control the underlying media automation infrastructure.

---

## Design Principles

The architecture separates **user interaction** from **automation execution**.

Users interact only with Seerr, while backend services handle media acquisition and processing.

```text
User interaction layer
        ↓
Request management
        ↓
Automation pipeline
        ↓
Media storage and playback
```

This separation improves usability while maintaining strong isolation between user-facing services and infrastructure components.

Seerr acts as the **front desk of the media pipeline**, allowing users to request content while the automation stack handles the operational complexity.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [Seerr Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/seerr/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
