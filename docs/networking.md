# Networking

# Overview

Project Alpha uses **two independent networks**, each designed for a specific operational purpose.

Rather than relying on a single network for every task, infrastructure management and Internet connectivity are intentionally separated. This architecture reduces operational risk, simplifies troubleshooting, and mirrors the use of dedicated management networks commonly found in enterprise environments.

---

# Network Architecture

Project Alpha consists of two completely separate networks.

## 1. Home Network (Internet)

Purpose:

- Ubuntu package updates (`apt`)
- Docker image downloads
- Git/GitHub
- Software updates
- Internet connectivity
- Remote VPN access (WireGuard)

The Home Network exists **only** to provide Internet connectivity.

Routine administration is **not** performed through this network.

```
                  Home Internet
                        │
                 Home Router / AP
                  ┌────────────┐
                  │            │
             Wi-Fi│            │Ethernet
                  │            │
          alpha-node01    Administration
                            Workstation
```

---

## 2. Management Network

Purpose:

- SSH
- SCP / SFTP
- Infrastructure administration
- Service configuration
- File transfers
- Troubleshooting
- Testing

This network exists exclusively for administration.

It remains isolated from normal Internet traffic.

```
         Administration Workstation
                 Wi-Fi
                   │
                   │
          TP-Link AX1500
        Management Network
                   │
                Ethernet
                   │
             alpha-node01
```

---

# Why Two Networks?

Although both systems are technically capable of communicating through the Home Network, Project Alpha intentionally avoids using that path for routine administration.

This separation provides several advantages:

- Administrative traffic remains isolated from Internet traffic.
- Network experiments are less likely to interrupt management access.
- SSH access remains predictable during routing or firewall changes.
- Infrastructure services can be tested without affecting the primary home network.
- The design reflects enterprise environments where management traffic is separated from production traffic.

---

# Network Interfaces

## alpha-node01

### Wi-Fi Interface

Purpose

- Internet access
- Package updates
- Docker Hub
- GitHub
- WireGuard remote access

### Ethernet Interface

Purpose

- Dedicated management network
- SSH administration
- Service management
- Infrastructure maintenance

---

## Administration Workstation 

### Ethernet Interface

Purpose

- Home Internet access
- General computing
- GitHub
- Documentation
- Web browsing

### Wi-Fi Interface

Purpose

- Dedicated connection to the Project Alpha Management Network
- SSH
- SCP
- Infrastructure administration

---

# Administration Policy

Project Alpha follows a simple operational rule:

> **Internet traffic uses the Home Network.**
>
> **Infrastructure administration uses the Management Network.**

This separation ensures administrative access remains available independently of normal Internet connectivity and provides a controlled environment for infrastructure development and testing.

---

# Administration Environment

Project Alpha is administered from a dedicated administration workstation.

Although the workstation runs **Microsoft Windows** as its native operating system, daily infrastructure administration is performed from a dedicated Linux virtual machine named **alpha-admin**.

```
Windows Workstation
│
├── Daily desktop use
├── GitHub
├── Documentation
└── Oracle VirtualBox
      │
      ▼
 alpha-admin (Ubuntu Desktop)
      │
      ├── SSH
      ├── Git
      ├── Docker CLI
      ├── Linux utilities
      └── Infrastructure administration
            │
            ▼
      Management Network
            │
            ▼
        alpha-node01
```

## Why a Linux Administration VM?

Project Alpha intentionally avoids depending on Windows-specific administration tools such as PowerShell or OpenSSH implementations that may differ between Windows versions or feature configurations.

Using **alpha-admin** provides:

- A consistent Linux administration environment.
- Native OpenSSH tools.
- Consistent Bash shell behavior.
- Better compatibility with Linux documentation.
- A development environment that closely mirrors the production server.

This approach ensures that administration workflows remain portable, reproducible, and independent of Windows-specific configuration changes.

# Future Improvements

- Dedicated management switch
- VLAN segmentation
- Network monitoring
- Centralized logging
- Automated configuration backups
- IPv6 support
