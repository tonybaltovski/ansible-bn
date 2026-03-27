# Project Guidelines

## Overview

This is an Ansible repository (`ansible-bn`) for managing a fleet of Ubuntu and Debian machines. All managed hosts run Debian-based distros (Ubuntu or Debian).

## Architecture

- **Inventory**: `inventory/hosts.yml` — three host groups: `ubuntu`, `debian`, and `desktops`
- **Group variables**: `inventory/group_vars/` — `all.yml` for shared vars, per-group files as needed (e.g., `desktops.yml`)
- **Host variables**: `inventory/host_vars/<hostname>.yml` — per-host overrides (users, packages, etc.)
- **Roles**: `roles/<role_name>/` — standard Ansible role layout (tasks, defaults, handlers, templates, files)
- **Main playbook**: `site.yml` — applies roles to hosts; `common`, `docker`, and `ros` to all; `desktop` to the `desktops` group
- **Config**: `ansible.cfg` — inventory path, sudo escalation, no host key checking
- **External deps**: `requirements.yml` — Galaxy collections and roles

## Roles

- **common** — apt dist-upgrade, common packages, unattended-upgrades
- **docker** — rootless Docker (per-user via `docker_users` variable)
- **ros** — ROS 2 install; auto-selects distro by OS codename, skips unsupported releases
- **desktop** — Google Chrome and VS Code (split into `tasks/chrome.yml` and `tasks/vscode.yml`)

## Conventions

- Target OS is always Debian-based; use `ansible.builtin.apt` for package management.
- Use fully qualified collection names (e.g., `ansible.builtin.apt`, not `apt`).
- Role defaults go in `roles/<role>/defaults/main.yml`; only override in group/host vars when needed.
- Playbook privilege escalation is set globally via `ansible.cfg` (`become: true`, `become_method: sudo`).
- Python interpreter on all hosts is `/usr/bin/python3`.
- Split large task files using `ansible.builtin.include_tasks` (e.g., one file per application).

## Build and Test

```bash
# Install Ansible
pipx install --include-deps ansible

# Install external roles/collections
ansible-galaxy install -r requirements.yml

# Test connectivity
ansible all -m ping

# Run full playbook
ansible-playbook site.yml

# Dry run
ansible-playbook site.yml --check --diff

# Target a group or host
ansible-playbook site.yml --limit ubuntu
ansible-playbook site.yml --limit desktops
ansible-playbook site.yml --limit <hostname>
```

## When Adding New Functionality

- Create a new role under `roles/` with at least `tasks/main.yml` and `defaults/main.yml`.
- Add the role to `site.yml` (or a group/host-specific play if it shouldn't apply to all hosts).
- Keep tasks idempotent.
- Use handlers for service restarts triggered by config changes.
- For roles with multiple applications, split into separate task files and include them from `main.yml`.
