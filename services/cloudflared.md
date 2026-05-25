# Cloudflared

Cloudflared is used to securely expose selected internal services to the internet through Cloudflare Tunnel.

Instead of opening ports on the router or exposing a public IP address, all inbound internet traffic is received through outbound encrypted connections established from the homelab to Cloudflare’s global edge network.

This design completely removes the need for direct inbound connectivity to the infrastructure.

---

## Purpose

Cloudflared forms the core of the external access strategy.

Its responsibilities are strictly limited to:

- establishing outbound tunnels to Cloudflare  
- receiving inbound HTTPS traffic from Cloudflare  
- forwarding traffic internally to the reverse proxy layer on the same edge system

Cloudflared does **not**:

- terminate TLS  
- perform authentication  
- apply security policies  
- route traffic dynamically  
- access internal services directly  

It functions purely as a secure transport mechanism between the internet and the internal reverse proxy infrastructure.

---

## Architectural Role

Cloudflared is part of the dedicated edge access layer.

Key characteristics:

- runs on a dedicated edge system  
- initiates only outbound connections  
- does not require any open inbound ports  
- never exposes internal networks directly  
- relies entirely on Cloudflare Tunnel for connectivity  

The edge layer is the only part of the homelab allowed to communicate with Cloudflare infrastructure.

---

## External Access Flow

All externally initiated traffic follows a deterministic and controlled path:

```
Client Browser
→ Cloudflare Edge Network
→ Cloudflare Tunnel
→ Cloudflared
→ Edge Reverse Proxy (TCP passthrough)
→ Internal Reverse Proxy
→ Target Service
```

At no point does external traffic reach internal networks directly.

Every request must pass through multiple explicit layers before reaching any service.

---

## Tunnel Characteristics

Cloudflare Tunnel provides several important properties:

- connections are always initiated from inside the homelab  
- multiple persistent connections are established to Cloudflare points of presence  
- traffic is encrypted end-to-end between Cloudflared and Cloudflare  
- no NAT rules or port forwarding are required  
- exposure can be instantly revoked by disabling the tunnel  

If the tunnel is offline, services simply become unreachable from the internet without affecting internal access.

---

## TLS Handling Model

Cloudflared does not handle TLS termination.

The TLS model is intentionally layered:

- Cloudflare presents public certificates to external clients  
- Cloudflared forwards traffic without modification  
- the edge reverse proxy performs TCP passthrough  
- the internal reverse proxy manages HTTPS termination and certificates  

This ensures that TLS behavior remains identical for both internal and external access paths.

---

## Deployment Model

Cloudflared is deployed as a lightweight container within the edge environment.

General characteristics:

- single stateless container  
- no persistent data required  
- token-based authentication  
- automatic reconnection on failure  
- minimal resource usage  

The tunnel is authenticated using a unique token generated from the Cloudflare dashboard.

---

## Security Properties

Using Cloudflare Tunnel provides several important security benefits:

- no inbound ports exposed on the router  
- no public IP address reachable from the internet  
- complete removal of direct attack surface  
- strict separation between external and internal networks  
- all traffic forced through controlled reverse proxy paths  
- centralized visibility and control  

Even in the unlikely event of a Cloudflared compromise, network segmentation and firewall rules prevent lateral movement toward internal systems.

---

## Failure Behavior

| Failure Scenario | Impact |
|---|---|
| Cloudflared service down | External access unavailable |
| Cloudflare outage | External access unavailable |
| Edge system unavailable | No internet exposure |
| Internal service down | Service unavailable both internally and externally |

Internal access to services remains unaffected by tunnel issues as long as the services themselves are operational.

---

## Design Rationale

Cloudflare Tunnel was chosen in order to:

- eliminate traditional port forwarding  
- minimize public exposure  
- centralize internet-facing access  
- reduce the external attack surface  
- simplify TLS and DNS management  
- provide predictable and auditable connectivity  

Cloudflared is therefore considered:

- an edge transport component  
- not a reverse proxy  
- not an authentication layer  
- not a security decision point  

All security and access control decisions occur deeper within the infrastructure.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

- [Cloudflared Docker Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/cloudflared/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.
