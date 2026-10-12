# CTF Write-Up: [Level 8 → 9](https://overthewire.org/wargames/bandit/bandit9.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit9</summary>

`4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

</details>

---

## Challenge

The password is the only line in `data.txt` that appears exactly once. Everything else is a duplicate.

---

## Solution

```bash
sort data.txt | uniq -c | sort -n
```

```
1 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
10 0gGmyPWCYc25YtbwSP3kcNZdu9TFIB6s
10 0GWwWUcG1DRo7zNiaZXfsSEGaHie3ij0
...
```

The line with count `1` is the unique one.

> `sort` : Groups identical lines together so `uniq` can detect them — it only works on adjacent duplicates.  
> `uniq -c` : Prefixes each line with its occurrence count.  
> `sort -n` : Sorts numerically, pushing the unique line to the top.

---

Log into the next level:

```bash
ssh bandit9@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 8 → 9 — OverTheWire | Completed ✅*
