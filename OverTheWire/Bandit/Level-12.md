# CTF Write-Up: [Level 11 → 12](https://overthewire.org/wargames/bandit/bandit12.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit12</summary>

`7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

</details>

---

## Challenge

The password is in `data.txt`, ROT13 encoded, every letter shifted 13 positions.

---

## Solution

```bash
cat data.txt
```

```
Gur cnffjbeq vf 7k16JArUVv5LxVuJfsSVdbbtaHGlw9D4
```

```bash
tr 'a-zA-Z' 'n-za-mN-ZA-M' < data.txt
```

```
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

> ROT13 is symmetric, encoding and decoding use the exact same transformation.  
> `tr 'a-zA-Z' 'n-za-mN-ZA-M'` : Maps each letter to its ROT13 equivalent.

---

Log into the next level:

```bash
ssh bandit12@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 11 → 12 — OverTheWire | Completed ✅*
