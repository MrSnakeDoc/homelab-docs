# Jellyfin

Jellyfin is the media streaming platform of the homelab.

It provides access to a self-hosted media library and allows users to stream locally stored content from web browsers, mobile devices, and smart TVs.

Jellyfin operates close to the storage layer in order to efficiently access large media files and avoid unnecessary network transfers.

---

## Purpose

Jellyfin serves as the media consumption platform of the homelab.

Its primary responsibilities include:

* indexing and organizing media libraries
* managing metadata and artwork
* streaming media to client devices
* handling playback sessions and transcoding
* exposing APIs for companion services

Jellyfin does not manage media acquisition, download automation, or content requests.

Those responsibilities are handled by other components of the media stack.

---

## Architectural Role

Jellyfin represents the final stage of the media pipeline.

Automation and media management systems prepare and organize content, while Jellyfin provides the interface through which users consume that content.

```text
Media Pipeline
   → Media library
      → Jellyfin
         → Client devices
```

It is a user-facing application rather than an infrastructure component.

---

## Deployment Model

The media server runs as a containerized service directly on the storage host.

Key characteristics include:

* containerized execution model
* persistent configuration and metadata storage
* direct access to media directories
* hardware-accelerated transcoding support

Running the service close to the storage layer minimizes network overhead and improves streaming performance.

High availability is not implemented.
Media availability is tied to the storage system hosting the library.

---

## Media Storage Model

Media files are stored locally on the storage host and mounted directly into the Jellyfin container.

This design provides several advantages:

* direct disk access without network filesystem overhead
* predictable I/O performance
* simplified permission management
* efficient handling of large media files

Media libraries are exposed to Jellyfin as individual directories, allowing clear separation between content categories.

---

## Access Model

Jellyfin is never exposed directly from the storage host.

All external traffic passes through dedicated ingress and reverse proxy layers.

### Local Access

```text
Client Browser
  → Internal DNS
    → Reverse Proxy
      → Jellyfin
```

### Remote Access

When external access is enabled, requests follow a controlled multi-layer access path:

```text
Client
  → External access layer
    → Edge connector
      → Public reverse proxy
        → Jellyfin
```

This model ensures that the storage system hosting the media library is never directly exposed to the internet.

---

## Authentication Model

Jellyfin uses its native authentication system.

Features include:

* local user accounts
* per-user library access control
* user profiles and playback history

Authentication is managed independently from infrastructure identity systems.

---

## Failure Scenarios

| Failure                          | Impact                                  |
| -------------------------------- | --------------------------------------- |
| Jellyfin unavailable             | Media streaming unavailable             |
| Storage host unavailable         | Media library inaccessible              |
| Public access layer unavailable  | External access interrupted             |
| Hardware transcoding unavailable | Playback falls back to direct streaming |

Service failures affect media playback only and do not impact infrastructure operation.

---

## Security Model

The media server follows several security principles:

* no direct exposure of the storage host to the internet
* HTTPS enforced through reverse proxy layers
* containerized execution with minimal privileges
* filesystem access limited to required media directories

The storage system hosting the media library remains isolated from direct internet exposure and only accepts traffic from explicitly authorized internal services.

If compromised, the service could expose media content but would not grant control over infrastructure components.

---

## Design Principles

The architecture prioritizes **data locality**.

Media workloads benefit from running close to storage because media files are large and frequently accessed during playback.

```text
storage → compute

not

compute → storage
```

This design reduces network overhead, improves streaming performance, and simplifies the overall system architecture.

Jellyfin acts as the final consumption layer of the media platform.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [Jellyfin Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/jellyfin/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
