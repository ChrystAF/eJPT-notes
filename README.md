# eJPTv2 Study Notes

Field notes and command references I built while studying for the **eLearnSecurity Junior Penetration Tester (eJPTv2)** certification. The exam is a 48-hour, fully hands-on assessment: you get a network of machines and a set of questions that can only be answered by actually performing the engagement — recon, scanning, enumeration, exploitation, pivoting, and post-exploitation. There is no multiple-choice theory; every answer comes from a flag, a config file, a credential, or a host you reached.

These notes are organized to match how the exam actually flows and how the course (INE PTS / eJPT) splits its material. Use the index below to jump to a phase; each section is written as a quick-reference you can read mid-engagement, not as a textbook.

> **How to use this during the exam:** work top-to-bottom through the methodology, and when a service or surface appears (SMB, HTTP, a login form, a shell), jump to that topic note. Keep [`cheatsheets/`](cheatsheets/README.md) open in a second tab for copy-paste commands.

---

## Exam domains (eJPTv2)

The exam weights three areas. This repo is organized around them:

| Domain | Weight | Where in this repo |
|---|---|---|
| **Assessment Methodologies & Auditing** | ~25% | [methodology](methodology/README.md), [enumeration](enumeration/README.md), [tools](tools/README.md) |
| **Host & Network Penetration Testing** | ~45% | [exploitation](exploitation/README.md), [privilege-escalation](privilege-escalation/README.md), [pivoting](pivoting/README.md), [labs](labs/README.md) |
| **Web Application Penetration Testing** | ~30% | [web](web/README.md) |

---

## Index

- **[Methodology](methodology/README.md)** — the five-phase engagement flow, scoping, and a box-writeup template to keep notes consistent.
- **[Enumeration](enumeration/README.md)** — per-service enumeration playbooks (SMB, FTP, SSH, HTTP, DNS, SNMP) — the phase that wins or loses the exam.
- **[Web](web/README.md)** — web app recon and the core eJPT web vulns (dir brute-forcing, SQLi, XSS, LFI, file upload, command injection).
- **[Exploitation](exploitation/README.md)** — searchsploit, Metasploit workflow, manual exploits, and reverse/bind shells.
- **[Privilege Escalation](privilege-escalation/README.md)** — Linux and Windows local privilege escalation checklists.
- **[Pivoting](pivoting/README.md)** — routing, port forwarding, and SOCKS proxies to reach the second network — a frequent exam theme.
- **[Tools](tools/README.md)** — purpose, usage, and "when to reach for it" notes on each tool, phase by phase.
- **[Cheatsheets](cheatsheets/README.md)** — dense copy-paste command reference for exam day.
- **[Labs](labs/README.md)** — post-exploitation enumeration notes and practice-box takeaways.

---

## Quick-start checklist

1. **Confirm scope** — know your target subnet(s) and stay inside them.
2. **Host discovery** — `nmap -sn <cidr>` or `fping -a -g <cidr>` to find live hosts.
3. **Port + service scan** — `nmap -sV -sC -p- <ip>` on each host; note versions.
4. **Enumerate every open service** — don't exploit before you've enumerated. See [enumeration](enumeration/README.md).
5. **Find the foothold** — match a version/misconfig to an exploit. See [exploitation](exploitation/README.md).
6. **Loot + escalate** — grab creds/configs, then escalate. See [privilege-escalation](privilege-escalation/README.md).
7. **Pivot** — if a host has a second NIC, route through it to the internal network. See [pivoting](pivoting/README.md).
8. **Answer the questions** — every exam question maps to something in the steps above. Document as you go.

---

*These are personal study notes written while earning the eJPT. They're meant as a learning and revision aid for authorized testing, lab practice, and the exam — not a script to run against systems you don't own or have permission to test.*
