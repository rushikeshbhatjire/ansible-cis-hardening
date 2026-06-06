<div align="center">

# 🔒 ansible-cis-hardening

**Ansible role applying CIS Benchmark Level 1 & 2 controls to Rocky Linux 9**  
Built and verified on real OpenStack/KVM cloud infrastructure.

![Ansible](https://img.shields.io/badge/Ansible-2.14+-EE0000?style=flat-square&logo=ansible&logoColor=white)
![Rocky Linux](https://img.shields.io/badge/Rocky_Linux-9-10B981?style=flat-square&logo=rockylinux&logoColor=white)
![CIS](https://img.shields.io/badge/CIS-Level_1_%26_2-0052CC?style=flat-square)
![Controls](https://img.shields.io/badge/Controls-40%2B-F59E0B?style=flat-square)
![Idempotent](https://img.shields.io/badge/Idempotent-✓-22C55E?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-6B7280?style=flat-square)

</div>

---

## Architecture

| Node | Role | IP | OS |
|---|---|---|---|
| `ansible-control` | Ansible Control Node | 157.119.42.57 | Rocky Linux 9 |
| `cis-target-01` | Managed Node | 43.242.225.78 | Rocky Linux 9.5 (Blue Onyx) |

> **Environment:** OpenStack/KVM · CloudPe · SSH key-based auth · No agent required on targets

---

## What it does

| Section | Domain | Controls Applied |
|---|---|---|
| §1 | Filesystem | `/tmp`, `/dev/shm`, `/var/tmp` mount hardening · cramfs/squashfs/udf disabled |
| §2 | Patches | GPG enforcement · `dnf-automatic` · full system update |
| §3 | Sudo | PTY requirement · sudo log at `/var/log/sudo.log` |
| §4 | Integrity | AIDE install · DB init · daily cron check at 05:00 |
| §6 | Process | Core dumps disabled · ASLR enabled (`randomize_va_space=2`) |
| §7 | Network | IP forwarding · source routing · TCP syncookies · martian logging |
| §8 | Firewall | firewalld installed · enabled · SSH rule permanent |
| §9 | Audit | auditd · rsyslog · login/logout + sudoers audit rules |
| §10 | SSH | 12 `sshd_config` options hardened · LogLevel INFO · Protocol 2 |
| §11 | PAM | SHA-512 hashing · password aging · faillock account lockout |

---

## Quick start

**1. Clone**
```bash
git clone https://github.com/rushikeshbhatjire/ansible-cis-hardening.git
cd ansible-cis-hardening
```

**2. Configure inventory**
```bash
cp inventory/hosts.ini.example inventory/hosts.ini
# Edit with your target IP and SSH key path
```

**3. Dry run first — always**
```bash
ansible-playbook -i inventory/hosts.ini site.yml --check --diff
```

**4. Apply**
```bash
ansible-playbook -i inventory/hosts.ini site.yml
```

---

## Key defaults

All 50+ variables are in `roles/cis_hardening/defaults/main.yml`. Every control can be toggled on/off.

```yaml
cis_ssh_permit_root_login: "prohibit-password"  # key auth allowed, passwords blocked
cis_install_aide: true                           # set false to skip AIDE on first run
cis_enable_firewalld: true                       # set false if using nftables
cis_disable_ipv6: false                          # set true if IPv6 not in use
cis_lockout_attempts: 5                          # failed logins before lockout
cis_password_min_length: 14                      # minimum password length
```

Override per host using `host_vars/<hostname>.yml` or at runtime with `-e varname=value`.

---

## Idempotency

Verified — second run produces **0 changed, 0 failed**.  
Safe to run repeatedly in CI/CD pipelines or scheduled compliance checks.

---

## Lessons learned

**SSH lockout after first run**  
Default CIS sets `PermitRootLogin: no` — locked out root-only environments. Fixed by switching to `prohibit-password` (key auth allowed, passwords blocked). Always keep your SSH session open during the first run.

**Heredoc variable expansion**  
Using `EOF` as a heredoc delimiter causes bash to silently expand Ansible Jinja2 variables like `{{ item }}`. Fixed by using `ENDOFFILE` as the quoted delimiter throughout.

**Check mode + service dependencies**  
The `firewalld` module fails in `--check` mode when the service isn't running yet. Fixed with `when: not ansible_check_mode` on service start and firewall rule tasks.

---

## Environment

Tested on Rocky Linux 9.5 (Blue Onyx) deployed on OpenStack/KVM cloud infrastructure.

---

## Author

**Rushikesh Bhatjire** — Infrastructure & Cloud Operations Engineer  
[linkedin.com/in/rushikesh-bhatjire](https://linkedin.com/in/rushikesh-bhatjire)

## License

MIT
