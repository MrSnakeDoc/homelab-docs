# Forgejo

Forgejo is the self-hosted Git platform of the homelab.

It provides source code management, repository hosting, and collaborative development features for internal projects. The service enables version control workflows while keeping all code and project data fully under local control.

Forgejo acts as the **source control layer** of the development platform and integrates with automation systems to trigger build and deployment pipelines.

---

## Purpose

Forgejo exists to provide centralized management of source code repositories.

Its primary responsibilities include:

* hosting Git repositories
* managing project collaboration
* handling issues and pull requests
* storing project documentation
* triggering automation workflows

It serves as the central entry point for development activity inside the homelab.

Forgejo does not perform:

* continuous integration
* build orchestration
* artifact management
* deployment automation

Those responsibilities are handled by dedicated automation services.

---

## Architectural Role

Forgejo represents the **source control layer** of the development environment.

Developers interact with repositories through standard Git workflows while automation systems react to repository events.

```text
Developer
   → Forgejo
      → Git repositories
```

Repository events such as commits or pull requests can trigger external automation pipelines.

```text
Git push
   → Forgejo
      → Webhook
         → CI system
```

This separation ensures that version control remains independent from build and deployment systems.

---

## Deployment Model

The Git service runs as a containerized application within the application services layer.

Key characteristics include:

* containerized execution
* persistent repository storage
* relational database backend
* HTTPS exposure through the reverse proxy layer
* SSH support for Git operations

The service is considered a **stateful application** because it stores repositories and project metadata.

---

## Repository Storage Model

Git repositories are stored locally on the application host and persist independently from the container lifecycle.

Persistent storage includes:

* Git repository data
* repository metadata
* configuration
* attachments and project assets

This ensures that repositories remain durable even if the application container is replaced or upgraded.

---

## Access Model

Developers can interact with repositories using two standard Git access methods.

### Web Interface

The web interface provides repository browsing, issue tracking, and project management.

```text
Browser
  → Internal DNS
    → Reverse proxy
      → Forgejo
```

### Git Operations

Git clients interact with repositories using either HTTPS or SSH.

```text
Git client
  → Reverse proxy
    → Forgejo
```

SSH access allows developers to push and pull repositories using secure key-based authentication.

Both access methods are routed through the reverse proxy layers and no application ports are exposed directly.

---

## Authentication Model

Forgejo uses its native authentication system.

Features include:

* local user accounts
* SSH key authentication
* repository-level permissions
* organization and team management

User registration can be restricted to prevent unauthorized account creation.

Authentication is intentionally independent from infrastructure identity providers.

---

## Failure Scenarios

| Failure                   | Impact                         |
| ------------------------- | ------------------------------ |
| Forgejo unavailable       | Repository access unavailable  |
| Database unavailable      | Application cannot start       |
| Storage unavailable       | Repository data inaccessible   |
| Reverse proxy unavailable | Web and Git access interrupted |

Failures affect development workflows but do not impact infrastructure services.

---

## Security Model

The Git service follows several security principles:

* HTTPS enforced through reverse proxy layers
* SSH access routed through controlled entrypoints
* no direct container port exposure
* database access restricted to internal networks
* repository data stored on persistent volumes

If compromised, the service could expose source code but would not provide control over infrastructure components.

---

## Design Principles

The architecture follows several development platform principles:

* centralized version control
* separation between code hosting and automation
* durable repository storage
* secure developer access
* minimal operational complexity

Forgejo acts as the **foundation of the internal development platform**.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

* [Forgejo Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/forgejo/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
