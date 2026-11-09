# Methodology

A penetration test is a structured process, not a bag of tricks. The eJPT rewards people who work a repeatable methodology: you'll rarely be stuck because you ran out of exploits — you'll be stuck because you skipped enumeration on a service. Work the phases in order and document as you go.

The core loop: **recon → scan → enumerate → exploit → post-exploit → (pivot and repeat).**

---

## 1. Information Gathering (Reconnaissance)

Collect everything you can about the target before — and while — you attack it.

- **Passive recon** — no direct interaction with the target. `whois` for registration data, Google dorks for leaked files, `dnsdumpster`/`crt.sh` for infrastructure and subdomains, `theHarvester` for emails and hosts. Useful for context and for building username/password lists.
- **Active recon** — directly touching the target. Host discovery with `nmap -sn`, `fping`, `arp-scan`, or `netdiscover`; DNS record discovery with `dnsenum`/`dnsrecon`.

On the exam, recon is mostly *active* and internal — you're handed a subnet and need to map it fast.

## 2. Scanning

Turn "there are hosts here" into "here are the open ports and service versions."

- **Host discovery first**, then port scanning per host.
- Start wide, then go deep: a quick scan to find open ports, then a targeted `-sV -sC` scan on those ports, and a full `-p-` scan so you don't miss a high port.
- **Write down every version string.** The version of a service is usually the thing that points you to the exploit.

See [enumeration](../enumeration/README.md) for per-service follow-up and [nmap in tools](../tools/README.md) for flags.

## 3. Enumeration

This is where exams are won. For **every open port**, interact with the service and pull out usernames, shares, versions, config, and content.

- SMB → shares, users, null sessions
- HTTP → directories, tech stack, login pages, parameters
- FTP → anonymous login, writable dirs
- DNS → zone transfers, hostnames
- SNMP → system info, running processes, user accounts

Rule of thumb: **do not move to exploitation until you've enumerated everything.** The foothold is almost always hiding in enumeration output you skimmed past. Full playbooks in [enumeration](../enumeration/README.md).

## 4. Vulnerability Assessment

Match what you found to a known weakness.

- Feed version strings to `searchsploit` and Google.
- Check for default/weak credentials on every login you find.
- Note misconfigurations (anonymous FTP, writable shares, exposed admin panels, verbose errors).

Prioritize: a weak credential or an anonymous share beats a flaky memory-corruption exploit every time.

## 5. Exploitation

Use what you gathered to get a foothold.

- **Manual** — run a known public exploit (often from `searchsploit`) against a vulnerable version.
- **Metasploit** — search for the matching module, set options, run. Great for the exam's speed demands.
- **Web** — SQLi, LFI/RFI, file upload, command injection on web targets. See [web](../web/README.md).
- Catch shells with a `nc` listener; keep a [reverse-shell reference](../cheatsheets/README.md) handy.

Details and the full MSF workflow in [exploitation](../exploitation/README.md).

## 6. Post-Exploitation

You have a shell. Now expand.

- **Situational awareness** — who am I, what OS, what privileges, what's on the network (second NIC?).
- **Loot** — config files, password files, history, SSH keys, hashes.
- **Privilege escalation** — go from low-priv user to root/SYSTEM. Checklists in [privilege-escalation](../privilege-escalation/README.md).
- **Pivoting** — if this host can reach a network you can't, route through it. See [pivoting](../pivoting/README.md).

Then **repeat the whole loop** against the newly reachable hosts.

## 7. Cleanup & Reporting

Not optional — a test isn't finished until both are done (and in a real engagement this is the deliverable the client pays for).

- **Cleanup** — remove uploaded tools/payloads and any temporary accounts you created.
- **Reporting** — document reproduction steps, impact, and remediation for each finding. On the exam, your "report" is answering the questions correctly; keeping clean per-host notes makes that trivial.

---

## Mindset notes

- **Enumerate, don't guess.** When stuck, go back a phase — you missed something in enumeration.
- **Breadth before depth early, depth before breadth once you have a lead.** Map all hosts first; then fully exploit the most promising one.
- **Document continuously.** Use the [box writeup template](Box%20Writeup%20Template.md) so every host's notes look the same and nothing gets lost.
- **Time management (exam).** 48 hours is generous but the network is wide — don't rabbit-hole on one hard box while three easy ones sit un-enumerated.
