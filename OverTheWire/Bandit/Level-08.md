# CTF Write-Up: [Level 7 → 8](https://overthewire.org/wargames/bandit/bandit8.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit8</summary>

`dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

</details>

---

## Challenge

The password is in `data.txt`, on the line next to the word `millionth`. The file is ~4MB with thousands of word–password pairs.

---

## Solution

```bash
grep "millionth" data.txt
```

```
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

---

Log into the next level:

```bash
ssh bandit8@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 7 → 8 — OverTheWire | Completed ✅*
