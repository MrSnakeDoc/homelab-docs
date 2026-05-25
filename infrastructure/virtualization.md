# Virtualization Platform

This document describes how virtualization is used within the homelab and the principles that guide the deployment and management of virtual machines.

---

## Role of the Hypervisor

The entire infrastructure is built on a dedicated virtualization platform that serves as the foundation for all workloads.

The hypervisor is responsible for:

- running virtual machines  
- allocating compute and storage resources  
- providing snapshots and backups  
- isolating workloads  
- monitoring system health  

It is considered a pure infrastructure component.  
No user-facing or application services are ever executed directly on the host system.

---

## Why Virtualization

Virtualization was chosen as the core model for several reasons:

- Full operating system isolation  
- Flexibility to run different types of services  
- Easy experimentation without risk  
- Simplified upgrades and rollbacks  
- Clear separation of concerns  

This approach allows the homelab to behave more like a real-world infrastructure environment rather than a single multi-purpose server.

---

## Network Configuration

The hypervisor uses a simple and consistent networking model.

- A single VLAN-aware bridge is used to connect all virtual machines  
- Network segmentation is enforced through VLAN tagging  
- Traffic separation is handled by the upstream network equipment  
- The hypervisor itself remains neutral and minimal  

This design keeps the virtualization layer straightforward while still allowing strong logical isolation between workloads.

---

## Virtual Machine Design Principles

Virtual machines are created according to a few core rules:

- One main responsibility per VM  
- Minimal services per system  
- Clear network placement based on role  
- Least privilege by default  
- Predictable and repeatable configuration  

Rather than building large multi-purpose servers, the infrastructure favors small, focused systems that are easier to manage and secure.

---

## Typical Workload Roles

Although the exact number and configuration of virtual machines may evolve over time, the infrastructure generally includes systems dedicated to distinct functions such as:

- Application runtime for containerized services  
- DNS and network-level services  
- Edge access and service exposure  
- Development and testing environments  

Each role is isolated from the others to limit the impact of failures or misconfigurations.

---

## Resource Allocation Philosophy

Resources are allocated based on practical needs rather than fixed templates.

The general approach is:

- start small  
- monitor usage  
- scale resources when necessary  

This prevents over-provisioning while keeping the system flexible enough to adapt to new requirements.

---

## Snapshots and Maintenance

Snapshots are used as a safety mechanism during:

- system upgrades  
- configuration changes  
- major experiments  

They allow quick rollback in case of issues, making experimentation safer.

Snapshots are not treated as long-term backups but as temporary protection against mistakes.

---

## Backup Considerations

At the virtualization level, backups are primarily focused on:

- protecting critical virtual machines  
- enabling quick recovery  
- preparing for future off-host backup solutions  

The infrastructure is designed with the intention of progressively improving backup and disaster recovery strategies over time.

---

## Isolation Strategy

Isolation is one of the main benefits of the virtualization platform.

It provides:

- separation between applications and infrastructure  
- independence between different services  
- containment of failures  
- safe testing environments  

By keeping workloads separated, the homelab remains stable even when individual components change or break.

---

## Upgrade and Experimentation Model

One of the main purposes of the virtualization platform is to enable safe learning.

New ideas, tools, or configurations can be tested inside virtual machines without risking the stability of the host or other services.

This encourages:

- experimentation  
- incremental improvements  
- continuous learning  
- confidence in making changes  

---

## Summary

The virtualization layer is the backbone of the homelab.

It provides:

- stability  
- flexibility  
- security  
- recoverability  
- and a realistic environment for practicing infrastructure management  

By keeping the hypervisor clean and using virtual machines for all services, the homelab remains modular, maintainable, and ready for future evolution.