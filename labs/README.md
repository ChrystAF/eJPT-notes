# Post-Exploitation
Gaining accecss with a goal to get into a shell

## Privilege Escalation

* Windows Kernel Exploits: These are used to exploit vulnerabilities in the Windows Kernel, specifically for Windows NT.

## Missing Patch Enumeration:

* Tool: Windows-Exploit-Suggester.

* Purpose: This tool helps identify missing patches on a system.

## Escalation Enumeration:

* Tools: local_exploit_suggester (from Metasploit Framework).


* UACMe or Akagi.exe (Priv Esc Scripts).


* Purpose: These tools enumerate potential privilege escalation vectors.

## User Account Control (UAC) Bypass:
* Tools: Akagi64.exe and Metasploit's bypassuac_injection.
* Purpose: These are used to bypass User Account Control.
* Usage: Akagi64.exe is used with a key and the full path to an msfvenom meterpreter payload. The bypassuac_injection module is a Metasploit module for this purpose.

## Access Token Impersonation:
* Tool: Incognito (a Metasploit module).
* Usage: Use list tokens to view tokens and impersonate_token "{TOKEN_NAME}" to impersonate a specific token.

## Unattended Windows Setup:
* Purpose: To check for potential vulnerabilities from a mass Windows deployment.
* Method: Check the C:\Windows\Panther\Unattend.xml file.

## Escalation Checkers:
* Tools: Invoke-PrivescAudit and PrivescCheck.
* Purpose: These find common Windows privilege escalation flaws.
* Credential Harvesting

## Metasploit Meterpreter Kiwi: This is a tool within Metasploit for credential harvesting.
* Purpose: Dumps user hashes using the hashdump command.
* Mimikatz:
* Location: /usr/share/windows-resources/mimikatz/x64/mimikatz.exe.
Commands:
* privilege::debug: Checks if the shell has the necessary privileges after spawning.
* lsadump::sam: Dumps the SAM (Security Account Manager).
* sekurlsa::logonpasswords: Dumps logon passwords.
* Internal Enumeration
## Metasploit Modules:
*enum_logged_on_users: Lists users currently logged on to the system.
*enum_applications: Enumerates applications.
*checkvm: Checks if the system is a virtual machine.
* enum_av_excluded: Enumerates anti-virus exclusions.
* enum_computers: Enumerates computers.
* enum_patches: Enumerates installed patches.
* enum_shares: Enumerates shared folders.

## JAWS (Just Another Windows Enumeration Script):
* Tool: JAWS.
* Usage: powershell.exe -Execution Policy Bypass -File.\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt.
* Purpose: This PowerShell script performs enumeration on a Windows system.
* Persistence

## Metasploit Module: persistence_service.
* RDP Enabler:
* Tool: enable_rdp.
* Command: run getgui -e -u {UserName} -p {Password}.

## Hash Cracking
### John the Ripper:
* Usage: john --format NT {HashFile}.
* Purpose: Cracks NTLM hashes.

### Hashcat:
* Usage: hashcat -a3 -m 1000 {HashFile}.
* Purpose: Cracks NTLM hashes.
