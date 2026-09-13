# Ansible Inventory Management

A practical Ansible project demonstrating how to create and manage a static inventory, organize hosts into groups, use nested groups, and manage group-level and host-level variables.

## Project Goals

* Understand Ansible inventory structure
* Create host groups
* Create nested inventory groups
* Use `group_vars`
* Use `host_vars`
* Understand variable scope
* Inspect inventory data with `ansible-inventory`
* Target specific hosts and groups with `--limit`

## Project Structure

```text
Ansible-Inverntory/
├── group_vars/
│   ├── dbservers.yml
│   └── webservers.yml
├── host_vars/
│   └── web01.yml
├── inventory.ini
├── test_inventory.yml
└── README.md
```

## Inventory Structure

The inventory contains two server groups:

```text
production
├── webservers
│   ├── web01
│   └── web02
└── dbservers
    └── db01
```

### `inventory.ini`

```ini
[webservers]
web01
web02

[dbservers]
db01

[production:children]
webservers
dbservers
```

The `production` group is a parent group containing both `webservers` and `dbservers`.

## Group Variables

Web servers share common variables through:

```text
group_vars/webservers.yml
```

```yaml
server_role: web
http_port: 80
```

Database servers use:

```text
group_vars/dbservers.yml
```

```yaml
server_role: database
db_port: 5432
```

This demonstrates how variables can be automatically applied to every host belonging to a group.

## Host Variables

`web01` has an additional host-specific variable:

```text
host_vars/web01.yml
```

```yaml
server_id: web01
```

This variable applies only to `web01`.

For example:

```text
web01
├── server_role: web
├── http_port: 80
└── server_id: web01
```

While `web02` receives the group variables but does not have a `server_id`.

## Inventory Test Playbook

The project includes a simple playbook for testing inventory and variable resolution.

```yaml
---
- name: Test Ansible Inventory
  hosts: all
  gather_facts: false

  tasks:
    - name: Display host information
      ansible.builtin.debug:
        msg:
          - "Hostname: {{ inventory_hostname }}"
          - "Role: {{ server_role | default('undefined') }}"
          - "Server ID: {{ server_id | default('undefined') }}"
```

## Testing the Inventory

Run the playbook against all hosts:

```bash
ansible-playbook -i inventory.ini test_inventory.yml
```

Run only the web servers:

```bash
ansible-playbook -i inventory.ini test_inventory.yml --limit webservers
```

Run only the database servers:

```bash
ansible-playbook -i inventory.ini test_inventory.yml --limit dbservers
```

Run a specific host:

```bash
ansible-playbook -i inventory.ini test_inventory.yml --limit web01
```

Run the entire production group:

```bash
ansible-playbook -i inventory.ini test_inventory.yml --limit production
```

## Inspecting the Inventory

Display the inventory hierarchy:

```bash
ansible-inventory -i inventory.ini --graph
```

Display the complete inventory as JSON:

```bash
ansible-inventory -i inventory.ini --list
```

Inspect variables resolved for a specific host:

```bash
ansible-inventory -i inventory.ini --host web01
```

Example:

```json
{
    "http_port": 80,
    "server_id": "web01",
    "server_role": "web"
}
```

## What I Learned

* How static Ansible inventories are structured
* How hosts can be organized into logical groups
* How parent groups can contain child groups
* How `group_vars` applies variables to groups of hosts
* How `host_vars` provides host-specific configuration
* How Ansible combines group and host variables
* How to inspect Ansible's inventory resolution
* How to target specific inventory groups and hosts using `--limit`

## Key Ansible Concepts

```text
Inventory
   │
   ├── Hosts
   │
   ├── Groups
   │
   ├── Child Groups
   │
   ├── group_vars
   │
   └── host_vars
```

This project establishes the inventory foundation required for larger Ansible automation projects involving multiple servers, roles, templates, handlers, and deployments.

## Technologies

* Ansible
* YAML
* INI
* Linux
* Bash

````

One tiny thing: your directory is currently named `Ansible-Inverntory` — **`Inverntory` is misspelled**. I'd fix that before pushing:

```bash
cd ~/Projects/Ansible-Projects
mv Ansible-Inverntory Ansible-Inventory
cd Ansible-Inventory
````

Then:

```bash
git status
git add .
git commit -m "Add Ansible inventory management project"
```

And you're ready for the GitHub push. 🚀
