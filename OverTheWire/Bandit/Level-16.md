# CTF Write-Up: [Level 15 → 16](https://overthewire.org/wargames/bandit/bandit16.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit16</summary>

`kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

</details>

---

## Challenge

Same idea as the previous level, but port `30001` on `localhost` requires SSL/TLS encryption, a plain `nc` connection won't work.

---

## Solution

### Step 1: Try plain nc (it fails)

```bash
echo "8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo" | nc localhost 30001
```

```
(no response)
```

The server expects an encrypted connection, so `nc` gets nothing back.

---

### Step 2: Connect with SSL using ncat

```bash
echo "8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo" | ncat --ssl localhost 30001
```

```
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

---

Log into the next level:

```bash
ssh bandit16@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 15 → 16 — OverTheWire | Completed ✅*
