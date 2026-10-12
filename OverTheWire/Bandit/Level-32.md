# CTF Write-Up: [Level 31 → 32](https://overthewire.org/wargames/bandit/bandit32.html)

> **Platform:** OverTheWire | **Type:** Bandit

---

## 🚩 Password for Next Level

<details>
<summary>bandit32</summary>

`3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`

</details>

---

## Challenge

The repository has instructions to push a specific file to the remote. A server-side hook validates the submission and returns the password before rejecting the push.

---

## Solution

```bash
git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
cd repo
cat README.md
```

```
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```

```bash
echo 'May I come in?' > key.txt
git add -f key.txt
```

> `key.txt` is listed in `.gitignore`. `-f` forces git to track it anyway.

```bash
# only needed if git isn't configured on your machine yet
git config user.email "player@bandit.labs"
git config user.name "Bandit Player"
```

```bash
git commit -m "Add key file"
git push origin master
```

```
remote: ### Attempting to validate files... ####
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
To ssh://bandit.labs.overthewire.org:2220/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs
```

The push is rejected but the hook runs first and prints the password.

---

Log into the next level:

```bash
ssh bandit32@bandit.labs.overthewire.org -p 2220
```

---

*Bandit Level 31 → 32 — OverTheWire | Completed ✅*
