# CTF Write-Up: [Level 13 → 14](https://overthewire.org/wargames/bandit/bandit14.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit14</summary>

`MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

</details>

---

## Challenge

The password for the next level is stored in `/etc/bandit_pass/bandit14` and can only be read by user `bandit14`. Instead of a password, this level provides a private SSH key to authenticate as that user.

---

## Solution

### Step 1: Inspect the home directory

```bash
ls -l
```

```
total 8
-rw-r----- 1 bandit14 bandit13  467 Apr 3 15:17 HINT
-rw-r----- 1 bandit14 bandit13 1679 Apr 3 15:17 sshkey.private
```

```bash
file sshkey.private
```

```
sshkey.private: PEM RSA private key
```

---

### Step 2: Use the key to log in as bandit14

**Copy the key locally first with `scp`, then connect**

```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private .
chmod 600 sshkey.private
ssh bandit14@bandit.labs.overthewire.org -p 2220 -i sshkey.private
```

> SSH refuses to use a private key if its permissions are too open. `chmod 600` restricts access to the owner only, which is what SSH requires.

---

### Step 3: Read the password

```bash
cat /etc/bandit_pass/bandit14
```

```
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

---

*Bandit Level 13 → 14 — OverTheWire | Completed ✅*
