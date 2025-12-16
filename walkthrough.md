* <ins>🔍 Phase 1 </ins> : Network Discovery
  **Find target IP**
```
ip a
```
- This command is used to list all network interfaces and IP addresses on the attacker machine.
* <ins>🔍 Phase 2</ins> : Network Discovery
```
sudo netdiscover -i eth1 -r 194.168.x.x
```
- This command performs an ARP scan for network discovery using the eth1 interface.
It is used to identify live hosts and discover the target machine’s IP address.
* <ins>🌐 Phase 3 </ins> : Port Scanning
```
nmap -sS -sV -p- 192.168.x.x 
```
- This command performs a TCP SYN scan on all ports of the target system.
It identifies open ports and detects the services and versions running on them.
-  <ins> **⚠️ DNS Warning Fix**</ins>
```
nmap -sS -sV -p- -n 192.168.x.x
```
- The -n option disables DNS resolution to avoid DNS-related warnings.
This makes the scan faster and more reliable in lab environments.
* <ins>🌐 Phase 3</ins> : Web Enumeration
* **robots.txt check**
```  
http://192.168.x.x/robots.txt
```

**<ins>📌 Output:**</ins>
-
![1_3olegJiczAZe014yYac-JQ](https://github.com/user-attachments/assets/70966e8c-9e90-4ac5-abce-e5f5d06e5d7c)
- <ins>**🏳️ Flag 1 Capture**</ins>
```
curl http://192.168.xx.x/key-1-of-3.txt
```
**✅ Flag-1:**

> 07x403c8a58a1xx0d943455fbx0x724b9

