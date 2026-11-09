# Tools Index

Notes on the tools I used while studying for the eJPT: what each one is **for**, **how** to run it, and **when** to reach for it. Organized by engagement phase so you can find the right tool for the phase you're in.

**Jump to:** [Recon](#recon-passive) · [Active info-gathering](#information-gathering---active) · [Scanning & Enumeration](#scanning--enumeration-tools) · [Web](#web-tools) · [Password attacks](#password-attack-tools) · [Exploitation](#exploitation-tools) · [Post-exploitation](#post-exploitation-tools)

See also: [enumeration playbooks](../enumeration/README.md) · [cheatsheets](../cheatsheets/README.md)

## Recon (Passive)

## **host {CMD}**
- **Purpose**: A command-line tool for performing DNS lookups.
- **How to use**: Run `host <domain-name>` in your terminal.
- **When to use**: Use this for quick lookups to find a domain's IPv4 and IPv6 addresses, as well as its mail servers.

## **WebsiteURL/robots.txt {URL}**
- **Purpose**: A file on a web server that tells search engine crawlers which parts of the site they should and should not index.
- **How to use**: Simply append `/robots.txt` to the end of a website's URL in your browser.
- **When to use**: This can reveal folders and files that the site owner wants to hide from search engines, which may contain sensitive information.

## **WebsiteURL/sitemap.xml {URL}**
- **Purpose**: An XML file that lists all the publicly available pages on a website.
- **How to use**: Append `/sitemap.xml` to a website's URL in your browser.
- **When to use**: Use this to get a comprehensive map of a website's structure and find pages you might have otherwise missed during manual browsing.

## **Wappalyzer {Browser Add-On}**
- **Purpose**: A browser extension that identifies the technology stack of a website.
- **How to use**: Install the add-on in your browser. When you visit a website, click the extension icon to see a list of technologies used.
- **When to use**: Use this for a quick and easy way to identify a website's underlying technologies, such as its content management system (CMS), web server, and programming languages.

## **whatweb {CMD}**
- **Purpose**: A command-line tool that identifies what a website is running.
- **How to use**: Run `whatweb <website-url>` in your terminal.
- **When to use**: This is a powerful alternative to browser extensions for fingerprinting a website and identifying its technologies, including versions and server-side details.

## **httrack {CMD}**
- **Purpose**: A command-line tool that downloads an entire website to your local machine.
- **How to use**: Use a command like `httrack <website-url>` to create a local copy of a site.
- **When to use**: Use this to analyze a website's source code offline for hidden directories, sensitive information in comments, or other clues that might not be visible on the live site.

## **whois {CMD}**
- **Purpose**: A command-line tool for querying domain registration information.
- **How to use**: Run `whois <domain-name>` in your terminal.
- **When to use**: Use it to find out who a domain is registered to, their contact information, and the domain's registration and expiration dates.

## **netcraft.com {Website}**
- **Purpose**: A website that provides a comprehensive report on a given domain.
- **How to use**: Visit the website and enter a domain name into the search bar.
- **When to use**: Use this to get an all-in-one report on a website, including its hosting provider, operating system, and security details.

## **dnsdumpster.com {Website}**
- **Purpose**: A website that provides detailed DNS records and a visual map of a domain's infrastructure.
- **How to use**: Go to the website and enter a domain name.
- **When to use**: Use it for more advanced DNS enumeration to discover host records, subdomains, and other related domains in a clear, graphical format.

## **sublist3r {CMD}**
- **Purpose**: A command-line tool for subdomain enumeration.
- **How to use**: Run `sublist3r -d <domain-name>` in your terminal.
- **When to use**: Use this to find all publicly available subdomains of a target domain, which can significantly expand your attack surface.

## **Google Dorks {Search Engine}**
- **Purpose**: Advanced search queries that leverage Google's search engine to find specific information.
- **How to use**: Use special operators like `site:`, `filetype:`, and `intitle:` in your Google search queries.
- **When to use**: Use this to uncover sensitive data or accidental leaks, such as exposed configuration files, open directories, or specific file types that are not linked from the main website.

## **Google Hacking DB {Website}**
- **Purpose**: A database of common Google Dorks.
- **How to use**: Visit the website and browse the categorized list of dorks.
- **When to use**: This is a great resource for finding pre-written queries to look for specific types of information and vulnerabilities.

## **theHarvester {CMD}**
- **Purpose**: A command-line tool for gathering public information about a target.
- **How to use**: Use a command like `theHarvester -d <domain> -b <source>` to gather emails, subdomains, and hostnames.
- **When to use**: Use it to collect open-source intelligence (OSINT) from various public sources, which is useful for social engineering, password attacks, and further enumeration.

## **haveibeenpwned {Website}**
- **Purpose**: A website that checks if an email address or password has been compromised in a data breach.
- **How to use**: Go to the website and enter an email address or password.
- **When to use**: Use this to verify if any credentials for a target have been leaked online, which can be useful for validating potential passwords found through other means.

# Information Gathering - Active

## **wafw00f {CMD}**
- **Purpose**: A command-line tool that identifies and fingerprints Web Application Firewalls (WAFs).
- **How to use**: Run `wafw00f <website-url>` in your terminal.
- **When to use**: Use this to determine if a website is protected by a WAF. Knowing this is crucial for penetration testing, as a WAF can block common attack payloads.

## **dnsenum {CMD}**
- **Purpose**: A command-line tool for DNS enumeration, including zone transfers and brute-force methods.
- **How to use**: Run `dnsenum <domain-name>` in your terminal.
- **When to use**: Use this to gather DNS information such as host records, mail servers, and name servers. It can also attempt a zone transfer, which can reveal a full list of all hosts for a domain.

## **dnsrecon {CMD}**
- **Purpose**: A command-line tool for comprehensive DNS record discovery.
- **How to use**: Run `dnsrecon -d <domain-name>` in your terminal.
- **When to use**: Use this to perform various DNS lookups, including standard record types (A, MX, SOA, etc.), and to identify subdomains and cached records.

## **nmap {CMD}**
- **Purpose**: A powerful network scanner used for network discovery and security auditing.
- **How to use**: Run `nmap <target>` in your terminal. Options like `-sV` (version detection) and `-p-` (all ports) are common.
- **When to use**: This is an essential tool for discovering hosts and services on a network, identifying open ports, and fingerprinting operating systems.

## **netdiscover {CMD}**
- **Purpose**: A tool for ARP-based host discovery on a network.
- **How to use**: Run `netdiscover -i <interface>` in your terminal.
- **When to use**: Use this to actively scan your local network to discover live hosts without needing to ping them, which is useful when ICMP is blocked.

## **ping/fping {CMD}**
- **Purpose**: `ping` is a command-line utility that tests reachability of a host on an IP network. `fping` is a more efficient version for multiple hosts.
- **How to use**: Run `ping <target-ip>` or `fping -a -g <subnet>`.
- **When to use**: Use these for basic host discovery to determine if a target is alive and to measure network latency.

## **arp-scan {CMD}**
- **Purpose**: A tool that uses ARP to discover hosts on a local network.
- **How to use**: Run `arp-scan --interface=<interface> <ip-range>` in your terminal.
- **When to use**: This is a very effective tool for discovering all devices on a local subnet, as ARP does not rely on ping and works even when ICMP is blocked.

## **route -n {CMD}**
- **Purpose**: A command-line utility to view and manipulate the IP routing table.
- **How to use**: Run `route -n` in your terminal.
- **When to use**: Use this to understand the network topology, identify the default gateway, and map out the paths that network traffic takes to reach different destinations.

# Scanning & Enumeration Tools

## **nmap {CMD}** — scanning
- **Purpose**: The core network/port scanner. Covered under active recon above; listed here too because it's the backbone of the scanning phase.
- **Key flags**: `-sn` (host discovery), `-p-` (all ports), `-sV` (versions), `-sC` (default scripts), `-A` (aggressive), `-sU` (UDP), `-oN file` (save output), `--script` (NSE scripts).
- **When to use**: First thing against every host — find open ports, then re-scan the open ones with `-sV -sC`.

## **enum4linux {CMD}**
- **Purpose**: All-in-one SMB enumeration for Windows/Samba hosts.
- **How to use**: `enum4linux -a <ip>`.
- **When to use**: Any time 139/445 is open — pulls users, shares, OS, and password policy in one shot.

## **smbclient / smbmap {CMD}**
- **Purpose**: `smbclient` connects to SMB shares like an FTP client; `smbmap` lists shares with read/write permissions.
- **How to use**: `smbclient -L //<ip>/ -N`, `smbclient //<ip>/<share> -N`, `smbmap -H <ip>`.
- **When to use**: After finding SMB — look for readable/writable shares and loot.

## **snmpwalk {CMD}**
- **Purpose**: Walks an SNMP tree to dump system info.
- **How to use**: `snmpwalk -v2c -c public <ip>`.
- **When to use**: When UDP 161 is open — can leak processes, software, and user accounts.

## **nikto {CMD}**
- **Purpose**: Web server vulnerability scanner.
- **How to use**: `nikto -h http://<ip>`.
- **When to use**: Quick first pass on a web server to flag outdated software and common misconfigs.

# Web Tools

## **gobuster / ffuf / dirb {CMD}**
- **Purpose**: Directory and file brute-forcing (content discovery).
- **How to use**: `gobuster dir -u http://<ip> -w <wordlist> -x php,txt`, `ffuf -u http://<ip>/FUZZ -w <wordlist>`.
- **When to use**: On every web server — find hidden pages, admin panels, and uploads.

## **Burp Suite {GUI}**
- **Purpose**: Intercepting proxy to view, modify, and replay HTTP requests.
- **How to use**: Set browser proxy to 127.0.0.1:8080; use Proxy → Intercept and Repeater.
- **When to use**: The central web-testing tool — manual injection testing, tampering parameters, replaying login requests.

## **sqlmap {CMD}**
- **Purpose**: Automated SQL injection detection and exploitation.
- **How to use**: `sqlmap -u "http://<ip>/p.php?id=1" --batch --dbs`; for forms, `sqlmap -r request.txt`.
- **When to use**: Any parameter that reacts to a `'` — dump databases and credentials.

## **wpscan {CMD}**
- **Purpose**: WordPress enumeration and vuln scanner.
- **How to use**: `wpscan --url http://<ip> --enumerate u,p`.
- **When to use**: When WhatWeb/source shows WordPress — enumerate users, plugins, and weak passwords.

# Password Attack Tools

## **hydra {CMD}**
- **Purpose**: Online brute-forcer for network logins.
- **How to use**: `hydra -l <user> -P <wordlist> ssh://<ip>` (also ftp, http-form, rdp).
- **When to use**: Once you have a likely username — target a service's login. Don't blind-brute on the exam; enumerate the username first.

## **john / hashcat {CMD}**
- **Purpose**: Offline hash cracking.
- **How to use**: `john --format=NT hashes.txt`, `hashcat -m 1000 hashes.txt rockyou.txt`.
- **When to use**: After dumping hashes (SAM, `/etc/shadow`, DB dumps) — recover plaintext passwords to reuse.

## **Wordlists {Reference}**
- **rockyou.txt**: `/usr/share/wordlists/rockyou.txt` — the default password list.
- **dirb/common.txt, SecLists**: `/usr/share/wordlists/` — directories and more. SecLists is the broadest collection.

# Exploitation Tools

## **searchsploit {CMD}**
- **Purpose**: Offline search of Exploit-DB.
- **How to use**: `searchsploit <product> <version>`, `searchsploit -m <id>` to copy locally.
- **When to use**: After enumeration — match a service version to a public exploit.

## **Metasploit (msfconsole) {CMD}**
- **Purpose**: Exploitation framework — modules, payloads, session management.
- **How to use**: `search` → `use` → `set` options → `run`. Catch manual payloads with `multi/handler`.
- **When to use**: Fast, reliable exploitation and post-exploitation; the exam allows it. Full workflow in [exploitation](../exploitation/README.md).

## **msfvenom {CMD}**
- **Purpose**: Standalone payload generator.
- **How to use**: `msfvenom -p <payload> LHOST=<ip> LPORT=4444 -f <format> -o out`.
- **When to use**: Build a reverse shell to upload/deliver, then catch it with a listener or `multi/handler`.

## **netcat (nc) {CMD}**
- **Purpose**: Swiss-army networking tool — listeners, banner grabbing, file transfer.
- **How to use**: `nc -lvnp 4444` (listener), `nc <ip> <port>` (connect/banner), `nc <ip> 4444 < file` (transfer).
- **When to use**: Catch reverse shells, grab service banners, move files.

# Post-Exploitation Tools

## **linpeas / winPEAS {Scripts}**
- **Purpose**: Automated privilege-escalation enumeration for Linux / Windows.
- **How to use**: Transfer to target and run; read the highlighted findings.
- **When to use**: Immediately after getting a shell — surfaces SUID, sudo, cron, tokens, and misconfigs. See [privilege-escalation](../privilege-escalation/README.md).

## **GTFOBins / LOLBAS {Websites}**
- **Purpose**: Catalogs of how to abuse legitimate binaries for privesc/bypass (GTFOBins = Linux, LOLBAS = Windows).
- **How to use**: Look up any binary you can run as root / that's SUID.
- **When to use**: When `sudo -l` or an SUID search returns a binary — the escape is almost always listed.

## **mimikatz {CMD}**
- **Purpose**: Dumps Windows credentials and hashes from memory.
- **How to use**: `privilege::debug` then `sekurlsa::logonpasswords`.
- **When to use**: After reaching SYSTEM/admin on Windows — harvest creds to reuse and pivot.

## **chisel / proxychains {CMD}**
- **Purpose**: Tunneling and proxying to pivot into internal networks.
- **How to use**: See [pivoting](../pivoting/README.md).
- **When to use**: When a compromised host reaches a subnet you can't — route your tools through it.
