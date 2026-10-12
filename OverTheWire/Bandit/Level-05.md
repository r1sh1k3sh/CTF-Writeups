# CTF Write-Up: [Level 4 → 5](https://overthewire.org/wargames/bandit/bandit5.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit5</summary>

`4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

</details>

---

## Challenge

The password is in the only human-readable file inside `inhere`. Ten files, all named `-fileXX`, only one is ASCII text.

---

## Solution

```bash
cd inhere/
file -- $(ls)
```

```
-file00: data
-file01: data
-file02: data
-file03: DOS executable (COM), start instruction 0x8c887e10 c3ee96c9
-file04: data
-file05: data
-file06: data
-file07: ASCII text
-file08: data
-file09: data
```

`-file07` is the only human-readable file. Read it:

```bash
cat -- -file07
```

```
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```

> `file` : Identifies file type, useful for spotting ASCII text among binary data.  
> `--` : Required because filenames start with `-`, which the shell would otherwise treat as flags.

---

Log into the next level:

```bash
ssh bandit5@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 4 → 5 — OverTheWire | Completed ✅*
