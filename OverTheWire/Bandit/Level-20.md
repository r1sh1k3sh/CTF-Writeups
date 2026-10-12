# CTF Write-Up: [Level 19 → 20](https://overthewire.org/wargames/bandit/bandit20.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit20</summary>

`0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

</details>

---

## Challenge

A setuid binary in the home directory runs commands as `bandit20`. Use it to read the password file that only `bandit20` can access.

---

## Solution

```bash
ls -l
```

```
total 16
-rwsr-x--- 1 bandit20 bandit19 14888 Apr 3 15:17 bandit20-do
```

The `s` in the permissions indicates setuid, the binary runs as its owner (`bandit20`) regardless of who executes it.

```bash
./bandit20-do
```

```
Run a command as another user.
  Example: ./bandit20-do whoami
```

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

```
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

---

Log into the next level:

```bash
ssh bandit20@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 19 → 20 — OverTheWire | Completed ✅*
