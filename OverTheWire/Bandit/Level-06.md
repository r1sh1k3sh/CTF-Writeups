# CTF Write-Up: [Level 5 → 6](https://overthewire.org/wargames/bandit/bandit6.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit6</summary>

`HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

</details>

---

## Challenge

The password is somewhere under `inhere` across 20 subdirectories. The file is human-readable, exactly 1033 bytes, and not executable.

---

## Solution

```bash
find . -size 1033c ! -perm /u+x
```

```
./maybehere07/.file2
```

```bash
cat ./maybehere07/.file2
```

```
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

> `-size 1033c` : Match files exactly 1033 bytes (`c` = bytes).  
> `! -perm /u+x` : Exclude files with execute permission set for the owner.

---

Log into the next level:

```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 5 → 6 — OverTheWire | Completed ✅*
