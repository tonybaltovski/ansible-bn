# ansible-bn

Ansible repository for managing Ubuntu and Debian hosts.

## Prerequisites

Install Ansible on the control machine:

```bash
sudo apt update
sudo apt install -y pipx
pipx install --include-deps ansible
pipx ensurepath
```

## Repository Structure

```
ansible.cfg              # Ansible configuration
inventory/
  hosts.yml              # Host inventory (Ubuntu & Debian groups)
  group_vars/all.yml     # Variables for all hosts
  host_vars/             # Per-host variable overrides
roles/
  common/                # Base role applied to all hosts
requirements.yml         # External collections/roles
site.yml                 # Main playbook
```

## Usage

### Add hosts

Edit `inventory/hosts.yml` and uncomment/add hosts under the `ubuntu` or `debian` group:

```yaml
ubuntu:
  hosts:
    my-machine:
      ansible_host: 192.168.1.10
```

### Test connectivity

```bash
ansible all -m ping
```

### Run the full playbook

```bash
ansible-playbook site.yml
```

### Target a specific group

```bash
ansible-playbook site.yml --limit ubuntu
ansible-playbook site.yml --limit debian
```

### Target a single host

```bash
ansible-playbook site.yml --limit my-machine
```

### Dry run (check mode)

```bash
ansible-playbook site.yml --check --diff
```

### Install external roles/collections

```bash
ansible-galaxy install -r requirements.yml
```