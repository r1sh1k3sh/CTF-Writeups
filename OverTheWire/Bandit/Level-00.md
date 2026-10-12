# CTF Write-Up: [Level 0](https://overthewire.org/wargames/bandit/bandit0.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## Challenge

Log into the game server via SSH for the first time.

| | |
|---|---|
| **Host** | `bandit.labs.overthewire.org` |
| **Port** | `2220` |
| **Username** | `bandit0` |
| **Password** | `bandit0` |

---

## Solution

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

First connection will ask you to verify the host fingerprint. Type `yes`. Password is `bandit0`.

> `-p 2220` : Specifies port `2220` instead of the default SSH port `22`.

---

*Bandit Level 0 — OverTheWire | Completed ✅*
