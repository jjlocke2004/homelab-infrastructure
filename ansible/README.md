# Ansible

Baseline provisioning and SSH hardening for the lab's two Debian 13 server nodes (media node and monitoring node). I'm using this to replace manual setup steps with repeatable, reviewable configuration.

## Layout

```
ansible/
├── ansible.cfg
├── inventory.example.yml     # copy to inventory.yml and fill in your hosts
├── site.yml                  # applies both roles to the servers group
└── roles/
    ├── baseline/
    │   └── tasks/main.yml
    └── ssh_hardening/
        ├── tasks/main.yml
        └── handlers/main.yml
```

## Roles

### `baseline`
- Updates the apt cache (with a one-hour cache validity)
- Installs a base toolset: curl, git, htop, btop, vim, tmux
- Installs `unattended-upgrades` and `apt-listchanges` and enables automatic security updates
- Sets the timezone to America/New_York

### `ssh_hardening`
Installs a drop-in config at `/etc/ssh/sshd_config.d/00-hardening.conf` that:
- Disables root login and password authentication (key-only)
- Disables keyboard-interactive authentication and X11 forwarding
- Limits authentication attempts to 3 and the login grace time to 30 seconds

Safeguards built into the role, because this is the kind of change that can lock you out of a headless server:
- **Lockout guard:** the play fails before changing anything if the Ansible user has no `authorized_keys`.
- **Config validation:** the new file is checked with `sshd -t` before it is installed.
- **Immediate apply and re-check:** handlers are flushed right away, then the play waits to confirm it can still connect.
- The `00-` prefix makes this file load first, so it takes precedence over other drop-ins.

## Usage

Requirements: `ansible-core` on the control machine and the `community.general` collection (used for the timezone module).

```bash
ansible-galaxy collection install community.general
cp inventory.example.yml inventory.yml     # then edit with your hosts
ansible all -m ping                        # confirm connectivity
ansible-playbook site.yml --check --diff   # dry run: shows what would change
ansible-playbook site.yml                  # apply
```

### Example run (first run)

```
PLAY RECAP ********************************************************************************************************
media-node                 : ok=9    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
monitoring-node            : ok=9    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

### Example run (second run, no drift)

```
PLAY RECAP ***********************************************************
media-node      : ok=9  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
monitoring-node : ok=9  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

`changed=0` on a repeat run confirms the roles are idempotent.

## Notes

- `host_key_checking` is enabled in `ansible.cfg`, so connect to each host once with SSH before the first run.
- The real `inventory.yml` is gitignored; only the placeholder example is committed.

## Roadmap

- Docker/Podman role that installs and hardens docker/podman
- Firewall rules role
- Deploying the actual compose stacks role
