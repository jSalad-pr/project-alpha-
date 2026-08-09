
# Project Alpha Roadmap

## Overview

Project Alpha is a self-hosted infrastructure laboratory built around a repurposed physical server, a dedicated Linux administration environment, a Maintenance LAN, Docker-based services, and a separate General/Home network connection.

The project is designed to develop practical experience in:

- Linux administration
- Network administration
- Virtualization
- Docker
- DNS
- Reverse proxies
- TLS
- Private PKI
- VPN infrastructure
- Monitoring
- Security
- Automation
- Infrastructure documentation

The roadmap defines the intended direction of Project Alpha.

It does not replace the changelog.

The changelog records what has happened.

The roadmap records what should happen next.

---

# 1. Current Project State

Project Alpha currently has the following foundational architecture:

```text
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
                    Ubuntu Server
                           │
                           ▼
                     Docker Engine
                           │
             ┌─────────────┼─────────────┐
             │             │             │
             ▼             ▼             ▼
          Services      Networking     Security
```

The current service layer includes:

- Portainer
- Uptime Kuma
- Vaultwarden
- Nginx Proxy Manager
- AdGuard Home
- WireGuard
- Step CA

The documentation foundation includes:

- Hardware
- Architecture
- Networking
- Services
- Security
- PKI
- Changelog
- Roadmap

---

# 2. Roadmap Philosophy

Project Alpha should progress from foundational infrastructure toward increasingly advanced administration and security.

The general progression is:

```text
Foundation
    │
    ▼
Documentation
    │
    ▼
Service Integration
    │
    ▼
Security Hardening
    │
    ▼
PKI / TLS
    │
    ▼
Remote Access
    │
    ▼
Monitoring
    │
    ▼
Backup / Recovery
    │
    ▼
Automation
    │
    ▼
Infrastructure as Code
```

The objective is not to deploy features simply for the sake of having them.

Each stage should provide practical infrastructure experience and produce a maintainable system.

---

# 3. Phase 1 — Foundation

## Status

**Established**

The foundational infrastructure has been created.

### Completed

- [x] Repurpose physical server
- [x] Establish `alpha-node01`
- [x] Install Ubuntu Server
- [x] Establish headless server operation
- [x] Establish SSH administration
- [x] Establish `alpha-admin`
- [x] Establish Maintenance LAN
- [x] Establish General/Home connectivity
- [x] Install Docker Engine
- [x] Install Docker Compose
- [x] Establish Docker networking
- [x] Deploy foundational services

---

# 4. Phase 2 — Documentation Foundation

## Status

**Established**

The major architecture documents have been created and synchronized.

### Completed

- [x] `hardware.md`
- [x] `architecture.md`
- [x] `networking.md`
- [x] `services.md`
- [x] `security.md`
- [x] `pki.md`
- [x] `changelog.md`
- [x] `roadmap.md`

### Documentation Objective

Maintain a clear distinction between:

```text
Observed
```

```text
Implemented
```

```text
Requires Verification
```

and:

```text
Planned
```

This prevents the documentation from becoming detached from the actual infrastructure.

---

# 5. Phase 3 — Service Integration

## Status

**In Progress**

The major services exist, but the project should continue integrating them into a coherent infrastructure platform.

### Objectives

- [ ] Verify all service containers
- [ ] Verify persistent volumes
- [ ] Verify Docker networks
- [ ] Verify service dependencies
- [ ] Verify service restart behavior
- [ ] Verify internal DNS
- [ ] Verify reverse-proxy routing
- [ ] Verify service accessibility from `alpha-admin`
- [ ] Document verified service endpoints
- [ ] Document actual service dependencies

### Success Criteria

Every deployed service should have:

- A defined purpose
- A defined access path
- Persistent data where required
- Documented dependencies
- A known operational state

---

# 6. Phase 4 — Host Hardening

## Status

**Planned / Requires Verification**

The server host should be hardened before expanding the infrastructure further.

### Objectives

- [ ] Review SSH configuration
- [ ] Review authentication methods
- [ ] Review administrative users
- [ ] Review sudo permissions
- [ ] Review filesystem permissions
- [ ] Review listening ports
- [ ] Review running services
- [ ] Review firewall configuration
- [ ] Review Docker privileges
- [ ] Review container privileges
- [ ] Remove unnecessary services
- [ ] Verify operating-system update process

### Validation

Useful validation commands include:

```bash
ss -tulpn
```

```bash
systemctl --type=service
```

```bash
sudo ufw status
```

```bash
docker ps
```

```bash
docker network ls
```

The actual state should be documented only after verification.

---

# 7. Phase 5 — Internal DNS

## Status

**Established / Requires Integration Verification**

AdGuard Home provides the internal DNS foundation.

### Objectives

- [ ] Define internal naming convention
- [ ] Create required DNS records
- [ ] Verify DNS resolution from `alpha-admin`
- [ ] Verify service-name resolution
- [ ] Verify upstream DNS configuration
- [ ] Document DNS architecture
- [ ] Verify DNS behavior across the Maintenance LAN

### Target Model

```text
alpha-admin
     │
     ▼
AdGuard Home
     │
     ▼
Internal DNS
     │
     ▼
Project Alpha Services
```

---

# 8. Phase 6 — Reverse Proxy

## Status

**Established / Requires Integration Verification**

Nginx Proxy Manager provides the reverse-proxy layer.

### Objectives

- [ ] Verify proxy network
- [ ] Verify backend connectivity
- [ ] Configure internal service names
- [ ] Verify proxy routing
- [ ] Verify TLS integration
- [ ] Remove unnecessary direct service exposure
- [ ] Document proxy mappings

### Target Model

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
Docker Network
  │
  ▼
Backend Service
```

---

# 9. Phase 7 — Private PKI

## Status

**Established / Requires Deployment Verification**

Step CA provides the private PKI foundation.

### Objectives

- [ ] Verify Step CA installation
- [ ] Verify CA hierarchy
- [ ] Verify root CA
- [ ] Verify intermediate CA
- [ ] Define certificate naming convention
- [ ] Issue test certificate
- [ ] Verify certificate validation
- [ ] Establish internal trust
- [ ] Integrate certificates with internal services
- [ ] Document certificate lifecycle

### Target Hierarchy

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

### Security Requirement

Private CA keys must never be committed to Git.

---

# 10. Phase 8 — Internal TLS

## Status

**Planned**

Once the private PKI is verified, internal TLS can be deployed.

### Objectives

- [ ] Establish trusted CA on `alpha-admin`
- [ ] Issue internal certificates
- [ ] Configure Nginx Proxy Manager
- [ ] Enable HTTPS for selected services
- [ ] Verify certificate chains
- [ ] Verify hostname matching
- [ ] Verify certificate expiration
- [ ] Document TLS deployment

### Target Model

```text
Client
  │
  │ HTTPS
  ▼
Nginx Proxy Manager
  │
  ▼
Internal Service
```

---

# 11. Phase 9 — VPN

## Status

**Established / Requires Security Verification**

WireGuard provides the VPN foundation.

### Objectives

- [ ] Verify WireGuard configuration
- [ ] Verify peer configuration
- [ ] Verify routing
- [ ] Verify allowed networks
- [ ] Verify DNS behavior over VPN
- [ ] Restrict VPN access appropriately
- [ ] Test remote administration
- [ ] Document VPN access policy

### Target Model

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

VPN credentials should be treated as privileged access credentials.

---

# 12. Phase 10 — Monitoring

## Status

**Established / Requires Expansion**

Uptime Kuma provides the monitoring foundation.

### Objectives

- [ ] Monitor core services
- [ ] Monitor DNS
- [ ] Monitor reverse proxy
- [ ] Monitor VPN availability
- [ ] Monitor critical endpoints
- [ ] Establish alerting
- [ ] Document monitoring targets
- [ ] Establish operational response procedures

### Target Model

```text
Infrastructure
     │
     ▼
Health Checks
     │
     ▼
Uptime Kuma
     │
     ▼
Alerts / Operational Visibility
```

---

# 13. Phase 11 — Logging

## Status

**Planned**

Project Alpha should eventually provide centralized visibility into important system and service logs.

### Objectives

- [ ] Identify critical log sources
- [ ] Review SSH authentication logs
- [ ] Review Docker logs
- [ ] Review reverse-proxy logs
- [ ] Review DNS logs
- [ ] Review VPN logs
- [ ] Determine retention requirements
- [ ] Evaluate centralized logging solutions
- [ ] Document log-management architecture

The project should not claim centralized security logging until such a system is actually implemented.

---

# 14. Phase 12 — Backup

## Status

**Planned**

Backups are required before the infrastructure can be considered resilient.

### Objectives

- [ ] Identify critical data
- [ ] Identify configuration data
- [ ] Identify persistent Docker volumes
- [ ] Identify PKI data
- [ ] Identify credential-management data
- [ ] Define backup locations
- [ ] Define backup frequency
- [ ] Protect backup credentials
- [ ] Encrypt sensitive backups
- [ ] Document restoration procedures

### Critical Principle

A backup is not considered reliable until restoration has been tested.

---

# 15. Phase 13 — Disaster Recovery

## Status

**Planned**

Disaster recovery builds on the backup system.

### Objectives

- [ ] Document hardware-replacement procedure
- [ ] Document Ubuntu installation procedure
- [ ] Document Docker installation
- [ ] Restore Compose configuration
- [ ] Restore persistent volumes
- [ ] Restore DNS configuration
- [ ] Restore reverse proxy configuration
- [ ] Restore PKI
- [ ] Restore credential-management infrastructure
- [ ] Test full recovery

### Target Outcome

A failed server should be recoverable without relying on undocumented personal knowledge.

---

# 16. Phase 14 — Automation

## Status

**Future**

Project Alpha should eventually automate repetitive administrative tasks.

Potential automation areas include:

- Service deployment
- Updates
- Backups
- Health checks
- Certificate renewal
- Configuration validation
- Monitoring
- Documentation generation

Automation should be introduced only after the underlying manual procedures are understood and documented.

---

# 17. Phase 15 — Infrastructure as Code

## Status

**Future**

The project can eventually transition from manually configured infrastructure toward reproducible infrastructure definitions.

Potential technologies include:

- Docker Compose
- Ansible
- Terraform
- Shell automation
- Git-based configuration management

The goal is to make infrastructure reproducible rather than dependent on undocumented manual configuration.

---

# 18. Phase 16 — Security Validation

## Status

**Future**

Once the infrastructure foundation is stable, Project Alpha can be used as a controlled environment for security testing.

Potential areas include:

- Port scanning
- Service enumeration
- Authentication testing
- Network segmentation testing
- Container security testing
- TLS validation
- DNS security testing
- Vulnerability assessment
- Log analysis

Testing should remain limited to infrastructure that the user owns or is explicitly authorized to test.

---

# 19. Phase 17 — Advanced Infrastructure

## Status

**Future**

Longer-term development may include:

- Additional virtualization
- Additional Linux nodes
- More complex network segmentation
- Service discovery
- Automated provisioning
- High-availability experimentation
- Infrastructure monitoring
- Centralized logging
- Automated recovery
- Infrastructure-as-code workflows

These are future possibilities rather than current infrastructure requirements.

---

# 20. Project Alpha Learning Path

The project is intentionally structured as a learning progression.

```text
Linux
 │
 ▼
Networking
 │
 ▼
Docker
 │
 ▼
DNS
 │
 ▼
Reverse Proxy
 │
 ▼
TLS / PKI
 │
 ▼
VPN
 │
 ▼
Monitoring
 │
 ▼
Security
 │
 ▼
Backup / Recovery
 │
 ▼
Automation
 │
 ▼
Infrastructure as Code
```

Each stage builds on the previous stage.

---

# 21. Priority Order

The recommended priority order is:

```text
1. Verify existing services
        │
        ▼
2. Host security hardening
        │
        ▼
3. Internal DNS verification
        │
        ▼
4. Reverse proxy verification
        │
        ▼
5. PKI verification
        │
        ▼
6. Internal TLS
        │
        ▼
7. VPN security verification
        │
        ▼
8. Monitoring expansion
        │
        ▼
9. Backups
        │
        ▼
10. Disaster recovery
        │
        ▼
11. Automation
        │
        ▼
12. Infrastructure as Code
```

This order prioritizes stability and security before adding additional complexity.

---

# 22. Documentation Synchronization

The documentation itself should remain synchronized with the live infrastructure.

The synchronization process should be:

```text
Live System
     │
     ▼
Verify Configuration
     │
     ▼
Update Documentation
     │
     ▼
Commit to Git
     │
     ▼
Review Repository
```

Documentation should not be updated solely from memory when the live configuration can be verified.

---

# 23. Milestone Tracking

## Foundation

- [x] Physical server
- [x] Ubuntu Server
- [x] SSH
- [x] `alpha-admin`
- [x] Maintenance LAN
- [x] General/Home Network
- [x] Docker
- [x] Docker Compose

## Documentation

- [x] Hardware
- [x] Architecture
- [x] Networking
- [x] Services
- [x] Security
- [x] PKI
- [x] Changelog
- [x] Roadmap

## Integration

- [ ] Verify services
- [ ] Verify DNS
- [ ] Verify reverse proxy
- [ ] Verify service routing
- [ ] Verify persistent storage

## Security

- [ ] Host hardening
- [ ] SSH hardening verification
- [ ] Firewall verification
- [ ] Container security review
- [ ] Secret-management review

## PKI / TLS

- [ ] Verify Step CA
- [ ] Verify CA hierarchy
- [ ] Issue test certificate
- [ ] Establish trust
- [ ] Deploy internal TLS

## Remote Access

- [ ] Verify WireGuard
- [ ] Verify routing
- [ ] Verify DNS over VPN
- [ ] Verify access restrictions

## Operations

- [ ] Expand monitoring
- [ ] Centralize important logs
- [ ] Establish backups
- [ ] Test restoration
- [ ] Document disaster recovery

## Automation

- [ ] Automate repetitive tasks
- [ ] Introduce configuration management
- [ ] Develop infrastructure-as-code workflows

---

# 24. Definition of Project Alpha Maturity

Project Alpha should not be considered complete merely because every planned service is installed.

A mature state requires:

```text
Working Infrastructure
        +
Documented Configuration
        +
Security Controls
        +
Monitoring
        +
Backups
        +
Recovery Procedures
        +
Reproducibility
```

The final objective is a system that can be:

- understood,
- administered,
- secured,
- monitored,
- recovered,
- and reproduced.

---

# 25. Long-Term Vision

The long-term goal of Project Alpha is to evolve from a personal homelab into a practical infrastructure engineering environment.

The project should eventually demonstrate competence across:

```text
Linux Administration
        │
        ▼
Network Administration
        │
        ▼
Containerization
        │
        ▼
Security
        │
        ▼
PKI / TLS
        │
        ▼
Monitoring
        │
        ▼
Automation
        │
        ▼
Infrastructure as Code
```

The emphasis remains on practical implementation rather than theoretical configuration.

---

# 26. Current Next Steps

The immediate development priorities are:

1. Verify the existing service layer.
2. Verify host security configuration.
3. Verify internal DNS.
4. Verify reverse-proxy routing.
5. Verify Step CA and the PKI hierarchy.
6. Establish internal TLS where appropriate.
7. Verify WireGuard security and routing.
8. Expand monitoring.
9. Establish reliable backups.
10. Test recovery.
11. Begin automation.
12. Begin infrastructure-as-code work.

---

# 27. Roadmap Maintenance

This roadmap should be updated whenever:

- A major milestone is completed.
- A project priority changes.
- A planned feature is removed.
- A new infrastructure requirement is identified.
- A security requirement changes.
- A service is added or retired.

Completed work should also be reflected in `changelog.md`.

The roadmap should describe the future state.

The changelog should describe the historical state.

---

# Summary

Project Alpha has completed its foundational infrastructure and documentation phase.

The project is now transitioning into:

```text
FOUNDATION
     │
     ▼
INTEGRATION
     │
     ▼
HARDENING
     │
     ▼
PKI / TLS
     │
     ▼
MONITORING
     │
     ▼
BACKUP / RECOVERY
     │
     ▼
AUTOMATION
     │
     ▼
INFRASTRUCTURE AS CODE
```

The immediate objective is not to add more services.

The immediate objective is to **verify, integrate, secure, monitor, and make reproducible the infrastructure that already exists.**
