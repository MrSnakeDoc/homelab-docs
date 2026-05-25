# Mealie

Mealie is the recipe management and meal planning platform of the homelab.

It provides a web interface for storing, organizing, and searching cooking recipes while maintaining a structured long-term personal food knowledge base.

Within the infrastructure, Mealie is treated as a **personal knowledge service**, similar in role to documentation tools but focused on culinary data.

---

## Purpose

Mealie exists to centralize personal cooking knowledge and provide a structured system for managing recipes and meal planning.

Its primary responsibilities include:

* storing personal and curated recipes
* organizing culinary knowledge in a structured format
* providing a searchable recipe catalog
* supporting meal planning workflows

The service is intentionally limited to **personal knowledge management**.

Mealie does not perform:

* public recipe publishing
* anonymous access
* social networking features
* high-availability workloads

It is designed for private household use rather than public distribution.

---

## Architectural Role

Mealie acts as the **knowledge layer for culinary information** within the homelab.

It allows users to capture, organize, and retrieve cooking knowledge while remaining independent from other application services.

```text
User
  → Mealie
    → Recipe database
      → Personal knowledge retrieval
```

Unlike media or infrastructure services, Mealie focuses on **long-term knowledge storage** rather than automation pipelines.

---

## Deployment Model

The recipe platform runs as a containerized application within the public application network segment.

Key characteristics include:

* containerized execution model
* persistent application database
* local storage for media and attachments
* moderate resource usage

High availability is not implemented.

Temporary service downtime only affects recipe access and does not impact other infrastructure components.

---

## Data Storage Model

Mealie stores its data using two persistence layers.

### Application Database

The primary application data is stored in a relational database.

This database contains:

* recipe metadata
* categories and tags
* user accounts
* application configuration

Using a relational database ensures data consistency and long-term reliability for structured recipe information.

### Local Media Storage

Media assets associated with recipes are stored in a persistent storage volume.

This storage typically contains:

* recipe images
* attachments
* import and export artifacts

The separation between database metadata and media storage allows efficient management of structured data and large files.

---

## Access Model

Mealie is a user-facing service but is never exposed directly from the host system.

All access passes through the infrastructure reverse proxy layers.

### Local Access

```text
Client
  → Internal DNS
    → Reverse proxy
      → Mealie
```

### Remote Access

When external access is enabled, requests follow the controlled external access path of the infrastructure.

```text
Client
  → External access layer
    → Edge connector
      → Public reverse proxy
        → Mealie
```

This model ensures that the application is exposed only through controlled ingress and reverse proxy layers.

---

## Authentication Model

Mealie uses its native authentication system.

Security relies on:

* manually created user accounts
* disabled public registration
* no anonymous access

External identity providers are intentionally not required.

This design keeps the service operational even if centralized identity systems become unavailable.

---

## Failure Scenarios

| Failure                   | Impact                                |
| ------------------------- | ------------------------------------- |
| Mealie unavailable        | Recipes and meal planning unavailable |
| Database unavailable      | Application cannot operate            |
| Storage unavailable       | Recipe media inaccessible             |
| Reverse proxy unavailable | Service inaccessible                  |

Failures affect only recipe access and do not impact other infrastructure services.

---

## Security Model

The recipe platform follows several security principles:

* HTTPS enforced through reverse proxy layers
* no direct container port exposure
* restricted account creation
* containerized execution with minimal privileges
* database access restricted to the local application stack

The service is treated as a **private household application** rather than a public platform.

---

## Design Principles

The architecture prioritizes **long-term knowledge preservation**.

Recipes represent personal knowledge that evolves over time through experimentation, adaptation, and refinement.

```text
Personal knowledge
      ↓
Structured storage
      ↓
Searchable recipes
      ↓
Practical daily usage
```

This design ensures that culinary knowledge remains organized, searchable, and portable across the lifetime of the homelab.

Mealie acts as the **culinary knowledge base** of the infrastructure.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [Mealie Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/mealie/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
