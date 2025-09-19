# Methodology 
The primary methodology you'll use is a structured approach that breaks down the process into distinct phases.

## 1. Information Gathering (Reconnaissance)
This is the initial phase where you collect as much information about your target as possible without directly interacting with it. The goal is to build a detailed profile of the target's network, services, and potential vulnerabilities.

Passive Reconnaissance: Gathering information from public sources without alerting the target. This includes using tools like whois to get domain registration details, Google Dorks to find leaked files, and services like DNS Dumpster to map a website's infrastructure.

Active Reconnaissance: Directly interacting with the target's network to gather more detailed information. This is where you use tools like nmap for port scanning, ping for host discovery, and dnsenum to discover DNS records.

## 2. Scanning and Vulnerability Analysis
Once you've gathered initial information, you move on to actively scanning the target to identify potential weaknesses.

Vulnerability Scanning: You will use a scanner to automatically find common vulnerabilities in services and applications.

Enumeration: This involves systematically identifying and mapping out a network's users, shares, and services. You'll use tools like enum4linux to gather information from Windows systems or smbclient to find shared files.

## 3. Exploitation
This is the phase where you use the information gathered to gain a foothold on the target system. The eJPT focuses on using known exploits.

Manual Exploitation: Using tools like Metasploit to find and execute exploits for specific vulnerabilities you've discovered.

Web Exploitation: For web-based targets, you'll look for vulnerabilities such as SQL injection or Local File Inclusion (LFI).

## 4. Post-Exploitation and Pivoting
After gaining initial access, you'll try to elevate your privileges and move laterally to other systems on the network.

Privilege Escalation: This involves moving from a low-privilege account to a higher-privilege account (e.g., administrator or root). This can be done by exploiting misconfigured services or using cracked passwords.

Pivoting: Once you have control of one machine, you can use it as a bridge to attack other machines that you couldn't reach before. This is done by creating a new network route or setting up a SOCKS proxy to tunnel your traffic through the compromised host.
