# CTF Write-Up: [Level 29 → 30](https://overthewire.org/wargames/bandit/bandit30.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit30</summary>

`qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`

</details>

---

## Challenge

The password isn't in the commit history this time, it's on a different branch.

---

## Solution

```bash
git clone ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo
cd repo
cat README.md
```

```
- password: <no passwords in production!>
```

```bash
git log
```

```
commit 921cad124cfe5b4ba9f648de1894f75656ff0ff4
Author: Ben Dover <noone@overthewire.org>
Date:   Thu Apr 3 15:17:40 2025 +0000

    fix username

commit edd6383ec473cb45c7f620ad3d762f31ebbc41ea
Author: Ben Dover <noone@overthewire.org>
Date:   Thu Apr 3 15:17:40 2025 +0000

    initial commit of README.md
```

Nothing in history either. Check all branches:

```bash
git branch -a
```

```
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

Inspect the `dev` branch:

```bash
git show origin/dev
```

```diff
+- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

---

Log into the next level:

```bash
ssh bandit30@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 29 → 30 — OverTheWire | Completed ✅*
