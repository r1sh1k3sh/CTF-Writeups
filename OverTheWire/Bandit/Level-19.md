# CTF Write-Up: [Level 18 → 19](https://overthewire.org/wargames/bandit/bandit19.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit19</summary>

`cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

</details>

---

## Challenge

The password is in `~/readme`, but `.bashrc` has been modified to immediately log you out on login, making a normal interactive session impossible.

---

## Solution

SSH can execute a command directly without spawning an interactive shell, which sidesteps the `.bashrc` logout entirely:

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat ~/readme"
```

```
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

---

Log into the next level:

```bash
ssh bandit19@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 18 → 19 — OverTheWire | Completed ✅*
