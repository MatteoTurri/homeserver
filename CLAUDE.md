# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Ansible playbook for provisioning a personal homeserver running on Ubuntu. The main entry point is `playbook.yml`, which runs against a single host defined in `hosts.ini`. All tasks run with `become: true` (root).

## Commands

Run the full playbook against the server:
```
ansible-playbook -i hosts.ini playbook.yml --ask-vault-pass
```

Run a specific set of tasks using tags or `--start-at-task`:
```
ansible-playbook -i hosts.ini playbook.yml --ask-vault-pass --start-at-task "Deploy Immich"
```

Local testing with Vagrant (creates a VirtualBox VM with ZFS):
```
vagrant up
```

Ansible dependencies (roles/collections) are cached in `.ansible/`. Install external roles with:
```
ansible-galaxy install <role-name> -p .ansible/roles
```

## Architecture

**Playbook structure:** `playbook.yml` imports task files from `tasks/` sequentially. There is no role-based structure for local tasks — each service is a standalone task file.

**Variable layering:**
- `server_user` is defined inline in `playbook.yml`
- Public vars live in `vars/wireguard.yml` and `vars/immich.yml`
- `vars/wireguard_private.yml` contains vault-encrypted secrets (git-ignored)
- `vars/immich.yml` uses `lookup('password', ...)` to auto-generate `vars/immich.password` (also git-ignored)

**External roles** (installed via ansible-galaxy, not vendored):
- `githubixx.ansible_role_wireguard` — WireGuard VPN
- `geerlingguy.nfs` — NFS server

**Templates** in `templates/` are Jinja2 files deployed to the server. Immich uses two templates (`immich.env.j2` and `immich-compose.yml.j2`) that get deployed to `/opt/immich/` and are managed via `community.docker.docker_compose_v2`.

**Storage assumption:** A ZFS pool named `data` must already exist (the playbook ensures properties but does not create the pool). All service data lives under `/mnt/data/`.

## Conventions

- Task files use fully qualified collection names (e.g., `ansible.builtin.apt`, `community.general.zfs`)
- Templates use `.j2` extension for Docker/env files, `.jinja` for systemd units
- The `hosts.ini` file is git-ignored; the committed inventory reference is just the hostname `homeserver`
