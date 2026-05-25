# Security Model

This document describes the security philosophy and access control model used within the homelab.

The entire design is based on a simple idea:

**Nothing is trusted by default.**

All communication is explicitly allowed, never implicitly assumed.

---

## Core Philosophy

The security model follows these principles:

- Deny-by-default at every layer  
- Strong network segmentation  
- Explicit firewall rules  
- Minimal administrative exposure  
- Centralized authentication  
- Layered defense mechanisms  

The objective is not theoretical perfection, but practical and consistent security.

---

## Trust Zones

The network is divided into multiple trust zones, each with a clearly defined purpose and level of confidence.

### External Zone – Untrusted

- Represents the public internet
- Considered completely hostile
- No internal resource is directly reachable

### Edge Zone – Restricted

- Acts as the controlled ingress layer between the internet and internal networks
- Does not host application workloads
- Can only forward traffic to explicitly authorized services in the Public Zone
- Has no direct access to infrastructure or trusted internal networks

### Public Zone – Semi-Trusted

- Hosts internet-facing applications and reverse proxies
- Considered higher risk due to external exposure
- Isolated from infrastructure and trusted internal networks
- Outbound access is restricted to explicitly authorized services only
- Administrative access is limited to trusted endpoints and approved protocols

### Internal / Trusted Zone

- Used by personal devices  
- Allowed to access selected services  
- Does not have unrestricted access to infrastructure  

### Infrastructure Zone

- Contains core systems and servers  
- Isolated from user and untrusted networks  
- Components do not implicitly trust each other  

### IoT Zone – Untrusted

- Dedicated to potentially insecure devices  
- No access to internal services
- Devices on the trusted network can initiate connections to IoT devices, but IoT devices cannot initiate connections toward internal networks
- Internet access only

### Development Zone – Semi-Trusted

- Dedicated and isolated environment for testing and development workloads  
- No access to internal infrastructure or production services  
- Limited inbound access allowed only from trusted administrative devices  

### Management Zone – Trusted

- Used exclusively for management interfaces and administrative access to network and infrastructure devices  
- Access restricted to highly trusted and authenticated endpoints only  

### Guest Zone – Untrusted

- Fully isolated network  
- No access to any internal resource  

---

## Default Network Policy

The entire network operates under strict default rules:

- All VLANs are isolated from each other  
- Inter-zone communication is blocked by default  
- Access is only allowed through explicit firewall rules  
- No implicit trust exists between networks  

This prevents accidental exposure and lateral movement.

---

## Firewall Strategy

Security is enforced at multiple layers:

### Gateway Firewall

- Controls inter-VLAN routing  
- Defines which networks may communicate  
- Filters east–west traffic  
- Restricts access to management interfaces  

### Host Firewalls

- Every virtual machine follows a deny-by-default policy  
- Only required ports are opened  
- Inbound access is explicitly defined  

### Reverse Proxies

- Act as controlled access points  
- Terminate TLS  
- Enforce authentication  
- Hide internal service details  

---

## Wireless Access Control

Access to all wireless networks is restricted using additional device-level controls.

- Wi-Fi networks are protected with strong authentication credentials (WPA3 with a 30 characters randomized password)
- In addition to the Wi-Fi password, MAC address filtering is enforced
- Only explicitly authorized devices are allowed to join wireless networks  
- Possessing the Wi-Fi password alone is not sufficient to gain access  

This policy applies to both:

- the trusted internal Wi-Fi network  
- the dedicated IoT Wi-Fi network  

New devices must be manually approved before they are able to connect.  
This measure adds an extra layer of protection against unauthorized access, even in the event of credential leakage.

MAC filtering is used as a complementary security mechanism and does not replace encryption or network segmentation.

The only exception to this rule is the guest Wi-Fi network, which does not use MAC address filtering but remains fully isolated from all internal networks and still follows the same principles of strong encryption and authentication.

---

## Administrative Access Model

Administrative access is intentionally restrictive.

- Critical infrastructure management interfaces are never directly exposed to the internet  
- Remote administration is allowed exclusively via trusted networks, secure tunnels, or explicitly authorized devices
- Publicly accessible web interfaces are limited to selected services and protected through authentication and HTTPS
- SSH access is restricted to trusted administrative endpoints only  
- Password authentication is disabled wherever possible  
- Key-based authentication is mandatory for administrative systems  

Different device types have different levels of access, ensuring that high-privilege operations can only be performed from secure and trusted endpoints.

---

## Remote Access

All remote connectivity relies on a single secure entry point:

- A VPN connection is required for administration  
- All traffic remains encrypted  
- Internal DNS filtering is preserved  
- Access rules remain consistent regardless of location  

There is no direct port forwarding, dynamic DNS exposure, or public SSH access.

---

## Service Exposure Restrictions

External access to services follows strict rules:

- Only selected applications can be reached from the internet  
- All exposure goes through the edge layer  
- Internal networks are never directly reachable  
- HTTPS is enforced for every exposed service  
- Authentication is required whenever possible  

This ensures that even publicly reachable services remain protected.

---

## Public Network Rules

Public-facing services are isolated in a dedicated network segment with strict communication policies.

- No direct access to infrastructure networks  
- No unrestricted outbound access  
- Access to internal services is explicitly limited and filtered  
- Administrative access is restricted to trusted devices only  
- Only explicitly authorized ports and destinations are allowed  
- Public workloads are treated as higher-risk systems due to external exposure  

This design limits lateral movement and reduces the impact of a potential service compromise.

---

## Development Network Rules

The development environment is intentionally isolated:

- Cannot access internal infrastructure  
- Limited inbound access from trusted devices  
- Used only for testing and experimentation  

This prevents experimental workloads from affecting production services.

---

## IoT Network Rules

IoT devices are treated as high risk:

- No access to internal networks  
- No ability to initiate internal connections  
- Internet access only  
- Strictly segmented from trusted devices  

This limits the impact of compromised or insecure hardware.

---

## Identity and Authentication

Authentication practices are standardized:

- Unique credentials per system  
- SSH keys instead of passwords  
- Centralized authentication where possible  
- Multi-factor authentication for critical services  

No credentials are shared between hosts.

---

## Break-Glass Procedures

Emergency access mechanisms exist but are tightly controlled.

These mechanisms are:

- documented  
- rarely used  
- limited to infrastructure recovery scenarios  

Their sole purpose is to avoid total lockout while keeping normal access paths secure.

---

## Design Guarantees

By design, the following are impossible:

- Direct infrastructure-level administrative access from the internet  
- Public SSH exposure  
- Uncontrolled inter-VLAN communication  
- Infrastructure access from untrusted devices  

All access paths are explicit, authenticated, and logged.

---

## Scope

This security model reflects the current state of the homelab.

It is designed to be practical, maintainable, and realistic while still following strong zero-trust inspired principles.

Future architectural changes may refine these rules, but the core philosophy will remain the same:
**explicit trust, minimal exposure, and controlled access.**
