# CTF Write-Up: [Level 32 → 33](https://overthewire.org/wargames/bandit/bandit33.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit33</summary>

`tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`

</details>

---

## Challenge

The login drops into a custom "UPPERCASE SHELL" that converts all input to uppercase, breaking normal commands. Escape it to read the password.

---

## Solution

```bash
ssh bandit32@bandit.labs.overthewire.org -p 2220
```

```
WELCOME TO THE UPPERCASE SHELL
>>
```

Every command gets uppercased and fails. Use `$0`, it expands to the current shell executable and spawns a real shell, bypassing the restriction entirely:

```bash
$0
```

```
$
```

```bash
whoami
```

```
bandit33
```

The uppercase shell binary is setuid `bandit33`, so the spawned shell inherits those privileges.

```bash
cat /etc/bandit_pass/bandit33
```

```
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```

---

🎉 That's all 33 Bandit levels — congratulations!

*As of June 2026*

---

*Bandit Level 32 → 33 — OverTheWire | Completed ✅*
