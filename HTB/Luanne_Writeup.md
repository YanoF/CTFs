# The Nmap scan for all the open ports.

# Nmap 7.91 scan initiated Wed Dec 16 13:57:23 2020 as: nmap -oN luanne_scan.txt -sV -sC -T4 -p- -A 10.10.10.218
Warning: 10.10.10.218 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.10.218
Host is up (0.25s latency).
Not shown: 65304 closed ports, 228 filtered ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.0 (NetBSD 20190418-hpn13v14-lpk; protocol 2.0)
| ssh-hostkey: 
|   3072 20:97:7f:6c:4a:6e:5d:20:cf:fd:a3:aa:a9:0d:37:db (RSA)
|   521 35:c3:29:e1:87:70:6d:73:74:b2:a9:a2:04:a9:66:69 (ECDSA)
|_  256 b3:bd:31:6d:cc:22:6b:18:ed:27:66:b4:a7:2a:e4:a5 (ED25519)
80/tcp   open  http    nginx 1.19.0
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=.
| http-robots.txt: 1 disallowed entry 
|_/weather
|_http-server-header: nginx/1.19.0
|_http-title: 401 Unauthorized
9001/tcp open  http    Medusa httpd 1.12 (Supervisor process manager)
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=default
|_http-server-header: Medusa/1.12
|_http-title: Error response
Service Info: OS: NetBSD; CPE: cpe:/o:netbsd:netbsd

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Dec 16 14:20:41 2020 -- 1 IP address (1 host up) scanned in 1398.77 seconds


# The  nmap vulnerability scan.

Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-27 17:36 EAT
Nmap scan report for 10.10.10.218
Host is up (0.27s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
| http-enum: 
|_  /robots.txt: Robots file
| http-method-tamper: 
|   VULNERABLE:
|   Authentication bypass by HTTP verb tampering
|     State: VULNERABLE (Exploitable)
|       This web server contains password protected resources vulnerable to authentication bypass
|       vulnerabilities via HTTP verb tampering. This is often found in web servers that only limit access to the
|        common HTTP methods and in misconfigured .htaccess files.
|              
|     Extra information:
|       
|   URIs suspected to be vulnerable to HTTP verb tampering:
|     / [GENERIC]
|   
|     References:
|       http://www.mkit.com.ar/labs/htexploit/
|       http://www.imperva.com/resources/glossary/http_verb_tampering.html
|       http://capec.mitre.org/data/definitions/274.html
|_      https://www.owasp.org/index.php/Testing_for_HTTP_Methods_and_XST_%28OWASP-CM-008%29
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
9001/tcp open  tor-orport
|_ssl-ccs-injection: No reply from server (TIMEOUT)
|_sslv2-drown: 



From the vuln Scan, it is clear that our targert is vulnerable to AuthC bypass by HTTP verb tampering.

