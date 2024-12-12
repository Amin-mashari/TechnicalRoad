# Ansible Observability Playbook

This repository contains an Ansible playbook for setting up and managing observability tools like Prometheus, Grafana, and Loki. It is designed to automate the installation, configuration, and management of these tools on a group of servers.

## Table of Contents
- [Overview](#overview)
- [Requirements](#requirements)
- [Usage](#usage)
- [Playbook Structure](#playbook-structure)
- [Tags](#tags)
- [Roles](#roles)
- [License](#license)

## Overview
This playbook is designed to:
- Install and configure Prometheus for monitoring
- Set up Grafana for visualization and dashboards
- Deploy Loki for log aggregation
- Ensure common configurations and prerequisites are applied to all nodes in the observability group.

## Requirements
- Ansible 2.9 or higher
- Access to target hosts with SSH keys or password authentication
- Privileged access (sudo) on target hosts
- Inventory file defining the target hosts (default group: `observ`)

## Usage

1. Define your inventory file (e.g., `inventory.yml`) to specify the `observ` group with target hosts:
     ```ini
      [observ]
      server ansible_host=1.1.1.1 ansible_user=ansible
     ```

2. Run the playbook with appropriate tags:
   - To apply all roles:
     ```bash
     ansible-playbook -i inventory.yml playbook.yml
     ```
   - To apply specific roles using tags:
     ```bash
     ansible-playbook -i inventory.yml playbook.yml --tags "prometheus,grafana"
     ```
    - Ensure that pass `ask-become-pass` for root privilage
        ```bash
        ansible-playbook -i inventory/hosts.ini playbook.yml -u ansible --ask-become-pass
        ```
3. Verify the setup:
   - Access Prometheus at `http://<host>:9090`
   - Access Grafana at `http://<host>:3000`
   - Verify Loki logs through Grafana's data source settings

## Playbook Structure
The playbook is structured into multiple sections for modularity and flexibility:

```yaml
---
- name: common
  hosts: observ
  become: true
  roles:
    - { role: common }
  tags:
    - loki
    - prometheus   
    - grafana

- name: prometheus
  hosts: observ
  become: true
  roles:
    - { role: prometheus }
  tags:
    - prometheus

- name: grafana
  hosts: observ
  become: true
  roles:
    - { role: grafana }
  tags:
    - grafana

- name: loki
  hosts: observ
  become: true
  roles:
    - { role: loki }
  tags:
    - loki
```

### Tags
- `common`: Applies shared configurations across all observability tools.
- `prometheus`: Sets up and configures Prometheus.
- `grafana`: Sets up and configures Grafana.
- `loki`: Sets up and configures Loki.

### Roles
Each role (`common`, `prometheus`, `grafana`, `loki`) contains tasks, handlers, and templates for the respective tool. Ensure the roles are defined in your `roles/` directory.


## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.


