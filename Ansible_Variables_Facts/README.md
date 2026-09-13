# Ansible Variables & Facts — System Information Collector

A practical Ansible project for collecting system information using Ansible Facts, custom variables, registered command output, conditionals, loops, and variable manipulation.

The project demonstrates how Ansible can inspect a Linux system, process the collected information, make decisions based on system state, and generate a consolidated system report.

## Project Goals

* Understand Ansible Facts
* Work with Ansible variables
* Use registered command output
* Manipulate variables with `set_fact`
* Use conditionals with `when`
* Iterate over system information with loops
* Collect hardware, operating system, network, and filesystem information
* Build a structured system information report

## Project Structure

```text
Ansible_Variables_Facts/
├── inventory.ini
├── system_info.yml
└── README.md
```

## Requirements

* Linux
* Ansible Core 2.21+
* Python 3

## Inventory

The project currently uses the local Kali Linux machine as the managed host.

```ini
[servers]
localhost ansible_connection=local
```

Test the connection:

```bash
ansible -i inventory.ini servers -m ping
```

Expected result:

```text
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

## Running the Project

Run the playbook with:

```bash
ansible-playbook -i inventory.ini system_info.yml
```

The playbook gathers system facts and generates a system information report.

## Information Collected

The playbook collects:

* Hostname
* Operating system
* OS version
* Kernel version
* CPU information
* RAM
* IP address
* System uptime
* Filesystem information
* Root filesystem usage

Example:

```text
Hostname: kali
OS: Kali 2026.2
Kernel: 6.19.14+kali-amd64
CPU: 8 vCPUs
RAM: 7772 MB
IP Address: 192.168.100.159
Root Disk Usage: 26.9%
```

## Ansible Concepts Demonstrated

### Ansible Facts

Facts are automatically gathered from managed hosts.

Examples:

```yaml
{{ ansible_facts['hostname'] }}
{{ ansible_facts['distribution'] }}
{{ ansible_facts['kernel'] }}
{{ ansible_facts['memtotal_mb'] }}
```

The project uses the modern `ansible_facts[...]` syntax.

### Custom Variables

Custom variables are defined with `vars`:

```yaml
vars:
  report_name: "System Information Report"
  memory_warning_threshold: 4000
```

These variables can then be reused throughout the playbook.

### Registered Variables

Command output can be stored using `register`:

```yaml
- name: Get uptime
  ansible.builtin.command: uptime
  register: uptime_result
  changed_when: false
```

The command output can then be accessed with:

```yaml
{{ uptime_result.stdout }}
```

### Conditionals

The playbook uses `when` to make decisions based on system facts.

Example:

```yaml
when: ansible_facts['memtotal_mb'] < memory_warning_threshold
```

This allows Ansible to display a warning only when the system has less memory than the configured threshold.

Disk usage is handled similarly:

```yaml
when: root_disk_usage | float > 80
```

### Loops

Filesystem facts are processed using a loop:

```yaml
loop: "{{ ansible_facts['mounts'] }}"
```

This allows the playbook to inspect multiple mounted filesystems.

### Variable Manipulation

`set_fact` is used to create calculated and structured variables.

For example, root filesystem usage is calculated and stored as:

```yaml
root_disk_usage
```

A structured `system_report` variable is also created containing the collected information.

## What I Learned

Through this project I learned how to:

* Gather and inspect Ansible Facts
* Access nested fact data
* Define custom Ansible variables
* Register command output
* Access `stdout` from registered variables
* Use `when` conditionals
* Iterate over fact data with loops
* Calculate values with Jinja2 expressions
* Create variables dynamically with `set_fact`
* Combine multiple pieces of system information into a structured report
* Use Ansible for practical Linux system inspection

## Future Improvements

Possible extensions include:

* Collecting CPU model and architecture
* Reporting network interfaces
* Monitoring individual filesystem usage
* Adding configurable warning thresholds
* Exporting the report to JSON
* Generating an HTML system report
* Running the collector against multiple remote Linux hosts
* Adding Windows host support
* Scheduling the collector with Ansible Automation Platform or AWX

## Status

**Completed — Project #26**

This project is part of a hands-on Ansible and DevOps learning roadmap focused on practical automation rather than theory-heavy learning.
