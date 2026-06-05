# CTF Write-Up: [Level 16 → 17](https://overthewire.org/wargames/bandit/bandit17.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## Challenge

The next credentials are retrieved by submitting the current password to a port on `localhost` in the range `31000–32000`. The right port uses SSL/TLS and returns an RSA private key instead of a plain password.

---

## Solution

### Step 1: Scan the port range

```bash
nmap -sV -p 31000-32000 localhost
```

```
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
```

Two ports use SSL: `31518` and `31790`. The rest are plain echo services (decoys).

---

### Step 2: Test the SSL ports

```bash
for port in 31518 31790; do
    echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | ncat --ssl localhost $port
done
```

```
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
Correct!
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
```

Port `31518` echoes the input back. Port `31790` returns `Correct!` followed by an RSA private key, that's the credential for the next level.

---

### Step 3: Save the key and log in

Copy the full key output, save it locally, then lock down the permissions:

```bash
nano bandit17.rsa   # paste the key, save
chmod 600 bandit17.rsa
```

```bash
ssh bandit17@bandit.labs.overthewire.org -p 2220 -i bandit17.rsa
```

```
bandit17@bandit:~$
```

> SSH will refuse to use a private key if its permissions are too open, `chmod 600` is required.

---

*Bandit Level 16 → 17 — OverTheWire | Completed ✅*
