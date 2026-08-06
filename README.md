# Homelab Infrastructure

> A continuously evolving self-hosted infrastructure designed to develop practical experience in Linux administration, Docker, networking, automation, monitoring, and cybersecurity using consumer-grade hardware.

---

# Host Platform
![Hardware Overview](images/hardware-overview.png)

The environment is hosted on an **HP Notebook 15-ba009dx (2016)** running **Debian Linux Trixie 13**. 

Rather than relying on enterprise hardware, this project demonstrates that meaningful infrastructure and security experience can be developed using readily available consumer hardware while working within real-world resource constraints.

## Hardware Specifications

| Component | Specification |
|-----------|---------------|
| Host | HP Notebook 15-ba009dx |
| Operating System | Debian Linux Trixie 13 |
| Processor | AMD A6-7310 APU with Radeon R4 Graphics |
| Memory | 6 GB DDR3 |
| Storage | 500 GB Seagate HDD + External SSD Samsung M.2 SATA 128GB |
| Battery | N/A - Always plugged in |
| Container Platform | Docker & Docker Compose |
| Backups | Encrypted 16GB Sandisk USB |

---

## Server Migration (Implemented Aug. 4TH)
[Server Migration Documentation](/docs/server-migration.md)

Switched the operating system from MX Linux with XFCE desktop environment to minimal command-line only Debian Linux Trixie 13.

![ssh-debian-server](images/ssh-debian-server.png)

The server originally ran MX Linux for its user-friendly experience and familiarity while building out the infrastructure and learning Linux administration. 

As the environment matured, the desktop interface became unnecessary since all management was performed remotely through SSH. The migration reduced system overhead, improved resource efficiency, and transformed the machine into a true headless server environment.

---

## Security Model

- Services are accessed privately through Tailscale
- SSH uses key-based authentication
- UFW denies unsolicited inbound access
- CrowdSec and Fail2Ban provide automated blocking

---

## Backup and Recovery

- Docker configuration directories are backed up with rsync
- Backups are stored on encrypted removable media
- Large replaceable media files are excluded
- Compose files and configuration data are prioritized

---

# Homepage Dashboard
![Homepage Dashboard](images/homepage-dashboard.png)

Homepage provides a centralized dashboard for managing and monitoring the homelab. It serves as the primary interface for accessing services, viewing system health, and organizing the infrastructure into logical groups.

---

# Architecture Overview
![Architecture Diagram](diagrams/architecture-diagram.png)

The infrastructure is organized into modular Docker Compose stacks, allowing services to remain independent, easier to maintain, and simple to expand over time.

Current deployments include media, monitoring, documentation, management, automation, and security infrastructure organized into independent Docker Compose stacks.

---

# Current Infrastructure

## Media Infrastructure
[Media Platform Documentation](docs/media-platform.md)

### Core Technologies

- Jellyfin
- Homepage
- Homarr
- Portainer
- Vaultwarden
- n8n

### Focus Areas

- Docker Compose
- Container networking
- Persistent storage
- Service management
- Dashboard customization
- Workflow automation
---

## Media Stack Addition - Navidrome Music Server

[Navidrome Music Server Documentation](docs/navidrome-music-server.md)

### Core Technologies

- Navidrome
- Docker Compose
- Asunder
- FLAC
- MusicBrainz Picard
- Tailscale
- Linux filesystem permissions
- SSD-backed storage

### Focus Areas

- Self-hosted music streaming
- Physical CD digitization
- Lossless audio preservation
- Music metadata management
- Album artwork organization
- Persistent Docker storage
- Linux permissions troubleshooting
- Private remote access
- Resource-aware service deployment

---

## Monitoring Infrastructure
[Monitoring Stack Documentation](docs/monitoring-stack.md)

### Core Technologies

- Grafana
- Prometheus
- Node Exporter
- Uptime Kuma

### Focus Areas

- Infrastructure monitoring
- Metrics collection
- Service availability monitoring
- System visualization
- Host performance analysis
- Docker host monitoring
---

## Documentation Infrastructure

[Documentation Stack Documentation](docs/documentation-stack.md)

### Core Technologies

- Nextcloud
- PostgreSQL
- Redis

### Focus Areas

- Self-hosted file management
- Basic Markdown and text editing
- Document organization and synchronization
- Persistent application storage
- Database-backed application deployment
- Private remote access
- Resource-aware service design
- Docker stack separation
  
---

## Security Infrastructure

[Security Hardening Documentation](docs/security-hardening.md)

### Core Technologies

- Tailscale
- Tailnet Lock
- UFW
- CrowdSec
- CrowdSec Firewall Bouncer
- Fail2Ban
- SSH Key Authentication
- LUKS and rsync

### Focus Areas

- Private remote access
- Linux host hardening
- Default-deny firewall configuration
- SSH access control
- Automated threat detection and blocking
- VPN-isolated application traffic
- Encrypted configuration backups
- Credential and secret protection
- Docker service isolation
- Security validation and recovery planning

# Current Learning Objectives

This project focuses on developing practical experience with:

- Linux system administration
- Docker and containerized infrastructure
- Infrastructure monitoring
- Network administration
- Secure remote access
- Infrastructure documentation
- Automation workflows
- Security hardening
- Self-hosted services
- Troubleshooting

---

# Project Roadmap

| Stack | Key services | Status |
| ------| -------------| -------|
| Media | Jellyfin, Navidrome | Operational |
| Monitoring | Grafana, Prometheus, Node Exporter, Uptime Kuma | Operational |
| Management | Homepage, Portainer, n8n | Operational |
| Documentation | Nextcloud, PostgreSQL, Redis | Operational |
| Security and access | Tailscale, UFW, CrowdSec, Fail2Ban, SSH keys | Operational |
| Document processing | Paperless-ngx | Planned |
| Office editing | OnlyOffice | Deferred—resource constraints |

---

# Repository Structure

```text
.
├── README.md
├── docs
│   ├── media-platform.md
│   ├── monitoring-stack.md
│   ├── documentation-stack.md
│   ├── security-hardening.md
├── diagrams
├── images
```

---

# Documentation

Project documentation is organized into focused guides that describe the deployment, configuration, troubleshooting, and lessons learned throughout the development of the homelab.

Future planned documentation includes:

- Wazuh Deployment
- CVE Remediation
- Backup Strategy

---

# Design Philosophy

This project emphasizes learning through practical implementation rather than enterprise hardware.

By building and maintaining services on an aging consumer laptop, the focus remains on architecture, troubleshooting, documentation, automation, and continuous improvement instead of expensive infrastructure.

Every addition to the environment is documented, tested, and evaluated before becoming part of the long-term platform.

---

## Planned Improvements

- Paperless-ngx document processing
- Expanded backup and restoration testing
- Additional infrastructure automation
- Improved Docker network segmentation
- Centralized log collection
- Additional service health monitoring
- Hardware and memory upgrades

---
