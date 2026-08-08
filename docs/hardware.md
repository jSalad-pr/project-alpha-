# Project Alpha Hardware

## Overview

Project Alpha is built using repurposed personal hardware and a virtualized administration environment.

The primary hardware involved in the Project Alpha infrastructure is:

- **MSI GE76 Raider 11UE** — `alpha-node01`, the primary physical server
- **Windows Workstation** — primary physical administration workstation
- **alpha-admin** — Ubuntu Desktop VM running on the Windows workstation
- **Maintenance Router / Access Point** — dedicated Maintenance LAN networking hardware

This document records the physical hardware specifications and the role each device plays in Project Alpha.

---

# 1. alpha-node01 — MSI GE76 Raider 11UE

## Hardware Overview

`alpha-node01` is a repurposed MSI GE76 Raider 11UE gaming laptop converted into the primary Project Alpha server.

### System

| Component | Specification |
|---|---|
| Manufacturer | Micro-Star International Co., Ltd. (MSI) |
| Model | GE76 Raider 11UE |
| Current Hostname | `alpha-node01` |
| Original Operating System | Windows 11 Home 64-bit |
| Current Operating System | Ubuntu Server 26.04 LTS |
| Firmware | UEFI |
| BIOS Version | `E17K3IMS.11D` |
| DirectX Baseline | DirectX 12 |

The original Windows installation was documented with DxDiag before the machine was converted into the Project Alpha server.

---

## CPU

### Intel Core i7-11800H

| Specification | Value |
|---|---|
| CPU | Intel Core i7-11800H |
| Generation | 11th Gen Intel Core |
| Physical Cores | 8 |
| Threads | 16 |
| Base Clock | 2.30 GHz |
| Architecture | x86-64 |

The i7-11800H provides the primary compute resources for `alpha-node01`.

The processor's 8-core / 16-thread configuration provides sufficient CPU capacity for the current Docker-based Project Alpha infrastructure.

---

## Memory

### System RAM

| Specification | Value |
|---|---|
| Installed RAM | 16 GB |
| DxDiag Reported | 16,384 MB |
| Windows Available Memory | ~16,086 MB |
| Memory Type | DDR4 |

The available-memory figure was recorded under the original Windows installation and should not be interpreted as the current Ubuntu memory allocation.

---

# GPU

The GE76 Raider contains both integrated and discrete graphics.

## Integrated GPU — Intel UHD Graphics

| Specification | Value |
|---|---|
| GPU | Intel UHD Graphics |
| Type | Integrated |
| Dedicated Memory Reported | 128 MB |
| Shared System Memory | ~8 GB |
| Total Available Graphics Memory | ~8 GB |

The integrated GPU is part of the laptop's original hardware platform.

It is not required for the normal Project Alpha server workload.

---

## Discrete GPU — NVIDIA GeForce RTX 3060 Laptop GPU

| Specification | Value |
|---|---|
| GPU | NVIDIA GeForce RTX 3060 Laptop GPU |
| Dedicated VRAM | 6 GB |
| DxDiag Dedicated Memory | 5,996 MB |
| Shared Memory | 8,042 MB |
| Total Reported Display Memory | 14,038 MB |
| PCI Device ID | `10DE:2520` |

The RTX 3060 is part of the original GE76 Raider hardware.

It is currently not required for the primary Project Alpha server workload.

The GPU is nevertheless documented because it is a significant component of the physical server hardware and may provide future compute possibilities.

---

# Display

The original laptop display was documented before the machine was converted into a headless server.

| Specification | Value |
|---|---|
| Resolution | 1920 × 1080 |
| Refresh Rate | 144 Hz |
| Native Mode | 1920 × 1080 @ 144 Hz |
| Panel | Internal |
| Monitor ID | `AUO978F` |
| HDR | Not supported |
| Color Mode | SDR |
| Approx. Maximum Luminance | 270 nits |

The internal display is part of the physical laptop but is not required for normal server operation.

`alpha-node01` is intended to operate as a headless server and is normally administered remotely through SSH.

---

# Storage

## Samsung NVMe SSD

The original Windows hardware baseline identified the primary internal NVMe drive as:

| Specification | Value |
|---|---|
| Manufacturer | Samsung |
| Model | `MZVLQ1T0HALB-00000` |
| Advertised Capacity | ~1 TB |
| Windows Reported Capacity | 975.9 GB |
| Original File System | NTFS |
| Free Space at Documentation | 330.7 GB |
| Interface | NVMe |

The NTFS filesystem and free-space values describe the original Windows state before the machine was converted to Ubuntu Server.

The drive became part of the server storage platform following the operating-system migration.

---

# Wireless Networking

## Killer Wi-Fi 6E AX1675x 160 MHz

The GE76 Raider includes a Killer Wi-Fi 6E wireless adapter.

| Specification | Value |
|---|---|
| Adapter | Killer Wi-Fi 6E AX1675x 160 MHz |
| Variant | 210NGW |
| Interface | Wireless |
| PCI Device ID | `8086:2725` |
| Technology | Wi-Fi 6E |

### Project Alpha Role

The wireless adapter provides `alpha-node01` with connectivity to the **General/Home Network**.

Its intended purposes include:

- Internet connectivity
- Operating-system updates
- Package downloads
- Docker/container image downloads
- External dependency acquisition
- Future remote-access functionality

The Wi-Fi interface is **not the normal administration path** for Project Alpha.

Project Alpha administration is intentionally performed through the Maintenance LAN.

---

# Ethernet Networking

## Killer E3100G 2.5 Gigabit Ethernet

The GE76 Raider includes a Killer E3100G wired Ethernet controller.

| Specification | Value |
|---|---|
| Controller | Killer E3100G |
| Interface | Ethernet |
| Maximum Link Capability | 2.5 GbE |
| PCI Device ID | `10EC:3000` |

### Project Alpha Role

The Ethernet interface provides `alpha-node01` with its connection to the **Maintenance LAN**.

It is the primary physical network interface for Project Alpha administration.

The Maintenance Ethernet connection is used for:

- SSH administration
- Docker administration
- Service management
- Internal DNS
- Internal service access
- Private PKI administration
- Network troubleshooting
- Infrastructure management

---

# Network Hardware Role Summary

`alpha-node01` is a dual-network physical server.

| Physical Interface | Network | Primary Role |
|---|---|---|
| Killer Wi-Fi 6E AX1675x | General/Home Network | Internet and external dependencies |
| Killer E3100G 2.5GbE | Maintenance LAN | Project Alpha administration and services |

This separation is intentional.

The General/Home connection exists to provide external connectivity.

The Maintenance LAN is the authoritative operational network for Project Alpha.

---

# Other Integrated Hardware

The original system hardware baseline also identified several additional components.

## Audio

The system contains:

- Realtek Audio
- Intel Smart Sound Technology
- NVIDIA High Definition Audio
- MSI audio components

These components are part of the original laptop platform but are not required for the server's normal workload.

---

## Camera

The laptop contains an integrated camera.

The original Windows hardware baseline identified:

- Integrated Camera
- USB Video Class support
- Microsoft USB Video Device driver

The camera is not required for Project Alpha infrastructure operation.

---

## Card Reader

The laptop includes:

- Realtek PCIe Card Reader

The card reader is not required for normal Project Alpha operation.

---

## USB Controller

The laptop includes:

- Intel USB 3.20 eXtensible Host Controller

This provides the system's USB controller functionality.

---

# Firmware

The original Windows hardware baseline recorded:

| Specification | Value |
|---|---|
| BIOS | `E17K3IMS.11D` |
| Firmware Interface | UEFI |

Firmware information is retained as part of the original physical hardware baseline.

---

# Server Conversion

## From Windows Laptop to Linux Server

The GE76 Raider originally functioned as a general-purpose Windows laptop.

The machine was subsequently repurposed as the primary Project Alpha server.

### Original State

```text
MSI GE76 Raider 11UE
│
├── Windows 11 Home
├── Intel Core i7-11800H
├── 16 GB DDR4
├── RTX 3060 Laptop GPU
├── Samsung ~1 TB NVMe
├── Wi-Fi 6E
├── 2.5 GbE
└── General-purpose / gaming system

# Operating System & Software Platform

## alpha-node01 Operating System

`alpha-node01` currently runs Ubuntu Server 26.04 LTS.

The operating system replaced the original Windows installation when the MSI GE76 Raider 11UE was repurposed as the Project Alpha server.

| Component | Current Configuration |
|---|---|
| Hostname | `alpha-node01` |
| Operating System | Ubuntu Server 26.04 LTS |
| System Type | 64-bit Linux server |
| Server Role | Primary Project Alpha infrastructure host |
| Administration | SSH |
| Network Management | NetworkManager |
| Time Synchronization | Chrony |
| Container Runtime | Docker Engine |
| Container Orchestration | Docker Compose |
| Source Control | Git |
| Server Mode | Headless |

---

## Base Operating System

### Ubuntu Server 26.04 LTS

Ubuntu Server provides the base operating system for `alpha-node01`.

The system was installed specifically to convert the former Windows laptop into a dedicated Linux infrastructure server.

The server is intended to operate without requiring a local graphical desktop environment.

Administration is performed remotely through SSH from the dedicated `alpha-admin` Ubuntu Desktop VM.

---

# System Administration

## SSH

SSH is the primary remote administration mechanism.

```text
alpha-admin
     │
     │ SSH
     ▼
alpha-node01
