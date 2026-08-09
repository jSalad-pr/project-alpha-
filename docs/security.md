# Project Alpha Security

## Overview

Project Alpha uses a layered security model built around network separation, controlled administration, container isolation, credential management, private PKI, VPN infrastructure, and monitoring.

Security is treated as an infrastructure property rather than a single service.

The primary security boundary begins with the separation between:

1. **Maintenance LAN** — the trusted administration and internal infrastructure network.
2. **General/Home Network** — the external connectivity network used for Internet access and external dependencies.

The server, containers, credentials, certificates, network services, and administrative interfaces are then protected through additional layers.

Project Alpha security is an evolving implementation.

This document distinguishes between:

- controls that have been implemented,
- controls that are part of the current architecture,
- and controls that remain planned or require additional hardening.

---

# 1. Security Objectives

The primary security objectives of Project Alpha are:

- Restrict infrastructure administration to the Maintenance LAN.
- Separate management traffic from normal Internet connectivity.
- Minimize unnecessary network exposure.
- Protect administrative credentials.
- Protect service credentials and secrets.
- Provide encrypted access where appropriate.
- Establish internal certificate infrastructure.
- Monitor service availability.
- Maintain recoverable infrastructure configuration.
- Reduce the impact of individual service compromise.
- Build practical Linux and infrastructure-security experience.

---

# 2. Security Architecture

The overall security model is layered.

```text
                    EXTERNAL NETWORK
                           │
                           │
                     General/Home
                           │
                           ▼
                       Internet
                           │
                           │
                    External Threats
                           │
                           ▼
                  ┌─────────────────┐
                  │  Network Layer  │
                  │                 │
                  │ Dual-Network    │
                  │ Architecture    │
                  └────────┬────────┘
                           │
                           ▼
                    Maintenance LAN
                           │
                           ▼
                    alpha-node01
                           │
                  ┌────────┴────────┐
                  │                 │
                  ▼                 ▼
             Host Security     Docker Layer
                  │                 │
                  │          ┌──────┼──────┐
                  │          │      │      │
                  │          ▼      ▼      ▼
                  │       Services Services Services
                  │
                  └──────────┬──────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
          Credentials       PKI          Monitoring
```

Security controls operate at multiple layers rather than relying on a single defensive mechanism.

---

# 3. Network Segmentation

Network separation is one of the foundational security controls in Project Alpha.

`alpha-node01` uses two network interfaces with different responsibilities.

```text
Intel AX210
     │
     ▼
General/Home Network
     │
     ▼
Internet
```

and:

```text
Killer E3100G
     │
     ▼
Maintenance LAN
     │
     ▼
Project Alpha Administration
```

The two paths are intentionally separated.

---

# 4. Maintenance LAN Security Boundary

The Maintenance LAN is the authoritative administration network.

Administrative services are intended to be accessed through this network rather than directly through the General/Home Network.

The Maintenance LAN is used for:

- SSH
- Docker administration
- Internal DNS
- Reverse-proxy administration
- Infrastructure management
- Service administration
- PKI administration
- Network troubleshooting

This creates a logical management boundary.

---

# 5. General/Home Network

The General/Home Network provides external connectivity.

Its primary purpose is:

- Internet access
- Operating-system updates
- Package downloads
- Docker image downloads
- External repositories
- External dependencies

The General/Home Network should not be treated as the trusted Project Alpha management plane.

---

# 6. Administrative Access

SSH is the primary remote administration mechanism for `alpha-node01`.

The intended administration path is:

```text
Administrator
      │
      ▼
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
      │
      ▼
SSH
```

This keeps normal server administration on the Maintenance LAN.

---

# 7. SSH Security

SSH provides remote shell access to `alpha-node01`.

SSH is an important administrative control because it allows the server to operate headlessly while remaining remotely manageable.

Security considerations include:

- Strong authentication
- Restricting SSH access to the appropriate network
- Avoiding unnecessary Internet exposure
- Maintaining current host software
- Monitoring authentication activity
- Disabling unnecessary authentication methods
- Using least-privilege administrative accounts where practical

The exact final SSH hardening configuration should be treated as an implementation detail that must be verified on the live server.

---

# 8. Administrative Account Separation

Project Alpha uses a dedicated Linux administration environment:

```text
alpha-admin
```

The VM provides the primary Linux workstation from which Project Alpha infrastructure is administered.

This creates a separation between:

- the physical Windows workstation,
- the Linux administration environment,
- and the production-like infrastructure server.

The architecture therefore resembles a small infrastructure administration environment rather than requiring administration directly from the server console.

---

# 9. Least Privilege

Project Alpha follows the principle of least privilege.

Users, services, and containers should receive only the permissions required for their intended functions.

This principle applies to:

- Linux users
- SSH access
- Docker access
- Service accounts
- File permissions
- Container permissions
- API credentials
- DNS administration
- PKI administration

Administrative privileges should not be granted unnecessarily.

---

# 10. Docker Security

Project Alpha uses Docker to isolate individual services from one another and from the host operating system.

The architecture is:

```text
alpha-node01
      │
      ▼
Docker Engine
      │
      ├── Portainer
      ├── Uptime Kuma
      ├── Vaultwarden
      ├── Nginx Proxy Manager
      ├── AdGuard Home
      ├── WireGuard
      └── Step CA
```

Containerization provides operational separation.

However, containers should not be considered an absolute security boundary.

A compromised container may still present risks to:

- the host,
- Docker,
- mounted volumes,
- credentials,
- network resources,
- or other services.

Container configuration must therefore be hardened independently.

---

# 11. Docker Network Isolation

Project Alpha uses a Docker network named:

```text
proxy
```

with the documented subnet:

```text
172.21.0.0/16
```

This network provides an internal communication layer for containers.

The Docker network is separate from the physical Maintenance LAN.

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
proxy
172.21.0.0/16
```

Services should only be attached to the networks they require.

---

# 12. Container Exposure

Containers should not automatically be exposed directly to external networks.

Where possible, internal services should remain accessible through controlled internal paths.

The preferred model is:

```text
Client
  │
  ▼
Maintenance LAN
  │
  ▼
Internal DNS
  │
  ▼
Reverse Proxy
  │
  ▼
Docker Network
  │
  ▼
Service
```

This reduces the number of independently exposed service endpoints.

---

# 13. Reverse Proxy Security

Nginx Proxy Manager provides the centralized reverse-proxy layer.

Its security role includes:

- Centralizing HTTP/HTTPS access
- Providing controlled routing
- Supporting TLS
- Reducing direct backend exposure
- Creating a consistent service-access layer

The architecture is:

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
Docker Proxy Network
  │
  ▼
Backend Service
```

The reverse proxy should not be interpreted as a substitute for application authentication or authorization.

---

# 14. Internal DNS Security

AdGuard Home provides internal DNS functionality.

Internal DNS allows Project Alpha services to use stable internal names.

The intended flow is:

```text
Client
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

Internal DNS should be restricted to the networks and clients that require it.

DNS configuration is part of the infrastructure trust model because incorrect DNS can redirect clients to unintended destinations.

---

# 15. Credential Management

Vaultwarden provides centralized credential-management functionality.

The security objective is to avoid storing infrastructure credentials casually in:

- shell history,
- plain-text documentation,
- Git repositories,
- source code,
- screenshots,
- or unsecured local files.

The intended architecture is:

```text
Administrator
      │
      ▼
Vaultwarden
      │
      ▼
Protected Credentials
```

Credentials should be referenced by function rather than embedded directly into infrastructure documentation.

---

# 16. Secrets Management

Secrets include information such as:

- Passwords
- API keys
- Access tokens
- Private keys
- Database credentials
- Service credentials
- VPN keys
- Certificate authority keys

Secrets must not be committed to the public Project Alpha repository.

Documentation should use placeholders where necessary.

For example:

```text
PASSWORD=<REDACTED>
API_KEY=<REDACTED>
PRIVATE_KEY=<REDACTED>
```

rather than recording actual secret values.

---

# 17. GitHub Repository Security

Project Alpha uses Git for source control and GitHub for repository hosting.

The repository contains infrastructure documentation and configuration-related material.

Sensitive credentials should never be committed to the repository.

Particular care should be taken with:

- `.env` files
- API keys
- passwords
- private keys
- VPN private keys
- database credentials
- certificate authority private keys
- authentication tokens
- backup credentials

If a secret is accidentally committed, removing the visible line from the current version is not sufficient.

The exposed credential should be considered compromised and rotated.

---

# 18. Private PKI

Project Alpha includes private PKI infrastructure.

Step CA provides the foundation for internal certificate issuance.

The intended architecture is:

```text
Root CA
   │
   ▼
Intermediate CA
   │
   ├── Service Certificate
   ├── Service Certificate
   └── Service Certificate
```

Private certificates can be used to provide authenticated encrypted communication between internal clients and services.

The detailed certificate architecture is documented separately in:

```text
docs/pki.md
```

---

# 19. Certificate Security

Private keys associated with the Project Alpha PKI are sensitive credentials.

They should be:

- Stored securely
- Restricted by filesystem permissions
- Excluded from Git
- Backed up securely
- Protected against unauthorized access

Certificate files and public certificates are not equivalent to private keys.

Public certificates may be distributed to trusted clients.

Private CA keys must remain protected.

---

# 20. VPN Security

WireGuard provides the Project Alpha VPN infrastructure.

The intended model is:

```text
Remote Client
      │
      │ Encrypted Tunnel
      ▼
WireGuard
      │
      ▼
Project Alpha Network
```

VPN access should be treated as privileged network access.

A valid VPN credential or key should not automatically imply unrestricted administrative privileges.

Access should be limited according to the intended role of the VPN client.

---

# 21. Monitoring

Uptime Kuma provides service monitoring.

Monitoring is a security-adjacent operational control because service failures can indicate:

- Configuration problems
- Resource exhaustion
- Network failures
- Application failures
- Infrastructure changes
- Potential security incidents

The monitoring architecture is:

```text
Project Alpha Services
        │
        ▼
   Health Checks
        │
        ▼
   Uptime Kuma
```

Monitoring does not replace dedicated security logging.

---

# 22. Logging

System and service logs are important for troubleshooting and security analysis.

Relevant log sources may include:

- SSH authentication logs
- system logs
- Docker logs
- reverse-proxy logs
- DNS logs
- VPN logs
- application logs

Logs should be retained according to the needs of the project.

The current documentation should not claim centralized security logging unless such a system has actually been implemented.

---

# 23. Host Security

`alpha-node01` is the physical infrastructure host.

Host-level security therefore protects the foundation on which all containers depend.

Important host-security controls include:

- Current operating-system updates
- Restricted administrative access
- Strong authentication
- Appropriate filesystem permissions
- Controlled network exposure
- Service minimization
- Secure SSH configuration
- Monitoring
- Backup and recovery planning

The host should be treated as a high-value infrastructure asset.

---

# 24. Operating-System Updates

Ubuntu Server is the base operating system for `alpha-node01`.

Keeping the operating system updated reduces exposure to known vulnerabilities.

Updates should be obtained through trusted repositories.

The General/Home Network provides the external connectivity required for package downloads and updates.

The Maintenance LAN remains the administration path.

---

# 25. Service Updates

Containerized services also require maintenance.

Examples include:

- Docker Engine
- Portainer
- Uptime Kuma
- Vaultwarden
- Nginx Proxy Manager
- AdGuard Home
- WireGuard
- Step CA

Updating a service should account for:

1. Configuration compatibility
2. Persistent data
3. Backup availability
4. Dependency changes
5. Rollback requirements

Container images should not be updated blindly without considering the state of persistent data.

---

# 26. Backup Security

Backups contain the same sensitive information that exists in the infrastructure they represent.

A backup may contain:

- Credentials
- Service databases
- Configuration files
- Private keys
- Certificates
- Application data
- Infrastructure state

Therefore backups must be treated as sensitive assets.

A backup that is accessible to an unauthorized person can effectively bypass many of the security controls protecting the live system.

---

# 27. Recovery Planning

Project Alpha should maintain the ability to recover the infrastructure after:

- Hardware failure
- Storage failure
- Configuration corruption
- Accidental deletion
- Container failure
- Operating-system failure
- Credential compromise

Recovery planning should identify:

- What needs to be backed up
- Where backups are stored
- How backups are protected
- How restoration is performed
- How restoration is verified

A backup should not be considered reliable until restoration has been tested.

---

# 28. Physical Security

`alpha-node01` is a physical laptop serving as the primary infrastructure host.

Physical access to the machine therefore represents a direct security concern.

An attacker with unrestricted physical access may potentially bypass logical controls through:

- Hardware access
- Boot manipulation
- Storage access
- Firmware access
- Direct console access

The physical location of the server should therefore be considered part of the security boundary.

---

# 29. Firmware Security

The server uses UEFI firmware.

Firmware configuration is part of the physical security model.

Relevant considerations include:

- Firmware access protection
- Boot-order control
- Secure boot considerations
- Physical access restrictions
- Firmware updates

The documentation should distinguish between controls that are configured and controls that are merely recommended.

---

# 30. Security Boundaries

Project Alpha contains several security boundaries.

```text
                    Internet
                       │
                       ▼
              General/Home Network
                       │
                       │
               ┌───────┴───────┐
               │               │
               ▼               │
          alpha-node01         │
               │               │
               ▼               │
         Maintenance LAN ◄─────┘
               │
               ▼
          alpha-admin
               │
               ▼
        Administrative Access
```

Inside the server:

```text
alpha-node01
     │
     ▼
Docker Engine
     │
     ├── Service A
     ├── Service B
     ├── Service C
     └── Service D
```

Each boundary provides a different type of isolation.

---

# 31. Threat Model

Project Alpha's primary threats include:

## Unauthorized Network Access

An unauthorized system attempts to reach infrastructure services.

### Mitigation

- Maintenance LAN separation
- Restricted service exposure
- Authentication
- VPN controls
- Host/network security

---

## Credential Theft

An attacker obtains an administrative credential.

### Mitigation

- Vaultwarden
- Strong credentials
- Secret-management practices
- Avoiding credentials in Git
- Credential rotation

---

## Service Compromise

An attacker compromises a vulnerable containerized service.

### Mitigation

- Container isolation
- Network segmentation
- Limited service exposure
- Service updates
- Least privilege
- Monitoring

---

## Host Compromise

An attacker gains control of `alpha-node01`.

### Mitigation

- Restricted administration
- Operating-system updates
- SSH hardening
- Physical security
- Reduced service exposure
- Monitoring

---

## Data Loss

Infrastructure data is lost through hardware or configuration failure.

### Mitigation

- Persistent storage
- Backups
- Recovery procedures
- Tested restoration

---

## Secret Exposure

A password, private key, or token is accidentally published.

### Mitigation

- Do not commit secrets
- Use Vaultwarden
- Use placeholders in documentation
- Rotate exposed credentials
- Review Git history when necessary

---

# 32. Security Philosophy

Project Alpha follows a defense-in-depth model.

No individual component is assumed to provide complete security.

For example:

```text
Network Separation
       +
Authentication
       +
Least Privilege
       +
Container Isolation
       +
Credential Management
       +
PKI
       +
VPN
       +
Monitoring
       +
Backups
```

Together these controls provide stronger protection than relying on any single mechanism.

---

# 33. Implemented vs Planned Controls

Project Alpha documentation distinguishes implementation state.

## Implemented / Established

The architecture currently establishes:

- Dedicated Maintenance LAN
- Dual-network server architecture
- SSH administration
- Docker containerization
- Docker network separation
- Portainer
- Uptime Kuma
- Vaultwarden
- Nginx Proxy Manager
- AdGuard Home
- WireGuard infrastructure
- Private PKI infrastructure

## Requires Verification / Hardening

The following should be explicitly verified on the live infrastructure:

- SSH hardening configuration
- Firewall configuration
- Container privilege configuration
- Service exposure
- Docker socket access
- File permissions
- Backup restoration
- Certificate trust configuration
- VPN access restrictions
- Logging configuration

## Planned / Future

Potential future security work includes:

- More comprehensive host firewall policy
- Centralized security logging
- Automated vulnerability scanning
- Automated backup validation
- More granular service isolation
- Automated certificate lifecycle management
- Additional access-control policies
- Security auditing procedures

The distinction prevents planned controls from being represented as already operational.

---

# 34. Security Validation

Security should be validated through observable system state.

Examples include:

```bash
ss -tulpn
```

for listening services,

```bash
ip route
```

for routing,

```bash
nmcli device status
```

for network interfaces,

```bash
docker ps
```

for running containers,

```bash
docker network ls
```

for Docker networks,

and:

```bash
sudo systemctl status ssh
```

for SSH service status.

Additional tools should be introduced as the security architecture develops.

---

# 35. Security Documentation Rules

Project Alpha documentation should follow these rules:

### Rule 1

Never place real passwords in Git.

### Rule 2

Never place private keys in Git.

### Rule 3

Never publish API tokens.

### Rule 4

Use placeholders for sensitive values.

### Rule 5

Distinguish implemented controls from planned controls.

### Rule 6

Document observed configuration separately from intended architecture.

### Rule 7

Do not claim a security control exists without verifying it.

### Rule 8

Treat backups as sensitive infrastructure assets.

### Rule 9

Treat VPN access as privileged access.

### Rule 10

Treat the server host as a high-value security boundary.

---

# 36. Current Security Architecture

The current Project Alpha security model can be summarized as:

```text
                         INTERNET
                            │
                            ▼
                   GENERAL/HOME NETWORK
                            │
                            ▼
                       alpha-node01
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
        HOST SECURITY             DOCKER ENGINE
                │                       │
                │                ┌──────┼──────┐
                │                │      │      │
                │                ▼      ▼      ▼
                │             Services Services
                │
                ▼
        MAINTENANCE LAN
                │
                ▼
           alpha-admin
                │
                ▼
         ADMINISTRATION
```

Security services provide additional layers:

```text
Vaultwarden → Credentials
Step CA     → PKI / Certificates
WireGuard   → VPN
AdGuard     → Internal DNS
NPM         → Controlled Service Access
Uptime Kuma → Monitoring
```

---

# 37. Security Roadmap

Future security development should proceed in stages.

## Phase 1 — Foundation

- Network separation
- SSH administration
- Docker isolation
- Credential management
- Internal DNS

## Phase 2 — Encryption

- Private PKI
- Internal TLS
- Certificate deployment
- Certificate trust management

## Phase 3 — Remote Access

- WireGuard
- VPN access controls
- Remote administration policies

## Phase 4 — Host Hardening

- Firewall
- SSH hardening
- Service minimization
- File-permission review
- Container hardening

## Phase 5 — Monitoring

- Centralized logs
- Security event visibility
- Service monitoring
- Alerting

## Phase 6 — Recovery

- Automated backups
- Backup encryption
- Restoration testing
- Disaster-recovery documentation

---

# 38. Summary

Project Alpha uses a layered security architecture.

The foundation is network separation:

```text
General/Home Network
        │
        ▼
Internet / External Dependencies


Maintenance LAN
        │
        ▼
Administration / Internal Infrastructure
```

The server layer provides:

```text
Ubuntu Server
      │
      ▼
Docker Engine
      │
      ▼
Containerized Services
```

Security infrastructure provides:

```text
Vaultwarden → Credentials
Step CA     → PKI
WireGuard   → VPN
AdGuard     → Internal DNS
NPM         → Reverse Proxy
Uptime Kuma → Monitoring
```

The security model is intentionally defense-in-depth.

Project Alpha does not treat network separation, Docker, VPN, PKI, credentials, or monitoring as complete security solutions individually.

Instead, each contributes one layer to the overall infrastructure security model.

The architecture remains under active development, and controls should be documented as implemented only after they have been verified on the live system.
