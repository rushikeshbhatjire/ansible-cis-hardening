# Ansible CIS Hardening — Rocky Linux 9
Ansible role that applies CIS Benchmark controls (Level 1 & Level 2) to Rocky Linux 9 systems. Built and tested on a real OpenStack cloud environment.

## What it does
| Section | Controls Applied |
|---|---|
| 1 — Filesystem | /tmp, /dev/shm, /var/tmp mount hardening, disabled cramfs/squashfs/udf |
| 2 — Patches | GPG check enforcement, dnf-automatic, system updates |
| 3 — Sudo | PTY enforcement, sudo logging |
| 4 — Integrity | AIDE installation, database init, daily cron check |
| 6 — Process | Core dump disable, ASLR enable |
| 7 — Network | IP forwarding, source routing, ICMP, TCP syncookies, martian logging |
| 8 — Firewall | firewalld install, enable, SSH rule |
| 9 — Audit | auditd, rsyslog, login/sudoers audit rules |
| 10 — SSH | 10 hardened sshd_config settings, LogLevel, Protocol 2 |
| 11 — PAM | Password aging, SHA-512 hashing, account lockout via faillock |

## Requirements
- Ansible >= 2.14
- Target: Rocky Linux 9 (RHEL 9 compatible)
- SSH key-based access to target nodes

## Usage

### 1. Clone the repo
\`\`\`bash
git clone https://github.com/rushikeshbhatjire/ansible-cis-hardening.git
cd ansible-cis-hardening
\`\`\`

### 2. Configure inventory
\`\`\`bash
cp inventory/hosts.ini.example inventory/hosts.ini
# Edit hosts.ini with your target IPs and SSH key path
\`\`\`

### 3. Dry run first
\`\`\`bash
ansible-playbook -i inventory/hosts.ini site.yml --check --diff
\`\`\`

### 4. Apply hardening
\`\`\`bash
ansible-playbook -i inventory/hosts.ini site.yml
\`\`\`

## Defaults — toggle controls on/off
All controls are configurable in \`roles/cis_hardening/defaults/main.yml\`.
\`\`\`yaml
cis_ssh_permit_root_login: "prohibit-password"  # change per environment
cis_install_aide: true                           # set false to skip AIDE
cis_enable_firewalld: true                       # set false if using nftables
cis_disable_ipv6: false                          # set true if IPv6 not needed
\`\`\`

## Idempotency
Verified idempotent — running the playbook multiple times makes no unintended changes.

## Environment
Tested on Rocky Linux 9.5 (Blue Onyx) deployed on OpenStack/KVM cloud infrastructure.

## Author
Rushikesh Bhatjire — Infrastructure & Cloud Operations Engineer
[linkedin.com/in/rushikesh-bhatjire](https://linkedin.com/in/rushikesh-bhatjire)

## License
MIT
