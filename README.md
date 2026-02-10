# Homeserver

Ansible playbook for provisioning a personal homeserver running on Ubuntu 24.04.

## Prerequisites

- Ansible installed locally
- SSH access to the server (host `homeserver` in your SSH config)
- A ZFS pool named `data` must already exist on the server
- External roles installed:

```sh
ansible-galaxy install githubixx.ansible_role_wireguard -p .ansible/roles
ansible-galaxy install geerlingguy.nfs -p .ansible/roles
```

## Usage

Run the full playbook:

```sh
ansible-playbook -i hosts.ini playbook.yml --ask-vault-pass --ask-become-pass
```

Run from a specific task:

```sh
ansible-playbook -i hosts.ini playbook.yml --ask-vault-pass --ask-become-pass --start-at-task "Deploy Immich"
```

Test locally with Vagrant:

```sh
vagrant up
```

## Services

| Service | Description |
|---|---|
| ZFS | Pool `data` mounted on `/mnt/data` with lz4 compression |
| WireGuard | VPN with NAT (subnet `10.7.0.0/24` + IPv6) |
| Docker | Docker CE with compose plugin |
| Immich | Photo management on port 2283 |
| rtcwake | Auto shutdown at 21:30, wake after 10h30m |
| NFS | Shares for Backups, Documents, Downloads, Music, Pictures, Videos |
| Powertop | Power optimization |
| MiniDLNA | DLNA media server on port 8200 |
