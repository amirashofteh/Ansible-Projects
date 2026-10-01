# Basic Website Deployment with Ansible

A hands-on Ansible project that deploys a static website using Nginx on Kali Linux.

Kali acts as both the Ansible control node and the managed node. Ansible uses a local connection rather than SSH.

## What the project does

- Ensures Nginx is installed.
- Creates `/var/www/ansible-lab`.
- Generates an HTML homepage from a Jinja2 template.
- Configures Nginx to listen on `127.0.0.1:8081`.
- Disables the default site by removing its enabling symlink.
- Enables the lab site.
- Validates Nginx configuration before starting the service.
- Ensures Nginx is running and enabled at boot.
- Reloads Nginx through a handler when site configuration changes.

Port 8081 was chosen because a Docker service already used port 80. The website is accessible only from the local machine.

## Project files

| File | Purpose |
|---|---|
| `inventory.ini` | Defines the local lab host |
| `site.yml` | Deploys the Nginx website |
| `inspect.yml` | Gathers and displays machine information |
| `render-homepage.yml` | Generates a preview inside the project directory |
| `templates/index.html.j2` | Homepage template |
| `templates/website.conf.j2` | Nginx site configuration template |

## Requirements

- Kali Linux with Ansible installed
- Sudo access
- Available TCP port 8081

Tested with Ansible Core 2.21.2.

## Inventory

```ini
[lab]
localhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3
```

## Deploy

Run commands from the project directory.

Check playbook syntax:

```bash
ansible-playbook -i inventory.ini site.yml --syntax-check
```

Deploy the website:

```bash
ansible-playbook -i inventory.ini site.yml --ask-become-pass --diff
```

Enter your sudo password when prompted.

This playbook changes the local Nginx setup, including disabling its default site.

## Verify the website

```bash
curl -i http://127.0.0.1:8081
```

A successful deployment returns HTTP 200 and the generated homepage.

Open the website locally:

http://127.0.0.1:8081

## Check idempotency

Run the deployment again:

```bash
ansible-playbook -i inventory.ini site.yml --ask-become-pass
```

When the managed state already matches the playbook, the expected recap includes:

```text
changed=0    failed=0
```

The reload handler should not run when none of its notifying tasks changes.

## Template practice

Generate a local preview:

```bash
ansible-playbook -i inventory.ini render-homepage.yml
```

Override the title for one execution:

```bash
ansible-playbook -i inventory.ini render-homepage.yml \
  -e '{"website_title": "My Production Website"}' --diff
```

Preview changes without applying them:

```bash
ansible-playbook -i inventory.ini render-homepage.yml --check --diff
```

For this template task, check mode reports predicted changes while leaving the destination file unchanged.

## Concepts practiced

- Inventories and local connections
- YAML playbooks and tasks
- Facts and debug output
- Variables and command-line overrides
- Jinja2 templates
- Package, directory, and symlink management
- Privilege escalation
- Handlers and notifications
- Syntax checking, check mode, and diff mode
- Idempotency

## Validation completed

- Local Ansible ping succeeded.
- Machine inspection completed without changes.
- Homepage preview creation and an unchanged second run succeeded.
- Variable overrides and template check mode were exercised.
- Website deployment completed without failures.
- HTTP verification returned 200 OK.

## Next improvements

- Add automated HTTP verification.
- Organize deployment tasks into a reusable role.
- Manage separate Linux hosts over SSH.
