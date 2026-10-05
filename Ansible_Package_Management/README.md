Project 28 — Ansible Package Management
A hands-on learning project that manages and verifies software packages on Kali Linux using Ansible. Kali acts as both the control node and managed node through a local connection. Multi-host SSH management is a future extension.
Features
- Install required packages from a shared variables file.
- Explicitly remove packages listed for removal.
- Refresh the APT package index when requested.
- Use present for installation or latest for selected package upgrades.
- Gather package facts and assert package presence or absence.
- Demonstrate idempotency and preview changes with check mode.
Project files
Path	Purpose
inventory/hosts	Local managed host and connection settings
group_vars/all.yml	Package lists, installation state, and cache-refresh setting
playbook.yml	Package management and verification tasks
README.md	Usage, verified results, and lessons learned


Requirements
- Kali Linux with Ansible installed
- Sudo access
- Access to configured APT repositories for downloads and index refreshes
Lab environment: Ansible Core 2.21.2. Commands below run from the project directory.
Inventory
inventory/hosts:
[lab]
localhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3
Default variables
group_vars/all.yml:
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
Ansible loads these shared variables for inventory hosts. Keep installation and removal lists separate, with no package in both lists.
Setting	Meaning
present	Ensure installation without requiring the newest version
latest	Ensure the newest version available in the current APT indexes
absent	Request removal, potentially including dependent packages
refresh_package_cache	Control whether package indexes are refreshed


Test and run
Test the local connection:
ansible -i inventory/hosts lab -m ansible.builtin.ping
Check playbook syntax:
ansible-playbook -i inventory/hosts playbook.yml --syntax-check
Run package management:
ansible-playbook -i inventory/hosts playbook.yml --ask-become-pass --diff
Enter the local user's sudo password when prompted. Repeat the run to check idempotency. With the defaults and required packages installed, the observed recap was:
ok=4 changed=0 unreachable=0 failed=0 skipped=3
The cache refresh, package removal, and removal verification skip under the default settings.
Optional package-index refresh
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass \
  -e '{"refresh_package_cache": true}'
This refreshes package information; it does not request upgrades. The extra variable overrides the default for this execution only. The verified run executed the refresh task and returned:
ok=5 changed=0 unreachable=0 failed=0 skipped=2
Preview and apply selected upgrades
Preview:
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass --check --diff \
  -e '{"package_install_state": "latest"}'
Review the package-manager summary, including dependency changes, before applying. Check mode predicts changes; it does not perform the package upgrades.
Apply the reviewed changes:
ansible-playbook -i inventory/hosts playbook.yml \
  --ask-become-pass --diff \
  -e '{"package_install_state": "latest"}'
Repeat with the same indexes to verify no further changes are needed. This manages the listed packages and their dependencies, rather than requesting a full system upgrade.
Verification
The playbook gathers installed package information with ansible.builtin.package_facts. Assertions check each installation-list package is present and each removal-list package is absent.
These assertions verify presence or absence, not whether an installed version is the newest. During a check-mode run, they inspect the actual installed state, not the predicted post-upgrade state. A loop checks multiple packages but counts as one task in the recap.
Verified lab results
Exercise	Observed result
Local connection	ping: pong
Initial installation	changed=1, failed=0; six packages verified
Repeat installation	changed=0, failed=0
Add tree to installation list	Already installed; task reported ok and assertion passed
Remove tree from installation list	changed=0; direct query confirmed it remained installed
Explicitly remove tree	Removed tree and seven dependent packages; all eight subsequently restored and verified
Default run after recovery	changed=0, failed=0, skipped=3
Optional index refresh	Task executed; changed=0, failed=0
Preview latest	Four proposed upgrades, zero removals; no upgrades applied in check mode
Apply latest	Upgraded curl, libcurl3t64-gnutls, libcurl4-gnutls, and libcurl4t64; changed=1, failed=0
Repeat latest	changed=0, failed=0, skipped=3


Results describe this lab session; future results depend on installed state and repository indexes.
Dependency-removal lesson
Removing a package from packages_to_install stops managing its installation; it does not uninstall it. Removal requires an explicit absent request.
In this Kali lab, removing tree also removed creddump7, kali-linux-core, kali-linux-default, kali-linux-headless, kali-system-cli, kali-system-core, and kali-system-gui. All eight packages were restored and their installed status verified. The final removal list is empty.
Before any future removal exercise, simulate the specific proposed removal and review the complete dependency impact:
apt-get --simulate remove PACKAGE_NAME
Replace PACKAGE_NAME with the intended lab package. Use a disposable VM or container for removal experiments. An empty removal list is the current default; the playbook does not automatically reject removal of packages outside that list. Do not treat an APT suggestion to run autoremove as part of this project.
Troubleshooting lessons
- Use one tasks: key per play. Duplicate keys can cause earlier tasks to be ignored.
- Use packages_to_remove: [] for an empty list, or an indented list beneath packages_to_remove: for entries; do not combine both forms.
- Define refresh_package_cache in the variables file. A command-line override lasts only for that execution.
- A successful syntax check alone does not establish that all runtime variables are valid.
Future extensions
- Manage additional Linux hosts over SSH.
- Add guards against overlapping installation and removal lists.
- Add reviewed dependency-removal safeguards.
- Practice full-system upgrades in a disposable environment.
- Organize tasks into a reusable role.
Multi-host SSH execution and full-system upgrades have not been completed in this project.
