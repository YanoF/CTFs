1. Nmap scan - Port scanning.

nmap
-----
>my metholodology: nmap -T4 -p- -A -sV -oN scan.txt <Target IP>
	
>nmap -Pn  - do not ping the host.
>nmap -sS  - stealth scan
>nmap -sU  - UDP scan
>nmap -O   - OS detection
>nmap -sV  - Service version detection
>nmap -v, -vv - verbose, and very verbose, respectively.
>nmap -oX - xml output
>nmap -A  - aggressive scan
>nmap -p, -p-  - scanning a specific port and all the 65535 ports respectively.
>nmap --script - enabling the nmap scripting engine.
>nmap --script vuln - running all scripts out of the vulnerability category.
