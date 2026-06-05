# CTF Write-Up: [Level 17 → 18](https://overthewire.org/wargames/bandit/bandit18.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit18</summary>

`x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

</details>

---

## Challenge

Two files exist in the home directory, `passwords.old` and `passwords.new`. The password is the one line that differs between them.

---

## Solution

```bash
ls -l
```

```
total 8
-rw-r----- 1 bandit18 bandit17 3300 Apr  3 15:17 passwords.new
-rw-r----- 1 bandit18 bandit17 3300 Apr  3 15:17 passwords.old
```

```bash
diff passwords.old passwords.new
```

```
42c42
< 390zFj2NETFVZkqYw8UEFdN6h40oGVtT
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

The line prefixed with `>` is from `passwords.new`, that's the password.

---

Log into the next level:

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220
```

```
Byebye !
Connection to bandit.labs.overthewire.org closed.
```

> The `Byebye!` disconnect is expected and is the challenge for Level 18 → 19.

---

*Bandit Level 17 → 18 — OverTheWire | Completed ✅*
