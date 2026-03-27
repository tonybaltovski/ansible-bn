# ansible-bn

Ansible repository for managing a fleet of Ubuntu and Debian machines.

## Prerequisites

Install Ansible and pre-commit on the control machine:

```bash
sudo apt update
sudo apt install -y pipx
pipx install --include-deps ansible
pipx install pre-commit
pipx ensurepath
```

## Setup

```bash
# Install external roles/collections
ansible-galaxy install -r requirements.yml

# Install pre-commit hooks
pre-commit install
```

## Repository Structure

```
ansible.cfg              # Ansible configuration
site.yml                 # Main playbook
requirements.yml         # External Galaxy collections/roles
.pre-commit-config.yaml  # Pre-commit hook configuration
.yamllint                # yamllint configuration
.ansible-lint            # ansible-lint configuration
inventory/
  hosts.yml              # Host inventory (ubuntu, debian, workstations)
  group_vars/            # Group variable files
  host_vars/             # Per-host variable overrides
roles/
  common/                # apt dist-upgrade, common packages, unattended-upgrades
  docker/                # Rootless Docker per user
  ros/                   # ROS 2 (auto-selects distro by OS codename)
  desktop/               # Google Chrome and VS Code
  docker_compose/        # Docker Compose service deployment per host
  nfs_client/            # NFS client mounts per host
```

## Usage

### Add hosts

Edit `inventory/hosts.yml` and add hosts under the appropriate group (`ubuntu`, `debian`, or `workstations`):

```yaml
ubuntu:
  hosts:
    my-machine:
      ansible_host: 192.168.1.10
```

### Configure per-host options

Create `inventory/host_vars/<hostname>.yml`:

```yaml
docker_users:
  - tbaltovski

docker_compose_user: tbaltovski
docker_compose_services:
  - portainer

nfs_mounts:
  - src: "nas:/srv/data"
    path: /mnt/data
```

### Test connectivity

```bash
ansible all -m ping
```

### Run the full playbook

```bash
ansible-playbook site.yml
```

### Target a specific group or host

```bash
ansible-playbook site.yml --limit ubuntu
ansible-playbook site.yml --limit workstations
ansible-playbook site.yml --limit my-machine
```

### Dry run (check mode)

```bash
ansible-playbook site.yml --check --diff
```

## CI

GitHub Actions runs on push/PR to `main`:
- **yamllint** — YAML syntax and style
- **ansible-lint** — Ansible best practices
- **syntax-check** — playbook parse validation

Pre-commit runs the same checks locally before each commit.