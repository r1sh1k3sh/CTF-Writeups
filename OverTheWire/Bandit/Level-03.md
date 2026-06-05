# CTF Write-Up: [Level 2 → 3](https://overthewire.org/wargames/bandit/bandit3.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit3</summary>

`MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

</details>

---

## Challenge

The password is stored in a file called `--spaces in this filename--`. Two problems, it starts with `--` and has spaces in the name.

---

## Solution

```bash
ls -l
```

```
total 4
-rw-r----- 1 bandit3 bandit2 33 Apr 3 15:17 --spaces in this filename--
```

```bash
cat -- '--spaces in this filename--'
```

```
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

> `--` : Signals end of options so the shell doesn't interpret the leading `--` as a flag.  
> Quotes : Treats the spaces as part of the filename, not argument separators.

Alternatively, escape the spaces:

```bash
cat -- --spaces\ in\ this\ filename--
```

---

Log into the next level:

```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 2 → 3 — OverTheWire | Completed ✅*
