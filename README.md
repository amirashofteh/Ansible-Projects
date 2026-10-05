# Ansible Automation Projects

A hands-on collection of **Ansible infrastructure automation projects** focused on Linux administration, configuration management, service deployment, package management, templating, and repeatable infrastructure operations.

This repository documents my progression from Ansible fundamentals toward reusable and production-style infrastructure automation.

---

## 🚀 Projects

| # | Project | Focus | Status |
|---|---|---|---|
| 01 | [Ansible First Playbook](./Ansible_First_Playbook) | Playbook fundamentals and task execution | ✅ Completed |
| 02 | [Ansible Inventory](./Ansible_Inventory) | Inventory configuration and managed hosts | ✅ Completed |
| 03 | [Ansible Variables & Facts](./Ansible_Variables_Facts) | Variables, gathered facts, and system information | ✅ Completed |
| 04 | [Basic Website Deployment](./Basic_Website_Deployment) | Nginx website deployment and Jinja2 templating | ✅ Completed |
| 05 | [Ansible Package Management](./Ansible_Package_Management) | Package installation, removal, updates, and verification | ✅ Completed |
| 06 | [Ansible Service Management](./Ansible_Service_Management) | Nginx service lifecycle management and HTTP verification | ✅ Completed |

Each project contains its own configuration, playbooks, inventory, and documentation where applicable.

---

## 🗂️ Repository Structure

```text
Ansible-Projects/
├── Ansible_First_Playbook/
├── Ansible_Inventory/
├── Ansible_Package_Management/
├── Ansible_Service_Management/
├── Ansible_Variables_Facts/
├── Basic_Website_Deployment/
├── .gitignore
└── README.md
```

---

## 🛠️ Technologies

- Ansible
- YAML
- Linux
- SSH
- Nginx
- Jinja2
- Bash
- Git
- Python

---

## 🎯 Objectives

The goal of this repository is to develop practical infrastructure automation skills by building and testing real Ansible workflows.

The projects focus on areas such as:

- Managing Linux hosts
- Writing reusable playbooks
- Working with inventories and variables
- Gathering system facts
- Automating package installation and removal
- Managing Linux services
- Deploying applications and configuration files
- Using Jinja2 templates
- Verifying infrastructure state after automation
- Building toward multi-host and production-style automation

---

## ⚙️ Environment

Current Ansible development and testing is primarily performed on Linux-based environments using local and SSH-managed hosts.

Check your installed version:

```bash
ansible --version
```

Test connectivity:

```bash
ansible -i inventory.ini all -m ping
```

Run a playbook:

```bash
ansible-playbook -i inventory.ini playbook.yml
```

Inventory paths and playbook names vary by project.

---

## 🔍 Engineering Approach

For each project, I aim to follow a repeatable workflow:

```text
Understand the task
        ↓
Build the inventory
        ↓
Write the playbook
        ↓
Run syntax validation
        ↓
Use check/diff mode when appropriate
        ↓
Execute automation
        ↓
Verify the resulting system state
        ↓
Troubleshoot failures
        ↓
Document the implementation
```

Useful validation commands include:

```bash
ansible-playbook --syntax-check playbook.yml
```

```bash
ansible-playbook --check --diff playbook.yml
```

---

## 📈 Roadmap

The repository will progressively expand into more advanced Ansible concepts and infrastructure scenarios.

### Next Projects

- File & Directory Management
- User & Group Management
- SSH Key Deployment
- Nginx Deployment
- Ansible Templates
- Handlers
- Roles
- Multi-Host Configuration
- Docker Deployment with Ansible
- Security Hardening
- Automated Backups
- Monitoring Deployment
- CI/CD Integration
- Production-Style Infrastructure Automation

### Progression

```text
Ansible Fundamentals
        ↓
Inventory & Playbooks
        ↓
Variables & Facts
        ↓
Package & Service Management
        ↓
Templates & Handlers
        ↓
Roles
        ↓
Multi-Host Automation
        ↓
Application Deployment
        ↓
Security & Operations
        ↓
CI/CD Integration
        ↓
Production Infrastructure Automation
```

---

## 📌 Repository Status

🟢 **Actively maintained**

This repository is part of my hands-on DevOps and infrastructure automation portfolio. New projects are added as I progress into more advanced Ansible concepts and real-world automation scenarios.
