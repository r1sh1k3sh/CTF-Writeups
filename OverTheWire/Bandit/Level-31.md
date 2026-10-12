# CTF Write-Up: [Level 30 → 31](https://overthewire.org/wargames/bandit/bandit31.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit31</summary>

`fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`

</details>

---

## Challenge

The repository has one commit, one branch, and a nearly empty README. The password is hidden in a git tag.

---

## Solution

```bash
git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo
cd repo
cat README.md
```

```
just an epmty file... muahaha
```

```bash
git log
```

```
commit bd393e0e59a075f92fd84edc0ad8d13f64572de2
Author: Ben Dover <noone@overthewire.org>
Date:   Fri Apr 3 15:17:58 2026 +0000

    initial commit of README.md
```

```bash
git branch -a
```

```
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

Nothing in the files, history, or branches. Check tags:

```bash
git tag
```

```
secret
```

```bash
git show secret
```

```
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

---

Log into the next level:

```bash
ssh bandit31@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 30 → 31 — OverTheWire | Completed ✅*
