# Armageddon writeup.

Machine name: Armageddon
Machine IP: 10.10.10.233

#Initial nmap scan:
Open ports:
-port 22: running ssh.
-port 80: running http.

nmap scan results:


Let's add armageddon.htb in the /etc/hosts

# Web reconnaisance.
So Let’s first enumerate port 80. 
Let's start with a ffuf scan. The robots.txt directory seems fun and some disallowed entries of files && directories.

Further web enum using gobuster as well.

ffuf -u http://armageddon.htb/FUZZ -t 100 -w /opt/Seclists/Discovery/Web-content/directory-list-2.3-medium.txt -e .php,.html,.txt,.py,.bak,.apk -mc 200,301 -c



By all indications, this appears to be running a Drupal CMS.

Searching for a drupal 7 exploit using searchsploit from the terminal yields some promising leads, we seem to have a winner! Heading over to msfconsole.

A search on msfconsole gives us the right exploit to deliver. Set all the necesarry fields fields and execute the payload and voila, we have a meterpreter shell:


Further enumeration: I encountered with some file that gives me MySQL login credentials:


under  /var/www/html/sites/default; there's an interesting file, settings.php. On this file, we find some credentials, see as below;


username: drupaluser
password: CQHEy@9M*m23gBVj


Lets connect to the DB:

mysql -u drupaluser -h localhost -p CQHEy@9M*m23gBVj -D drupal -e 'show databases;'




