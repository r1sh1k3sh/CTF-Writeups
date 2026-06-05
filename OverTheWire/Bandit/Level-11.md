# CTF Write-Up: [Level 10 → 11](https://overthewire.org/wargames/bandit/bandit11.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit11</summary>

`dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

</details>

---

## Challenge

The password is in `data.txt`, Base64 encoded.

---

## Solution

```bash
cat data.txt
```

```
VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==
```

```bash
base64 -d data.txt
```

```
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

> Base64 is encoding, not encryption. `base64 -d` is all it takes to reverse it.

---

Log into the next level:

```bash
ssh bandit11@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 10 → 11 — OverTheWire | Completed ✅*
