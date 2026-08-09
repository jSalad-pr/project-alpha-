# Project Alpha Architecture

## Overview

Project Alpha is a self-hosted infrastructure environment built around a dedicated physical server, a virtualized Linux administration environment, a Maintenance LAN, and a separate General/Home network connection.

The architecture intentionally separates two network roles:

1. **Maintenance LAN** — the primary administration and internal-access network for Project Alpha.
2. **General/Home Network** — the external connectivity path used for Internet access and external dependencies.

The Maintenance LAN is the normal Project Alpha administration path.

The General/Home Network is not the normal administration path.

The services themselves run on `alpha-node01` through Docker. The Maintenance LAN provides the network path used to administer and access that infrastructure.

---

# 1. Architectural Principles

Project Alpha follows several core architectural principles.

## Separation of Network Roles

The project uses two network paths with intentionally different purposes.

### Maintenance LAN

The Maintenance LAN provides:

- SSH administration
- Server administration
- Docker administration
- Internal service access
- Internal DNS
- Private PKI administration
- Infrastructure troubleshooting
- Local infrastructure management

Normal Project Alpha administration occurs through this network.

### General/Home Network

The General/Home Network provides:

- Internet connectivity
- Ubuntu / operating-system updates
- Package downloads
- Docker image downloads
- External dependencies
- External connectivity required by infrastructure services
- Future remote-access capabilities

The General/Home Network is not the normal Project Alpha administration path.

---

# 2. Physical and Virtual Architecture

The Project Alpha administration environment begins with the physical Windows workstation.

The workstation hosts the `alpha-admin` Ubuntu Desktop virtual machine.

`alpha-admin` is the Linux administration environment used to manage the Project Alpha infrastructure.

The physical topology is:

```text
Windows Workstation
        │
        │ hosts
        ▼
┌─────────────────┐
│   alpha-admin   │
│ Ubuntu Desktop  │
│       VM        │
└────────┬────────┘
         │
       Wi-Fi
         │
         ▼
┌──────────────────────┐
│ Maintenance Router / │
│        Access Point  │
└──────────┬───────────┘
           │
        Ethernet
           │
           ▼
┌──────────────────────┐
│     alpha-node01     │
│    Ubuntu Server     │
│                      │
│  Ethernet + Wi-Fi    │
└──────────────────────┘
```

---

# 3. alpha-node01

`alpha-node01` is the primary physical Project Alpha infrastructure host.

It is an MSI GE76 Raider 11UE repurposed from a general-purpose Windows laptop into a headless Ubuntu Server system.

The server provides the physical compute, storage, networking, and container runtime for the Project Alpha infrastructure.

The server operates with two physical network connections.

```text
                    alpha-node01
                         │
              ┌──────────┴──────────┐
              │                     │
          Ethernet                 Wi-Fi
              │                     │
              ▼                     ▼
       Maintenance LAN       General/Home Network
              │                     │
       Administration        Internet / External
       Internal Access         Dependencies
```

These two interfaces intentionally perform different roles.

---

# 4. Maintenance LAN Path

The Maintenance LAN is the authoritative administration path for Project Alpha.

The normal administration path is:

```text
Windows Workstation
        │
        ▼
alpha-admin
Ubuntu Desktop VM
        │
        │ Wi-Fi
        ▼
Maintenance Router / AP
        │
        │ Ethernet
        ▼
alpha-node01
        │
        ▼
Project Alpha infrastructure
```

This path is used for infrastructure management rather than relying on the General/Home Network.

Typical administration activities include:

- SSH
- Docker administration
- Service configuration
- Internal DNS management
- PKI administration
- Network troubleshooting
- Infrastructure development

---

# 5. General/Home Network Path

`alpha-node01` also maintains an independent Wi-Fi connection to the General/Home Network.

```text
alpha-node01
      │
      │ Intel AX210 Wi-Fi
      ▼
General/Home Network
      │
      ├── Internet
      ├── Ubuntu updates
      ├── Package repositories
      ├── Docker image downloads
      └── External dependencies
```

This connection exists primarily to provide external connectivity.

It is intentionally separate from the normal Project Alpha administration path.

Chapter 1 established this dual-network design by configuring the Intel AX210 for Home Network Internet access while preserving the isolated Ethernet lab network.

---

# 6. Virtualization Boundary

The Windows workstation and `alpha-admin` serve different roles.

The Windows workstation is the physical host.

`alpha-admin` is the Ubuntu Desktop virtual machine running on that workstation.

```text
┌─────────────────────────────────────┐
│       Windows Workstation           │
│                                     │
│       Windows Operating System      │
│                                     │
│       ┌─────────────────────────┐   │
│       │       alpha-admin       │   │
│       │     Ubuntu Desktop VM   │   │
│       │                         │   │
│       │ Linux Administration    │   │
│       │ Environment             │   │
│       └─────────────────────────┘   │
└─────────────────────────────────────┘
```

This creates a dedicated Linux administration environment without requiring the physical workstation itself to run Linux.

---

# 7. Container Architecture

The application and infrastructure services run on `alpha-node01`.

Docker Engine provides the container runtime.

Docker Compose is used to manage the individual service deployments.

The architecture is therefore:

```text
alpha-node01
     │
     ▼
Docker Engine
     │
     ▼
Docker Compose Projects
     │
     ├── Portainer
     ├── Uptime Kuma
     ├── Vaultwarden
     ├── Nginx Proxy Manager
     ├── AdGuard Home
     ├── WireGuard
     └── Step CA
```

The containers run on `alpha-node01`.

The Maintenance LAN is the network used to administer and access the infrastructure.

The containers should not be described as if they physically "live inside" the Maintenance LAN.

---

# 8. Docker Network Architecture

Project Alpha uses a shared Docker proxy network to allow services that require inter-container communication to communicate through a common Docker network.

The shared network is:

```text
proxy
172.21.0.0/16
```

Independent Docker Compose projects can attach to this shared network when required.

This design allows infrastructure services such as Nginx Proxy Manager to communicate with backend services without placing every service into a single Compose project.

---

# 9. Service Architecture

The current Project Alpha infrastructure contains several independent services.

## Portainer

Portainer provides graphical Docker administration.

Primary role:

- Container management
- Docker administration
- Infrastructure visibility

---

## Uptime Kuma

Uptime Kuma provides infrastructure monitoring.

Primary role:

- Service availability monitoring
- Health monitoring
- Operational visibility

---

## Vaultwarden

Vaultwarden provides centralized credential management.

Primary role:

- Infrastructure credential storage
- Secure credential organization
- Replacement for plaintext credential storage

Vaultwarden is deployed as a Docker service with persistent storage.

---

## Nginx Proxy Manager

Nginx Proxy Manager provides the reverse-proxy layer.

Primary role:

- Internal service routing
- HTTP/HTTPS gateway
- Friendly internal service access
- TLS integration

Nginx Proxy Manager communicates with backend services through the shared Docker proxy network.

---

## AdGuard Home

AdGuard Home provides internal DNS functionality.

Primary role:

- Internal hostname resolution
- DNS management
- Upstream DNS handling
- Project Alpha internal service discovery

---

## WireGuard

WireGuard provides VPN functionality for Project Alpha.

Primary role:

- Secure remote-access capability
- Private network access
- VPN infrastructure

WireGuard operates as part of the Docker-based infrastructure while using the underlying Linux networking stack.

---

## Step CA

Step CA provides the Project Alpha private Certificate Authority infrastructure.

Primary role:

- Private certificate authority
- Internal certificate issuance
- Service certificate management
- Internal TLS infrastructure

The PKI architecture uses a Root CA and Intermediate CA model.

Detailed PKI documentation belongs in `pki.md`.

---

# 10. Internal Service Access

Project Alpha uses internal DNS and reverse-proxy infrastructure to provide human-readable service names.

The intended access architecture is:

```text
Administration Client
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
Docker Proxy Network
        │
        ├── Vaultwarden
        ├── Uptime Kuma
        ├── Portainer
        ├── AdGuard Home
        └── Other internal services
```

This creates a layered service-access model:

```text
Network
   ↓
DNS
   ↓
Reverse Proxy
   ↓
Docker Network
   ↓
Service
```

---

# 11. PKI Architecture

Project Alpha uses a private Certificate Authority rather than relying exclusively on publicly trusted certificates for internal infrastructure.

The intended trust hierarchy is:

```text
Project Alpha Root CA
        │
        ▼
Project Alpha Intermediate CA
        │
        ├── Vaultwarden
        ├── Uptime Kuma
        ├── Nginx Proxy Manager
        ├── Portainer
        └── AdGuard Home
```

The Root CA is intended to remain protected and is used to establish trust for the Intermediate CA.

The Intermediate CA is responsible for issuing service certificates.

Detailed certificate configuration, trust distribution, and PKI procedures belong in `pki.md`.

---

# 12. Administration Architecture

The complete normal administration path is:

```text
Windows Workstation
        │
        ▼
alpha-admin
Ubuntu Desktop VM
        │
        ▼
Maintenance LAN
        │
        ▼
alpha-node01
        │
        ├── SSH
        ├── Docker
        ├── Internal DNS
        ├── Reverse Proxy
        ├── PKI
        ├── Monitoring
        └── Infrastructure Services
```

The Windows workstation itself is not the Linux administration environment.

`alpha-admin` provides that Linux administration environment.

---

# 13. Complete Project Alpha Architecture

The complete architecture can be represented as:

```text
                           GENERAL / HOME NETWORK
                                      │
                                      │ Wi-Fi
                                      │
                                      ▼
                              ┌───────────────┐
                              │ alpha-node01  │
                              │ Ubuntu Server │
                              │               │
                              │ Intel AX210   │
                              │      Wi-Fi    │
                              │               │
                              │ Killer E3100G │
                              │     Ethernet  │
                              └───────┬───────┘
                                      │
                                      │ Ethernet
                                      ▼
                           ┌─────────────────────┐
                           │ Maintenance Router  │
                           │        / AP         │
                           └──────────┬──────────┘
                                      │
                               Maintenance LAN
                                      │
                       ┌──────────────┴──────────────┐
                       │                             │
                       ▼                             ▼
                Windows Workstation          Project Alpha
                       │                     Internal Access
                       │
                       │ hosts
                       ▼
                ┌───────────────┐
                │  alpha-admin  │
                │ Ubuntu Desktop│
                │      VM       │
                └───────────────┘


                              alpha-node01
                                   │
                                   ▼
                             Docker Engine
                                   │
                            Docker Compose
                                   │
                    ┌──────────────┼──────────────┐
                    │              │              │
                    ▼              ▼              ▼
                Portainer     Uptime Kuma    Vaultwarden
                    │
                    ├──────────────┐
                    ▼              ▼
             Nginx Proxy      AdGuard Home
               Manager
                    │
                    ├── Internal Service Routing
                    ├── Internal DNS
                    ├── TLS / PKI Integration
                    └── WireGuard
                                   │
                                   ▼
                            Project Alpha
                           Internal Services
```

---

# 14. Architectural Boundaries

Project Alpha contains several distinct boundaries.

## Physical Boundary

The MSI GE76 Raider 11UE is the physical infrastructure host.

## Virtualization Boundary

`alpha-admin` is isolated as an Ubuntu Desktop VM running on the Windows workstation.

## Network Boundary

The Maintenance LAN and General/Home Network serve different operational purposes.

## Container Boundary

Docker isolates the individual infrastructure services from the host operating system and from one another.

## Service Boundary

Each major infrastructure service is maintained as an independent Docker Compose project.

## PKI Boundary

The Root CA is separated from the Intermediate CA and service certificates.

---

# 15. Design Philosophy

Project Alpha is designed to resemble a small production-inspired infrastructure environment using repurposed hardware.

The architecture intentionally combines:

- Linux server administration
- Network segmentation
- Virtualization
- Containerization
- Internal DNS
- Reverse proxy infrastructure
- Monitoring
- Credential management
- Private PKI
- VPN infrastructure
- Remote administration
- Infrastructure documentation

The objective is not simply to host applications.

The objective is to build and operate an infrastructure environment that demonstrates practical systems and network administration skills.

---

# 16. Chapter 1 Architectural State

At the Chapter 1 boundary, the core infrastructure architecture had been established.

Completed architectural foundations included:

- Ubuntu Server
- `alpha-node01`
- `alpha-admin`
- Maintenance LAN
- Dual-network operation
- Docker Engine
- Docker Compose
- Shared Docker network
- Portainer
- Uptime Kuma
- Vaultwarden
- Nginx Proxy Manager
- AdGuard Home
- WireGuard
- Internal DNS
- Private PKI infrastructure
- Infrastructure monitoring
- Secure credential-management direction

The architecture was functional, but some higher-level security and HTTPS work remained under development.

In particular, Chapter 1 should not be interpreted as meaning that every HTTPS/SNI configuration was completely finalized.

The architecture documentation therefore distinguishes between:

- infrastructure that was operational,
- infrastructure that was implemented but still being refined,
- and future improvements.
