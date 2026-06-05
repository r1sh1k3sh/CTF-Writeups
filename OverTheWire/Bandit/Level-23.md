# CTF Write-Up: [Level 22 → 23](https://overthewire.org/wargames/bandit/bandit23.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit23</summary>

`0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

</details>

---

## Challenge

Another cron job, but this time the script generates the output filename dynamically. Reading and following the script logic reveals where the password is stored.

---

## Solution

```bash
ls /etc/cron.d/
```

```
behemoth4_cleanup  clean_tmp  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  ...
```

```bash
cat /etc/cron.d/cronjob_bandit23
```

```
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

```bash
cat /usr/bin/cronjob_bandit23.sh
```

```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

The script runs as `bandit23`, so `$myname` is `bandit23`. It then hashes the string `I am user bandit23` with MD5 and uses that as the filename in `/tmp`. Replicate it manually to get the path:

```bash
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
```

```
8ca319486bfbbc3663ea0fbe81326349
```

```bash
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

```
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

---

Log into the next level:

```bash
ssh bandit23@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 22 → 23 — OverTheWire | Completed ✅*
