# Development Log 013 - Internal DNS

## Objective

Deploy an internal DNS service to provide centralized name resolution for Project Alpha infrastructure and establish the foundation for service discovery across the Maintenance LAN.

## Background

As Project Alpha expanded beyond the initial container services, relying on IP addresses and manually managed host information became increasingly impractical.

A dedicated internal DNS service provides a consistent naming layer for infrastructure while allowing services to be referenced by internal hostnames rather than individual IP addresses.

This also establishes a foundation for future internal HTTPS, service discovery, and infrastructure management.

## Implementation

AdGuard Home was deployed as a Docker container within the Project Alpha infrastructure platform.

The deployment provided:

- Centralized DNS resolution.
- Web-based administration.
- Persistent configuration storage.
- Integration with the existing Docker infrastructure.
- DNS-based access to internal Project Alpha services.

The administration environment was configured to use the Project Alpha DNS service for internal name resolution.

## Network Integration

The internal DNS service was designed to operate within the Project Alpha Maintenance LAN while preserving the separation between infrastructure management and General/Home network connectivity.

The resulting architecture provides:

```text
alpha-admin
     │
     │ DNS
     ▼
AdGuard Home
     │
     ├── Internal Project Alpha names
     │
     └── External DNS resolution
