# 🚩 OverTheWire Bandit – Level Solutions (0 → 14)

This document contains my solutions and the concepts I learned while completing the **Bandit Wargame** from [OverTheWire](https://overthewire.org/wargames/bandit/).  
The goal of Bandit is to teach **Linux fundamentals, file searching, encoding, and basic security concepts**.

---

# Levels 0 → 10: Linux Basics & File Searching

## Level 0 → 1
Used basic navigation commands to locate and read the password file.

**Commands**
```bash
ls
cd
cat
````

**Password**

```
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```

---

## Level 1 → 2

The file name was `-`, which the shell interprets as a flag.
Used `./-` to treat it as a filename.

**Command**

```bash
cat ./-
```

**Password**

```
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```

---

## Level 2 → 3

The file contained spaces in its name.
Used escape characters (`\`) to access it.

**Command**

```bash
cat spaces\ in\ this\ filename
```

**Password**

```
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

---

## Level 3 → 4

Searched for hidden files (files starting with `.`).

**Command**

```bash
ls -la
```

**Password**

```
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```

---

## Level 4 → 5

Identified the readable file among many files using the `file` command.

**Command**

```bash
file ./*
```

---

## Level 5 → 6

Used `find` with specific filters to locate the correct file.

**Command**

```bash
find . -type f ! -executable -readable -size 1033c
```

**Password**

```
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

---

## Level 6 → 7

Performed an advanced search by **owner, group, and file size**.

**Command**

```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

**Password**

```
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

---

## Level 7 → 8

Located the password stored next to the word **millionth** in a file.

**Command**

```bash
grep millionth data.txt
```

---

## Level 8 → 9

Filtered unique lines from a file.
`uniq` only works on **sorted data**, so sorting was required first.

**Command**

```bash
sort data.txt | uniq -u
```

**Password**

```
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

---

## Level 9 → 10

Extracted readable strings from a binary file and searched for the password.

**Command**

```bash
strings data.txt | grep =
```

**Password**

```
FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

---

# Levels 10 → 15: Encoding, Compression & Data Analysis

## Level 10 → 11

Decoded a **Base64 encoded** file.

**Command**

```bash
cat data.txt | base64 -d
```

---

## Level 11 → 12

Decoded a **ROT13 encrypted string**.

**Command**

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

---

## Level 12 → 13

Handled a file that had been **compressed multiple times** in different formats.

Steps included:

* Reversing a hex dump
* Identifying file types
* Extracting archives

**Tools Used**

```
xxd
gzip
bzip2
tar
file
```

**Example Command**

```bash
xxd -r data.txt > file
```

**Password**

```
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

---

## Level 13 → 14

Transferred a private SSH key from the remote server to the local machine and used it to log in.

**Command**

```bash
scp bandit13@bandit.labs.overthewire.org:sshkey.private .
```

**Important Note**

SSH keys require strict permissions:

```bash
chmod 600 sshkey.private
```

---

# 📚 Technical Concepts Learned

## Linux Permissions

File permissions follow **octal notation**:

```
4 = Read (r)
2 = Write (w)
1 = Execute (x)
```

Example:

```bash
chmod 700 file
```

This gives:

* **Owner:** Full permissions (rwx)
* **Group:** None
* **Others:** None

---

## Networking Fundamentals

### Common Ports

| Service | Port |
| ------- | ---- |
| HTTP    | 80   |
| HTTPS   | 443  |
| FTP     | 21   |
| SMTP    | 25   |

### Netstat

View active connections and listening services:

```bash
netstat -antp
```

---

## Data Manipulation

### Grep

Recursive searching:

```bash
grep -r "text" .
```

### Redirection

Suppress errors during large searches:

```bash
2>/dev/null
```

### Piping

Combining commands to process data efficiently:

```bash
sort data.txt | uniq
```

---

# ✅ Summary

Through these levels I practiced:

* Linux command-line navigation
* File discovery techniques
* Data filtering and manipulation
* Encoding and cryptography basics
* Compression formats
* SSH authentication

Bandit is an excellent introduction to **Linux security and CTF fundamentals**.


