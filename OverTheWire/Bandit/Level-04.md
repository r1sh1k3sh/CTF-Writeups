# CTF Write-Up: [Level 3 → 4](https://overthewire.org/wargames/bandit/bandit4.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit4</summary>

`2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

</details>

---

## Challenge

The password is stored in a hidden file inside the `inhere` directory.

---

## Solution

```bash
ls -la inhere/
```

```
total 12
drwxr-xr-x 2 root    root    4096 Apr 3 15:18 .
drwxr-xr-x 3 root    root    4096 Apr 3 15:18 ..
-rw-r----- 1 bandit4 bandit3   33 Apr 3 15:18 ...Hiding-From-You
```

```bash
cat inhere/...Hiding-From-You
```

```
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```

> `-a` : Shows hidden files (anything starting with `.`).

---

Log into the next level:

```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 3 → 4 — OverTheWire | Completed ✅*
