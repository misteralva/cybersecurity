# 🛡️ Cybersecurity: Attack & Defense Labs

🌐 **English** | [Español](README.es.md)

A repository of hands-on cybersecurity labs covering both **offense** and **defense**, in a dedicated virtual lab and on TryHackMe. Each lab has its own writeup: objective, environment, steps taken, and what I learned.

> 📚 This is the continuation of my [Bash](https://github.com/misteralva/Bash) and [python](https://github.com/misteralva/python) exercises repositories, using a similar format adapted to security labs.

---

## 🎯 Who is this repository for?

- For me, as a learning journal and lab log.
- For anyone who wants to see practical, documented offense/defense exercises instead of theory alone — built on real VMs, not just write-ups copied from a course.

> ℹ️ Lab statements (`Enunciado.md`) and writeups are currently written in Spanish.

---

## 🧪 Lab environment

- **Local:** VirtualBox on my own PC — an attacker VM (Kali) plus one or more target VMs (Metasploitable, deliberately vulnerable boxes from VulnHub/HackTheBox).
- **Remote:** [TryHackMe](https://tryhackme.com) for rooms that need infrastructure I can't easily replicate at home.
- Every lab states which environment it uses.

---

## 📁 Structure

```
Cybersecurity/
├── README.md
├── README.es.md
├── 01-reconocimiento/
├── 02-explotacion/
├── 03-trafico-y-deteccion/
├── 04-hardening-y-defensa/
└── 05-proyecto-final/
```

Each lab folder contains:

| File           | Content                                                                 |
| -------------- | ------------------------------------------------------------------------ |
| `Enunciado.md` | Objective, environment needed, concepts, steps to follow, and challenges |
| `writeup.md`   | My own writeup: commands used, findings, screenshots (described), and lessons learned |

---

## 🗺️ Learning path

### Block 1 · Reconnaissance & enumeration

| #  | Lab                              | Focus                                              | Status |
| --- | --------------------------------- | --------------------------------------------------- | ------ |
| 01 | [Nmap scanning](01-reconocimiento/01-escaneo-nmap/Enunciado.md) | Scan types, timing, service/version detection, basic evasion | ⏳ |
| 02 | Service enumeration                | SMB, FTP, HTTP enumeration                          | ⏳ |
| 03 | OSINT & DNS recon                  | `whois`, `dig`, subdomain enumeration                | ⏳ |

### Block 2 · Exploitation (lab only)

| #  | Lab                              | Focus                                              | Status |
| --- | --------------------------------- | --------------------------------------------------- | ------ |
| 04 | Metasploitable walkthrough         | Exploiting a known vulnerable service with Metasploit | ⏳ |
| 05 | Controlled brute force             | Hydra against a service in my own lab               | ⏳ |
| 06 | Basic privilege escalation         | Linux privesc on a lab VM                            | ⏳ |

### Block 3 · Traffic analysis & detection

| #  | Lab                              | Focus                                              | Status |
| --- | --------------------------------- | --------------------------------------------------- | ------ |
| 07 | Packet capture basics              | Wireshark/tcpdump, reading a capture                 | ⏳ |
| 08 | Spotting a port scan               | Recognizing scan patterns in logs/captures           | ⏳ |
| 09 | IDS basics                         | Minimal Snort/Suricata setup and alerting            | ⏳ |
| 10 | Brute-force detection in logs      | Analyzing `auth.log` for failed login patterns       | ⏳ |

### Block 4 · Hardening & active defense

| #  | Lab                              | Focus                                              | Status |
| --- | --------------------------------- | --------------------------------------------------- | ------ |
| 11 | SSH hardening                      | Key-only auth, port/policy changes, disabling root login | ⏳ |
| 12 | Firewall basics                    | `ufw`/`nftables` rules for a lab server              | ⏳ |
| 13 | Fail2ban                           | Blocking brute-force attempts automatically          | ⏳ |
| 14 | Permission & user audit             | Reviewing accounts, sudoers, file permissions         | ⏳ |
| 15 | Password policy                    | Enforcing strong password/account lockout policies    | ⏳ |

### Block 5 · Final project

| Lab                              | Focus                                              | Status |
| ----------------------------------- | ----------------------------------------------------- | ------ |
| [Attack vs Defense](05-proyecto-final/proyecto-ataque-defensa/Enunciado.md) | Full attack → detect → defend → compare cycle, before/after writeup | ⏳ |

**Legend:** ✅ done · ⏳ pending · 🔜 to be detailed

---

## 🧭 How to use this repository

1. Read the lab's `Enunciado.md`.
2. Set up the environment it specifies.
3. Work through it yourself and take notes as you go.
4. Write your own `writeup.md` before checking mine (once published).

> ⚠️ All labs are run against machines I own or legal practice platforms (TryHackMe, VulnHub, HackTheBox, Metasploitable). Nothing here targets systems I don't have permission to test.

---

## 👤 Author

**David** · [@misteralva](https://github.com/misteralva) · [misteralva.github.io](https://misteralva.github.io)

ASIR student interested in networking, cybersecurity and automation.
