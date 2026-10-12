# CTF Write-Up: [Level 0 → 1](https://overthewire.org/wargames/bandit/bandit1.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit1</summary>

`ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

</details>

---

## Challenge

The password for the next level is stored in a file called `readme` in the home directory.

---

## Solution

```bash
ls -l
```

```
total 4
-rw-r----- 1 bandit1 bandit0 438 Apr 3 15:17 readme
```

```bash
cat readme
```

```
Congratulations on your first steps into the bandit game!!
Please make sure you have read the rules at https://overthewire.org/rules/
The password you are looking for is:
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```

Log into the next level:

```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 0 → 1 — OverTheWire | Completed ✅*
