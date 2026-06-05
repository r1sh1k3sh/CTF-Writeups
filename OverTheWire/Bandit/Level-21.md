# CTF Write-Up: [Level 20 → 21](https://overthewire.org/wargames/bandit/bandit21.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit21</summary>

`EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

</details>

---

## Challenge

A setuid binary `suconnect` connects to a port you specify, reads the Level 20 password, and if correct, sends back the Level 21 password. The task is to set up a listener that supplies the password when `suconnect` connects.

---

## Solution

```bash
ls -l
```

```
total 16
-rwsr-x--- 1 bandit21 bandit20 15612 Apr  3 15:17 suconnect
```

```bash
./suconnect
```

```
Usage: ./suconnect <portnumber>
This program will connect to the given port on localhost using TCP. If it receives the correct password from the other side, the next password is transmitted back.
```

Spin up a `nc` listener in the background that serves the current password, then connect `suconnect` to it:

```bash
echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -l -p 4444 & sleep 1 && ./suconnect 4444
```

```
[1] 19
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```

---

Log into the next level:

```bash
ssh bandit21@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 20 → 21 — OverTheWire | Completed ✅*
