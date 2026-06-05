# CTF Write-Up: [Level 28 → 29](https://overthewire.org/wargames/bandit/bandit29.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit29</summary>

`4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`

</details>

---

## Challenge

The repository's README has the password redacted. Git history remembers everything though.

---

## Solution

```bash
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
cd repo
cat README.md
```

```
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx
```

```bash
git log
```

```
commit adc7f885a129baee883058b8a870739489f80194
Author: Morla Porla <morla@overthewire.org>
    fix info leak

commit a3437bddd447f2d496731658e86b98cbea9d3c98
Author: Morla Porla <morla@overthewire.org>
    add missing data
...
```

The `fix info leak` commit is the one that redacted the password. Inspect it:

```bash
git show
```

```diff
-  - password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
+  - password: xxxxxxxxxx
```

The original password is right there in the diff.

---

Log into the next level:

```bash
ssh bandit29@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 28 → 29 — OverTheWire | Completed ✅*
