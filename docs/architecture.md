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
