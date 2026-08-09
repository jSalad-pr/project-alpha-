# Project Alpha Changelog

## Overview

This document records the major development milestones, architectural changes, infrastructure implementations, and documentation updates made during the development of Project Alpha.

Project Alpha is an evolving self-hosted infrastructure laboratory.

The changelog is intended to provide a chronological record of the project's development rather than serve as a replacement for the individual architecture documents.

Detailed technical information should remain in the appropriate documentation under `docs/`.

---

# Development History

## Project Initialization

Project Alpha began as a personal infrastructure laboratory intended to provide practical experience with:

- Linux administration
- Network administration
- Virtualization
- Docker
- Reverse proxies
- DNS
- VPN infrastructure
- Private PKI
- Monitoring
- Automation
- Infrastructure design
- Security engineering

The project uses repurposed personal hardware rather than dedicated enterprise infrastructure.

The primary physical server became an MSI GE76 Raider 11UE laptop.

---

# Hardware Repurposing

## MSI GE76 Raider → `alpha-node01`

The MSI GE76 Raider 11UE was repurposed from a general-purpose Windows laptop into the primary Project Alpha server.

The system contains:

- Intel Core i7-11800H
- 16 GB DDR4 RAM
- NVIDIA RTX 3060 Laptop GPU
- Approximately 1 TB Samsung NVMe storage
- Intel AX210 wireless networking
- Killer E3100G 2.5 GbE networking

The system was assigned the hostname:

```text
alpha-node01
```

The machine became the primary physical infrastructure host for Project Alpha.

---

# Operating System Migration

## Windows → Ubuntu Server

The original Windows installation was replaced with Ubuntu Server.

The server currently operates as:

```text
alpha-node01
Ubuntu Server 26.04 LTS
```

The system was transitioned to headless server operation.

SSH became the primary method of remote administration.

This established the Linux foundation for the remainder of the Project Alpha infrastructure.

---

# Administration Environment

## `alpha-admin`

An Ubuntu Desktop virtual machine was established as the primary Linux administration environment.

The VM is hosted on the primary Windows workstation.

The intended administrative relationship became:

```text
Windows Workstation
        │
        ▼
alpha-admin
        │
        ▼
Maintenance LAN
        │
        ▼
alpha-node01
```

This allows Project Alpha administration to occur from a dedicated Linux environment without requiring a graphical environment on the server.

---

# Network Architecture

## Dual-Network Design

Project Alpha adopted a dual-network architecture.

The server uses separate physical network interfaces for separate purposes.

### General/Home Network

Provides:

- Internet connectivity
- Operating-system updates
- Package downloads
- Docker image downloads
- External dependencies

### Maintenance LAN

Provides:

- SSH administration
- Infrastructure management
- Internal service access
- Docker administration
- DNS administration
- PKI administration
- Network troubleshooting

This separation became a foundational architectural principle of Project Alpha.

---

# Network Interface Roles

The server's networking architecture was established around:

```text
alpha-node01
│
├── Wi-Fi / wlp48s0
│      └── General/Home Network
│
└── Ethernet / enp46s0
       └── Maintenance LAN
```

The General/Home connection provides external connectivity.

The Ethernet Maintenance LAN connection provides the primary Project Alpha administration path.

---

# Home Network Connectivity

The server's wireless interface was configured for General/Home Network connectivity.

The documented server Home Network address became:

```text
192.168.1.122
```

The wireless interface was documented as:

```text
wlp48s0
```

The Home Network connection provided the external connectivity required for:

- Ubuntu updates
- Package installation
- Docker image downloads
- External dependencies

---

# Maintenance LAN

The dedicated Maintenance LAN was established using a repurposed router/access point.

The physical architecture became:

```text
alpha-admin
      │
      │ Wi-Fi
      ▼
Maintenance Router / AP
      │
      │ Ethernet
      ▼
alpha-node01
```

This created a dedicated administration path separate from the server's normal Home Network connection.

---

# Linux Networking

Network management on `alpha-node01` was established using NetworkManager.

The project documented the server's physical interfaces and their intended roles.

The architecture intentionally avoids treating the two network interfaces as interchangeable.

Each interface has a defined operational purpose.

---

# Docker Installation

Docker Engine was installed on `alpha-node01`.

Docker became the primary container runtime for Project Alpha.

The resulting architecture became:

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
Project Alpha Services
```

---

# Docker Compose

Docker Compose was established for managing the Project Alpha containerized infrastructure.

Compose provides a repeatable method for defining:

- Containers
- Networks
- Volumes
- Environment configuration
- Service relationships
- Restart behavior

This established the foundation for the project's service layer.

---

# Docker Network

A Docker network named:

```text
proxy
```

was established.

The documented subnet is:

```text
172.21.0.0/16
```

This network provides an internal container communication layer.

The Docker network is separate from the physical Maintenance LAN.

---

# Portainer

Portainer was deployed as the graphical Docker management interface.

Its role is to provide visibility and management of the Docker environment.

Portainer complements direct Docker CLI administration rather than replacing it.

---

# Uptime Kuma

Uptime Kuma was deployed as the Project Alpha service-monitoring platform.

Its role is to provide visibility into service availability and operational health.

The monitoring layer establishes a distinction between:

```text
Service is deployed
```

and:

```text
Service is operational
```

---

# Vaultwarden

Vaultwarden was deployed to provide centralized credential-management functionality.

Its role is to provide a dedicated location for infrastructure credentials rather than storing sensitive values directly in:

- Git
- documentation
- source code
- shell history
- unsecured configuration files

---

# Nginx Proxy Manager

Nginx Proxy Manager was deployed as the reverse-proxy layer.

Its role is to provide centralized service routing and internal HTTP/HTTPS access.

The intended architecture became:

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
Docker Network
  │
  ▼
Backend Service
```

---

# AdGuard Home

AdGuard Home was deployed as the internal DNS service.

Its role includes:

- Internal DNS
- DNS forwarding
- Internal service resolution
- Infrastructure name resolution

This established the foundation for accessing internal infrastructure by name instead of relying exclusively on IP addresses.

---

# WireGuard

WireGuard was established as the Project Alpha VPN infrastructure.

Its intended role is to provide encrypted remote connectivity into the Project Alpha environment.

VPN access is treated as privileged infrastructure access and is therefore part of the project's security architecture.

---

# Private PKI

Private PKI infrastructure was introduced through Step CA.

The intended certificate hierarchy became:

```text
Root CA
   │
   ▼
Intermediate CA
   │
   ├── Internal Service Certificates
   ├── Internal Service Certificates
   └── Internal Service Certificates
```

The PKI layer provides the foundation for internal certificate issuance and trusted internal TLS.

---

# Security Architecture

Security documentation was expanded to formally describe Project Alpha's defense-in-depth model.

The architecture incorporates:

- Network separation
- SSH administration
- Docker isolation
- Credential management
- Internal DNS
- Reverse proxying
- VPN infrastructure
- Private PKI
- Monitoring
- Backup and recovery planning

The project explicitly distinguishes between:

- Implemented controls
- Controls requiring verification or hardening
- Future security improvements

This prevents planned security features from being represented as already operational.

---

# Documentation Expansion

The Project Alpha documentation structure was expanded into separate technical documents.

The primary documentation set includes:

```text
docs/
├── architecture.md
├── changelog.md
├── hardware.md
├── networking.md
├── pki.md
├── roadmap.md
├── security.md
└── services.md
```

The purpose of separating these documents is to keep architectural descriptions, hardware information, networking, services, security, PKI, roadmap information, and historical development records independently maintainable.

---

# Hardware Documentation

`hardware.md` was revised to document the physical server and supporting hardware in greater detail.

The documentation established:

- Physical server specifications
- CPU
- Memory
- GPU
- Storage
- Wireless networking
- Ethernet networking
- Display
- Firmware
- Integrated hardware
- Server conversion history

The document distinguishes original Windows hardware observations from the current Linux server state where appropriate.

---

# Architecture Documentation

`architecture.md` was expanded to describe the overall Project Alpha infrastructure.

The architecture documentation establishes the relationships between:

- Physical hardware
- Virtual administration
- Maintenance LAN
- General/Home Network
- Linux server
- Docker
- Internal services

The architecture documentation provides the high-level infrastructure model.

---

# Networking Documentation

`networking.md` was expanded to document the project's network architecture in detail.

The documentation establishes:

- Physical interfaces
- Network roles
- Maintenance LAN
- General/Home Network
- Routing
- DNS
- Administrative access
- Network isolation
- Docker networking
- Service access paths

The networking document serves as the primary reference for Project Alpha's network design.

---

# Services Documentation

`services.md` was expanded to document the Project Alpha service layer.

The service documentation covers:

- Docker Engine
- Docker Compose
- Portainer
- Uptime Kuma
- Vaultwarden
- Nginx Proxy Manager
- AdGuard Home
- WireGuard
- Step CA
- Docker networking
- Service relationships
- Service dependencies
- Service persistence
- Service access architecture

---

# Security Documentation

`security.md` was created to document the Project Alpha security architecture.

The document establishes security principles including:

- Network segmentation
- Administrative access control
- Least privilege
- Docker security
- Credential management
- Secret handling
- GitHub repository security
- Private PKI
- VPN security
- Monitoring
- Logging
- Host security
- Backup security
- Physical security
- Threat modeling

The security documentation also establishes the requirement that security controls should not be represented as implemented without verification.

---

# PKI Documentation

`pki.md` was created to document the Project Alpha private PKI architecture.

The PKI documentation provides a dedicated location for documenting:

- Certificate authority architecture
- Step CA
- Certificate issuance
- Trust relationships
- Internal certificates
- Certificate security
- PKI administration

---

# Current Infrastructure State

The Project Alpha infrastructure currently consists of:

```text
Physical Layer
      │
      ▼
MSI GE76 Raider 11UE
      │
      ▼
alpha-node01
      │
      ▼
Ubuntu Server 26.04 LTS
      │
      ▼
Docker Engine
      │
      ▼
Project Alpha Services
```

The administration environment is:

```text
Windows Workstation
      │
      ▼
alpha-admin
      │
      ▼
Maintenance LAN
      │
      ▼
alpha-node01
```

The external connectivity path is:

```text
alpha-node01
      │
      ▼
Wi-Fi
      │
      ▼
General/Home Network
      │
      ▼
Internet
```

---

# Current Service Layer

The documented service layer includes:

| Service | Primary Role |
|---|---|
| Docker Engine | Container runtime |
| Docker Compose | Container deployment |
| Portainer | Docker management |
| Uptime Kuma | Monitoring |
| Vaultwarden | Credential management |
| Nginx Proxy Manager | Reverse proxy |
| AdGuard Home | Internal DNS |
| WireGuard | VPN |
| Step CA | Private PKI |

---

# Current Security Model

The current security model is based on layered controls.

```text
Network Separation
        +
Controlled Administration
        +
Container Isolation
        +
Credential Management
        +
Private PKI
        +
VPN
        +
Monitoring
        +
Backups
```

No single component is treated as a complete security solution.

---

# Current Development Philosophy

Project Alpha is intended to function as a practical infrastructure laboratory.

The objective is not simply to deploy applications.

Each infrastructure component should provide an opportunity to develop practical skills in:

- Linux administration
- Networking
- Containerization
- Virtualization
- DNS
- Reverse proxies
- TLS
- PKI
- VPNs
- Monitoring
- Security
- Automation
- Infrastructure documentation

The infrastructure should remain understandable, reproducible, and documented.

---

# Documentation Accuracy Policy

Project Alpha documentation follows an important rule:

> **Observed system state takes precedence over assumptions.**

Documentation should distinguish between:

```text
Implemented
```

```text
Implemented but requiring verification
```

and:

```text
Planned
```

This prevents architectural plans from being mistaken for deployed infrastructure.

---

# Future Development

Future Project Alpha development is expected to continue through:

- Security hardening
- PKI deployment
- Internal TLS
- VPN refinement
- Monitoring improvements
- Backup implementation
- Recovery testing
- Service hardening
- Automation
- Infrastructure-as-code
- Additional Linux administration work
- Additional networking practice
- Documentation refinement

The project roadmap defines the intended direction while the changelog records what has actually occurred.

---

# Changelog Maintenance Rules

Future entries should:

1. Record significant infrastructure changes.
2. Record major configuration milestones.
3. Record important architectural decisions.
4. Record major security changes.
5. Record meaningful documentation changes.
6. Avoid recording every minor command or troubleshooting attempt.
7. Link detailed implementation information to the appropriate documentation.
8. Distinguish completed work from planned work.
9. Avoid storing secrets or sensitive credentials.
10. Preserve chronological development history.

---

# Current Milestone

Project Alpha has progressed from a repurposed personal laptop into a structured Linux infrastructure laboratory.

The current foundation includes:

```text
Physical Server
      │
      ▼
Ubuntu Server
      │
      ▼
Dual-Network Architecture
      │
      ▼
Docker
      │
      ▼
Infrastructure Services
      │
      ▼
Security / PKI / Monitoring
```

The project now has a formal documentation foundation covering:

- Hardware
- Architecture
- Networking
- Services
- Security
- PKI
- Roadmap
- Development history

The next stage is to reconcile the remaining historical development logs and roadmap with the current documented state.

---

# Milestone Summary

```text
[✓] Physical server established
[✓] Ubuntu Server established
[✓] alpha-node01 established
[✓] alpha-admin established
[✓] Maintenance LAN established
[✓] General/Home connectivity established
[✓] Docker established
[✓] Docker Compose established
[✓] Core services established
[✓] Internal DNS established
[✓] Reverse proxy established
[✓] VPN infrastructure established
[✓] Private PKI established
[✓] Security architecture documented
[✓] Hardware documented
[✓] Network architecture documented
[✓] Service architecture documented
[✓] PKI documentation established
[ ] Historical logs fully synchronized
[ ] Roadmap fully synchronized
[ ] Final repository audit
```

---

# End State

The changelog represents the historical development record of Project Alpha.

The detailed technical configuration remains distributed across the dedicated documentation files.

The project is now transitioning from its foundational build phase into a more structured infrastructure-development and hardening phase.
