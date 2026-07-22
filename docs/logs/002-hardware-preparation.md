# Development Log 002 - Hardware Preparation

## Objective

Prepare the server hardware for migration from Windows to Ubuntu Server while preserving existing data and documenting the system's hardware configuration.

## Background

Before replacing the operating system, it was necessary to safeguard personal files and establish a hardware baseline. Proper preparation reduces deployment risk and provides a known-good reference for future troubleshooting.

## Data Preservation

Approximately 30 GB of personal files were successfully backed up prior to migration.

An unused smartphone was repurposed as a temporary microSD card reader, allowing data recovery without purchasing additional hardware. This eliminated a hardware dependency and allowed the migration process to continue without delay.

## Hardware Inventory

The server hardware was documented before installation to establish a baseline reference.

| Component | Specification |
|-----------|---------------|
| System | MSI GE76 Raider 11UE |
| CPU | Intel Core i7-11800H |
| Memory | 16 GB DDR4 |
| Graphics | Intel UHD Graphics + NVIDIA RTX 3060 Laptop GPU |
| Firmware | UEFI BIOS |
| Display | 1920 × 1080 @ 144 Hz |

A DirectX Diagnostic (DxDiag) report was generated and archived to preserve the original hardware configuration before replacing the operating system.

## Validation

The following tasks were completed prior to migration:

- Personal data successfully backed up.
- Hardware inventory documented.
- Existing Windows installation verified.
- Hardware baseline captured for future reference.

## Outcome

The system was successfully prepared for operating system migration. Existing data was preserved, hardware was documented, and deployment could proceed with confidence that no important information would be lost.

## Lessons Learned

Preparation is an essential part of infrastructure deployment. Verifying backups and documenting hardware before making destructive changes reduces risk and simplifies future troubleshooting.

Repurposing available equipment can often eliminate unnecessary costs while maintaining project momentum.

## Next Steps

- Create Ubuntu Server installation media.
- Install Ubuntu Server.
- Configure remote administration.
