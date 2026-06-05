# CTF Write-Up: [Level 25 → 26](https://overthewire.org/wargames/bandit/bandit26.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit26</summary>

`s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`

</details>

---

## Challenge

An SSH key for `bandit26` is in the home directory, but the login immediately drops, `bandit26`'s shell isn't `/bin/bash`. Find out what it is and escape it.

---

## Solution

### Step 1: Grab the SSH key

```bash
ls -l
```

```
total 4
-r-------- 1 bandit25 bandit25 1679 Apr  3 15:17 bandit26.sshkey
```

Save it locally and lock permissions:

```bash
chmod 600 bandit26.sshkey
```

---

### Step 2: Identify the shell

```bash
cat /etc/passwd | grep bandit26
```

```
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

```bash
cat /usr/bin/showtext
```

```bash
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```

The "shell" just runs `more` on a text file and exits. The escape depends on `more` entering interactive mode, which only happens when the terminal is too small to display the full file.

---

### Step 3: Force `more` into interactive mode

**Shrink your terminal window to just 2–3 lines tall**, then connect:

```bash
ssh bandit26@bandit.labs.overthewire.org -p 2220 -i bandit26.sshkey
```

`more` will pause instead of exiting.

---

### Step 4: Escape to vim, read the password

While `more` is paused, press `v` to open vim. Then:

```vim
:e /etc/bandit_pass/bandit26
```

```
s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
```

---

### Step 5: Get a real shell (needed for next level)

Still inside vim:

```vim
:set shell=/bin/bash
:shell
```

You now have a live bash session as `bandit26`.

---

*Bandit Level 25 → 26 — OverTheWire | Completed ✅*
