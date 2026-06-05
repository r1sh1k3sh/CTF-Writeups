# CTF Write-Up: [Level 12 → 13](https://overthewire.org/wargames/bandit/bandit13.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit13</summary>

`FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

</details>

---

## Challenge

The password for the next level is stored in `data.txt`, which is a hexdump of a file that has been repeatedly compressed. A temporary working directory under `/tmp` is recommended.

---

## Solution

### Step 1: Inspect the file

```bash
ls -l
```

```
total 4
-rw-r----- 1 bandit13 bandit12 2575 Apr 3 15:17 data.txt
```

```bash
file data.txt
```

```
data.txt: ASCII text
```

```bash
cat data.txt
```

```
00000000: 1f8b 0808 ...
```

`data.txt` is a hexdump, the binary has to be restored before anything can be extracted.

---

### Step 2: Set up a temporary workspace

```bash
mktemp -d
```

```
/tmp/tmp.ZkcO3qovLb
```

```bash
cd /tmp/tmp.ZkcO3qovLb
cp ~/data.txt .
```

---

### Step 3: Reverse the hexdump

```bash
xxd -r data.txt > data1
```

```bash
file data1
```

```
data1: gzip compressed data
```

The extraction chain begins.

---

### Step 4: Work through the compression layers

Each step follows the same pattern, check the file type, rename with the right extension, then extract or decompress.

**Stage 1: gzip**

```bash
mv data1 data1.gz && gunzip data1.gz
file data1
```

```
data1: bzip2 compressed data
```

**Stage 2: bzip2**

```bash
mv data1 data1.bz2 && bzip2 -d data1.bz2
file data1
```

```
data1: gzip compressed data
```

**Stage 3: gzip**

```bash
mv data1 data1.gz && gunzip data1.gz
file data1
```

```
data1: POSIX tar archive (GNU)
```

**Stage 4: tar**

```bash
tar -xvf data1
```

```
data5.bin
```

```bash
file data5.bin
```

```
data5.bin: POSIX tar archive (GNU)
```

**Stage 5: tar**

```bash
tar -xvf data5.bin
```

```
data6.bin
```

```bash
file data6.bin
```

```
data6.bin: bzip2 compressed data
```

**Stage 6: bzip2**

```bash
mv data6.bin data6.bin.bz2 && bzip2 -d data6.bin.bz2
file data6.bin
```

```
data6.bin: POSIX tar archive (GNU)
```

**Stage 7: tar**

```bash
tar -xvf data6.bin
```

```
data8.bin
```

```bash
file data8.bin
```

```
data8.bin: gzip compressed data
```

**Stage 8: gzip**

```bash
mv data8.bin data8.bin.gz && gunzip data8.bin.gz
file data8.bin
```

```
data8.bin: ASCII text
```

```bash
cat data8.bin
```

```
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

---

### Compression chain summary

```
hexdump → gzip → bzip2 → gzip → tar → tar → bzip2 → tar → gzip → ASCII text
```

---

Log into the next level:

```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 12 → 13 — OverTheWire | Completed ✅*
