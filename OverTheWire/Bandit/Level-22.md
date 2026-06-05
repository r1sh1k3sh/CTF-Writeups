# CTF Write-Up: [Level 21 → 22](https://overthewire.org/wargames/bandit/bandit22.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit22</summary>

`tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

</details>

---

## Challenge

A cron job is running automatically at regular intervals. Inspecting it reveals where the password is being written.

---

## Solution

```bash
ls /etc/cron.d/
```

```
behemoth4_cleanup  clean_tmp  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  ...
```

```bash
cat /etc/cron.d/cronjob_bandit22
```

```
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

`* * * * *` means "every minute", the five fields represent minute, hour, day of month, month, and day of week. An asterisk in each means "any/all".

```bash
cat /usr/bin/cronjob_bandit22.sh
```

```bash
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

Every minute the script writes the password to a world-readable file in `/tmp`. Just read it:

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

```
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

---

Log into the next level:

```bash
ssh bandit22@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 21 → 22 — OverTheWire | Completed ✅*
