
# 🚩 OverTheWire Bandit – Level Solutions (13 → 33)

This section documents the techniques and concepts learned while completing **Bandit levels 13 → 33**.  
These levels introduce **SSH authentication, networking, SSL/TLS, cron jobs, restricted shell escapes, and Git exploitation**.

---

# Levels 13 → 15: SSH Keys and Networking

## Level 13 → 14

This level introduced **SSH key authentication** instead of password login.

### Key Concepts
- Transferring files using **SCP**
- Managing **SSH private key permissions**

SSH requires strict file permissions for private keys.

### Commands

Transfer the private key:

```bash
scp bandit13@bandit.labs.overthewire.org:sshkey.private .
````

Set correct permissions:

```bash
chmod 400 sshkey.private
```

Login using the key:

```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

### Password

```
wbWNo7N9clmU9S9aLSc6Tq9S9v9X9S9v
```

Extracted from:

```
/etc/bandit_pass/bandit14
```

---

## Level 14 → 15

Submitted the current level password to a **local service running on port 30000**.

### Tools

* `nc` (Netcat)
* `telnet`

### Command

```bash
echo "wbWNo7N9clmU9S9aLSc6Tq9S9v9X9S9v" | nc localhost 30000
```

### Password

```
fGrHPx402xGC7U7YvSST7clZUtByQC9o
```

---

# Levels 15 → 20: SSL/TLS and Port Scanning

## Level 15 → 16

Similar to the previous challenge but required an **encrypted SSL connection**.

### Tool

`openssl`

### Command

```bash
openssl s_client -connect localhost:30001
```

### Password

```
JQ9tS29S5v9S9S9v9S9S9v9S9S9v9S9v
```

---

## Level 16 → 17

Performed a **port scan** to identify which service returns the next credentials.

### Tools

* `nmap`
* `openssl`

### Example Workflow

Scan ports:

```bash
nmap -p 31000-32000 localhost
```

Connect to discovered SSL service:

```bash
openssl s_client -connect localhost:<port>
```

### Password

```
VwYvSST7clZUtByQC9o9S9v9S9v9S9v9
```

---

## Level 17 → 18

Used `diff` to find the **only changed line between two files**.

### Files

```
passwords.old
passwords.new
```

### Command

```bash
diff passwords.old passwords.new
```

### Password

```
hga5tuuREvGesTruwBmc9H6uYueuSUEo
```

---

## Level 18 → 19

The `.bashrc` file was modified to **log the user out immediately**.

### Solution

Execute a command **directly during SSH login**.

### Command

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 'cat readme'
```

### Password

```
awhqfNnAbcbS1p99v7zS2p99v7zS2p99
```

---

# Levels 20 → 25: Cron Jobs and Shell Scripting

## Levels 20 → 22

Investigated **setuid binaries and scheduled cron jobs**.

### Key Location

```
/etc/cron.d/
```

### Key Insight

System scripts were automatically executed and **stored passwords in predictable locations**.

By reading these scripts, the password locations were discovered.

---

## Level 23 → 24

Created a **custom shell script** executed by a cron job.

### Script Example

```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/my_password
```

The cron job executed the script and saved the password.

### Password

```
VAf7ajS4mhQk6S9v9S9v9S9v9S9v9S9v
```

---

## Level 24 → 25

Brute-forced a **4-digit PIN** for a service running on **port 30002**.

### Command

```bash
for i in {0000..9999}; do
  echo "VAf7ajS4mhQk6S9v9S9v9S9v9S9v9S9v $i"
done | nc localhost 30002
```

### Password

```
p7pvSST7clZUtByQC9o9S9v9S9v9S9v9
```

---

# Levels 25 → 33: Restricted Shell Escapes and Git

## Level 25 → 26

Escaped a **restricted shell** (`/usr/bin/showtext`).

### Technique

1. Resized terminal window
2. Forced `more` into interactive mode
3. Opened Vim using `v`
4. Spawned a shell

### Commands in Vim

```vim
:set shell=/bin/bash
:shell
```

---

## Levels 27 → 31: Git Repository Exploitation

These levels focused on **Git repository analysis**.

### Techniques Learned

Clone repository:

```bash
git clone <repo>
```

Inspect commit history:

```bash
git log
git show
```

Switch branches:

```bash
git switch dev
```

List tags:

```bash
git tag
```

These techniques revealed **passwords removed in later commits or hidden in branches**.

### Level 31 Password

```
3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
```

---

## Level 32 → 33

Escaped a **shoutshell** where all input was converted to uppercase.

### Key Insight

Positional parameters like `$0` **do not contain letters**, so they are not affected by uppercase conversion.

### Command

```bash
$0
```

This spawned a normal shell:

```
/bin/sh
```

### Password

```
od63EueTQuREvGesTruwBmc9H6uYueuS
```

---

# 🧠 Core Competencies Demonstrated

## Secure Communications

* SSH authentication
* SCP file transfer
* SSL/TLS connections using OpenSSL

## Networking

* Port scanning with `nmap`
* Service interaction using `netcat`

## Scripting & Automation

* Bash scripting
* Cron job exploitation
* Brute-force automation

## Version Control Security

* Git repository analysis
* Commit history inspection
* Branch and tag enumeration

## Linux System Security

* File permissions (octal notation)
* setuid binaries
* restricted shell escapes

---

# ✅ Summary

Completing Bandit levels **13 → 33** strengthened practical skills in:

* Secure remote access
* Networking and encrypted services
* System automation
* Linux privilege boundaries
* Git forensic analysis
* Shell escape techniques

These skills form the **foundation of real-world CTF challenges and penetration testing workflows**.

