# Network Overview

This document describes the general network architecture of the homelab, its segmentation model, and the main traffic flows.

The network is designed around a centralized routing approach with clear trust boundaries, strict segmentation, and controlled service exposure.

---

## Core Design Principles

The network follows several key principles:

- Strong segmentation using VLANs  
- Deny-by-default communication model  
- Explicit and minimal firewall rules  
- Centralized routing through a single gateway  
- No direct exposure of internal services  
- Dedicated zones based on function and trust level  

The goal is to create a predictable and secure environment where services and devices are isolated according to their purpose.

---

## Core Network Components

The main components of the network are:

- **Central Router and Gateway**  
  Handles inter-VLAN routing, DHCP, firewall rules, and internet connectivity.

- **Wireless Infrastructure**  
  Wireless networks are mapped to specific VLANs to maintain the same isolation rules as wired devices.

- **Virtualization Host**  
  The hypervisor connects to the network using VLAN-aware bridges, allowing virtual machines to be placed in dedicated network segments.

- **DNS Infrastructure**  
  Dedicated DNS servers provide name resolution, filtering, and local service discovery.

- **Reverse Proxy Layer**  
  All HTTP/HTTPS traffic is routed through reverse proxies rather than directly to services.
  
- **Management VLAN**  
  A dedicated network segment used exclusively for management interfaces and administrative access to network and infrastructure devices.

- **Guest VLAN**  
  A fully isolated network intended for temporary or external users. This network is heavily restricted and has no access to internal resources or services.

---

## Network Segmentation

The network is logically divided into multiple zones, each with a specific role and level of trust.

### Main Zones

- **Trusted Network**  
  Used by trusted personal devices and administrative workstations.

- **Infrastructure Network**
  Dedicated segment for core components such as virtualization host and storage systems.

- **Services Network**
  Hosts internal applications and shared infrastructure services not directly accessible from the internet.

- **Public Network**
  Host applications that internet-facing. This network is not exposed directly to the interne and can only be reached through the Edge Network. 

- **Edge Network**  
  Serves as the controlled ingress layer for public services. Responsible for traffic forwarding, and external connectivity.

- **Development and Testing Networks** 
  Isolated environments for experimentation and development work.

- **Untrusted Networks**
  Reserved for IoT devices and guest access, with heavy restrictions and no access to internal resources.

Each zone is isolated from the others unless explicit firewall rules allow communication.

---

## Traffic Flow Philosophy

Traffic within the homelab follows strict and predictable paths:

- User devices never access service networks directly  
- Untrusted networks cannot reach internal services  
- Core infrastructure is protected from lateral movement  
- External access is always funneled through a dedicated edge layer  
- All application access is routed through reverse proxies

This layered approach reduces the attack surface and limits the impact of misconfigurations or compromised devices.

---

## Internet Connectivity

Outbound and inbound internet connectivity is fully managed by the main router.

- Administrative services are never exposed to the public internet
- No internal subnet is directly exposed 
- No port forwarding is used for administrative access
- All external service exposure is centralized  
- Remote access relies on secure and authenticated mechanisms  

The internet-facing model is intentionally minimal to keep the homelab secure by design.

---

## Service Exposure Model

Application services are never published directly.

Access follows these general rules:

- Services are only reachable through reverse proxies  
- No container or virtual machine publishes ports to the internet  
- External access is mediated through a dedicated edge layer  
- HTTPS is enforced for all exposed services  
- Authentication is required whenever possible  

This model ensures that internal services remain protected even when they need to be accessible remotely.

---

## Local Access Flow

For users inside the local network, service access follows this pattern:

```
Client Device
→ Internal DNS
→ Reverse Proxy
→ Target Service
```

DNS resolution and HTTPS routing are centralized to provide consistent behavior across all services.

---

## External Access Flow

External access is routed through multiple layers to avoid direct exposure:

```
Client on Internet
→ Public DNS
→ Secure Tunnel
→ Edge connector
→ Internal Reverse Proxy (public network)
→ Target Service
```

Only the edge layer is ever reachable from the internet. Internal networks and services remain hidden and protected.

---

## Summary

The network architecture focuses on:

- clear segmentation  
- explicit trust boundaries  
- minimal exposure  
- centralized control  
- defense in depth  

This approach provides a secure, flexible, and maintainable foundation for hosting self-managed services.
