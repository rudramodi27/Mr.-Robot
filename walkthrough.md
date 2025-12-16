* 🔍 Phase 1: Network Discovery
1️⃣ Find target IP
```
ip a
```
- This command is used to list all network interfaces and IP addresses on the attacker machine.
* 🔍 Phase 2: Network Discovery
```
sudo netdiscover -i eth1 -r 194.168.x.x
```
- This command performs an ARP scan for network discovery using the eth1 interface.
It is used to identify live hosts and discover the target machine’s IP address.
* 🌐 Phase 3: Port Scanning
```
nmap -sS -sV -p- 192.168.x.x 
```
- This command performs a TCP SYN scan on all ports of the target system.
It identifies open ports and detects the services and versions running on them.
**⚠️ DNS Warning Fix**
```
nmap -sS -sV -p- -n 192.168.194.7
```
- The -n option disables DNS resolution to avoid DNS-related warnings.
This makes the scan faster and more reliable in lab environments.
