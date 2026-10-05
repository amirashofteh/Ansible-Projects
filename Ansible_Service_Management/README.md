# Ansible Service Management

> Project 29 in my hands-on Ansible automation roadmap.

A hands-on Ansible automation project for managing the Nginx service lifecycle on Linux, validating runtime and boot-time state, and verifying application availability through HTTP checks.

Kali Linux is used as both the Ansible control node and managed node through a local connection.

---

## What This Project Demonstrates

- Declarative Linux service management with Ansible
- Separation of runtime state and boot-time enablement
- Variable-driven service control
- Service-state validation using gathered facts
- Application-level verification with HTTP health checks
- Idempotent automation and repeatable execution

---

## Capabilities

- Ensure Nginx is installed
- Start, stop, or restart the service
- Enable or disable startup at boot
- Gather service facts
- Verify the requested service state
- Verify the requested boot setting
- Check that the existing lab website returns HTTP 200
- Demonstrate idempotent behavior
- Override service behavior at runtime with extra variables

---

## Execution Flow

```text
Inventory
   ↓
Variables
   ↓
Ansible Playbook
   ↓
APT Package Check
   ↓
systemd Service Management
   ↓
Service Facts
   ↓
Assertions
   ↓
HTTP Verification
```

---

## Project Structure

| Path | Purpose |
|---|---|
| `inventory/hosts` | Defines the managed machine |
| `group_vars/all.yml` | Defines the desired service settings |
| `playbook.yml` | Executes service management and verification |
| `README.md` | Documents usage, testing, and results |

---

## Requirements

- Kali Linux with systemd
- Ansible installed
- Sudo access
- Existing Nginx lab website available at `http://127.0.0.1:8081`

Tested with:

```text
Ansible Core 2.21.2
```

This project uses the website configuration created in the earlier **Basic Website Deployment** project.

It does not create the website or its Nginx configuration. On a fresh system, that configuration must be deployed before running the HTTP verification step.

---

## Inventory

Contents of `inventory/hosts`:

```ini
[lab]
localhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3
```

The project currently uses a local connection, with Kali acting as both the control node and managed node.

---

## Variables

Contents of `group_vars/all.yml`:

```yaml
---
service_name: nginx
service_state: started
service_enabled: true
```

| Variable | Purpose |
|---|---|
| `service_name` | Service managed by the systemd task |
| `service_state` | Requested runtime state |
| `service_enabled` | Whether startup at boot is enabled |

The package installation and HTTP verification tasks are specific to Nginx.

Changing `service_name` alone does not make the entire project compatible with another service.

---

## Playbook

Contents of `playbook.yml`:

```yaml
---
- name: Manage the lab service
  hosts: lab
  become: true

  tasks:
    - name: Ensure Nginx is installed
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Manage service state and boot setting
      ansible.builtin.systemd_service:
        name: "{{ service_name }}"
        state: "{{ service_state }}"
        enabled: "{{ service_enabled }}"

    - name: Gather service information
      ansible.builtin.service_facts:

    - name: Verify service matches
