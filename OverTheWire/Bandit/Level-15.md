# CTF Write-Up: [Level 14 → 15](https://overthewire.org/wargames/bandit/bandit15.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit15</summary>

`8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

</details>

---

## Challenge

The password for the next level can be retrieved by submitting the current level's password to port `30000` on `localhost`.

---

## Solution

### Step 1: Get the current password

```bash
cat /etc/bandit_pass/bandit14
```

```
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

---

### Step 2: Submit it to port 30000

```bash
nc localhost 30000
```

```
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

Or as a one-liner:

```bash
cat /etc/bandit_pass/bandit14 | nc localhost 30000
```

```
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

---

Log into the next level:

```bash
ssh bandit15@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 14 → 15 — OverTheWire | Completed ✅*
