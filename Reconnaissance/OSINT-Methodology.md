# 🔍 Reconnaissance & OSINT Methodology

**Type:** Passive Reconnaissance / Open Source Intelligence (OSINT)  
**Tester:** T-MOFE (Obed Ofobrukweta)  
**Tools:** crt.sh · Sublist3r · Hunter.io  
**Date:** 2024  
**Status:** ✅ Completed — Subdomains, Exposed Services & Open Ports Mapped  

---

## 📋 Objective

Document a structured passive reconnaissance methodology used to map the attack surface of a target organisation without sending a single packet to the target. The goal is to gather as much intelligence as possible using only publicly available information before any active testing begins.

> **Why recon matters:** The more you know before you touch the target, the more precise and effective your attack or assessment will be. Rushed or skipped recon is one of the most common reasons penetration tests miss critical vulnerabilities.

---

## 🧠 Recon Philosophy

Reconnaissance is split into two types:

| Type | Description |
|---|---|
| **Passive** | Gathering information without directly interacting with the target — using public sources only |
| **Active** | Directly probing the target (DNS queries, port scans) — leaves traces in logs |

This methodology focuses on **passive recon first** to build maximum intelligence with zero footprint.

---

## 🗺️ Recon Workflow

```
Target Identified
       ↓
  Subdomain Enumeration (crt.sh + Sublist3r)
       ↓
  Email & Staff Intelligence (Hunter.io)
       ↓
  Exposed Services & Open Ports
       ↓
  Attack Surface Map compiled
       ↓
  Hand off to Exploitation Phase
```

---

## 🔧 Phase 1 — Subdomain Enumeration

### Tool 1: crt.sh (Certificate Transparency Logs)

Every SSL/TLS certificate issued for a domain is publicly logged. crt.sh searches these logs to reveal subdomains that the organisation may not have intended to expose publicly.

**Usage:**
```
https://crt.sh/?q=%.targetdomain.com
```

**What to look for:**
- Staging and development subdomains (e.g. `dev.target.com`, `staging.target.com`)
- Admin portals (e.g. `admin.target.com`, `vpn.target.com`)
- Old or forgotten subdomains that may be unpatched

**Findings from lab practice:**
- Discovered multiple subdomains including development and staging environments
- Identified subdomains pointing to third-party services (potential supply chain risk)
- Found admin-facing subdomains not linked from the main site

---

### Tool 2: Sublist3r

Sublist3r automates subdomain enumeration by querying multiple public sources simultaneously — Google, Bing, Yahoo, Netcraft, VirusTotal, and more.

**Usage:**
```bash
sublist3r -d targetdomain.com -o subdomains.txt
```

**Flags used:**
| Flag | Purpose |
|---|---|
| `-d` | Target domain |
| `-o` | Save output to file |
| `-v` | Verbose mode — shows results in real time |

**What Sublist3r adds over crt.sh:**
- Broader source coverage across multiple search engines
- Faster enumeration for large domains
- Output saved to file for easy reference in later phases

**Findings from lab practice:**
- Corroborated subdomains found via crt.sh
- Discovered additional subdomains not present in certificate logs
- Combined output from both tools gave a more complete subdomain list

---

## 📧 Phase 2 — Email & Staff Intelligence

### Tool 3: Hunter.io

Hunter.io finds email addresses associated with a domain by scraping publicly indexed sources. This is used to identify staff, understand email format patterns, and support social engineering assessment or phishing simulation planning.

**Usage:**
```
https://hunter.io/domain/targetdomain.com
```

**What Hunter.io reveals:**
- Email addresses of staff indexed publicly
- The organisation's email format pattern (e.g. `firstname.lastname@company.com`)
- Departments represented (IT, HR, Finance — useful for targeted phishing simulation)

**Findings from lab practice:**
- Identified the email naming convention used by the target organisation
- Found publicly exposed staff emails across multiple departments
- Information noted for potential use in social engineering phase of a full engagement

> 💡 **Note:** In a real engagement, this intelligence feeds into phishing simulations or helps identify targets for credential stuffing attacks using leaked password databases.

---

## 🌐 Phase 3 — Exposed Services & Open Ports

With subdomains mapped, each discovered asset was investigated for exposed services and open ports using passive sources.

**What was investigated:**
- HTTP/HTTPS services running on non-standard ports
- Exposed admin panels and login portals
- Outdated software versions visible in HTTP response headers
- Misconfigured services accessible without authentication

**Findings from lab practice:**
- Located exposed services on subdomains that were not visible from the main domain
- Identified open ports on assets that suggested running services (web servers, databases)
- Found HTTP response headers leaking software version information — useful for targeted exploitation

---

## 📊 Attack Surface Summary

After completing all three phases, the following attack surface map was compiled:

| Asset Type | Finding | Risk |
|---|---|---|
| Subdomains | Dev/staging environments discovered | 🟠 High |
| Subdomains | Admin portals identified | 🔴 Critical |
| Email addresses | Staff emails and naming convention exposed | 🟠 High |
| Exposed services | Services running on non-standard ports | 🟠 High |
| HTTP headers | Software version information leaking | 🟡 Medium |

---

## 🛠️ Tools Summary

| Tool | Type | Purpose |
|---|---|---|
| crt.sh | Passive | Subdomain enumeration via SSL certificate logs |
| Sublist3r | Passive | Automated subdomain enumeration across multiple sources |
| Hunter.io | Passive | Email address and staff intelligence gathering |

---

## 🧠 Key Lessons Learned

- **Passive recon reveals more than most people expect.** Subdomains, staff emails, exposed services, and software versions are all publicly available — organisations often don't realise how much they're leaking.
- **Always combine tools.** crt.sh and Sublist3r overlap but each finds things the other misses — using both produces a more complete picture.
- **Recon output directly shapes exploitation strategy.** A forgotten dev subdomain with an older software version is a far more attractive target than the hardened main site.
- **Document everything.** Recon notes saved to files (subdomains.txt, emails.txt) keep the engagement organised and ensure nothing is missed.

---

## ⚠️ Disclaimer

All reconnaissance documented here was conducted against **intentionally vulnerable lab targets** or domains used in security training exercises with explicit permission. Passive OSINT techniques using public data sources were applied. No unauthorised systems or private data were accessed.

---

*← [Back to Portfolio](../README.md)*
