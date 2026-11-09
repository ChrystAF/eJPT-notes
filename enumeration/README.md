# Enumeration

The single highest-value phase for the eJPT. Scanning tells you a port is open; enumeration tells you what's behind it and how to get in. **Enumerate every open service before you try to exploit anything.** Most footholds are credentials, shares, or files you find here — not memory-corruption exploits.

Workflow per host:

1. Full scan to find open ports (`nmap -p- <ip>`), then `-sV -sC` on the open ones.
2. For each service, run the matching playbook below.
3. Record versions, usernames, shares, files, and any creds in your [box writeup](../methodology/Box%20Writeup%20Template.md).

---

## Port → service quick map

| Port | Service | First move |
|---|---|---|
| 21 | FTP | Try anonymous login; check version for known exploits |
| 22 | SSH | Grab banner/version; brute-force only with a found username |
| 23 | Telnet | Banner; try default creds |
| 25 | SMTP | `VRFY`/`EXPN` user enumeration |
| 53 | DNS | Zone transfer attempt |
| 80/443 | HTTP(S) | Full web enum — see [web](../web/README.md) |
| 110/143 | POP3/IMAP | Banner; creds reuse |
| 139/445 | SMB | Shares, users, null session, version |
| 161 | SNMP | `snmpwalk` with `public` community |
| 1433 | MSSQL | Default creds, `mssqlclient` |
| 3306 | MySQL | Default/weak creds |
| 3389 | RDP | Creds reuse; `xfreerdp` |
| 5985 | WinRM | `evil-winrm` with found creds |

---

## SMB (139 / 445)

The classic eJPT win. Enumerate shares, users, and version.

```bash
# Version + vuln scripts
nmap -p139,445 --script "smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-*" <ip>

# List shares (null / anonymous session)
smbclient -L //<ip>/ -N
smbmap -H <ip>                      # shows read/write perms per share

# Connect to a share
smbclient //<ip>/<share> -N

# Full enum (users, shares, policy, OS)
enum4linux -a <ip>
```

- A **writable share** can be a foothold (drop a payload, trigger it).
- Note the OS/SMB version — old Windows SMB (e.g. the EternalBlue family) is a common exploit path.
- Reuse any usernames found here against SSH/RDP/WinRM.

## FTP (21)

```bash
nmap -p21 --script "ftp-anon,ftp-syst" <ip>
ftp <ip>            # try user: anonymous, pass: anything
```

- **Anonymous login + writable dir** on a box also running a web server = upload a web shell.
- Check the FTP server version with `searchsploit`.

## SSH (22)

```bash
nc <ip> 22          # banner / version
# Brute-force ONLY once you have a likely username:
hydra -l <user> -P <wordlist> ssh://<ip>
```

- Don't blind-brute SSH on the exam — find the username first (SMB, web, SNMP), then target it.
- Found a private key? `chmod 600 key && ssh -i key user@ip`.

## HTTP / HTTPS (80 / 443)

Full treatment in [web](../web/README.md). Minimum first pass:

```bash
whatweb http://<ip>                 # tech stack
gobuster dir -u http://<ip> -w /usr/share/wordlists/dirb/common.txt
# check /robots.txt, page source, and any login form for default creds
```

## DNS (53)

```bash
# Zone transfer — can dump every host record for a domain
dig axfr @<ip> <domain>
dnsenum <domain>
```

## SNMP (161/UDP)

Often overlooked, very chatty when open with the default `public` community.

```bash
nmap -sU -p161 --script snmp-* <ip>
snmpwalk -v2c -c public <ip>
```

- Can leak running processes, open ports, installed software, and **user accounts** — great for building a brute-force list.

## SMTP (25)

```bash
nmap -p25 --script smtp-enum-users <ip>
# Manual user enumeration:
#   VRFY <user>   /   EXPN <list>   over a telnet/nc session to port 25
```

Valid usernames feed credential attacks elsewhere.

---

## Enumeration discipline

- **One service at a time, write down everything.** Version strings and usernames are the currency of the next phase.
- **Reuse credentials everywhere.** A password found on one service very often works on another (and on another host).
- **If you're stuck, you under-enumerated.** Re-read your scan output, run the `-p-` full scan you skipped, and check the service versions against `searchsploit`.
