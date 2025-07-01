Use this template in Obsidian to register your findings per target.

---
## 🖥️ Target System: `IP/Hostname`


<summary>1. 🧭 Initial Reconnaissance</summary>

- **Tools Used**: 
- **Host Discovery**:
  - Was the host alive? (Yes/No)
  - Ping Response:
- **Port Scan Results**:
  - Open Ports:
  - Service Versions:
  - OS Detection:
- **Notes**:
  - Unusual banners or TTL:

---

<summary>2. 📂 Enumeration</summary>

- **Ports/Services Analyzed**: 
- **Tools Used**:
- **Findings**:
  - Directories found:
  - Enumerated users/accounts:
  - Software versions:
- **Screenshots/Proof**:
  - ![[screenshot-name.png]]
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
