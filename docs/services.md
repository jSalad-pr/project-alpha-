# Project Alpha Services

## Overview

Project Alpha uses a Docker-based service architecture running on `alpha-node01`.

The physical server provides the underlying Linux operating system, networking, storage, and compute resources.

Docker Engine provides the container runtime.

Docker Compose is used to manage the deployed infrastructure services.

The service layer currently includes:

- Portainer
- Uptime Kuma
- Vaultwarden
- Nginx Proxy Manager
- AdGuard Home
- WireGuard
- Step CA

The services are designed to provide the core infrastructure capabilities required by Project Alpha, including:

- Container management
- Monitoring
- Credential management
- Reverse proxying
- Internal DNS
- VPN infrastructure
- Private PKI

---

# 1. Service Architecture

The overall service architecture is:

```text
                         alpha-node01
                              │
                              ▼
                        Ubuntu Server
                              │
                              ▼
                         Docker Engine
                              │
                              ▼
                       Docker Compose
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
         Management       Networking        Security
             │                │                │
             ▼                ▼                ▼
         Portainer       AdGuard Home      Vaultwarden
         Uptime Kuma    Nginx Proxy Mgr     Step CA
                          WireGuard
```

The services run on the physical `alpha-node01` server.

They are accessed and administered through the Project Alpha network architecture described in `networking.md`.

---

# 2. Service Categories

Project Alpha services can be grouped by their primary function.

## Management

- Portainer
- Uptime Kuma

## Networking

- AdGuard Home
- Nginx Proxy Manager
- WireGuard

## Security

- Vaultwarden
- Step CA

These categories describe the primary purpose of each service. Some services provide functionality across more than one category.

---

# 3. Docker Engine

Docker Engine is the container runtime for Project Alpha.

Docker provides the environment in which the individual infrastructure services execute.

The relationship is:

```text
alpha-node01
      │
      ▼
Ubuntu Server
      │
      ▼
Docker Engine
      │
      ├── Container
      ├── Container
      ├── Container
      └── Container
```

Docker allows the services to remain separated from the host operating system while sharing the server's underlying compute, storage, and networking resources.

---

# 4. Docker Compose

Docker Compose is used to define and manage the Project Alpha container deployments.

Compose provides a repeatable way to describe:

- Containers
- Images
- Volumes
- Networks
- Environment configuration
- Service dependencies
- Restart behavior

The Project Alpha service architecture is organized around individual services rather than treating the entire infrastructure as a single monolithic application.

---

# 5. Shared Docker Network

Project Alpha uses a shared Docker network named:

```text
proxy
```

with the documented subnet:

```text
172.21.0.0/16
```

The shared network provides an internal communication layer for services that need to communicate with one another.

The relationship between the physical network and Docker network is:

```text
Maintenance LAN
        │
        ▼
alpha-node01
        │
        ▼
Docker Engine
        │
        ▼
proxy
172.21.0.0/16
        │
        ├── Service
        ├── Service
        └── Service
```

The Docker network is a container-level network.

It should not be confused with the physical Maintenance LAN.

---

# 6. Portainer

## Purpose

Portainer provides a graphical management interface for Docker.

It gives Project Alpha an administrative interface for viewing and managing the container environment.

## Primary Functions

Portainer is used for:

- Container management
- Docker environment visibility
- Container status inspection
- Image management
- Network management
- Volume management
- Docker administration

## Architectural Role

Portainer belongs to the management layer of Project Alpha.

```text
Administrator
      │
      ▼
Maintenance LAN
      │
      ▼
alpha-node01
      │
      ▼
Docker Engine
      │
      ▼
Portainer
```

Portainer does not replace command-line Docker administration.

It provides an additional management interface.

---

# 7. Uptime Kuma

## Purpose

Uptime Kuma provides service and infrastructure monitoring.

Its primary purpose is to provide visibility into whether Project Alpha services are available and responding.

## Primary Functions

Uptime Kuma can provide:

- Service monitoring
- Availability checks
- Health visibility
- Monitoring history
- Operational alerts

## Architectural Role

Uptime Kuma belongs to the monitoring and management layer.

```text
Project Alpha Services
        │
        ▼
   Health Checks
        │
        ▼
   Uptime Kuma
        │
        ▼
Operational Visibility
```

Uptime Kuma helps provide an operational view of the infrastructure rather than simply hosting services without monitoring.

---

# 8. Vaultwarden

## Purpose

Vaultwarden provides centralized credential-management functionality.

It is used as part of Project Alpha's approach to securely organizing infrastructure credentials.

## Primary Functions

Vaultwarden provides:

- Credential storage
- Password management
- Secure credential organization
- Centralized access to infrastructure credentials

## Architectural Role

Vaultwarden belongs to the security layer of Project Alpha.

```text
Administrator
      │
      ▼
Maintenance LAN
      │
      ▼
Nginx Proxy Manager
      │
      ▼
Vaultwarden
      │
      ▼
Persistent Application Data
```

Vaultwarden is deployed as a Docker service on `alpha-node01`.

---

# 9. Nginx Proxy Manager

## Purpose

Nginx Proxy Manager provides the reverse-proxy layer for Project Alpha.

It provides a centralized entry point for internal HTTP/HTTPS service access.

## Primary Functions

Nginx Proxy Manager provides:

- Reverse proxying
- Internal service routing
- Host-based routing
- HTTP/HTTPS handling
- TLS integration
- Centralized service entry points

## Architectural Role

The reverse proxy sits between clients and backend services.

```text
Client
  │
  ▼
Internal DNS
  │
  ▼
Nginx Proxy Manager
  │
  ▼
Docker Proxy Network
  │
  ├── Vaultwarden
  ├── Uptime Kuma
  ├── Portainer
  └── Other Services
```

This creates a centralized service-routing layer.

---

# 10. AdGuard Home

## Purpose

AdGuard Home provides internal DNS functionality for Project Alpha.

It provides the DNS layer used to resolve internal infrastructure names.

## Primary Functions

AdGuard Home provides:

- Internal DNS
- DNS forwarding
- Upstream DNS configuration
- Internal service resolution
- DNS administration
- Infrastructure name resolution

## Architectural Role

AdGuard Home sits within the networking layer.

```text
Administration Client
        │
        ▼
Maintenance LAN
        │
        ▼
AdGuard Home
        │
        ▼
Internal DNS
        │
        ▼
Project Alpha Service
```

This allows infrastructure services to be addressed through names rather than requiring users to remember individual IP addresses.

---

# 11. WireGuard

## Purpose

WireGuard provides VPN functionality for Project Alpha.

It is intended to provide secure private network access when remote connectivity is required.

## Primary Functions

WireGuard provides:

- VPN connectivity
- Encrypted network tunnels
- Private network access
- Remote-access infrastructure

## Architectural Role

WireGuard belongs to the networking and remote-access layer.

```text
Remote Client
      │
      │ Encrypted VPN Tunnel
      ▼
WireGuard
      │
      ▼
Project Alpha Network
      │
      ├── Internal Services
      ├── DNS
      └── Administration
```

The exact remote-access policy and exposure model are documented separately as part of the security architecture.

---

# 12. Step CA

## Purpose

Step CA provides the private Certificate Authority infrastructure for Project Alpha.

It is used to support internal certificate issuance and private TLS infrastructure.

## Primary Functions

Step CA provides:

- Private Certificate Authority functionality
- Certificate issuance
- Internal certificate management
- Trust hierarchy support
- Internal TLS infrastructure

## Architectural Role

Step CA belongs to the security and PKI layers.

The intended certificate hierarchy is:

```text
Project Alpha Root CA
        │
        ▼
Project Alpha Intermediate CA
        │
        ├── Internal Service Certificate
        ├── Internal Service Certificate
        └── Internal Service Certificate
```

The detailed PKI design is documented separately in `pki.md`.

---

# 13. Service Access Architecture

Project Alpha services are intended to be accessed through several infrastructure layers.

The general access model is:

```text
Administrator
      │
      ▼
alpha-admin
      │
      ▼
Maintenance LAN
      │
      ▼
Internal DNS
      │
      ▼
Nginx Proxy Manager
      │
      ▼
Docker Network
      │
      ▼
Project Alpha Service
```

Each layer performs a different function.

### Maintenance LAN

Provides network access.

### Internal DNS

Provides service-name resolution.

### Nginx Proxy Manager

Provides reverse-proxy routing.

### Docker Network

Provides container-to-container communication.

### Application Service

Provides the actual application or infrastructure function.

---

# 14. Service Communication

The services do not all require direct communication with one another.

Where communication is required, Docker networking provides the appropriate internal path.

A simplified model is:

```text
Client
  │
  ▼
DNS
  │
  ▼
Reverse Proxy
  │
  ▼
Docker Network
  │
  ▼
Backend Service
```

This architecture avoids requiring every client to communicate directly with every container.

---

# 15. Service Persistence

Services that maintain important state require persistent storage.

Examples include:

- Vaultwarden data
- Uptime Kuma monitoring data
- AdGuard Home configuration
- Nginx Proxy Manager configuration
- PKI data
- Other service-specific application state

Persistent data should remain separate from ephemeral container state.

The general model is:

```text
Container
    │
    ▼
Application
    │
    ▼
Persistent Volume / Storage
```

Deleting or recreating a container should not automatically imply deletion of its persistent application data.

---

# 16. Service Isolation

Each Project Alpha service runs as a containerized workload.

Containerization provides a degree of separation between services and the host operating system.

The architecture is:

```text
alpha-node01
      │
      ▼
Docker Engine
      │
 ┌────┼────┬────┬────┬────┐
 │    │    │    │    │    │
 ▼    ▼    ▼    ▼    ▼    ▼
P.   U.K. Vault NPM  DNS  VPN
```

The container boundary should not be treated as a complete security boundary.

Host security, authentication, network controls, permissions, secrets management, and service-specific security remain necessary.

---

# 17. Service Dependencies

Some Project Alpha services depend on other infrastructure layers.

A simplified dependency model is:

```text
                     Project Alpha Services
                              │
                              ▼
                       Docker Engine
                              │
                 ┌────────────┼────────────┐
                 │            │            │
                 ▼            ▼            ▼
              Storage      Networking     DNS
                 │            │            │
                 │            ▼            ▼
                 │       Docker Proxy   AdGuard
                 │          Network      Home
                 │            │
                 └────────────┼────────────┐
                              │            │
                              ▼            ▼
                           Services     Reverse
                                        Proxy
```

The exact dependency relationships vary by service.

The architecture should therefore distinguish infrastructure dependencies from application-level dependencies.

---

# 18. Reverse Proxy and Backend Services

Nginx Proxy Manager acts as the central HTTP/HTTPS routing layer for services that are exposed through the reverse proxy.

The intended flow is:

```text
Client
  │
  ▼
Internal DNS
  │
  ▼
Nginx Proxy Manager
  │
  │ routing
  ▼
Docker Proxy Network
  │
  ├── Portainer
  ├── Uptime Kuma
  ├── Vaultwarden
  └── Other Internal Services
```

This allows backend services to remain behind a common entry point.

---

# 19. DNS and Service Discovery

AdGuard Home provides the DNS layer required for internal service discovery.

The general model is:

```text
service.alpha.internal
          │
          ▼
     AdGuard Home
          │
          ▼
      DNS Answer
          │
          ▼
Nginx Proxy Manager
          │
          ▼
    Backend Service
```

The DNS layer and reverse-proxy layer therefore perform complementary functions.

DNS resolves the name.

The reverse proxy determines the backend destination.

---

# 20. Monitoring Architecture

Uptime Kuma provides service-level monitoring.

The monitoring model is:

```text
Project Alpha Service
        │
        ▼
   Health / Status
        │
        ▼
   Uptime Kuma
        │
        ▼
 Monitoring History
```

Monitoring is important because infrastructure that is merely deployed is not necessarily infrastructure that is operational.

Uptime Kuma provides a dedicated visibility layer for service availability.

---

# 21. Management Architecture

Portainer provides a graphical management layer while SSH and Docker CLI remain available for direct administration.

The management model is:

```text
Administrator
      │
      ├───────────────┐
      │               │
      ▼               ▼
     SSH          Portainer
      │               │
      └───────┬───────┘
              ▼
        Docker Engine
              │
              ▼
       Project Services
```

This provides both:

- command-line administration
- graphical administration

without making either interface the exclusive management mechanism.

---

# 22. Security-Related Services

Several services contribute directly to the security architecture.

### Vaultwarden

Provides centralized credential management.

### Step CA

Provides private certificate infrastructure.

### WireGuard

Provides encrypted VPN connectivity.

### Nginx Proxy Manager

Provides controlled service entry and TLS integration.

These services form part of a layered security architecture rather than acting as independent security solutions.

---

# 23. Service Layering

The Project Alpha infrastructure can be represented as several layers.

```text
┌──────────────────────────────────────┐
│          Application Services        │
│                                      │
│     Vaultwarden / Other Services     │
└──────────────────────────────────────┘
                  │
┌──────────────────────────────────────┐
│          Service Access Layer        │
│                                      │
│       Nginx Proxy Manager            │
└──────────────────────────────────────┘
                  │
┌──────────────────────────────────────┐
│             DNS Layer                │
│                                      │
│          AdGuard Home                │
└──────────────────────────────────────┘
                  │
┌──────────────────────────────────────┐
│          Container Layer             │
│                                      │
│        Docker / Compose              │
└──────────────────────────────────────┘
                  │
┌──────────────────────────────────────┐
│            Host Layer                │
│                                      │
│        alpha-node01                  │
│        Ubuntu Server                 │
└──────────────────────────────────────┘
                  │
┌──────────────────────────────────────┐
│          Physical Layer              │
│                                      │
│       MSI GE76 Raider 11UE           │
└──────────────────────────────────────┘
```

---

# 24. Operational Responsibilities

Each service has a distinct operational responsibility.

| Service | Primary Responsibility |
|---|---|
| Docker Engine | Container runtime |
| Docker Compose | Container deployment management |
| Portainer | Docker administration |
| Uptime Kuma | Monitoring |
| Vaultwarden | Credential management |
| Nginx Proxy Manager | Reverse proxy |
| AdGuard Home | Internal DNS |
| WireGuard | VPN |
| Step CA | Private PKI |

This separation of responsibilities helps prevent the infrastructure from becoming dependent on a single application performing unrelated functions.

---

# 25. Service Deployment Philosophy

Project Alpha favors services that are:

- Self-hostable
- Containerized
- Independently maintainable
- Documentable
- Accessible through the internal network
- Capable of persistent configuration
- Useful for infrastructure-management practice

The objective is not to deploy as many applications as possible.

Each service should have a clear infrastructure purpose.

---

# 26. Operational State

The service documentation should distinguish between three states.

## Operational

A service has been deployed and is functioning as part of the infrastructure.

## Implemented / Being Refined

A service exists but configuration, security, TLS, or integration work remains.

## Planned

A service or capability has been identified for future implementation but is not yet considered part of the operational infrastructure.

This distinction prevents the documentation from representing future plans as completed infrastructure.

---

# 27. Chapter 1 Service State

At the Chapter 1 documentation boundary, the core Docker-based service infrastructure had been established.

The documented infrastructure included:

- Docker Engine
- Docker Compose
- Portainer
- Uptime Kuma
- Vaultwarden
- Nginx Proxy Manager
- AdGuard Home
- WireGuard
- Private PKI / Step CA infrastructure
- Shared Docker networking

The service layer was functional, while some security, HTTPS, certificate, and integration work remained under development.

---

# 28. Service Relationship Summary

The major service relationships can be summarized as:

```text
                         Project Alpha
                              │
                              ▼
                         alpha-node01
                              │
                              ▼
                        Docker Engine
                              │
              ┌───────────────┼────────────────┐
              │               │                │
              ▼               ▼                ▼
          Management      Networking        Security
              │               │                │
        ┌─────┴─────┐     ┌───┴────┐      ┌────┴─────┐
        │           │     │        │      │          │
        ▼           ▼     ▼        ▼      ▼          ▼
    Portainer   Uptime  AdGuard   NPM  Vaultwarden Step CA
                 Kuma   Home       │
                                   │
                                WireGuard
```

---

# 29. Complete Service Architecture

The complete Project Alpha service model is:

```text
                         ADMINISTRATION
                               │
                               ▼
                         alpha-admin
                               │
                               ▼
                        Maintenance LAN
                               │
                               ▼
                          alpha-node01
                               │
                               ▼
                         Docker Engine
                               │
                    ┌──────────┼──────────┐
                    │          │          │
                    ▼          ▼          ▼
                Portainer  Uptime Kuma  Security
                                          │
                               ┌──────────┼──────────┐
                               │          │          │
                               ▼          ▼          ▼
                         Vaultwarden   Step CA   WireGuard
                              
                              
                         NETWORK SERVICES
                               │
                       ┌───────┴────────┐
                       │                │
                       ▼                ▼
                 AdGuard Home   Nginx Proxy Manager
                       │                │
                       │                ▼
                       │        Docker Proxy Network
                       │                │
                       └────────┬───────┘
                                │
                                ▼
                         Internal Services
```

---

# 30. Summary

Project Alpha uses Docker as the primary service-deployment platform.

The current service architecture provides the major infrastructure capabilities required by the project:

- **Portainer** provides Docker management.
- **Uptime Kuma** provides monitoring.
- **Vaultwarden** provides credential management.
- **Nginx Proxy Manager** provides reverse proxying.
- **AdGuard Home** provides internal DNS.
- **WireGuard** provides VPN infrastructure.
- **Step CA** provides private PKI infrastructure.

These services run on `alpha-node01` and interact with the physical and virtual network architecture documented in `networking.md` and `architecture.md`.

The service architecture is intentionally modular.

Each service has a defined responsibility, can be maintained independently, and contributes to the larger Project Alpha infrastructure environment.

The resulting platform provides a practical foundation for continued work in:

- Linux administration
- Docker administration
- Network administration
- DNS
- Reverse proxying
- TLS
- PKI
- VPNs
- Monitoring
- Credential management
- Infrastructure automation
