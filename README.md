# Project Alpha

> A self-hosted infrastructure lab built to develop practical experience in Linux administration, networking, virtualization, Docker, security, and systems engineering.

---

## Overview

Project Alpha is a personal homelab infrastructure project focused on building, operating, troubleshooting, and documenting a self-hosted Linux environment.

The project is designed as a practical systems-engineering environment rather than a collection of isolated services. Development focuses on understanding how infrastructure components interact across networking, virtualization, operating systems, containers, DNS, VPNs, reverse proxies, monitoring, and private trust infrastructure.

The project is intentionally documented as it develops. The development journal preserves the raw chronological history of the project, while this repository provides the organized and reproducible representation of the infrastructure.

---

## Architecture

Project Alpha uses a dedicated **Maintenance LAN** as its primary management and service network.

The general/home network exists as a separate connectivity path for Internet access and external dependencies such as operating-system updates, package downloads, container image downloads, and future remote-access capabilities.

The general/home network is **not used as the normal administration path for Project Alpha**.

All normal infrastructure administration is intentionally performed through the Maintenance LAN.

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
                     │     Dual-homed       │
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
