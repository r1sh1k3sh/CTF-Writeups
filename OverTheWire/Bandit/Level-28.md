# CTF Write-Up: [Level 27 → 28](https://overthewire.org/wargames/bandit/bandit28.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit28</summary>

`Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`

</details>

---

## Challenge

A git repository is hosted on the OverTheWire server. Clone it and find the password inside.

---

## Solution

```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
```

Enter the Level 27 password when prompted. Then:

```bash
cd repo
cat README
```

```
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

---

Log into the next level:

```bash
ssh bandit28@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 27 → 28 — OverTheWire | Completed ✅*
