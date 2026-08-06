# Server Migration: MX Linux to Debian 13 (Trixie) 
This document covers the migration of the homelab host from MX Linux 25 to Debian 13 ("Trixie"), including the backup process, fresh OS installation, re-hardening, service restoration, and verification.

## Overview
On August 4, 2026, the homelab server was migrated from MX Linux 25 with XFCE to a minimal Debian Trixie 13 command-line installation.

## Reason for Migration
- Remove unnecessary desktop environment overhead
- Free additional system resources for Docker workloads
- Transition to a true headless server model
- Align closer with traditional Linux server deployments

## Migration Summary

| Item | Before | After |
|---|---|---|
| OS | MX Linux 25 (Debian-based) | Debian 13 "Trixie" |
| Install Source | — | Official Debian website ISO |
| Flashing Tool | — | Balena Etcher |
| Backup Method | — | `rsync` to external SSD |
| Backup Location | — | `/mnt/ssd` |

---

## Backup Process

Before wiping the host, all Docker configuration and persistent application data needed to be backed up somewhere outside the machine being reinstalled.

There was no dedicated USB drive set aside for this. Instead, this was easy since I already had an external enclosure with an SSD connected and set to auto-mount, so that existing SSD was used as the backup target at `/mnt/ssd`.
Most of the docker configs were sitting on the external SSD to test speed and responsiveness of containers as oppossed to them sitting in the internal HDD which made migration much quicker.
I still hold a seperate encrypted USB drive with the docker configs, ssh keys, passwords etc. however the SSD I knew would speed up the migration.

### What Was Backed Up

The goal was to capture everything needed to bring the Docker environment back up with minimal reconfiguration:

- Docker Compose files for every stack
- Per-service persistent config/data directories (bind mounts)
- Any supporting configuration files needed for the containers to run correctly (env files, `.yml` overrides, dashboard configs, etc.)

### Backup Command

`rsync` was used to copy the Docker directory tree to the mounted SSD:

```bash
rsync -avh --progress /path/to/docker/ /mnt/ssd/docker-backup/
```

- `-a` — archive mode, preserves permissions, timestamps, and symlinks
- `-v` — verbose output
- `-h` — human-readable sizes
- `--progress` — shows transfer progress per file

### Why rsync

`rsync` was chosen over a simple `cp` because it preserves file permissions and ownership (important for Docker bind mounts), and it can be safely re-run if the backup needs to be repeated or updated before the wipe.

---

## OS Installation

With the backup confirmed on the external SSD, the host was reinstalled from scratch.

1. Downloaded the Debian 13 ("Trixie") installation image from the official Debian website.
2. Flashed the image to installation media using Balena Etcher.
3. Booted the server from the flashed media and ran a fresh installation of Debian 13.
4. Completed base system setup (user account, disk partitioning, base packages).

---

## Post-Install Hardening

After the base OS install, the system was brought back to its prior security baseline before any services were restored.


### System Hardening Steps

- SSH hardening 
  - Disabled direct root login over SSH
  - Configured key-based authentication
  - Disabled password authentication (after verifying SSH key access)
  - Configured Fail2Ban again
- Configured Automatic security update (unattended-upgrades)
- UFW Firewall default deny incoming | allow incoming through tailscale only

### Credential and Key Rotation

Since this was effectively a new machine identity, keys and credentials were rotated rather than reused:

- Generated new SSH keypair(s) for server access and updated authorized keys
- Rotated Tailscale node key / re-authenticated the device
- Rotated any API keys, tokens, or service credentials stored in `.env` files

Rotating keys during a migration (rather than copying old ones over) reduces the risk of carrying forward a compromised or stale credential from the previous install.

---

## Restoring Docker Configuration

Docker and Docker Compose were reinstalled on the fresh Debian 13 system, then the backed-up configuration was restored from the SSD:

```bash
rsync -avh --progress /mnt/ssd/docker-backup/ /path/to/docker/
```

Because the original directory structure, permissions, and bind-mount paths were preserved by `rsync`, each stack could be brought back up with:

```bash
docker compose up -d
```

run from within each stack's directory.

---

## Service Verification

After restoring configs and bringing each stack back online, every service was checked to confirm it matched its pre-migration state:

- Confirmed all containers were running: `docker ps`
- Checked logs for startup errors: `docker compose logs`
- Verified persistent data (media libraries, dashboards, vault entries, workflows) was intact
- Confirmed Tailscale connectivity and access to each service
- Spot-checked each service in the browser to confirm functionality end-to-end

---

## Skills Practiced

- Full host migration planning and execution
- Data backup and restoration using `rsync`
- Working with external/auto-mounted storage as a backup target
- Clean OS installation and base system hardening
- Firewall rule reapplication
- Credential and SSH key rotation
- Docker Compose stack restoration
- Post-migration service verification

---

## Future Improvements

- Move from manual `rsync` backups to a scheduled/automated backup solution
- Document a formal backup rotation and retention policy
- Consider imaging or a repeatable provisioning script to speed up future migrations
- Add integrity verification (checksums) to the backup process
