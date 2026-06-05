# CTF Write-Up: [Level 6 → 7](https://overthewire.org/wargames/bandit/bandit7.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit7</summary>

`morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

</details>

---

## Challenge

The password is somewhere on the entire server, owned by user `bandit7`, group `bandit6`, and exactly 33 bytes.

---

## Solution

```bash
find / -size 33c -user bandit7 -group bandit6 2>/dev/null
```

```
/var/lib/dpkg/info/bandit7.password
```

```bash
cat /var/lib/dpkg/info/bandit7.password
```

```
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

> `-user` / `-group` : Filter by owner and group.  
> `2>/dev/null` : Suppresses the flood of "Permission denied" errors when searching from `/`.

---

Log into the next level:

```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 6 → 7 — OverTheWire | Completed ✅*
