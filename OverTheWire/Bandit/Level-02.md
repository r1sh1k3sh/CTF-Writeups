# CTF Write-Up: [Level 1 → 2](https://overthewire.org/wargames/bandit/bandit2.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit2</summary>

`263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

</details>

---

## Challenge

The password is stored in a file called `-` in the home directory. The catch, shells interpret `-` as stdin/stdout, not a filename.

---

## Solution

```bash
ls -l
```

```
total 4
-rw-r----- 1 bandit2 bandit1 33 Apr 3 15:17 -
```

Prefix with `./` to treat it as a path instead of a flag:

```bash
cat ./-
```

```
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```

> `./` : Forces the shell to read `-` as a file in the current directory, not as stdin.  
> Alternatively: `cat -- -` where `--` signals end of options.

---

Log into the next level:

```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 1 → 2 — OverTheWire | Completed ✅*
