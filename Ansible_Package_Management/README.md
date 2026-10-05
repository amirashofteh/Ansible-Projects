# Project 28 — Ansible Package Management

A hands-on Ansible project that manages software packages on Kali Linux.

Kali acts as both the control node and managed node using a local connection. Multi-host SSH management remains a future extension.

## Features

- Install required packages using variables.
- Explicitly remove unwanted packages.
- Refresh the APT package index when requested.
- Manage installation with `present` or selected upgrades with `latest`.
- Verify package presence and absence using package facts and assertions.
- Demonstrate idempotency.
- Preview changes using check mode and diff mode.

## Project Structure

| Path | Purpose |
|---|---|
| `inventory/hosts` | Defines the local managed host |
| `group_vars/all.yml` | Defines package lists and settings |
| `playbook.yml` | Manages and verifies packages |
| `README.md` | Documents usage and verified results |

## Requirements

- Kali Linux
- Ansible installed
- Sudo access
- Access to configured APT repositories for downloads

Tested with Ansible Core 2.21.2.

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
packages_to_install:
  - curl
  - git
  - vim
  - htop
  - wget
  - unzip

packages_to_remove: []

package_install_state: present
refresh_package_cache: false
```

Keep installation and removal lists separate. A package should not appear in both lists.

| Setting | Meaning |
|---|---|
| `present` | Ensure the package is installed |
| `latest` | Ensure the newest version available in current APT indexes |
| `absent` | Request package removal |
| `refresh_package_cache` | Enable or skip package-index refresh |

## Usage

Run commands from the project directory.

### Test connectivity

```bash
ansible -i inventory/hosts lab -m ansible.builtin.ping
```

Expected result: `ping: pong`.

### Check syntax

```bash
ansible-playbook -i inventory/hosts playbook.yml --syntax-check
```

### Install and verify packages

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass --diff
```

Enter your Kali sudo password when prompted.

### Verify idempotency

Repeat the normal run:

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass
```

With the default settings and packages already installed, the verified recap was:

```text
ok=4 changed=0 unreachable=0 failed=0 skipped=3
```

Cache refresh, removal, and removal verification skip under the defaults.

## Refresh the Package Index

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass \
  -e '{"refresh_package_cache": true}'
```

This refreshes package information without requesting software upgrades.

The command-line variable overrides the default for this execution only.

Verified result:

```text
ok=5 changed=0 unreachable=0 failed=0 skipped=2
```

The refresh task executed and reported no change.

## Preview Selected Package Upgrades

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass --check --diff \
  -e '{"package_install_state": "latest"}'
```

Check mode predicts package changes without applying the upgrades.

Review the complete package-manager summary, including dependency upgrades and removals.

## Apply Reviewed Upgrades

```bash
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass --diff \
  -e '{"package_install_state": "latest"}'
```

This manages packages in the installation list and their dependencies. It does not request a full-system upgrade.

Repeat the command to check idempotency against the same package indexes.

## Package Verification

The playbook uses:

- `ansible.builtin.package_facts` to collect installed package information.
- `ansible.builtin.assert` to verify required packages are present.
- Another assertion to verify removal-list packages are absent.

These assertions check presence or absence, not whether versions are the newest available.

During check mode, package facts describe the actual installed state, not the predicted state after upgrades.

## Verified Results

| Exercise | Observed result |
|---|---|
| Local connection | Successful `pong` response |
| Initial installation | `changed=1`, `failed=0`; six packages verified |
| Repeat installation | `changed=0`, `failed=0` |
| Add `tree` to installation list | Already installed; verification passed |
| Remove `tree` from installation list | No changes; `tree` remained installed |
| Explicitly remove `tree` | Also removed seven dependent packages |
| Restore removed packages | All eight verified as installed |
| Optional cache refresh | Task executed successfully |
| Preview `latest` | Four proposed upgrades, zero removals |
| Apply `latest` | Four packages upgraded successfully |
| Repeat `latest` | `changed=0`, `failed=0` |

The upgraded packages were:

- `curl`
- `libcurl3t64-gnutls`
- `libcurl4-gnutls`
- `libcurl4t64`

Results reflect the tested lab session. Future changes depend on installed packages and repository indexes.

## Dependency-Removal Lesson

Removing a package from `packages_to_install` does not uninstall it. It simply stops requesting that package's installation.

Explicit removal requires `state: absent`.

During this lab, removing `tree` also removed:

- `creddump7`
- `kali-linux-core`
- `kali-linux-default`
- `kali-linux-headless`
- `kali-system-cli`
- `kali-system-core`
- `kali-system-gui`

All eight packages were subsequently restored and verified.

The final configuration keeps:

```yaml
packages_to_remove: []
```

Before future removal exercises, preview dependency effects:

```bash
apt-get --simulate remove PACKAGE_NAME
```

Replace `PACKAGE_NAME` with the intended package and review every proposed removal.

Use a disposable environment for removal experiments. The current playbook does not prevent APT from removing dependent packages outside the removal list. APT's `autoremove` suggestion is not a required project step.

## Troubleshooting Lessons

- Use one `tasks:` section per play.
- Use spaces for YAML indentation.
- An empty list is written as `packages_to_remove: []`.
- For a populated list, remove `[]` and place entries beneath the key.
- Define default variables in `group_vars/all.yml`.
- Command-line overrides apply only to that execution.
- Syntax checking alone does not validate all runtime variables.

## Concepts Practiced

- Inventory and local connections
- Shared variables with `group_vars`
- Privilege escalation
- APT package management
- Conditional tasks with `when`
- Boolean filters
- Package facts
- Assertions and loops
- Desired state and idempotency
- Check mode and diff mode
- Dependency analysis and recovery

## Future Improvements

- Manage multiple Linux hosts over SSH.
- Reject overlapping installation and removal lists.
- Add dependency-removal safeguards.
- Practice full-system upgrades in a disposable environment.
- Organize tasks into a reusable role.

Multi-host SSH management and full-system upgrades have not yet been completed.
