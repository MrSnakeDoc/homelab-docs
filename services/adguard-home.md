# AdGuard Home

This document describes the design and deployment of AdGuard Home within the homelab.

AdGuard Home provides network-wide DNS resolution, filtering, and ad blocking for all internal networks.

DNS is treated as a core infrastructure service and is therefore isolated from application workloads, container platforms, and user environments.

---

## Purpose

AdGuard Home acts as the central DNS layer for the entire homelab.

Its main responsibilities include:

- Network-wide ad and tracker blocking  
- Malicious domain filtering  
- Privacy and security protections  
- Centralized DNS visibility and statistics  
- Secure upstream DNS resolution  
- Internal domain name resolution  

All devices rely on AdGuard for DNS queries.  
No client communicates directly with public DNS providers.

---

## High-Level Architecture

The DNS infrastructure is built around two independent AdGuard Home instances to provide redundancy and reliability.

### Design Principles

- Dedicated virtual machines for DNS  
- No dependency on container platforms  
- Full isolation from application services  
- Client-side DNS failover  
- Encrypted upstream resolution  
- Minimal attack surface  

DNS must remain available even if other parts of the infrastructure are unavailable.

---

## Redundancy Model

Two AdGuard instances are deployed:

- a primary DNS resolver  
- a secondary DNS resolver  

Both systems:

- run identical configurations  
- use the same upstream providers  
- are completely independent  
- operate on the same dedicated network segment  

Redundancy is achieved through standard DNS client behavior:

- devices are configured with two DNS servers  
- if the primary instance is unavailable, clients automatically fall back to the secondary instance  
- no real-time synchronization between servers is required  

This approach keeps the design simple, reliable, and easy to maintain.

---

## Network Design

### Dedicated DNS Network

DNS services operate on a dedicated and isolated network segment reserved exclusively for DNS.

Advantages of this model:

- DNS remains operational even if application workloads fail  
- Each DNS instance runs on a dedicated virtual machine  
- Containerized services are isolated from the main application platform  
- Reverse proxies are used only for management access, not for DNS functionality  
- Strict firewall rules can be enforced  
- Clear separation between infrastructure and application layers  

DNS is intentionally considered part of the infrastructure layer rather than an application workload.

---

## Client Configuration

All clients receive DNS settings automatically via DHCP from the network gateway.

The configuration distributed to clients includes:

- a primary DNS server  
- a secondary DNS server  

Clients never query the router or external providers directly for DNS resolution.

This ensures that:

- filtering policies apply consistently  
- DNS statistics remain centralized  
- upstream encryption is always enforced  

---

## DNS Resolution Model

### External Domain Resolution

For public domains, the resolution path is:

```
Client
→ AdGuard (primary or secondary)
→ DNS-over-HTTPS upstream resolver
→ Internet
```

All outbound DNS queries are encrypted using DNS-over-HTTPS (DoH).

Upstream providers are selected based on:

- privacy  
- security  
- reliability  
- redundancy  

No plaintext DNS traffic is allowed to leave the homelab.

---

### Internal Domain Resolution

AdGuard is also responsible for resolving internal domain names.

For local services, AdGuard forwards specific internal zones to the internal network gateway, which acts as the authoritative resolver for those domains.

This design allows:

- centralized filtering through AdGuard  
- authoritative local name resolution  
- compatibility with internal DNS records  
- consistent behavior across all networks  

---

## Split DNS and Selective Forwarding

The homelab uses a single unified DNS namespace for both internal and external services.

By default:

- internal domains are resolved locally  
- public domains are resolved through encrypted upstream resolvers  

However, some services using the same domain namespace are hosted outside of the homelab.

To handle this scenario, AdGuard is configured with explicit per-domain forwarding rules that bypass internal resolution and send selected domains directly to public DNS providers.

### Why This Is Necessary

Without explicit exceptions:

- external services would incorrectly resolve to internal addresses  
- monitoring and remote access could break  
- Cloudflare Tunnel integrations would fail  

### How It Works

- Internal services are resolved locally by default  
- External services must be explicitly declared  
- Each external host is individually defined  
- Resolution behavior is deterministic and predictable  

### Resulting Behavior

**Internal service resolution**

```
internal service
→ AdGuard
→ internal gateway
→ internal IP address
```

**External service resolution**

```
external service
→ AdGuard
→ public DoH resolver
→ public DNS records
```

### Benefits of This Approach

- Single, unified domain namespace  
- No need for separate internal and external domains  
- Clear and auditable DNS behavior  
- Explicit control over routing decisions  
- Compatibility with external monitoring and tunnels  

Every domain has a known and documented resolution path.

---

## DNS Traffic Characteristics

### Internal Traffic

- Protocols: TCP and UDP  
- Port: 53  
- Encryption: none (internal only)  
- Scope: local network communication  

### External Traffic

- Protocol: DNS-over-HTTPS  
- Port: 443  
- Encryption: mandatory  
- Scope: outbound internet resolution  

---

## Management Interface Access

The AdGuard web administration interface is not directly reachable from client networks.

Access follows the same secure administration model as other infrastructure components.

### Access Path

```
Client
→ Internal DNS
→ Reverse Proxy
→ AdGuard Web Interface
```

Key characteristics:

- No direct access from the DNS network  
- No exposed management ports  
- HTTPS only  
- Access restricted to trusted networks and VPN users  

This prevents the DNS infrastructure from being managed directly from untrusted locations.

---

## Security Model

The DNS infrastructure follows strict security rules:

- The DNS network cannot initiate connections toward other internal networks  
- Only DNS service ports are reachable by clients  
- No lateral movement is permitted  
- Outbound traffic is limited to encrypted DNS-over-HTTPS  

Firewall policies enforce:

- clients may query DNS services  
- DNS servers may contact approved upstream providers using encrypted protocols  
- Administrative access is allowed exclusively through the internal reverse proxy layer, restricted to the Trusted VLAN (see [Security Model](../network/security-model.md)) or VPN connections, and limited to explicitly authorized devices
- no other communication paths are permitted

This keeps the DNS layer minimal, predictable, and well protected.

---

## Operational Benefits

This architecture provides several important advantages:

- High availability of name resolution  
- Independence from application platforms  
- Reduced attack surface  
- Clear separation of responsibilities  
- Simple and reliable redundancy  
- Consistent filtering across all devices  

By isolating DNS at both the virtualization and network levels, the homelab ensures that name resolution remains stable even during maintenance or partial outages.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

- [AdGuard Home Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/adguard/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.