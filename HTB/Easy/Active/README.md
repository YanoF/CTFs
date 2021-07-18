MACHINE NAME: Active
MACHINE IP: 10.10.10.100

There are a number of open ports on this machine, including some interesting ones, 445,88 and 139.

DNS is open, so let's do a dnsrecon to see if there's anything interesting.

dnsrecond -d 10.10.10.100 -r 10.0.0.0/8

Wee'll also havea look at port 445, for any SMB related info.