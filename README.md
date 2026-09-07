# Ansible Projects

A hands-on collection of Ansible automation projects covering configuration management, server provisioning, application deployment, security hardening, and infrastructure automation.

## 📚 Projects

| #  | Project                                    | Level        | Status |
| -- | ------------------------------------------ | ------------ | ------ |
| 01 | Ansible Fundamentals & Inventory           | Beginner     | 🚧     |
| 02 | Ansible Ad-Hoc Commands                    | Beginner     | 🚧     |
| 03 | User & Group Management                    | Beginner     | 🚧     |
| 04 | Package Management                         | Beginner     | 🚧     |
| 05 | Service Management                         | Beginner     | 🚧     |
| 06 | File & Directory Management                | Beginner     | 🚧     |
| 07 | SSH Key Deployment                         | Beginner     | 🚧     |
| 08 | Nginx Deployment                           | Beginner     | 🚧     |
| 09 | Apache Deployment                          | Beginner     | 🚧     |
| 10 | Ansible Variables & Facts                  | Beginner     | 🚧     |
| 11 | Ansible Templates                          | Intermediate | 🚧     |
| 12 | Ansible Handlers                           | Intermediate | 🚧     |
| 13 | Ansible Roles                              | Intermediate | 🚧     |
| 14 | Multi-Server Configuration                 | Intermediate | 🚧     |
| 15 | Docker Deployment with Ansible             | Intermediate | 🚧     |
| 16 | Monitoring Stack Deployment                | Intermediate | 🚧     |
| 17 | Security Hardening                         | Intermediate | 🚧     |
| 18 | Automated Backup                           | Intermediate | 🚧     |
| 19 | CI/CD with Ansible                         | Advanced     | 🚧     |
| 20 | Production-Style Infrastructure Automation | Advanced     | 🚧     |

## 🗂️ Repository Structure

```text
Ansible-Projects/
├── README.md
├── .gitignore
├── inventories/
├── playbooks/
├── roles/
├── group_vars/
└── host_vars/
```

## 🛠️ Technologies

* Ansible
* YAML
* Linux
* SSH
* Docker
* Git
* Bash
* Python
* Nginx
* Prometheus
* Grafana

## 🎯 Goals

The purpose of this repository is to build practical Ansible skills through real-world projects rather than relying only on theoretical exercises.

Projects gradually progress from basic Ansible commands and playbooks to reusable roles, multi-server automation, security hardening, application deployment, and CI/CD integration.

## 🚀 Usage

Clone the repository:

```bash
git clone git@github.com:amirashofteh/Ansible-Projects.git
cd Ansible-Projects
```

Check the installed Ansible version:

```bash
ansible --version
```

Test connectivity to managed hosts:

```bash
ansible all -m ping
```

Run a playbook:

```bash
ansible-playbook playbooks/example.yml
```

## 📈 Learning Path

```text
Ansible Basics
      ↓
Inventory & Ad-Hoc Commands
      ↓
Playbooks
      ↓
Variables & Facts
      ↓
Templates & Handlers
      ↓
Roles
      ↓
Multi-Server Automation
      ↓
Docker & Application Deployment
      ↓
Security Hardening
      ↓
CI/CD
      ↓
Production Automation
```

## 📌 Status

This repository is actively being developed as part of a hands-on DevOps learning roadmap.

More projects will be added progressively.
