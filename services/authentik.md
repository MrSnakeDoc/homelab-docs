# Authentik

Authentik is the central identity provider (IdP) of the homelab.

It provides authentication, authorization, and identity federation for all compatible services that support external identity providers, whether internal or externally exposed.

Rather than relying on multiple independent authentication systems, Authentik centralizes identity management and enforces consistent security policies across the entire infrastructure.

---

## Purpose

Authentik acts as the single source of truth for user authentication.

Its responsibilities include:

- Centralized Single Sign-On (SSO)  
- OAuth2 and OpenID Connect (OIDC) integration  
- Multi-factor authentication (MFA / TOTP)  
- Access policies and conditional rules  
- Identity enforcement for protected services  
- Unified user management  

The objective is to provide a consistent and secure authentication layer for all supported applications.

---

## Scope and Limitations

Authentik is used exclusively for application-level authentication.

It is **not** used for:

- Linux system authentication  
- SSH access  
- Virtual machine login  
- Infrastructure-to-infrastructure authentication  
- Network device authentication  

Authentik remains strictly an application identity provider and does not replace traditional administrative access controls.

---

## Integration Model

Services integrate with Authentik using standard protocols such as:

- OpenID Connect  
- OAuth2  
- SAML (when required)  

Applications that support external identity providers delegate authentication to Authentik instead of managing local user accounts.  
Services that do not support such integrations continue to use their native authentication systems.

This ensures:

- consistent credentials across services  
- centralized access control  
- simplified user lifecycle management  
- stronger overall security

---

## Integration Reality

While Authentik is the preferred and primary authentication method, not all services support external identity providers.

Two categories of applications exist within the homelab:

### Services Supporting Authentik

Modern applications that support:

- OpenID Connect  
- OAuth2  
- SAML  

are fully integrated with Authentik and rely on it for user authentication.

For these services:

- local user accounts are disabled when possible  
- authentication policies are centralized  
- MFA is enforced through Authentik  

### Services Without External Authentication Support

Some applications do not provide any form of external identity integration.

These services:

- rely on their own built-in authentication mechanisms  
- manage local user accounts independently  
- cannot be federated through Authentik  

For such services, security relies on:

- strong unique passwords  
- HTTPS-only access  
- reverse proxy protections  
- network-level access controls  

Authentik remains the central identity provider whenever technically possible, but it does not replace native authentication for applications that lack integration capabilities.

---

## Authentication Flows

### Internal Access

For services accessed from inside the network, the typical authentication flow is:

```
Client Browser
→ Service through internal reverse proxy
→ Authentik
→ User authentication (SSO / MFA)
→ Redirect back to service
```

Users authenticate once and can access multiple services without repeated logins.

---

### External Access

For services exposed to the internet, authentication follows a layered model:

```
Client Browser
→ External tunnel
→ Edge reverse proxy
→ Internal reverse proxy
→ Authentik
→ Authentication and policy evaluation
→ Target service
```

At no time is Authentik directly reachable from the internet.  
All access is mediated by the reverse proxy infrastructure.

---

## Security Model

Authentik is considered a sensitive core application and follows strict security rules:

- Access is available only through HTTPS  
- Direct exposure of Authentik ports is prohibited  
- All authentication passes through controlled reverse proxies  
- Multi-factor authentication is enabled for administrative accounts  
- Access policies are centrally managed  

This ensures that identity management remains protected even when services are publicly accessible.

---

## Network Placement

Authentik is hosted within the main application environment alongside other internal services.

Design characteristics:

- No direct exposure to untrusted networks  
- All access routed through internal reverse proxies  
- Segmented from infrastructure and network services  
- Not reachable from IoT or restricted networks  

Authentik is treated as an internal application platform component rather than as part of the infrastructure layer.

---

## Deployment Architecture

Authentik is deployed using a containerized multi-service stack.

The deployment typically includes:

| Component | Role |
|---|---|
| Authentik Server | Web interface and authentication engine |
| Authentik Worker | Background tasks and policy processing |
| PostgreSQL | Persistent identity database |
| Redis | Caching and task queue |

All components communicate through an internal container network and are never exposed directly to clients.

---

## Exposure Model

Only the Authentik web interface is reachable, and only through the reverse proxy layer.

Key characteristics:

- HTTPS is mandatory  
- No direct port exposure  
- Authentication pages are protected  
- Administrative access follows the same rules as other internal services  

This approach ensures that the identity platform benefits from the same security controls as the rest of the homelab.

---

## Operational Benefits

Using Authentik as a central identity provider provides several advantages:

- Single account for all services that support external authentication  
- Consistent security policies  
- Reduced password reuse  
- Simplified user management  
- Unified audit trail  
- Stronger authentication through MFA  

It significantly improves both security and usability compared to managing credentials independently in each application.

---

## Related Resources

Deployment details and configuration examples are maintained in a separate repository:

- [Authentik Docker Compose Configuration](https://github.com/MrSnakeDoc/homelab-blueprint/blob/main/authentik/compose.yml)

This repository contains the technical implementation while this document focuses on architecture and design principles.