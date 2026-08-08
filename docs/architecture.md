# Project Alpha Architecture

## Overview

Project Alpha is a self-hosted infrastructure environment designed around a dedicated Maintenance LAN.

The architecture intentionally separates:

1. The **General/Home Network**, which provides external Internet connectivity and access to outside dependencies.
2. The **Maintenance LAN**, which provides the operational network for Project Alpha administration and internal services.

The General/Home Network exists as an external connectivity path but is **not used for normal Project Alpha administration**.

All infrastructure administration is intentionally performed through the Maintenance LAN.

---

## Architectural Philosophy

Project Alpha follows a simple principle:

> **External connectivity and infrastructure management are separate concerns.**

The Project Alpha infrastructure is designed to remain operational and administrable through its local Maintenance LAN without requiring the General/Home Network for normal management.

The General/Home Network exists primarily to provide:

- Internet connectivity
- Operating-system updates
- Package downloads
- Container image downloads
- External dependency acquisition
- Future remote-access capabilities

The Maintenance LAN provides:

- SSH administration
- Server administration
- Docker administration
- Service configuration
- Internal DNS
- Private PKI administration
- Internal service access
- Infrastructure troubleshooting
- Local WireGuard access

Although the General/Home Network and Maintenance LAN can technically provide paths between systems, the General/Home Network is **not an authorized or intended administration path**.

---

# High-Level Topology

```text
                         WINDOWS WORKSTATION
                    ┌────────────────────────┐
                    │       Windows OS       │
                    │                        │
                    │  ┌──────────────────┐  │
                    │  │   alpha-admin    │  │
                    │  │ Ubuntu Desktop VM │  │
                    │  └────────┬─────────┘  │
                    └───────────┼────────────┘
                                │
                         Wi-Fi / Shared
                       Network Connectivity
                                │
                                ▼
                     ┌──────────────────────┐
                     │   MAINTENANCE LAN    │
                     │                      │
                     │ Project Alpha        │
                     │ Management Network   │
                     └──────────┬───────────┘
                                │
                             Ethernet
                                │
                                ▼
                     ┌──────────────────────┐
                     │     alpha-node01     │
                     │     Ubuntu Server    │
                     │                      │
                     │      Dual-Homed      │
                     └──────────┬───────────┘
                                │
                           Docker Engine
                                │
                ┌───────────────┼────────────────┐
                │               │                │
            Portainer      Uptime Kuma       Vaultwarden
                │               │                │
                ├───────────────┼────────────────┤
                │               │                │
          Nginx Proxy      AdGuard Home       Step CA
            Manager
                │
            WireGuard
                │
                ▼
         Internal Services
