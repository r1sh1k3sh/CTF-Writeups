# CTF Write-Up: [Level 26 → 27](https://overthewire.org/wargames/bandit/bandit27.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit27</summary>

`upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`

</details>

---

## Challenge

From the shell obtained in the previous level, grab the password for `bandit27`.

---

## Solution

```bash
ls -l
```

```
total 20
-rwsr-x--- 1 bandit27 bandit26 14888 Apr  3 15:17 bandit27-do
-rw-r----- 1 bandit26 bandit26   258 Apr  3 15:17 text.txt
```

Another setuid binary, this one runs as `bandit27`. Use it to read the password file directly:

```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```

```
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
```

---

Log into the next level:

```bash
ssh bandit27@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 26 → 27 — OverTheWire | Completed ✅*
