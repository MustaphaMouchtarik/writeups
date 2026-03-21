# HTB Writeup — Interpreter (Medium)

---

## Target Information

| Field | Value |
|-------|-------|
| Box Name | Interpreter |
| Difficulty | Medium |
| IP Address | `10.129.244.184` |

---

## Enumeration

### Nmap Scan

Running an Nmap scan against the target reveals the following open ports:

| Port | State | Service |
|------|-------|---------|
| 22/tcp | open | SSH |
| 80/tcp | open | HTTP |
| 443/tcp | open | HTTPS |

### Web Enumeration

**Port 80 — HTTP**

Navigating to the HTTP endpoint reveals a **Mirth Connect** healthcare integration platform landing page.

![HTTP landing page](https://github.com/user-attachments/assets/0788b3d6-c0d8-4d4d-8027-f0ed5ef7f6b5)

**Port 443 — HTTPS**

The HTTPS endpoint presents the same interface but includes a login panel.

![HTTPS login interface](https://github.com/user-attachments/assets/f06198d6-b71f-4c14-a415-73e59bd6f1f0)

**Version Discovery**

Clicking *Launch Mirth Control* triggers the download of a `.jnlp` file. Inspecting its `codebase` attribute reveals the application version:

```
Mirth Connect v4.4.0
```

---

## Exploitation

### CVE-2023-43208 — Unauthenticated RCE

Research confirms that all Mirth Connect versions **prior to 4.4.1** are vulnerable to an unauthenticated Remote Code Execution vulnerability.

> **CVE-2023-43208** — Unauthenticated Remote Code Execution affecting Mirth Connect < 4.4.1

![CVE details](https://github.com/user-attachments/assets/ac630839-699d-4acd-ab35-3088b131b473)

**Steps taken:**

1. Cloned the public exploit repository.
2. Executed the exploit script against the target.
3. Obtained a shell as `mirth@interpreter`.

---

## Post-Exploitation

### Credential Discovery

While exploring the filesystem, a configuration file was found at:

```
/conf/mirth.properties
```

It contained plaintext database credentials:

```properties
# Database credentials
database.username = mirthdb
database.password = MirthPass123!
```

### Database Enumeration

Using the discovered credentials to access MySQL, the database `mc_bdd_prod` was identified. Two notable tables were found:

**Table: `Person`**

| Field | Value |
|-------|-------|
| username | `sedric` |

**Table: `person_password`**

| Field | Value |
|-------|-------|
| person_ID | `2` |
| password | `u/+LBBOUnadiyFBsMOoIDPLbUR0rk59kEkPU17itdrVWA/kLMt3w+w==` |

The password field appears to be **Base64-encoded binary data**, likely a hashed or encrypted value requiring further analysis.
