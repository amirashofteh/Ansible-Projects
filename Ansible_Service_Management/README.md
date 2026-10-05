# Project 29 — Ansible Service Management

A hands-on Ansible project that manages Nginx on Kali Linux and verifies its service state, boot setting, and HTTP response.

Kali acts as both the control node and managed node through a local connection.

## Features

- Ensure Nginx is installed.
- Start, stop, or restart the service.
- Enable or disable startup at boot.
- Gather service facts.
- Verify service settings against the requested variables.
- Check that the existing lab website returns HTTP 200.
- Demonstrate idempotency.

## Project Structure

| Path | Purpose |
|---|---|
| `inventory/hosts` | Defines the managed machine |
| `group_vars/all.yml` | Defines desired service settings |
| `playbook.yml` | Executes management and verification tasks |
| `README.md` | Documents usage and results |

## Requirements

- Kali Linux with systemd
- Ansible installed
- Sudo access
- The existing Nginx lab website configured at `http://127.0.0.1:8081`

Tested with Ansible Core 2.21.2.

This project uses the website configuration from the earlier Basic Website Deployment project. It does not create the website or its Nginx configuration. On a fresh machine, deploy that configuration before running this project's HTTP verification.

## Inventory

Contents of `inventory/hosts`:

```ini
[lab]
localhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3
```

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

The package-installation and HTTP-check tasks are specific to Nginx. Changing `service_name` alone does not adapt the whole project to another service.

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

    - name: Verify service matches the requested settings
      ansible.builtin.assert:
        that:
          - >-
            ansible_facts.services[service_name ~ '.service'].state ==
            ('stopped' if service_state == 'stopped' else 'running')
          - >-
            ansible_facts.services[service_name ~ '.service'].status ==
            ('enabled' if service_enabled | bool else 'disabled')
        success_msg: "Service matches the requested settings"

    - name: Verify the lab website responds
      ansible.builtin.uri:
        url: http://127.0.0.1:8081
        status_code: 200
        use_proxy: false
      when: service_state != 'stopped'
```

The exercises use `started`, `stopped`, and `restarted`. The assertion expects a stopped service for `stopped` and a running service for the other two states.

## Usage

Run commands from the project directory.

### Test connectivity

```bash
ansible -i inventory/hosts lab -m ansible.builtin.ping
```

### Check syntax

```bash
ansible-playbook -i inventory/hosts playbook.yml --syntax-check
```

### Ensure Nginx is running and enabled

```bash
ansible-playbook -i inventory/hosts playbook.yml --ask-become-pass
```

Enter your Kali user's sudo password when prompted.

With the existing website and service already configured, the final verified run returned:

```text
ok=6 changed=0 unreachable=0 failed=0 skipped=0
```

## Stop and Start

Stop Nginx while keeping startup at boot enabled:

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass \
  -e '{"service_state": "stopped"}'
```

This makes the lab website unavailable. HTTP verification skips when the requested state is `stopped`.

Check directly:

```bash
systemctl is-active nginx
systemctl is-enabled nginx
```

Expected:

```text
inactive
enabled
```

Start Nginx again using the defaults:

```bash
ansible-playbook -i inventory/hosts playbook.yml --ask-become-pass
```

## Disable and Enable Startup at Boot

Disable startup at boot while keeping Nginx running:

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass \
  -e '{"service_enabled": false}'
```

The requested state is now running and disabled.

Restore startup at boot:

```bash
ansible-playbook -i inventory/hosts playbook.yml --ask-become-pass
```

Runtime state and startup at boot are independent. Disabling a service does not stop it; stopping it does not disable it.

## Restart

Request an explicit restart:

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass \
  -e '{"service_state": "restarted"}'
```

| State | Behavior |
|---|---|
| `started` | Starts the service only if needed |
| `stopped` | Stops the service only if needed |
| `restarted` | Requests a restart each time the task executes |

An explicit restart can briefly interrupt the website. One restart was verified in this lab; repeated-restart behavior was explained but not recorded as a completed test.

## Variable Overrides

The `-e` option supplies variables for one execution.

For example:

```bash
-e '{"service_state": "stopped"}'
```

does not edit `group_vars/all.yml`. The requested change to the actual service remains until another action changes it.

Running again without an override uses the defaults from `all.yml`.

## Verification

The playbook checks two levels:

1. Service facts confirm the requested runtime and boot settings.
2. An HTTP request confirms the existing website returns status 200.

HTTP verification skips for an intentionally stopped service. It checks the response status, not the homepage content.

Direct checks:

```bash
systemctl is-active nginx
systemctl is-enabled nginx
curl -I http://127.0.0.1:8081
```

## Verified Lab Results

| Exercise | Observed result |
|---|---|
| Initial connection | Successful `pong` response |
| Initial service inspection | Active, enabled, HTTP 200 |
| Initial default playbook run | `changed=0`, `failed=0` |
| Stop Nginx | Service stopped; original running-only assertion failed |
| Correct verification and repeat stop | `changed=0`, `failed=0`; inactive and enabled |
| Start Nginx again | Direct check showed active and HTTP 200 |
| Disable startup at boot | `changed=1`, `failed=0`; assertion passed |
| Restore startup at boot | `changed=1`, `failed=0`; assertion passed |
| Explicit restart | `changed=1`, `failed=0`; assertion passed |
| Add HTTP verification and run defaults | `ok=6`, `changed=0`, `failed=0` |

## Lessons Learned

- Inventory defines where tasks run.
- Variables define the requested settings.
- The playbook defines the actions.
- Verification must follow the requested state.
- A later assertion failure does not undo earlier service changes.
- `started` and `stopped` support idempotent state management.
- `restarted` requests an action on every execution.
- Startup at boot and current runtime state are separate settings.

## Future Improvements

- Record a repeated-restart test.
- Add HTTP retries for services that take time to become ready.
- Validate allowed service-state values before making changes.
- Manage additional hosts over SSH.
- Use handlers for restarts triggered by configuration changes.

Multi-host service management has not yet been tested.
