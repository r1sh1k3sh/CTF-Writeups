# CTF Write-Up: [Level 9 → 10](https://overthewire.org/wargames/bandit/bandit10.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit10</summary>

`FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

</details>

---

## Challenge

The password is in `data.txt`, a binary file, buried among non-readable content. It's one of the few human-readable strings, preceded by several `=` characters.

---

## Solution

```bash
file data.txt
```

```
data.txt: data
```

Binary file, `cat` won't help. Use `strings` to pull readable text out, then `grep` for the `=` pattern:

```bash
strings data.txt | grep "="
```

```
========== the
========== password
========== is
========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

> `strings` : Extracts human-readable text from binary files.

---

Log into the next level:

```bash
ssh bandit10@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 9 → 10 — OverTheWire | Completed ✅*
