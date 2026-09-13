# 24 — Ansible First Playbook

A beginner Ansible project demonstrating how to use one Linux machine as an **Ansible controller** to remotely configure another Linux machine over SSH.

The playbook creates a directory and file, installs a package, writes content to the file, and manages file permissions.

---

## 🎯 Objectives

* Understand the basic Ansible architecture
* Configure an Ansible inventory
* Connect to a remote Linux host over SSH
* Use Ansible modules
* Use privilege escalation with `become`
* Create directories and files remotely
* Install packages remotely
* Understand Ansible idempotency
* Execute a complete Ansible playbook

---

## 🏗️ Architecture

```text
┌──────────────────────────┐
│     Ansible Controller   │
│                          │
│       Kali Linux         │
│                          │
│  ansible-playbook        │
└────────────┬─────────────┘
             │
             │ SSH
             │
             ▼
┌──────────────────────────┐
│      Target Server       │
│                          │
│       Kali Linux         │
│                          │
│   192.168.100.159        │
└──────────────────────────┘
```

---

## 📁 Project Structure

```text
Ansible-First-Playbook/
├── inventory
├── first-playbook.yml
└── README.md
```

---

## 📋 Inventory

The inventory defines which machines Ansible manages.

```ini
[servers]
server1 ansible_host=192.168.100.159 ansible_user=helpdesk
```

The `[servers]` group is referenced by the playbook.

### Inventory = WHERE

The inventory answers:

> Which machines should Ansible manage?

---

## 📜 Playbook

```yaml
---
- name: My First Ansible Playbook
  hosts: servers
  become: true

  tasks:

    - name: Create Ansible demo directory
      ansible.builtin.file:
        path: /opt/ansible-demo
        state: directory
        mode: '0755'

    - name: Create hello file
      ansible.builtin.file:
        path: /opt/ansible-demo/hello.txt
        state: touch
        mode: '0644'

    - name: Install htop
      ansible.builtin.package:
        name: htop
        state: present

    - name: Write message to hello file
      ansible.builtin.copy:
        content: "Hello from Ansible!\n"
        dest: /opt/ansible-demo/hello.txt
        mode: '0644'
```

---

## 🧩 Modules Used

### `ansible.builtin.file`

Used to manage files and directories.

The first task creates:

```text
/opt/ansible-demo
```

The second task creates:

```text
/opt/ansible-demo/hello.txt
```

---

### `ansible.builtin.package`

Used to manage packages on the target system.

```yaml
ansible.builtin.package:
  name: htop
  state: present
```

This tells Ansible:

> Make sure `htop` is installed.

---

### `ansible.builtin.copy`

Writes content to a file on the target:

```yaml
ansible.builtin.copy:
  content: "Hello from Ansible!\n"
  dest: /opt/ansible-demo/hello.txt
```

---

## 🔐 Privilege Escalation

The playbook uses:

```yaml
become: true
```

This tells Ansible to use privilege escalation, normally through `sudo`.

Because SSH password authentication and sudo password authentication are being used:

```bash
ansible-playbook -i inventory first-playbook.yml \
  --ask-pass \
  --ask-become-pass
```

---

## 🧪 Testing Connectivity

Before running the playbook, Ansible connectivity was tested with:

```bash
ansible -i inventory servers -m ping --ask-pass
```

Successful result:

```text
server1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

This confirmed that the controller could successfully connect to the target through SSH and execute Ansible modules.

---

## ▶️ Running
