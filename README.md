# 🕹️ OverTheWire — Bandit Level 6 → Level 7

In this short lab I completed **Bandit Level 6 → Level 7**.
The goal was to **find a file anywhere on the server** that matches three properties:

* **Owned by user `bandit7`**
* **Owned by group `bandit6`**
* **Exactly `33` bytes in size**

---

## 📋 Lab Overview

**🎯 Goal**

* Search the whole filesystem to locate a file that meets the three properties above and read the password it contains.

**💡 Why this matters**

* Teaches how to use `find` effectively to search the entire system (`/`) with multiple filters.
* Demonstrates handling noisy output (permission errors) with redirection.
* Reinforces reading files once found using `cat`.

---

## 🧠 Learning Outcomes

| Skill               | Description                                                |
| ------------------- | ---------------------------------------------------------- |
| `find /`            | Recursively search from root when file location is unknown |
| `-user` / `-group`  | Filter by owner and group                                  |
| `-size 33c`         | Match files by exact byte size                             |
| `2>/dev/null`       | Hide permission-denied noise for cleaner output            |
| `cat /path/to/file` | Read the file containing the password                      |

---

## 🛠 Step-by-Step Journey

### Step 1 — SSH into Bandit Level 6

```bash
ssh -p 2220 bandit6@bandit.labs.overthewire.org
```

Use the password obtained from the previous level. After connecting, confirm your folder:

```bash
pwd
ls
```

Output (example):

```
/home/bandit6
# nothing obvious in the current dir
```

---

### Step 2 — Search the Entire Filesystem

Because the file could be anywhere, run `find` from root `/` and combine the three filters. Silence permission errors so output is easy to read:

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

**What this does**

* `find /` — start at root and search everything
* `-type f` — only files (no directories / symlinks)
* `-user bandit7` — owned by user `bandit7`
* `-group bandit6` — owned by group `bandit6`
* `-size 33c` — exactly 33 bytes (the `c` means bytes)
* `2>/dev/null` — hide permission denied errors

---

### Step 3 — Read the Password

The `find` output will be the full path to the matching file. In my run the returned path looked like:

```
/var/lib/dpkg/info/bandit7.password
```

Read it with `cat`:

```bash
cat /var/lib/dpkg/info/bandit7.password
```

Output:

```
<the 33-byte password string>
```

✅ Copy the password into your notes — this is the credential for Bandit Level 7.

---

### Step 4 — Exit the Session

```bash
exit
```

Connection closed — level complete.

---

## ✅ Key Commands Summary

|                             Task | Command                                                             |
| -------------------------------: | ------------------------------------------------------------------- |
|                  Connect via SSH | `ssh -p 2220 bandit6@bandit.labs.overthewire.org`                   |
| Search whole filesystem for file | `find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null` |
|           Read the password file | `cat /path/to/found/file`                                           |
|                     Exit session | `exit`                                                              |

---

## 💡 Notes / Tips

* Searching from `/` is necessary when the file can be anywhere; expect many "Permission denied" messages — use `2>/dev/null` to hide them.
* `-size 33c` is exact: `c` means bytes (not blocks).
* Combine `-user` and `-group` in the same `find` invocation to avoid post-filtering or looping.
* If `find` returns multiple paths, inspect each with `file` or `ls -l` to ensure it truly matches the criteria before `cat`ing it.
* Always be cautious when reading system files — the Bandit game uses safe test files but on real systems check permissions and context.

---

## 🔗 References

* OverTheWire — Bandit Wargame.
* `man find` — `find` command manual (for `-user`, `-group`, `-size`, redirection examples).
