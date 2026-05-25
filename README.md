# Homelab Documentation

This repository contains the documentation for my personal homelab infrastructure.  
It serves as a public knowledge base describing the architecture, services, configurations, and operational practices used to run a self-hosted environment.

The homelab is both a learning platform and a practical infrastructure used daily to host personal services, experiment with new technologies, and improve system administration skills.

---

## Purpose

The main objectives of this homelab are:

- **Skill Development**  
  Improve knowledge in cloud infrastructure, networking, system administration, and DevOps practices.

- **Experimentation**  
  Test new tools, architectures, and concepts in a controlled environment without impacting production systems.

- **Self-Hosting**  
  Run personal services in a reliable and secure way, with full control over data and infrastructure.

- **Automation and Monitoring**  
  Build repeatable deployments, automated workflows, and proper observability across the stack.

---

## Infrastructure Overview

The homelab is built around a compact but capable hardware setup, running a mix of virtual machines and containerized workloads.

Core components include:

- A Proxmox hypervisor hosting multiple virtual machines  
- Docker-based application servers  
- Network services such as DNS, reverse proxy, and VPN  
- Monitoring and alerting tools  
- Storage and media services

More details about the physical and virtual architecture can be found here:

- [Overview](./infrastructure/overview.md)
- [Virtualization](./infrastructure/virtualization.md)

More details about the network architecture can be found here:

- [Overview](./network/overview.md)
- [Security Model](./network/security-model.md)

---

## Hosted Services

The following services are currently running in the homelab:

- [AdGuard Home](./services/adguard-home.md)
- [Authentik](./services/authentik.md)
- [Cloudflared](./services/cloudflared.md)
- [Forgejo](./services/forgejo.md)
- [Gotify](./services/gotify.md)
- [Homepage](./services/homepage.md)
- [Jellyfin](./services/jellyfin.md)
- [Seerr](./services/seerr.md)
- [Jump](./operator-tools/jump.md)
- [Mealie](./services/mealie.md)
- [Outline](./services/outline.md)
- [Postgres and PgAdmin](./services/postgres-pgadmin.md)
- [Redis and Redis Insight](./services/redis-redis-insight.md)
- [Traefik](./services/traefik.md)
- [Uptime Kuma](./services/uptime-kuma.md)
- [Woodpecker](./services/woodpecker.md)

Each service is documented individually with details about deployment, configuration, and purpose.

---

## Long-Term Vision

This homelab has evolved from a simple collection of services into a more structured infrastructure focused on reliability and operational discipline.

The next major goals include:

- Reducing single points of failure  
- Improving backup and disaster recovery strategies  
- Increasing automation and reproducibility  
- Implementing better security practices  
- Exploring high availability patterns

A key upcoming step is experimenting with **Kubernetes (K3S)** as an orchestration layer to:

- Improve service resilience  
- Enable controlled updates and rollouts  
- Allow better workload distribution  
- Move closer to real-world production architectures

The objective is not to adopt Kubernetes for its own sake, but to learn how to design systems that continue to operate correctly even when components fail.

---

## Structure of This Repository

This repository is organized as a central place for all homelab-related documentation:

- architecture diagrams  
- service descriptions  
- configuration examples  
- operational procedures  
- troubleshooting guides  

It is meant to be continuously updated as the infrastructure evolves.

---

This project reflects an ongoing journey in self-hosting, automation, and infrastructure engineering.
