# Project Alpha Hardware

## Overview

Project Alpha is built from repurposed personal computing hardware.

The physical infrastructure consists of:

1. A primary Windows workstation used for administration and virtualization.
2. A repurposed laptop operating as `alpha-node01`, the primary Project Alpha server.
3. A dedicated maintenance networking device providing the Maintenance LAN.

---

## Primary Administration Workstation

The primary workstation is a Windows desktop PC.

### Role

The workstation serves as:

- Primary human interface for Project Alpha
- Windows administration environment
- Virtualization host
- Network administration workstation
- Development workstation

The workstation hosts the `alpha-admin` virtual machine.

```text
Windows Workstation
        │
        └── alpha-admin
              └── Ubuntu Desktop VM
