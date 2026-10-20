# Task 1 — Basic Network Scanning with Nmap

## Objective
Perform a network scan using Nmap to identify open ports and running services on a target machine, and document each finding with a security risk analysis.

## Tools & Environment
- **Attacker machine:** Kali Linux (VirtualBox)
- **Target machine:** Metasploitable2 (VirtualBox)
- Both VMs connected via a shared VirtualBox NAT Network / Host-only Adapter

## What is Nmap?
Nmap (Network Mapper) is an open-source tool used to discover hosts and services on a network by sending packets and analyzing the responses. It is widely used by security professionals to map attack surfaces during reconnaissance.

## Why Network Scanning Matters
Network scanning is the first step in understanding an organization's attack surface. Identifying open ports and running services helps security teams find misconfigured, outdated, or unnecessary services before an attacker does.

## Ethical Use Guidelines
This scan was performed only against a Metasploitable2 VM that I own and run locally for learning purposes. Scanning any system without explicit authorization is illegal and unethical. Always obtain written permission before scanning systems you do not own.

## Installation
Nmap comes pre-installed on Kali Linux. Verified with:
```bash
nmap --version
```

## Scans Performed

**1. Basic scan**
```bash
nmap 192.168.1.107
```



**2. Service version detection**
```bash
nmap -sV 192.168.1.107
```



**3. OS detection**
```bash
sudo nmap -O 192.168.1.107
```



**4. Combined scan, output saved**
```bash
nmap -sV -O <METASPLOITABLE2_IP> -oN nmap_scan_results.txt
```



## Findings

| Port | Service | Description | Security Risk |
|------|---------|-------------|----------------|
| 21   | FTP     | File Transfer Protocol | Anonymous login often allowed; sends credentials in plaintext |
| 22   | SSH     | Secure Shell remote access | Low risk if patched and key-based auth used |
| 23   | Telnet  | Remote login | High risk — transmits credentials in plaintext, should be disabled |
| 80   | HTTP    | Web server | Risk depends on hosted application vulnerabilities |
| 445  | SMB     | File/printer sharing | High risk — historically exploited (e.g., EternalBlue) |

*(Fill in with your actual scan output — Metasploitable2 typically exposes 15+ services.)*

## Files in this folder
- `nmap_scan_results.txt` — raw scan output
- `screenshots/` — terminal output screenshots for each scan

## Demo Video
*(Link to your screen recording here)*
