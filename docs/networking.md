# Project Alpha Networking

## Overview

Project Alpha uses a dual-network architecture designed to separate infrastructure administration from external network connectivity.

The network design contains two intentionally different network planes:

1. **Maintenance LAN** — the authoritative Project Alpha administration and internal-service network.
2. **General/Home Network** — the external connectivity network used for Internet access and external dependencies.

The two networks serve different operational purposes.

The General/Home Network is not the normal Project Alpha administration path.

The Maintenance LAN is the normal administration and internal-access path.

---

# 1. Network Architecture

The Project Alpha networking model is built around a dual-homed physical server.

`alpha-node01` has:

- One Ethernet connection to the Maintenance LAN
- One Wi-Fi connection to the General/Home Network

The overall topology is:

```text
                         GENERAL / HOME NETWORK
                                  │
                                  │ Wi-Fi
                                  ▼
                           ┌──────────────┐
                           │ alpha-node01 │
                           │   Wi-Fi      │
                           └──────┬───────┘
                                  │
                                  │
                                  │
                           ┌──────┴───────┐
                           │ alpha-node01 │
                           │   Ethernet   │
                           └──────┬───────┘
                                  │
                               Ethernet
                                  │
                                  ▼
                         MAINTENANCE ROUTER / AP
                                  │
                              Wi-Fi LAN
                                  │
                                  ▼
                           alpha-admin VM
                                  │
                                  ▼
                         Windows Workstation
```

The two server interfaces have separate operational responsibilities.

---

# 2. Network Roles

## Maintenance LAN

The Maintenance LAN is the primary Project Alpha management and internal-service network.

It is used for:

- SSH administration
- Server management
- Docker administration
- Internal DNS
- Internal service access
- Reverse-proxy administration
- Private PKI administration
- Network troubleshooting
- Infrastructure development

The Maintenance LAN is the authoritative administration path.

---

## General/Home Network

The General/Home Network provides external connectivity.

It is used for:

- Internet access
- Ubuntu updates
- Package downloads
- Docker image downloads
- External repositories
- External dependencies
- Other services requiring Internet connectivity

The General/Home Network is deliberately not used as the normal Project Alpha administration path.

---

# 3. Dual-Homed Server

`alpha-node01` is dual-homed.

The server has two independent physical network interfaces.

```text
                         alpha-node01
                              │
                 ┌────────────┴────────────┐
                 │                         │
             Wi-Fi                    Ethernet
                 │                         │
                 ▼                         ▼
        General/Home Network        Maintenance LAN
                 │                         │
                 ▼                         ▼
             Internet             Administration
             External             Internal Services
            Dependencies          Infrastructure
```

This separation allows the server to maintain Internet connectivity without making the General/Home Network the authoritative Project Alpha management network.

---

# 4. Wireless Interface

## Intel AX210

The General/Home Network connection is provided through the Intel AX210 wireless adapter.

| Property | Value |
|---|---|
| Adapter | Intel AX210 |
| Linux Interface | `wlp48s0` |
| Driver | `iwlwifi` |
| Network Role | General/Home Network |
| Primary Function | External connectivity |

The interface is used for normal Internet connectivity from `alpha-node01`.

---

## Wireless Operational Role

The Wi-Fi interface provides access to:

- Internet connectivity
- Ubuntu repositories
- Package updates
- Docker registries
- External dependencies
- Other external infrastructure resources

It is not the normal Project Alpha administration path.

Administrative access is intentionally performed through the Maintenance LAN.

---

# 5. Ethernet Interface

## Killer E3100G

The Maintenance LAN connection uses the laptop's wired Ethernet controller.

| Property | Value |
|---|---|
| Controller | Killer E3100G |
| Linux Interface | `enp46s0` |
| Maximum Link Capability | 2.5 GbE |
| Network Role | Maintenance LAN |
| Primary Function | Project Alpha administration |

The Ethernet connection provides the physical path between `alpha-node01` and the Maintenance LAN.

---

# 6. Maintenance LAN

The Maintenance LAN is the isolated operational network used to manage Project Alpha.

The physical path is:

```text
Windows Workstation
        │
        ▼
alpha-admin
Ubuntu Desktop VM
        │
        │ Wi-Fi
        ▼
Maintenance Router / Access Point
        │
        │ Ethernet
        ▼
alpha-node01
```

The Maintenance LAN exists separately from the General/Home Network.

This separation is intentional.

---

# 7. Administration Path

The normal Project Alpha administration path is:

```text
Administrator
      │
      ▼
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
      ├── SSH
      ├── Docker
      ├── DNS
      ├── Reverse Proxy
      ├── PKI
      └── Internal Services
```

This path keeps normal infrastructure administration on the Maintenance LAN.

---

# 8. General/Home Connectivity

The General/Home Network follows a separate path.

```text
alpha-node01
      │
      │ Intel AX210
      ▼
General/Home Network
      │
      ▼
Internet
```

This network is used for external dependencies rather than normal administration.

Examples include:

- `apt` repositories
- Ubuntu updates
- Docker image registries
- Git repositories
- External APIs
- Other required Internet resources

---

# 9. Network Interface Separation

The two interfaces are intentionally assigned different responsibilities.

| Interface | Hardware | Network | Primary Role |
|---|---|---|---|
| `wlp48s0` | Intel AX210 | General/Home Network | Internet / external dependencies |
| `enp46s0` | Killer E3100G | Maintenance LAN | Administration / internal infrastructure |

This separation is one of the foundational design decisions of Project Alpha.

The project does not treat both interfaces as interchangeable.

---

# 10. IP Addressing

## General/Home Network

At the Chapter 1 operational boundary, `alpha-node01` received the General/Home Network address:

```text
192.168.1.122
```

This address belongs to the Home Network connection.

It should therefore be understood as the server's General/Home Network address rather than as the authoritative Project Alpha administration address.

---

## alpha-admin

The `alpha-admin` Ubuntu Desktop VM was documented with the Home/LAN address:

```text
192.168.1.124
```

The exact address should be treated as an observed Chapter 1 configuration value rather than a permanent architectural guarantee.

The VM's purpose is to provide the Linux administration environment used to manage Project Alpha.

---

# 11. Historical Network State

Project Alpha's networking configuration evolved during Chapter 1.

The initial design intentionally created a physically separate Maintenance LAN before external Internet connectivity was introduced.

The early environment therefore operated with:

```text
alpha-node01
      │
      │ Ethernet
      ▼
Maintenance Router / AP
      │
      ▼
Administration Workstation
```

The server was initially reachable from the administration workstation while remaining isolated from the normal Home Network.

This was intentional during the initial infrastructure deployment.

---

# 12. Internet Connectivity Restoration

After the isolated Maintenance LAN was established, the General/Home Wi-Fi connection was configured.

The Intel AX210 became the external connectivity interface.

The resulting design became:

```text
                    alpha-node01
                         │
              ┌──────────┴──────────┐
              │                     │
             Wi-Fi               Ethernet
              │                     │
              ▼                     ▼
        General/Home          Maintenance LAN
              │                     │
          Internet              Administration
```

This allowed Project Alpha to maintain Internet access while preserving the dedicated administration path.

---

# 13. Routing Design

Dual-network operation requires deliberate routing behavior.

The two interfaces do not exist simply to provide two interchangeable paths to the same network.

They serve different purposes.

The intended routing model is:

```text
General/Home Network
        │
        └── Default external connectivity
                 │
                 ▼
              Internet


Maintenance LAN
        │
        └── Local administration
                 │
                 ▼
           Project Alpha
           infrastructure
```

The General/Home Network provides the external route.

The Maintenance LAN provides the local infrastructure-management path.

---

# 14. Default Gateway Considerations

A dual-homed Linux host must be handled carefully when multiple interfaces are present.

Multiple competing default gateways can produce asymmetric routing and unpredictable traffic paths.

Project Alpha therefore treats the network interfaces according to their operational roles rather than simply assigning both interfaces unrestricted default routing.

The desired model is:

```text
External traffic
      │
      ▼
General/Home Network
      │
      ▼
Internet


Project Alpha management traffic
      │
      ▼
Maintenance LAN
      │
      ▼
alpha-node01
```

The Maintenance LAN is not intended to become the server's general Internet gateway.

---

# 15. NetworkManager

NetworkManager is the primary network-management system used by `alpha-node01`.

It manages:

- Ethernet connections
- Wi-Fi connections
- Connection profiles
- Addresses
- Routes
- DNS integration
- Interface state

The operational network state should therefore be evaluated through NetworkManager rather than assuming that an older configuration file represents the current system.

Useful diagnostic commands include:

```bash
nmcli device status
```

```bash
nmcli connection show
```

```bash
ip addr
```

```bash
ip route
```

These commands provide the current operational network state.

---

# 16. Netplan History

Netplan was involved during the early network configuration process.

Project Alpha's networking work evolved as the server transitioned from the original isolated configuration to the final dual-network design.

Netplan configuration should therefore be treated as part of the configuration history.

The authoritative operational state is the configuration actually active on the system.

This distinction prevents historical configuration files from being mistaken for the final architecture.

---

# 17. DNS Architecture

Project Alpha uses internal DNS as part of its infrastructure service architecture.

The DNS layer provides internal name resolution for Project Alpha services.

The intended flow is:

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
Project Alpha Service
```

Internal DNS allows services to be addressed by stable names rather than relying exclusively on IP addresses.

---

# 18. AdGuard Home

AdGuard Home provides the primary internal DNS service within the Project Alpha infrastructure.

Its role includes:

- Internal DNS resolution
- Upstream DNS forwarding
- Internal service naming
- DNS management
- Infrastructure name resolution

AdGuard Home is deployed as a Docker service on `alpha-node01`.

---

# 19. Internal Naming

Project Alpha uses an internal naming model for infrastructure services.

The intended architecture is:

```text
service-name
      │
      ▼
Internal DNS
      │
      ▼
alpha-node01
      │
      ▼
Docker / Reverse Proxy
      │
      ▼
Internal Service
```

This provides a consistent naming layer between clients and containerized services.

The exact service names and DNS records should be documented with the services and DNS configuration rather than being duplicated inconsistently across every document.

---

# 20. DNS and Reverse Proxy Relationship

DNS and the reverse proxy provide separate layers.

DNS answers:

> "Where should this service name resolve?"

The reverse proxy answers:

> "Which internal service should receive this request?"

The resulting architecture is:

```text
Client
  │
  ▼
Internal DNS
  │
  ▼
Service Name
  │
  ▼
Nginx Proxy Manager
  │
  ▼
Docker Proxy Network
  │
  ▼
Backend Service
```

This separation makes the architecture easier to manage and troubleshoot.

---

# 21. Docker Networking

Docker operates on `alpha-node01`.

Project Alpha uses a shared Docker network named:

```text
proxy
```

with the documented subnet:

```text
172.21.0.0/16
```

The shared Docker network provides an internal container-to-container communication layer.

It is separate from the physical Maintenance LAN.

The relationship is:

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
Docker Network
172.21.0.0/16
```

The Docker network should therefore not be confused with the physical Maintenance LAN.

---

# 22. Physical Network vs Docker Network

Project Alpha contains multiple network layers.

```text
PHYSICAL NETWORKS

General/Home Network
        │
        └── Wi-Fi

Maintenance LAN
        │
        └── Ethernet


HOST

alpha-node01
        │
        ▼

CONTAINER NETWORK

Docker proxy
172.21.0.0/16
```

These networks have different purposes.

The physical networks connect systems.

The Docker network connects containers.

---

# 23. DNS Resolution Path

The intended internal DNS resolution path is:

```text
alpha-admin
     │
     ▼
Maintenance LAN
     │
     ▼
AdGuard Home
     │
     ▼
Internal DNS Record
     │
     ▼
Internal Service
```

External DNS requests can be forwarded through configured upstream DNS infrastructure when necessary.

This provides a distinction between:

- internal service resolution
- external DNS resolution

---

# 24. Operational Troubleshooting

Project Alpha networking should be diagnosed from the bottom of the stack upward.

Recommended order:

```text
1. Physical link
       ↓
2. Interface state
       ↓
3. IP addressing
       ↓
4. Routing
       ↓
5. DNS
       ↓
6. Docker networking
       ↓
7. Reverse proxy
       ↓
8. Application service
```

This prevents application-level troubleshooting from masking a lower-level networking problem.

---

# 25. Basic Network Diagnostics

The following commands are useful for diagnosing `alpha-node01`.

### Interface State

```bash
nmcli device status
```

### Connection Profiles

```bash
nmcli connection show
```

### IP Addresses

```bash
ip addr
```

### Routing Table

```bash
ip route
```

### DNS Configuration

```bash
resolvectl status
```

### DNS Resolution

```bash
resolvectl query example.com
```

### Connectivity

```bash
ping -c 4 192.168.1.1
```

### External Connectivity

```bash
ping -c 4 8.8.8.8
```

### Docker Networks

```bash
docker network ls
```

### Docker Network Details

```bash
docker network inspect proxy
```

These commands provide progressively deeper visibility into the network stack.

---

# 26. Security Boundaries

The network architecture establishes a basic separation between external connectivity and infrastructure administration.

The General/Home Network provides Internet access.

The Maintenance LAN provides infrastructure management.

This means normal administration does not depend on the server being administered through the General/Home Network.

The architecture therefore reduces unnecessary exposure of the management path.

This separation is an architectural control, not a replacement for host-level security, authentication, firewalling, or application security.

---

# 27. Network Evolution During Chapter 1

The networking work progressed through several stages.

## Stage 1 — Physical Isolation

The server was connected to the dedicated Maintenance LAN.

The administration workstation could reach the server through the isolated network.

Internet access was intentionally not part of the initial Maintenance LAN design.

---

## Stage 2 — Server Administration

SSH was established as the primary remote administration mechanism.

This allowed the server to be administered without requiring a local display or keyboard.

---

## Stage 3 — General/Home Wi-Fi

The Intel AX210 was configured for General/Home Network connectivity.

This restored Internet access while maintaining the Ethernet-based Maintenance LAN.

---

## Stage 4 — Dual-Network Operation

The server became dual-homed:

```text
Wi-Fi
  │
  ▼
General/Home Network
  │
  ▼
Internet


Ethernet
  │
  ▼
Maintenance LAN
  │
  ▼
Administration
```

---

## Stage 5 — Internal DNS

AdGuard Home was introduced as part of the internal infrastructure.

This established the foundation for internal service discovery and stable service naming.

---

## Stage 6 — Container Networking

Docker networking was introduced as the container-level networking layer.

The `proxy` network provided a shared communication layer for services requiring inter-container connectivity.

---

# 28. Current Chapter 1 Network State

At the Chapter 1 documentation boundary, the intended operational architecture was:

```text
                           GENERAL / HOME NETWORK
                                      │
                                      │ Wi-Fi
                                      ▼
                              Intel AX210
                                      │
                                      ▼
                               alpha-node01
                                      │
                              Killer E3100G
                                      │
                                      │ Ethernet
                                      ▼
                           MAINTENANCE LAN
                                      │
                              Maintenance AP
                                      │
                                      │ Wi-Fi
                                      ▼
                               alpha-admin
                                      │
                                      ▼
                            Windows Workstation
```

The two network paths have separate roles.

### General/Home Network

Used for:

- Internet
- Updates
- Downloads
- External dependencies

### Maintenance LAN

Used for:

- SSH
- Docker administration
- Internal services
- DNS
- PKI
- Infrastructure management

---

# 29. Networking Design Principles

Project Alpha's networking design follows these principles:

### Principle 1 — Separate Administration From External Connectivity

The management path should remain distinct from the general Internet path.

### Principle 2 — Use the Maintenance LAN as the Authoritative Management Plane

Project Alpha administration should occur through the Maintenance LAN.

### Principle 3 — Keep External Dependencies on the General/Home Network

Internet-dependent operations use the General/Home connection.

### Principle 4 — Separate Physical and Container Networks

The Maintenance LAN and Docker networks serve different layers.

### Principle 5 — Document Historical Changes

Network configuration evolved during development.

Historical configurations should not be confused with the final operational design.

### Principle 6 — Prefer Observable Configuration

Current interface, route, and DNS state should be verified with system tools rather than inferred from old configuration files.

---

# 30. Summary

Project Alpha uses a dual-network server architecture.

`alpha-node01` has:

```text
Intel AX210 Wi-Fi
        │
        ▼
General/Home Network
        │
        ▼
Internet / External Dependencies
```

and:

```text
Killer E3100G Ethernet
        │
        ▼
Maintenance LAN
        │
        ▼
Project Alpha Administration
```

The administration environment is:

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
```

The server then provides the containerized infrastructure:

```text
alpha-node01
      │
      ▼
Docker Engine
      │
      ▼
Docker Networks
      │
      ▼
Project Alpha Services
```

This network separation is a foundational part of Project Alpha's infrastructure design.

It allows external connectivity and internal administration to remain distinct while providing a foundation for Docker services, internal DNS, reverse proxying, private PKI, monitoring, credential management, and future VPN infrastructure.
